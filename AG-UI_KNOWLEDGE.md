# AG-UI Protocol: Complete Knowledge Base for QI-DS

**Date:** 2026-03-25
**Purpose:** Reference document for adopting AG-UI patterns into the Quiet Intelligence Design System

---

## 1. What AG-UI Actually Is

AG-UI (Agent-User Interaction Protocol) is an open, event-based protocol that standardizes how AI agents connect to user-facing applications. It sits in a three-layer stack:

| Layer | Protocol | Owner | What It Does |
|-------|----------|-------|-------------|
| Agent to User | **AG-UI** | CopilotKit/Community | Real-time agent-to-frontend communication |
| Agent to Tools | **MCP** | Anthropic | Agents access external tools and context |
| Agent to Agent | **A2A** | Google | Agents coordinate with other agents |

A single agent can use all three simultaneously. They are complementary, not competing.

AG-UI was born from CopilotKit's partnerships with LangGraph and CrewAI. It is now adopted by Microsoft, Google ADK, AWS Strands, Mastra, Pydantic AI, LlamaIndex, and others. The .NET SDK is in progress (relevant for our backend).

---

## 2. Why Traditional Architectures Fail for Agents

REST/GraphQL assume request/response. Agents break this because they have:

- **Long-running operations** with intermediate streaming (thinking for 30 seconds, then answering)
- **Nondeterministic behavior** (the agent might call a tool, or might answer directly)
- **Mixed I/O** (text, tool calls, state updates, UI proposals in the same stream)
- **Multi-turn sessions** (conversation state persists across many interactions)
- **Human-in-the-loop** (agent pauses mid-execution for approval)

AG-UI handles all of this with a single abstraction: `run(input) -> Observable<Event>`.

---

## 3. The Core Abstraction

Everything in AG-UI flows through one interface:

```typescript
class MyAgent extends AbstractAgent {
  run(input: RunAgentInput): RunAgent {
    return () => Observable<BaseEvent>
  }
}
```

The frontend calls `agent.runAgent(input)` and subscribes to an Observable that emits typed events. That is the entire protocol surface. Everything else (messages, tools, state, reasoning, interrupts) is event types flowing through this Observable.

---

## 4. Event Taxonomy (Complete)

7 categories, ~25 event types:

### 4.1 Lifecycle Events
Control agent execution boundaries.

```
RunStarted    { threadId, runId, parentRunId?, input? }
StepStarted   { stepName }
StepFinished   { stepName }
RunFinished    { threadId, runId, result?, outcome?, interrupt? }
RunError       { message, code? }
```

Pattern: `RunStarted -> [StepStarted/StepFinished]* -> RunFinished | RunError`

### 4.2 Text Message Events
Stream text token-by-token.

```
TextMessageStart    { messageId, role }
TextMessageContent  { messageId, delta }
TextMessageEnd      { messageId }
TextMessageChunk    { messageId?, role?, delta? }  // convenience wrapper
```

Roles: `"user" | "assistant" | "system" | "developer" | "tool"`

Pattern: `Start -> Content* -> End`

The `TextMessageChunk` convenience event auto-manages the Start/Content/End lifecycle. First chunk with a new messageId emits Start. Subsequent chunks emit Content. Empty delta or end signal emits End.

### 4.3 Tool Call Events
Agent invokes frontend-defined tools.

```
ToolCallStart   { toolCallId, toolCallName, parentMessageId? }
ToolCallArgs    { toolCallId, delta }  // streamed JSON fragments
ToolCallEnd     { toolCallId }
ToolCallResult  { messageId, toolCallId, content, role? }
ToolCallChunk   { toolCallId?, toolCallName?, delta? }  // convenience
```

Pattern: `Start -> Args* -> End -> Result`

Key insight: **Tools are defined in the frontend and passed to the agent.** The frontend controls what the agent can do, not the other way around.

### 4.4 State Management Events
Bidirectional state sync between agent and frontend.

```
StateSnapshot     { snapshot }           // complete state replacement
StateDelta        { delta: JsonPatch[] } // incremental RFC 6902 patches
MessagesSnapshot  { messages }           // full conversation history
```

JSON Patch operations: `add | remove | replace | move | copy | test`

Pattern: `Snapshot -> Delta* -> [Snapshot when drift detected]`

Both agent and frontend can read and write state. Conflicts are resolved by periodic snapshot resynchronization.

### 4.5 Activity Events
Frontend-only structured messages. Never sent to the LLM.

```
ActivitySnapshot  { messageId, activityType, content, replace? }
ActivityDelta     { messageId, activityType, patch: JsonPatch[] }
```

Activity types are custom strings: `"PLAN"`, `"SEARCH"`, `"SCRAPE"`, etc. These render as structured UI cards in the frontend (progress indicators, search results, plan outlines) without polluting the conversation history sent to the model.

### 4.6 Reasoning Events
Expose (or hide) agent thinking.

```
ReasoningStart          { messageId }
ReasoningMessageStart   { messageId, role }
ReasoningMessageContent { messageId, delta }
ReasoningMessageEnd     { messageId }
ReasoningEnd            { messageId }
ReasoningEncryptedValue { subtype, entityId, encryptedValue }
ReasoningMessageChunk   { messageId, delta? }  // convenience
```

Three visibility modes:
1. **Full**: Stream complete chain-of-thought via ReasoningMessageContent
2. **Summary**: Emit condensed text + encrypted full reasoning
3. **Hidden**: Only encrypted events, no visible text

The `ReasoningEncryptedValue` supports zero-data-retention compliance. The client stores an opaque blob and forwards it back on subsequent turns.

### 4.7 Special Events

```
Raw     { event, source? }    // wrap external system events
Custom  { name, value }       // application-defined extensions
```

### 4.8 Draft Events (not yet stable)

```
MetaEvent            { metaType, payload }     // side-band annotations
RunFinished.outcome  "success" | "interrupt"   // interrupt support
RunStarted.parentRunId                         // branching support
```

---

## 5. Message Model

Messages are the accumulated result of events. 7 message types:

| Role | Purpose | Key Fields |
|------|---------|-----------|
| `user` | End-user input | content (text or InputContent[]: text, binary with mimeType/url/data) |
| `assistant` | AI response | content, toolCalls[], encryptedContent? |
| `system` | Agent instructions | content (required) |
| `tool` | Tool execution results | content, toolCallId, error?, encryptedValue? |
| `activity` | Frontend-only UI elements | activityType, content (structured JSON) |
| `developer` | Debug communications | content |
| `reasoning` | Agent thinking | content, encryptedValue? |

Activity messages are **never sent to the model**. They exist only for frontend rendering.

Tool calls inside assistant messages:
```typescript
{
  id: string,
  type: "function",
  function: { name: string, arguments: string }  // JSON-encoded args
}
```

---

## 6. Shared State Architecture

State is a structured JSON object that both agent and frontend can read and write.

**Snapshot**: Complete state replacement. Used at connection start, after errors, or when drift is detected.

**Delta**: RFC 6902 JSON Patch for incremental updates. Atomic (all ops succeed or all fail). Original state is never mutated.

```typescript
// Delta example: user changes theme preference
{ op: "replace", path: "/preferences/theme", value: "dark" }

// Delta example: agent adds a suggestion
{ op: "add", path: "/suggestions/-", value: { text: "Try compact view", confidence: 0.85 } }
```

The `fast-json-patch` library handles delta application with graceful error recovery.

**Conflict resolution**: No built-in algorithm. Best practice is periodic snapshot resynchronization when inconsistencies are detected.

---

## 7. Tool System

### Frontend-Defined Tools

Tools are registered in the frontend and passed to the agent at runtime:

```typescript
const tool = {
  name: "confirmAction",
  description: "Ask the user to confirm an action",
  parameters: {
    type: "object",
    properties: {
      action: { type: "string" },
      importance: { type: "string", enum: ["low", "medium", "high", "critical"] }
    },
    required: ["action"]
  }
}

agent.runAgent({ tools: [tool], context: [...] })
```

This is a critical architectural decision: **the frontend controls what the agent can do.** Tools are security boundaries.

### Tool Invocation Flow
1. Agent decides to call a tool -> emits `ToolCallStart`
2. Arguments stream as JSON fragments -> `ToolCallArgs` (multiple)
3. Argument streaming ends -> `ToolCallEnd`
4. Frontend executes the tool locally -> returns `ToolCallResult`
5. Result becomes part of conversation history
6. Agent uses result in subsequent reasoning

---

## 8. Capability Discovery

Agents optionally expose `getCapabilities()` returning structured metadata:

```typescript
{
  identity: { name, type, description, version, provider },
  transport: { streaming, websocket, binary, webhooks, resumable },
  tools: { supported, items[], parallelCalls, clientProvided },
  output: { structuredJson, mimeTypes },
  state: { snapshots, deltas, longTermMemory, persistent },
  multiAgent: { delegation, handoffs, subAgents },
  reasoning: { thinkingTokens, streamingReasoning, encrypted },
  multimodal: { input: { image, audio, video, pdf, file }, output: { image, audio } },
  execution: { codeExecution, sandboxing, limits },
  humanInTheLoop: { approvals, interventions, feedback },
  custom: { /* anything else */ }
}
```

Frontends use this for **adaptive UI**: only show reasoning panels if the agent supports reasoning, only show approval workflows if the agent supports interrupts, etc.

---

## 9. Generative UI

Two-step pattern for agents to propose UI:

**Step 1 (Agent says WHAT):** Agent calls `generateUserInterface` tool with:
- `description`: what the UI should do
- `data`: pre-populated values
- `output`: JSON Schema for expected submission

**Step 2 (Frontend decides HOW):** A dedicated UI generator converts the spec into actual components (JSON Forms, React, or whatever the frontend uses).

The agent never emits raw HTML/React. It proposes intent + data schema. The frontend renders using its own component library. This is why a strong component registry matters.

---

## 10. Interrupts (Human-in-the-Loop)

Agent pauses execution and requests human input:

```typescript
// Agent emits
{
  type: "RUN_FINISHED",
  outcome: "interrupt",
  interrupt: {
    id: "approval-001",
    reason: "database_modification",
    payload: { action: "DELETE", table: "users", affectedRows: 42, riskLevel: "high" }
  }
}

// User responds, frontend sends
{
  threadId: "same-thread",
  resume: {
    interruptId: "approval-001",
    payload: { approved: true, modifications: { dryRun: true } }
  }
}
```

Use cases: human approval before risky operations, information gathering mid-flow, policy enforcement, multi-step wizards, error recovery with user guidance.

---

## 11. Middleware

Event transformation layer between agent and frontend:

```typescript
const loggingMiddleware: MiddlewareFunction = (input, next) => {
  return next.run(input).pipe(
    tap(event => console.log(`[${event.type}]`, event))
  )
}
agent.use(loggingMiddleware)
```

Built-in: `FilterToolCallsMiddleware` for restricting tool access.

Middleware forms a sequential pipeline. Events flow through each layer. Only `runAgent()` executes middleware chains.

---

## 12. Agentic Protocol Stack Integration

A single agent in our system would use all three:

```
User (Angular Frontend)
  |
  | AG-UI events (SSE/HTTP)
  |
Agent Runtime
  |                    |
  | MCP (tools)        | A2A (other agents)
  |                    |
External APIs      Specialized Agents
(CRM, DB, etc.)    (analytics, NLP, etc.)
```

AG-UI handles the user-facing layer. MCP handles tool access. A2A handles agent-to-agent delegation.

---

## 13. How AG-UI Empowers Quiet Intelligence in Our DS

### Direct QI Alignments

| AG-UI Feature | QI Application |
|--------------|----------------|
| **Reasoning events with 3 visibility modes** | Progressive disclosure. Default to hidden. Let user pull to expand. QI principle: show the answer first |
| **Activity events (never sent to model)** | "Searching..." / "Analyzing..." progress cards that feel informative without being noisy. Activity events are ephemeral. They vanish when the agent finishes. Quiet |
| **State deltas (RFC 6902)** | Every state change is logged with actor, timestamp, previous value, new value, and reason. Full audit trail. Deltas provide efficient incremental sync while maintaining transparency |
| **Frontend-defined tools** | The DS controls what agents can do. No rogue tool calls. Governance by design, not by policy document |
| **Interrupts** | Agent pauses for approval on risky actions. Quiet Intelligence means knowing when to ask instead of proceeding. The interrupt UI should be calm, not alarming |
| **Capability discovery** | Adaptive UI that only shows features the connected agent supports. No phantom buttons. No "feature coming soon" |
| **TextMessageChunk auto-lifecycle** | Simplifies streaming implementation. Agent emits deltas, protocol handles Start/End. Less code, fewer bugs |
| **Reasoning traces** | Stored in plain text for audit compliance. Displayed collapsed by default (QI: progressive disclosure) but always inspectable. ZDR is NOT used in enterprise governance context |

### QI Anti-Patterns to Guard Against

| AG-UI Feature | Risk | Mitigation |
|--------------|------|-----------|
| Activity events | Over-communicating ("Searching..." for 200ms lookups) | Only emit activities for operations >2s. Short operations should be invisible |
| Reasoning visibility | Showing raw thinking = noisy | Default to hidden mode. Only expose via user-triggered expand |
| State snapshots | Frequent full-state broadcasts feel heavy | Use deltas for 95% of updates. Snapshots only for initial connection and error recovery |
| Tool call rendering | Showing every tool invocation adds noise | Tool calls are logged for audit. UI rendering is contextual: show user-facing tools, log-only for background tools |
| Interrupts | Must balance governance with usability | ALWAYS interrupt for: filesystem access, DS file changes, new component proposals, governance changes. NEVER interrupt for: read-only queries, validation checks, search operations |

---

## 14. Adoption Plan for QI-DS

### What We Adopt (Protocol Patterns, Not Dependencies)

We do not depend on CopilotKit or any specific SDK. We adopt the AG-UI event vocabulary and architectural patterns.

#### 14.1 Component Registry Enhancement

Every component in `ai-component-registry.json` gets an `agentProtocol` schema:

```json
{
  "name": "data-table",
  "agentProtocol": {
    "readable": {
      "currentSort": { "type": "object", "properties": { "column": "string", "direction": "asc|desc" } },
      "selectedRows": { "type": "array", "items": "string" },
      "filterState": { "type": "object" },
      "visibleColumns": { "type": "array", "items": "string" }
    },
    "actions": [
      {
        "name": "sortByColumn",
        "description": "Sort the table by a specific column",
        "parameters": {
          "columnId": { "type": "string", "required": true },
          "direction": { "type": "string", "enum": ["asc", "desc"] }
        }
      },
      {
        "name": "filterRows",
        "description": "Apply a filter to the table data",
        "parameters": {
          "column": { "type": "string", "required": true },
          "operator": { "type": "string", "enum": ["equals", "contains", "gt", "lt"] },
          "value": { "type": "string", "required": true }
        }
      }
    ],
    "events": {
      "emits": ["rowSelected", "sortChanged", "filterApplied"],
      "listens": ["dataRefreshed", "stateSnapshot"]
    },
    "generativeUI": {
      "canBeProposed": true,
      "requiredData": ["columns", "rows"],
      "outputSchema": { "selectedRowIds": { "type": "array" } }
    }
  }
}
```

This makes every component AG-UI-compatible by design. An AI agent can:
- READ the component's current state (readable)
- EXECUTE actions on the component (actions, mapped to AG-UI tools)
- PROPOSE the component in generative UI (generativeUI)

#### 14.2 Event-Driven Interaction Contracts

Update `ai-interaction-contracts.json` to use AG-UI event vocabulary:

```json
{
  "contract": "ai-conversation",
  "aguiMapping": {
    "userMessage": "TextMessageStart(role=user) -> TextMessageContent* -> TextMessageEnd",
    "assistantResponse": "TextMessageStart(role=assistant) -> TextMessageContent* -> TextMessageEnd",
    "thinking": "ReasoningStart -> ReasoningMessageContent* -> ReasoningEnd",
    "toolExecution": "ToolCallStart -> ToolCallArgs* -> ToolCallEnd -> ToolCallResult",
    "stateSync": "StateSnapshot | StateDelta",
    "approval": "RunFinished(outcome=interrupt) -> user responds -> RunAgentInput(resume)"
  }
}
```

#### 14.3 Angular Service Architecture

Map AG-UI abstractions to Angular DI:

```
AbstractAgent     ->  AgentService (injectable, holds connection)
RunAgentInput     ->  AgentRunConfig (interface)
Observable<Event> ->  RxJS Observable<AgUiEvent>
State management  ->  NgRx or signal-based store with JSON Patch
Tool registration ->  ToolRegistryService (components self-register tools)
Middleware        ->  RxJS pipe operators (tap, filter, map)
```

#### 14.4 Motion System Alignment

Map AG-UI lifecycle events to our motion tokens:

```
RunStarted           -> --motion-normal entrance animation
StepStarted          -> Activity card slide-in (--ease-enter, --motion-fast)
TextMessageContent   -> Token fade-in (150ms, --ease-standard)
ReasoningStart       -> Collapsed thinking block, pulse animation
ToolCallStart        -> Subtle tool indicator (only for user-facing tools)
RunFinished          -> Completion state, spring settle animation
RunError             -> Error state, --motion-fast shake
interrupt            -> Calm approval card, --ease-enter slide-up
```

#### 14.5 Capability-Driven Adaptive UI

Our page templates should adapt based on agent capabilities:

```json
{
  "template": "conversation-inbox",
  "adaptiveRules": [
    { "if": "agent.capabilities.reasoning.streamingReasoning", "show": "reasoning-trace-block" },
    { "if": "agent.capabilities.humanInTheLoop.approvals", "show": "approval-workflow-panel" },
    { "if": "agent.capabilities.multimodal.input.image", "show": "image-upload-button" },
    { "if": "agent.capabilities.state.longTermMemory", "show": "memory-indicator" },
    { "unless": "agent.capabilities.tools.supported", "hide": "tool-result-renderer" }
  ]
}
```

No phantom buttons. No features that the connected agent does not support.

---

## 15. Key Takeaways

1. **AG-UI is an event vocabulary, not a framework.** We adopt the vocabulary, not a dependency.

2. **Frontend controls the agent, not the reverse.** Tools are defined in the frontend. The DS decides what agents can do with each component.

3. **Three visibility modes for reasoning align perfectly with QI.** Default to hidden. Summary for power users. Full for debugging. Progressive disclosure.

4. **Activity events solve the "agent is working" UX problem.** They are ephemeral, structured, and never pollute the LLM context. Perfect for quiet progress indication.

5. **State deltas (JSON Patch) are the silent memory mechanism.** Agent updates preferences/context incrementally. No announcements. User just gets better results.

6. **Generative UI is a two-step process: intent + rendering.** The agent proposes WHAT. Our DS decides HOW. This is why our component registry must be rich enough for agents to reference.

7. **Interrupts are the governance mechanism.** Agent knows when to ask for approval. This maps directly to our governance lifecycle.

8. **Capability discovery prevents phantom UI.** Only show features the agent supports. This prevents the false-claim problem at the UI level.

9. **The protocol is transport-agnostic.** SSE, WebSocket, binary. Our Angular services can switch transport without changing component code.

10. **A2A and MCP complete the picture.** AG-UI handles user-facing. MCP handles tool access. A2A handles agent-to-agent. Our DS only needs to care about AG-UI. The other two protocols are backend concerns.
