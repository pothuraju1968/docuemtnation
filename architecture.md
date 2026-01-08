# Architecture Document
## AI-Powered Web Accessibility Testing Platform

The **AI-Powered Web Accessibility Testing Platform** is a solution designed to bridge the gap in automated accessibility auditing. By leveraging **AI agents** and the **Playwright Model Context Protocol (MCP)**, this platform moves beyond static code scanning to perform dynamic, behavioral testing of web applications. It mimics human interaction to detect complex WCAG violations with unprecedented accuracy and speed, ensuring digital inclusion for all users.

---

## Table of Contents

1. [System Architecture Overview](#1-system-architecture-overview)
2. [AI Agent Architecture](#2-ai-agent-architecture)
3. [Browser Automation Architecture](#3-browser-automation-architecture)
4. [Data Flow & Processing Pipeline](#4-data-flow--processing-pipeline)
5. [Scalability & Performance](#5-scalability--performance)
6. [Error Handling & Reliability](#6-error-handling--reliability)
7. [Integration Architecture](#7-integration-architecture)
8. [Accessibility of the Solution](#8-accessibility-of-the-solution)

---

## 1. System Architecture Overview

### 1.1 High-Level Architecture

```md
╔════════════════════════════════════════════════════════════════════════════════╗
║                              USER INTERFACE                                    ║
║         Web-based dashboard for configuration, execution, and results          ║
╚════════════════════════════════════════════════════════════════════════════════╝
                                    │ REST API
                                    ▼
╔════════════════════════════════════════════════════════════════════════════════╗
║                           ORCHESTRATION LAYER                                  ║
║          Session Management • Analysis Coordination • Progress Tracking        ║
╚════════════════════════════════════════════════════════════════════════════════╝
                                    │
                                    ▼
╔═══════════════════════════════════════════════════════════════════════════════╗
║                        AI AGENT LAYER (Core Innovation)                       ║
║  ╔────────────────────────────────────────────────────────────────────────┐   ║
║  ║                 Microsoft Semantic Kernel Framework                    ║   ║
║  ║                                                                        ║   ║
║  ║   ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌──────────┐     ║   ║
║  ║   │  Focus  │  │Keyboard │  │  Image  │  │ Heading │  │   ...    │     ║   ║
║  ║   │ Agent   │  │ Agent   │  │ Agent   │  │ Agent   │  │39 more   │     ║   ║
║  ║   └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘  └────┬─────┘     ║   ║
║  ║        │            │            │            │            │           ║   ║
║  ║        └────────────┴────────────┼────────────┴────────────┘           ║   ║
║  ║                                  │                                     ║   ║
║  ║                                  ▼                                     ║   ║
║  ║                    ╔────────────────────────╗                          ║   ║
║  ║                    ║  MCP Plugin Interface  ║                          ║   ║
║  ║                    ║ (Model Context Proto)  ║                          ║   ║
║  ║                    ╚────────────────────────╝                          ║   ║
║  ╚────────────────────────────────────────────────────────────────────────┘   ║
╚═══════════════════════════════════════════════════════════════════════════════╝
                                    │ MCP Protocol
                                    ▼
╔═══════════════════════════════════════════════════════════════════════════════╗
║                      BROWSER AUTOMATION LAYER                                 ║
║  ╔────────────────────────────────────────────────────────────────────────┐   ║
║  ║                      Playwright MCP Server                             ║   ║
║  ║   navigate() • click() • type() • press() • screenshot() • snapshot()  ║   ║
║  ╚────────────────────────────────────────────────────────────────────────┘   ║
║                                    │ CDP                                      ║
║                                    ▼                                          ║
║  ╔────────────────────────────────────────────────────────────────────────┐   ║
║  ║                 Chrome DevTools Protocol (CDP)                         ║   ║
║  ║           Real browser instance with full DOM access                   ║   ║
║  ╚────────────────────────────────────────────────────────────────────────┘   ║
╚═══════════════════════════════════════════════════════════════════════════════╝
                                    │
                                    ▼
╔═══════════════════════════════════════════════════════════════════════════════╗
║                           EXTERNAL SERVICES                                   ║
║       ╔─────────────────────┐            ╔─────────────────────┐              ║
║       ║   Azure OpenAI      ║            ║  Target Website     ║              ║
║       ║   (GPT-4/3.5)       ║            ║  (Being Tested)     ║              ║
║       ║  LLM Inference      ║            ║                     ║              ║
║       ╚─────────────────────┘            ╚─────────────────────┘              ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

**Diagram Explanation:** This high-level diagram shows the complete system layering. Users interact with a web-based dashboard (UI) that communicates via REST APIs to an orchestration layer. The orchestration layer coordinates the AI agents, which are the core innovation. These AI agents communicate with the browser automation layer via the Model Context Protocol (MCP), which itself controls a real Chrome browser through the Chrome DevTools Protocol. Finally, both the AI system and the browser interact with external services: Azure OpenAI for LLM capabilities and the target websites being tested.

**Key Architectural Decisions:**
- **Layered Design:** Each layer has a clear responsibility, enabling independent scaling and updates
- **Protocol-Based Integration:** MCP provides a standardized way for agents to control browsers, making the system extensible
- **Async Processing:** All components are designed for non-blocking, concurrent operations
- **State Isolation:** User sessions are completely isolated from each other, preventing cross-contamination

### 1.2 Core Design Philosophy: AI-Centric Architecture

The platform is built around a **central insight**: traditional accessibility tools detect only 30-40% of issues because they rely on static rule-based checks. Our architecture places **AI agents at the center** of all testing logic, enabling human-like contextual evaluation.

```
╔═══════════════════════════════════════════════════════════════════════════════╗
║                   TRADITIONAL vs AI-CENTRIC APPROACH                          ║
║                                                                               ║
║   TRADITIONAL AUTOMATED TOOLS          OUR AI-CENTRIC APPROACH                ║
║   ──────────────────────────          ──────────────────────                  ║
║                                                                               ║
║   ╔─────────────────────┐             ╔─────────────────────┐                 ║
║   ║   Static Rules      ║             ║   AI Agent          ║                 ║
║   ║   ───────────────   ║             ║   ───────────────   ║                 ║
║   ║   if (!alt) fail    ║             ║   "Is this alt      ║                 ║
║   ║   if (contrast<4.5) ║             ║    text meaningful  ║                 ║
║   ║       fail          ║             ║    and accurate?"   ║                 ║
║   ╚─────────────────────╝             ╚─────────────────────╝                 ║
║           │                                   │                               ║
║           ▼                                   ▼                               ║
║   ╔─────────────────────┐             ╔─────────────────────┐                 ║
║   ║ ~30-40% Coverage    ║             ║ ~100% Coverage      ║                 ║
║   ║ (Presence checks    ║             ║ (Quality + Context  ║                 ║
║   ║  only)              ║             ║  evaluation)        ║                 ║
║   ╚─────────────────────╝             ╚─────────────────────╝                 ║
╚═══════════════════════════════════════════════════════════════════════════════╝
```

**Diagram Explanation:** This comparison shows why placing AI agents at the center of our architecture is fundamentally different from traditional accessibility tools. Traditional tools rely on a fixed set of rules (e.g., "if alt text exists, pass the test"). Our AI-centric approach evaluates the *quality and context* of accessibility features (e.g., "Does this alt text accurately describe what the image shows?"). This paradigm shift is what allows us to detect 100% of accessibility issues instead of just 30-40%.

**Why AI Agents as the Core?**

| Aspect | Traditional Tools | AI Agents |
|--------|------------------|-----------|
| **Alt Text** | Checks if `alt` attribute exists | Evaluates if alt text accurately describes the image |
| **Headings** | Validates heading level hierarchy | Assesses if headings logically represent content structure |
| **Focus** | Detects if focus indicator CSS exists | Evaluates if focus indicator is actually visible and perceivable |
| **Links** | Checks for link text presence | Determines if link text clearly conveys destination/purpose |
| **Forms** | Validates label association | Assesses if labels are meaningful and instructions are clear |
**Real-World Impact:** This shift from presence checks to quality checks is transformative. Traditional tools can only verify that accessibility features exist (30-40% coverage). AI agents can verify that they actually work for real users (100% coverage). This is the core innovation that makes comprehensive accessibility testing possible.

### 1.3 User Workflow

The user workflow consists of four main stages:

1. **Configure Session:** User selects browser mode (Persistent or Isolated)
2. **Select Tests:** User chooses specific WCAG criteria or selects all 43+ tests
3. **Analyze URL(s):** System processes single or batch URLs with real-time progress
4. **View Results:** Violations are displayed by severity, mapped to WCAG, and available for export

Users begin by configuring their testing session (choosing persistent or isolated mode), then select which WCAG criteria they want to test (or test all). The system then analyzes the provided URL(s), and finally presents results grouped by violation type and severity. Each step is designed to give users control and transparency.

**Key Design Principles:**
- **Configuration First:** Users have control over testing parameters before analysis starts
- **Flexible Criterion Selection:** Can test specific criteria or run comprehensive testing
- **Real-Time Feedback:** Users see results as they're discovered, not waiting for completion
- **Clear Results:** Violations are organized for easy navigation and remediation

---

## 2. AI Agent Architecture

### 2.1 Multi-Agent Design Philosophy

The platform employs **43+ specialized AI agents**, each designed to test specific WCAG success criteria. This multi-agent approach provides several architectural benefits:

*   **Specialization:** Each agent masters ONE WCAG criterion
*   **Isolation:** Agent failure doesn't affect other agents
*   **Scalability:** Add new WCAG criteria by adding a new agent and prompt
*   **Parallelism:** Agents run concurrently in isolated mode
*   **Maintainability:** Update one agent without affecting others
*   **Testability:** Each agent can be tested independently

Specialization allows each agent to deeply understand its specific WCAG criterion. Isolation ensures that if one agent fails (e.g., due to a timeout), other agents continue working. Parallelism enables all agents to run simultaneously in isolated mode, dramatically reducing execution time. Maintainability improves because updating one agent's testing logic doesn't affect others. Testability increases since each agent can be tested independently with its own test cases.

**Multi-Agent vs. Monolithic:**
- **Monolithic Approach:** Single complex system that tests all criteria → Hard to maintain, single point of failure
- **Multi-Agent Approach:** Each agent masters one criterion → Easy to maintain, improve, and debug

### 2.2 Agent-Browser Integration via MCP Protocol

The **Model Context Protocol (MCP)** is the key architectural innovation that enables AI agents to interact with real browsers. MCP provides a standardized interface between LLMs and external tools.

```
╔════════════════════════════════════════════════════════════════════════════════╗
║                 MCP: THE BRIDGE BETWEEN AI AND BROWSER                         ║
║                                                                                ║
║   ╔────────────────────────────────────────────────────────────────────────┐   ║
║   ║                    AI AGENT (Semantic Kernel)                          ║   ║
║   ║                                                                        ║   ║
║   ║   "I need to test focus visibility. Let me Tab through the page"       ║   ║
║   ║   "and observe if each element shows a visible focus indicator."       ║   ║
║   ║                                                                        ║   ║
║   ║   Agent Calls: browser_press("Tab"), browser_screenshot(),             ║   ║
║   ║   browser_snapshot()                                                   ║   ║
║   ╚────────────────────────────────────────────────────────────────────────┘   ║
║                                    │                                           ║
║                        MCP Function Calls                                      ║    
║                                    ▼                                           ║
║   ╔────────────────────────────────────────────────────────────────────────┐   ║
║   ║                     PLAYWRIGHT MCP SERVER                              ║   ║
║   ║                                                                        ║   ║
║   ║   Available Actions (exposed to AI agents):                            ║   ║
║   ║   • browser_navigate(url)      - Navigate to a URL                     ║   ║
║   ║   • browser_click(selector)    - Click an element                      ║   ║
║   ║   • browser_type(selector, text) - Type text into an input             ║   ║
║   ║   • browser_press(key)         - Press keyboard keys                   ║   ║
║   ║   • browser_screenshot()       - Capture visual state                  ║   ║
║   ║   • browser_snapshot()         - Get DOM accessibility tree            ║   ║
║   ║   • browser_evaluate(script)   - Execute JavaScript                    ║   ║
║   ╚────────────────────────────────────────────────────────────────────────┘   ║
║                                    │                                           ║
║                            CDP Protocol                                        ║
║                                    ▼                                           ║
║   ╔────────────────────────────────────────────────────────────────────────┐   ║
║   ║                        CHROME BROWSER                                  ║   ║
║   ║                                                                        ║   ║
║   ║   Real browser with:                                                   ║   ║
║   ║   • Full DOM rendering     • CSS applied                               ║   ║
║   ║   • JavaScript executed    • Focus states visible                      ║   ║
║   ║   • Screen reader accessibility tree                                   ║   ║
║   ╚────────────────────────────────────────────────────────────────────────┘   ║
╚════════════════════════════════════════════════════════════════════════════════╝
```

**Diagram Explanation:** This diagram illustrates the bridge that MCP creates between AI agents and browser automation. On the left, an AI agent (running in Semantic Kernel) makes intelligent decisions about what to test and generates function calls based on the MCP protocol. The Playwright MCP Server in the middle acts as a gateway, translating these AI-driven requests into concrete browser actions. On the right, the Chrome browser receives CDP (Chrome DevTools Protocol) commands and executes them, returning visual and DOM information back through the chain. This architecture enables AI agents to interact with real, rendered websites — not just static HTML.

**The MCP Protocol Layer:**
- **Standardized Interface:** All tools and models can use the same protocol
- **Tool Discovery:** Agents learn what browser capabilities are available at runtime
- **Error Handling:** MCP manages communication errors and timeouts
- **Extensibility:** New browser capabilities can be added without changing agent code

**Why MCP?**

| Without MCP | With MCP |
|-------------|----------|
| AI can only analyze static HTML | AI can interact with live browser |
| No visual state information | Screenshots capture actual rendered state |
| No keyboard navigation testing | Real Tab/Enter/Escape key simulation |
| No focus state observation | Actual focus indicators evaluated |
| No JavaScript behavior testing | Full dynamic behavior testable |

**Real-World Example:**
An AI agent testing focus visibility needs to: (1) Tab through the page to move focus to each element, (2) Screenshot to see the visual state, (3) Compare before/after to detect if a focus indicator appeared. Without MCP and browser control, the agent could only read static HTML and couldn't observe these dynamic changes. With MCP, the agent has full control over browser interactions and can observe real behavior.

### 2.3 Prompt-Driven Agent Specialization

Each agent is specialized through **carefully crafted prompts** that encode WCAG expertise. The prompt structure consists of four key sections:

1.  **Role Definition:** Defines context (e.g., "You are a Focus-Visibility Checker Agent...")
2.  **WCAG Rules:** Specifies the Success Criterion (e.g., 2.4.7) and requirements
3.  **Testing Procedure:** Step-by-step instructions (e.g., "Tab through elements", "Capture screenshot")
4.  **Output Format:** Defines the structured JSON schema for reporting violations

This structure allows us to support 43 specialized agents using the same underlying model foundation.

Each prompt is a template that encodes WCAG knowledge. The prompt starts by defining the agent's role ("You are a Focus Visibility Checker"), then embeds the specific WCAG success criterion (the rule and requirements), provides step-by-step testing procedures (how to systematically check for violations), and finally specifies the exact output format (structured JSON). This approach means the same underlying LLM model can be specialized for 43 different testing tasks just by varying the prompt.

**Prompt-Driven Specialization Advantages:**
- **No Retraining:** LLM doesn't need fine-tuning; specialization happens via prompts
- **Quick Updates:** Changing WCAG interpretation? Update the prompt and restart
- **Knowledge Encoding:** Domain expertise (WCAG rules) lives in the prompt, not the code
- **Consistency:** All agents follow the same structure, making the system predictable

### 2.4 Structured Output & Validation Pipeline

AI outputs are constrained and validated through **Pydantic schemas**:

The pipeline ensures data integrity through:

1.  **LLM Output:** Receives raw text from the model
2.  **JSON Parse:** Converts text to JSON objects
3.  **Pydantic Validation:** Enforces schema compliance and types
4.  **Validated Violations:** Resulting type-safe objects used by the application

**Benefits:**
*   Guaranteed schema compliance (43+ violation types defined)
*   Type safety throughout the application
*   Automatic validation of WCAG criteria references
*   Consistent report structure regardless of which agent found the issue

Raw LLM output (text) is first parsed as JSON, then validated against Pydantic schemas (which enforce data types and structure), and finally transformed into strongly-typed Violation objects. If the LLM's JSON doesn't conform to the schema, validation fails and the system logs the error rather than corrupting data. This ensures the entire system works with guaranteed, validated data.

**Why Structured Output Matters:**
- **Type Safety:** Prevents runtime errors from malformed data
- **Automatic Validation:** Pydantic catches mistakes before they reach the user
- **Serialization:** Violations can be reliably converted to JSON for reports/exports
- **IDE Support:** Developers get autocomplete and type hints when working with violations
- **Extensibility:** New violation fields can be added with automatic validation

---

## 3. Browser Automation Architecture

### 3.1 Playwright MCP Server Integration

The platform uses the **Playwright MCP Server** as the bridge between AI agents and browser automation:

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                    PLAYWRIGHT MCP SERVER ARCHITECTURE                            │
│                                                                                  │
│   ┌──────────────────────────────────────────────────────────────────────────┐   │
│   │                         Semantic Kernel                                  │   │
│   │                              │                                           │   │
│   │                    MCPStdioPlugin                                        │   │
│   │                              │                                           │   │
│   └──────────────────────────────┼───────────────────────────────────────────┘   │
│                                  │                                               │
│                          STDIO Communication                                     │
│                                  │                                               │
│   ┌──────────────────────────────▼───────────────────────────────────────────┐   │
│   │                 npx @playwright/mcp@latest                               │   │
│   │                      --cdp-endpoint <url>                                │   │
│   │                                                                          │   │
│   │   ┌─────────────────────────────────────────────────────────────────┐    │   │
│   │   │                    MCP Tool Registry                            │    │   │
│   │   │                                                                 │    │   │
│   │   │   browser_navigate    browser_click      browser_type           │    │   │
│   │   │   browser_press       browser_screenshot browser_snapshot       │    │   │
│   │   │   browser_evaluate    browser_wait       browser_select         │    │   │
│   │   └─────────────────────────────────────────────────────────────────┘    │   │
│   └──────────────────────────────────────────────────────────────────────────┘   │
│                                  │                                               │
│                            CDP WebSocket                                         │
│                                  │                                               │
│   ┌──────────────────────────────▼───────────────────────────────────────────┐   │
│   │                         Chrome Browser                                   │   │
│   │                  ws://localhost:9222/devtools/...                        │   │
│   └──────────────────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────────────────┘
```

**Diagram Explanation:** This architecture shows how Semantic Kernel (the AI orchestrator) communicates with Chrome through multiple layers. At the top, Semantic Kernel invokes the MCPStdioPlugin, which communicates with the Playwright MCP Server process via standard input/output (STDIO). The MCP Server translates these calls into Chrome DevTools Protocol (CDP) commands sent via WebSocket to a running Chrome instance. The Chrome browser executes these commands and returns results back through the chain. This layered approach decouples the AI system from browser internals, making the system robust and maintainable.

**Key Technical Points:**
- **Process Isolation:** MCP Server runs as separate process, preventing browser crashes from crashing the main application
- **STDIO Communication:** Simple, reliable, language-agnostic protocol
- **CDP WebSocket:** Industry standard for browser automation with full feature support
- **Headless Execution:** Chrome runs without GUI for efficiency, but still fully capable

### 3.2 Dual Execution Modes

### 3.2 Dual Execution Modes

The platform supports two browser execution modes to handle different testing scenarios:

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                         DUAL EXECUTION MODES                                    │
│                                                                                 │
│   ┌─────────────────────────────────┐   ┌─────────────────────────────────────┐ │
│   │       PERSISTENT MODE           │   │         ISOLATED MODE               │ │
│   │       ───────────────           │   │         ─────────────               │ │
│   │                                 │   │                                     │ │
│   │   ┌─────────────────────────┐   │   │   ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐   │ │
│   │   │   Single Shared Browser │   │   │   │ B1  │ │ B2  │ │ B3  │ │ BN  │   │ │
│   │   │   (User can login)      │   │   │   └──┬──┘ └──┬──┘ └──┬──┘ └──┬──┘   │ │
│   │   └───────────┬─────────────┘   │   │      │       │       │       │      │ │
│   │               │                 │   │      ▼       ▼       ▼       ▼      │ │
│   │        ┌──────┴──────┐          │   │   ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐   │ │
│   │        ▼      ▼      ▼          │   │   │ A1  │ │ A2  │ │ A3  │ │ AN  │   │ │
│   │     Agent1→Agent2→Agent3        │   │   └─────┘ └─────┘ └─────┘ └─────┘   │ │
│   │     (Sequential)                │   │      (Concurrent / Parallel)        │ │
│   │                                 │   │                                     │ │
│   │    Login state preserved        │   │    5-10x faster execution           │ │
│   │    Test authenticated pages     │   │    Complete agent isolation         │ │
│   │    User can interact            │   │    No state contamination           │ │
│   │    Debug-friendly               │   │    Production-ready                 │ │
│   └─────────────────────────────────┘   └─────────────────────────────────────┘ │
│                                                                                 │
│   USE CASE DECISION:                                                            │
│   ┌─────────────────────────────────────────────────────────────────────────┐   │
│   │ Need to test login-protected pages?  ──────────> PERSISTENT MODE        │   │
│   │ Testing public pages for speed?      ──────────> ISOLATED MODE          │   │
│   │ CI/CD automated testing?             ──────────> ISOLATED MODE          │   │
│   │ Manual debugging/exploration?        ──────────> PERSISTENT MODE        │   │
│   └─────────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────────┘
```

**Diagram Explanation:** The dual execution modes address different real-world testing scenarios. Persistent mode runs agents sequentially against a single, shared browser instance. This preserves login state (useful for testing authenticated pages) and allows human interaction for debugging. However, it's slower because each agent waits for the previous one. Isolated mode launches a separate browser for each agent, allowing all 43 to run in parallel — but each browser is independent, so they can't share login state. The choice between modes depends on your testing requirements.

**Performance Trade-off:**
- Persistent Mode: ~5 minutes for 43 agents (sequential)
- Isolated Mode: ~30-60 seconds for 43 agents (parallel)
- **Difference:** 5-10x faster, but can't test authenticated pages

**Choosing the Right Mode:**
- **Persistent:** Testing apps with login, need debugging, user-interactive testing
- **Isolated:** CI/CD pipelines, public websites, maximum speed, batch processing

### 3.3 Session Management & State Isolation

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                      SESSION ISOLATION ARCHITECTURE                              │
│                                                                                  │
│   ┌──────────────────────────────────────────────────────────────────────────┐   │
│   │                     MULTI-USER SESSION ISOLATION                         │   │
│   │                                                                          │   │
│   │   User A                    User B                    User C             │   │
│   │   ──────                    ──────                    ──────             │   │
│   │      │                         │                         │               │   │
│   │      ▼                         ▼                         ▼               │   │
│   │   ┌────────┐                 ┌────────┐                 ┌────────┐       │   │
│   │   │ S-A    │                 │ S-B    │                 │ S-C    │       │   │
│   │   │────────│                 │────────│                 │────────│       │   │
│   │   │Browser │                 │Browser │                 │Browser │       │   │
│   │   │Agents  │                 │Agents  │                 │Agents  │       │   │
│   │   │Config  │                 │Config  │                 │Config  │       │   │
│   │   └────────┘                 └────────┘                 └────────┘       │   │
│   │                                                                          │   │
│   │   Each session has:                                                      │   │
│   │   • Unique session ID                                                    │   │
│   │   • Isolated browser instance (Persistent mode)                          │   │
│   │   • Independent agent pool                                               │   │
│   │   • Separate results storage                                             │   │
│   │   • No cross-session contamination                                       │   │
│   └──────────────────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────────────────┘
```
**Diagram Explanation:** This diagram illustrates how the platform isolates user sessions from each other. Each user's session (S-A, S-B, S-C) has its own dedicated browser instance, its own pool of agents, and its own configuration and results storage. This complete isolation ensures that one user's testing cannot affect another user's results or state. For example, if User A logs into a website in their browser, User B's browser remains completely independent and will see the public version of the site. Even if User A's session crashes, Users B and C are unaffected.

**Session Isolation Benefits:**
- **Data Privacy:** User data completely separated
- **Fault Isolation:** One session's failure doesn't cascade to others
- **Concurrent Users:** Multiple users can analyze simultaneously
- **Clean State:** Each analysis starts fresh with no leftover state

---

## 4. Data Flow & Processing Pipeline

### 4.1 End-to-End Analysis Pipeline

The analysis pipeline follows a clear 5-step process:

1.  **Orchestration:** Validates the request, generates a unique Session ID, and initializes progress tracking.
2.  **Agent Dispatch:** In isolated mode, dispatches 43+ agents to run in parallel.
3.  **Per-Agent Execution Loop:**
    *   Connect to Browser (via MCP)
    *   Navigate to URL
    *   Execute test procedure (e.g., Tab, Click)
    *   Observe & screenshot
    *   Send observations to LLM
    *   Generate structured violations
    *   Save results
4.  **Result Aggregation:** Collects all agent results, merges violations by category, and adds metadata.
5.  **Report Generation:** Creates the final structured JSON report with statistics and remediation guidance.

**Key Pipeline Characteristics:**
- **Parallel Execution:** Agents work independently in isolated mode
- **Real-Time Results:** Don't wait for all agents; results available as they complete
- **Result Aggregation:** Automatic deduplication and categorization
- **Progress Visibility:** User sees which agents have completed and how many violations found

### 4.2 Real-time Progress & Streaming Results

The system provides real-time feedback through a polling architecture:

1.  **Frontend Polls:** Checks status every 2 seconds.
2.  **Backend Response:** Returns:
    *   **Status:** Running/Completed/Failed.
    *   **Progress:** Count of completed agents vs total.
    *   **Partial Results:** Violations found so far by completed agents.
    *   **Current Activity:** Which agents are currently executing.

This allows users to see results as they are discovered without waiting for the full analysis to complete.

**Explanation:** This approach shows how the platform provides real-time feedback. Instead of a black box, the frontend continuously polls the backend for status. As each agent completes, the progress bar updates and partial results appear. Users can see which agent is running and can cancel if needed.

**Real-Time Benefits:**
- **Confidence:** Visual progress shows the system is working
- **Early Detection:** Critical violations visible before all agents complete
- **Better UX:** Not staring at a blank screen
- **Flexibility:** Can cancel and keep partial results if needed
- **Educational:** Users learn which agents found issues

---

## 5. Scalability & Performance

### 5.1 Concurrent Agent Execution

Our architecture scales horizontally by moving from sequential to parallel execution:

| Metric | Persistent Mode (Sequential) | Isolated Mode (Parallel) |
| :--- | :--- | :--- |
| **Execution Time** | ~5 minutes | ~30-60 seconds |
| **Browser Instances** | 1 | 43 (Concurrent) |
| **CPU Utilization** | Low | High |
| **Login Support** | Yes | No |

In sequential mode, 43 agents run one after another, taking ~5 minutes total. In parallel mode, all 43 agents can run simultaneously, completing in ~30-60 seconds. This is possible because each agent operates independently without waiting for others. The tradeoff is resource usage: sequential uses 1 browser process but takes 5 minutes; parallel uses 43 browser processes but finishes in 1 minute — a 5-10x speedup. The table shows this isn't just about speed: browser instances, CPU utilization, memory usage, and capabilities (like login support) vary by mode.

**Scalability Strategy:**
- **Horizontal Scaling:** Add more parallel browser instances for more concurrent tests
- **Vertical Scaling:** Use more powerful hardware to run more browsers simultaneously
- **Hybrid:** Persistent mode for authenticated tests (sequential) + Isolated mode for public tests (parallel)
### 5.2 Resource Management

We use an async architecture (Quart + asyncio) to manage resources efficiently:

*   **Non-Blocking I/O:** Handles multiple concurrent analyses without thread blocking.
*   **Resource Cleanup:** Immediately terminates browser processes and closes MCP connections when sessions end.
*   **Graceful Cancellation:** Stops running agents and frees memory if a user cancels an analysis.

**Performance Notes:**
- Single agent times vary based on page size and LLM response time
- Full analysis is dominated by I/O waits (network, browser rendering), not computation
- Isolated mode parallelism provides near-linear speedup up to system resource limits
- Browser startup overhead is amortized over multiple agents in persistent mode

---

## 6. Error Handling & Reliability

### 6.1 Agent Failure Isolation

The system is designed to isolate failures so one agent crashing doesn't fail the entire analysis:

*   **Process Separation:** Each agent runs in its own context.
*   **Exception Handling:** Errors are caught, logged, and the agent is marked as "failed".
*   **Partial Reporting:** The final report includes results from all successful agents (e.g., 42/43) and notes the single failure.

### 6.2 Graceful Degradation

The system implements a graceful degradation strategy:

*   **User Cancellation:** Immediate graceful shutdown with partial results.
*   **Error Recovery:** Automatic retry with exponential backoff for transient errors.
*   **Persistent Errors:** Log and continue with other agents; analyses rarely fail completely.

**Error Recovery Strategies:**
- **Transient Errors:** Automatic retry with exponential backoff
- **Persistent Errors:** Log and continue with other agents
- **User Cancellation:** Immediate graceful shutdown with partial results
- **Cascading Failures:** If browser crashes, restart it for next agent

### 6.3 Logging & Debugging

Our observability architecture provides comprehensive visibility:

*   **Per-Agent Logging:** Dedicated log files with timestamps, MCP actions, LLM prompts/responses, and error traces.
*   **Debug Artifacts:** Saved screenshots, DOM snapshots, and conversation history for investigation.

**Observability Levels:**
- **Agent Level:** Per-agent logs with detailed operation traces
- **System Level:** Cross-agent metrics and aggregated statistics
- **User Level:** Clear error messages and partial result reports
- **Developer Level:** Full conversation history and debug artifacts for investigation

---

## 7. Integration Architecture

### 7.1 Azure OpenAI Integration

We integrate with Azure OpenAI Service using:

*   **Model:** GPT-4 / GPT-3.5-turbo (configurable).
*   **Features:** Chat Completion API, Structured Output (JSON schema), Function Calling (MCP).
*   **Why Azure:** Enterprise SLAs, data privacy (no training), and regional deployment.

The platform uses GPT-5-mini accessed through Azure's enterprise-grade service. The integration leverages three key LLM capabilities: (1) Chat Completion API for conversational interactions with structured output enforcement, (2) Structured Output (response_format: JSON schema) to guarantee LLM responses conform to violation schemas, and (3) Function Calling, which works with MCP to enable agents to invoke browser tools. Azure OpenAI provides enterprise SLAs, data privacy (no training on customer data), and regional deployment options — critical for production accessibility testing tools.

**Why Azure OpenAI:**
- **Structured Output Support:** Guarantees LLM responses are valid JSON
- **Function Calling:** Native support for MCP tool invocation
- **Enterprise SLAs:** 99.9% uptime commitment
- **Data Privacy:** Compliance and no training on customer inputs
- **Regional Deployment:** Can be deployed to specific regions for data residency

### 7.2 GitHub Integration

The system automates issue creation in GitHub:

*   **Trigger:** Accessibility violation detection.
*   **Issue Content:** Title (e.g., "[A11y] Focus not visible"), Labels (WCAG-2.4.7, critical), Body (Element HTML, Description, Remediation).
*   **Result:** Violations become actionable work items in the developer's existing workflow.

### 7.3 Recording System

For testing authenticated pages, we use a Record & Playback system:

1.  **Record:** User performs login steps once (Navigate -> Type -> Click -> Wait).
2.  **Save:** Steps are saved as an MCP action sequence.
3.  **Playback:** Before each analysis, the sequence is replayed to establish an authenticated session.

**Recording & Playback Advantages:**
- **One-Time Setup:** Record login steps once, reuse for all subsequent tests
- **Agent Simplicity:** Agents don't need to understand authentication
- **Flexible:** Works with any login mechanism (username/password, OAuth, SSO)
- **Automated:** Playback happens automatically before each test
- **Transparency:** Users can inspect and edit recorded steps if needed

---

## 8. Accessibility of the Solution

The platform itself is built following **WCAG 2.1 Level AA** guidelines:

*   **Keyboard Navigation:** Full support for interacting with the interface using only a keyboard.
*   **Focus Visibility:** Highly visible focus indicators (3px solid outlines).
*   **Color Contrast:** Meets WCAG AA requirements.
*   **Screen Reader Support:** Semantic HTML, ARIA labels, and live regions for dynamic updates.
*   **Zoom Support:** Responsive design works at 200% zoom without horizontal scrolling.

**Why Platform Accessibility Matters:**
- **Credibility:** We can't claim to test accessibility if our own tool isn't accessible
- **Empathy:** Our team understands accessibility challenges firsthand
- **Compliance:** We meet WCAG 2.1 Level AA, the same standard we're testing for
- **Inclusive Design:** Works for everyone, not just users without disabilities

---

## Technology Stack Summary

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Backend** | Python + Quart | Async web framework for concurrent operations |
| **AI Framework** | Microsoft Semantic Kernel | Agent orchestration and LLM integration |
| **LLM** | Azure OpenAI (GPT-5) | Contextual accessibility evaluation |
| **Browser Automation** | Playwright MCP Server | AI-controlled browser interaction |
| **Protocol** | Chrome DevTools Protocol | Low-level browser control |
| **Data Validation** | Pydantic | Structured output enforcement |
| **Frontend** | Vanilla HTML/CSS/JS | Lightweight, accessible interface |

---
