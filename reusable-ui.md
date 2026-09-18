
Absolutely — here’s a README-style document framing this as an **LLM + prompt + SDUI architecture**, with an emphasis on making the UI adaptive and useful rather than simply “AI-generated screens.”

 # LLM-Driven UI: From Prompts to Useful Interfaces

 ## Overview

 Traditional applications assume that the product team knows what interface the user needs and builds that interface ahead of time.

 With Large Language Models (LLMs), we can move toward a different paradigm:

 > **Instead of building every possible interface, build a system that can compose the right interface for the user's current goal.**

 This does not mean allowing an LLM to generate arbitrary HTML, React, SwiftUI, or Compose code.

 A safer and more powerful approach is to combine:

 - **LLMs** for understanding intent and deciding what information is useful
- **Prompts** for defining interaction and UI policies
- **Server-Driven UI (SDUI)** for describing UI composition
- **Design-system components** for consistent rendering
- **Structured schemas** for reliability and validation
- **Client-side capabilities** for interaction, state, and platform integration

 The resulting architecture can be thought of as:

```
                    User
                      │
                      ▼
               Natural language
                      │
                      ▼
              ┌───────────────┐
              │      LLM      │
              │               │
              │ Intent        │
              │ Context       │
              │ Reasoning     │
              │ UI planning   │
              └───────┬───────┘
                      │
                      ▼
                UI Schema / DSL
                      │
                      ▼
              ┌───────────────┐
              │ UI Validator  │
              │ & Policy      │
              └───────┬───────┘
                      │
                      ▼
               SDUI Renderer
                      │
                      ▼
             Design System / Native UI
```

 The key idea is that **the LLM decides what interface is useful; the application decides what interfaces are possible and safe.**

---

 # 1\. The Problem With Static UI

 Most applications are built around predefined screens:

```
Home
Search
Product
Checkout
Settings
Profile
```

 This works well when user intent maps cleanly to a known workflow.

 But real user requests often don't.

 For example:

 > "I'm going to Paris for three days. I like museums, don't want to walk too much, and my hotel is near the Louvre."

 A traditional application might respond with:

```
Search results
    ↓
List of hotels
    ↓
Map
    ↓
Restaurant search
```

 The user has to manually assemble the information.

 An LLM-powered application could instead recognize that the user needs:

```
Trip overview
├── Day 1 itinerary
├── Day 2 itinerary
├── Day 3 itinerary
├── Map
├── Weather
├── Restaurants
└── Booking actions
```

 The important change is not simply adding a chatbot.

 The application can generate an **interaction model appropriate to the task**.

---

 # 2\. From "Chat With AI" to "UI for Intent"

 A common first implementation of AI applications is:

```
User
  ↓
Chat input
  ↓
LLM
  ↓
Text response
```

 This is useful, but it forces every problem through conversation.

 A more capable architecture is:

```
User intent
     ↓
LLM
     ↓
Intent + UI plan
     ↓
Structured UI
```

 For example:

```
{
  "intent": "plan_trip",
  "goal": "create a 3-day Paris itinerary",
  "needs": [
    "itinerary",
    "map",
    "restaurants",
    "weather"
  ]
}
```

 The application can then construct a UI around that intent.

---

 # 3\. SDUI as the Rendering Layer

 The LLM should not directly generate application code.

 Instead, introduce a constrained UI schema.

 For example:

```
{
  "type": "screen",
  "title": "Your Paris Trip",
  "children": [
    {
      "type": "summary",
      "title": "3 days in Paris",
      "subtitle": "Museum-focused itinerary"
    },
    {
      "type": "map",
      "locations": ["Louvre", "Musée d'Orsay"]
    },
    {
      "type": "itinerary",
      "days": [
        {
          "title": "Day 1",
          "activities": []
        }
      ]
    },
    {
      "type": "action_group",
      "actions": [
        {
          "label": "Book a restaurant",
          "action": "search_restaurants"
        }
      ]
    }
  ]
}
```

 The client already knows how to render:

```
summary
map
itinerary
action_group
```

 The LLM only decides **which components to use and how to compose them**.

---

 # 4\. The Component Registry

 The foundation of this architecture is a component registry.

```
UI Registry
│
├── Text
├── Heading
├── Image
├── Card
├── List
├── Table
├── Chart
├── Map
├── Calendar
├── Timeline
├── Form
├── Button
├── ActionGroup
├── SearchResults
└── RecommendationList
```

 Each component has a strict schema.

 For example:

```
{
  "type": "button",
  "schema": {
    "label": "string",
    "action": "Action"
  }
}
```

 The LLM cannot invent:

```
<magic-neural-interface />
```

 unless that component has explicitly been registered.

 This creates a controlled boundary between probabilistic AI and deterministic software.

---

 # 5\. Prompts Become UI Policies

 Prompts should not merely say:

 > "Generate a UI."

 Instead, the prompt defines the application's UI policy.

 For example:

```
You are the UI planner for an application.

Your job is to transform user intent into
a useful interface.

Rules:

1. Prefer existing components.
2. Never invent components.
3. Use the minimum number of components
   necessary to accomplish the task.
4. Prefer interactive components when the
   user needs to make a decision.
5. Prefer visualization when relationships
   between data matter.
6. Ask for clarification when critical
   information is missing.
7. Never expose internal system data.
8. Every action must correspond to a
   registered application capability.
9. Return valid UI schema only.
```

 This makes the prompt part of the product's **interaction architecture**.

---

 # 6\. UI Planning Instead of UI Generation

 A useful distinction is:

```
UI generation
```

 versus:

```
UI planning
```

 We generally want the latter.

 The LLM should answer:

 > What does the user need to accomplish, and what interface would help them accomplish it?

 Rather than:

 > What HTML should I generate?

 For example:

```
User:
"Compare these three laptops."

LLM plan:

Goal:
    compare products

Useful components:
    ProductComparisonTable
    ProductSummary
    RecommendationActions

Required data:
    price
    CPU
    RAM
    battery
    weight
```

 Then the deterministic application retrieves the data and renders the UI.

---

 # 7\. Separate Planning From Execution

 A robust architecture separates these stages:

```
                ┌──────────────┐
User ──────────►│ Intent        │
                │ understanding │
                └──────┬───────┘
                       ▼
                ┌──────────────┐
                │ UI planning  │
                └──────┬───────┘
                       ▼
                ┌──────────────┐
                │ Validation   │
                └──────┬───────┘
                       ▼
                ┌──────────────┐
                │ Data/actions │
                └──────┬───────┘
                       ▼
                ┌──────────────┐
                │ UI rendering │
                └──────────────┘
```

 This is preferable to:

```
User → LLM → arbitrary code → execute
```

 because it gives the application explicit control over what the model is allowed to do.

---

 # 8\. Actions Are as Important as Components

 A useful AI UI isn't just visual.

 It needs to let users **do things**.

 For example:

```
{
  "type": "button",
  "label": "Add to calendar",
  "action": {
    "type": "add_to_calendar",
    "event_id": "event_123"
  }
}
```

 The LLM can select the action, but the application owns its implementation.

```
LLM
 │
 └── action: add_to_calendar
             │
             ▼
       Action Registry
             │
             ▼
       Native capability
```

 This allows AI-generated interfaces to remain grounded in real application capabilities.

---

 # 9\. UI as a Conversation State

 The UI doesn't need to be regenerated from scratch after every message.

 Instead, treat the interface as state.

```
Conversation State
        +
Application State
        +
User Context
        ↓
     UI Plan
        ↓
   UI State
```

 For example:

```
Initial:

┌──────────────────────┐
│ Find a restaurant    │
│                      │
│ Cuisine: [Any]       │
│ Date:    [Tonight]   │
│                      │
│ [Search]             │
└──────────────────────┘
```

 The user says:

 > "Actually, make it vegetarian."

 The system can update only the relevant portion:

```
Cuisine: [Vegetarian]
```

 rather than rebuilding the entire application.

 This creates a model closer to:

```
LLM → UI state transition
```

 rather than:

```
LLM → complete UI every time
```

---

 # 10\. Progressive Disclosure

 LLMs make it possible to avoid showing everything at once.

 Suppose a user asks:

 > "Help me buy a camera."

 The first UI might be:

```
What matters most?

[Image quality]

[Video]

[Portability]

[Budget]
```

 After the user chooses "Video":

```
What type of video?

[YouTube]

[Cinematic]

[Travel]

[Professional]
```

 Then:

```
Camera comparison
────────────────────────────
Camera A
Camera B
Camera C

[Compare]
```

 The interface evolves as the system learns what the user needs.

 This can reduce the need for enormous static forms.

---

 # 11\. The UI Can Adapt to the Information

 Different data deserves different representations.

 For example:

```
Small set of values
        ↓
      Cards

Many comparable values
        ↓
      Table

Time-based information
        ↓
     Timeline

Geographic information
        ↓
       Map

Sequential process
        ↓
     Stepper

Multiple choices
        ↓
     Selection UI
```

 An LLM can select the representation based on the task.

 The design system still controls the actual visual implementation.

---

 # 12\. Multi-Platform UI

 The same UI plan can be rendered differently on different platforms.

```
             UI Schema
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
      Web      iOS      Android
       │         │         │
       ▼         ▼         ▼
    React     SwiftUI   Compose
```

 For example:

```
{
  "type": "date_picker",
  "value": "2026-09-20"
}
```

 The web client might render:

```
DatePicker
```

 while iOS renders:

```
native date picker
```

 and Android uses its native equivalent.

 The semantic UI remains the same while the implementation remains platform-specific.

---

 # 13\. The LLM Should Have Capabilities, Not Unlimited Access

 Instead of giving the model arbitrary application access:

```
LLM
  ↓
Everything
```

 define explicit capabilities:

```
Capabilities
├── search_products
├── search_restaurants
├── get_weather
├── create_calendar_event
├── update_profile
├── start_checkout
└── navigate_to
```

 The model can request:

```
{
  "action": "search_products",
  "parameters": {
    "category": "laptop",
    "budget": 1500
  }
}
```

 The application validates the request and executes it.

 This creates a clean separation:

```
LLM = planner
Application = authority
```

---

 # 14\. Schema Validation

 Every generated UI should pass through validation.

```
LLM output
    │
    ▼
JSON Schema
    │
    ├── valid ──────► render
    │
    └── invalid
          │
          ▼
      repair/retry
```

 For example:

```
{
  "type": "button",
  "label": "Checkout",
  "action": "checkout"
}
```

 is valid only if:

```
button.action ∈ registered actions
```

 This dramatically reduces the surface area for malformed model output.

---

 # 15\. A Practical Architecture

 A production implementation could look like this:

```
                         ┌───────────────┐
                         │     User      │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │ Context Layer │
                         │               │
                         │ user state    │
                         │ app state     │
                         │ conversation  │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │      LLM      │
                         │               │
                         │ intent        │
                         │ planning      │
                         │ composition   │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │ UI Schema     │
                         │ / DSL         │
                         └───────┬───────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
             ┌─────────────┐          ┌──────────────┐
             │   Validator │          │ Action       │
             │   + Policy  │          │ Registry     │
             └──────┬──────┘          └──────┬───────┘
                    │                        │
                    └───────────┬────────────┘
                                ▼
                         ┌───────────────┐
                         │ SDUI Renderer │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │ Design System │
                         └───────┬───────┘
                                 │
                                 ▼
                         Native/Web UI
```

---

 # 16\. Example End-to-End Flow

 Consider:

 > "I need to prepare for a job interview tomorrow."

 The system might infer:

```
Intent:
    interview preparation

Context:
    interview = tomorrow

Likely needs:
    preparation checklist
    company research
    practice questions
    schedule
```

 The LLM generates a UI plan:

```
{
  "type": "screen",
  "title": "Interview Preparation",
  "children": [
    {
      "type": "progress",
      "value": 0.35
    },
    {
      "type": "checklist",
      "title": "Preparation",
      "items": []
    },
    {
      "type": "question_set",
      "title": "Practice Questions"
    },
    {
      "type": "action_group",
      "actions": [
        {
          "label": "Start mock interview",
          "action": "start_mock_interview"
        }
      ]
    }
  ]
}
```

 The client renders it using known components.

 The user then says:

 > "I only have 30 minutes."

 The LLM doesn't necessarily need to create a completely different application.

 It can transform the plan:

```
30-minute preparation
────────────────────────

10 min  Company research
10 min  Practice questions
10 min  Mock interview

[Start 30-minute session]
```

 The interface has effectively become an **adaptive task environment**.

---

 # 17\. Prompt + Schema + Design System

 The three most important pieces can be viewed as:

```
                 LLM
                  │
                Prompt
                  │
                  ▼
             UI planning
                  │
                  ▼
             UI Schema
                  │
                  ▼
           Design System
                  │
                  ▼
             Actual UI
```

 Each layer has a different responsibility.

 ### Prompt

 Defines:

 - reasoning guidelines
- interaction principles
- constraints
- available components
- available capabilities

 ### Schema

 Defines:

 - what the model is allowed to express
- component properties
- actions
- state
- navigation

 ### Design system

 Defines:

 - visual language
- accessibility
- platform behavior
- interaction mechanics
- component implementation

 This separation is critical.

---

 # 18\. What the LLM Should NOT Control

 The LLM should generally not be responsible for:

```
❌ Raw HTML
❌ Arbitrary JavaScript
❌ CSS
❌ Authentication
❌ Authorization
❌ Database mutations
❌ Security decisions
❌ Payment execution
❌ Accessibility guarantees
❌ Platform-specific lifecycle
```

 Instead:

```
LLM controls:

✓ Intent interpretation
✓ Information selection
✓ UI composition
✓ Component configuration
✓ Suggested actions
✓ Progressive disclosure
✓ Interaction flow
```

 The application remains authoritative over execution.

---

 # 19\. Why This Paradigm Is Interesting

 Traditional UI assumes:

```
Product designers
      ↓
Predefined user journeys
      ↓
Predefined screens
```

 AI-native UI can instead operate as:

```
User goal
    ↓
Context
    ↓
LLM
    ↓
Dynamic interaction model
    ↓
Known UI primitives
```

 This changes the fundamental unit of product design.

 Instead of asking:

 > "What screens does our application have?"

 we can ask:

 > "What capabilities does our application have, and what interfaces can help users combine those capabilities?"

 That is a much more flexible abstraction.

---

 # 20\. Design Principle

 The central principle of this architecture is:

 > **Don't let the LLM invent the application. Give the LLM a vocabulary with which it can compose the application.**

 That vocabulary consists of:

```
Components
+ Actions
+ Data
+ State
+ Navigation
+ Policies
```

 The LLM becomes a **UI orchestrator** rather than a UI implementation engine.

---

 # 21\. Proposed MVP

 A practical first version does not need a sophisticated autonomous UI agent.

 Start with:

```
1. Define 15–30 reusable components.

2. Define a strict JSON schema.

3. Define an action registry.

4. Create a UI-planning system prompt.

5. Ask the LLM to output only the schema.

6. Validate every response.

7. Render the schema through the existing design system.

8. Track user interaction and UI effectiveness.

9. Gradually add more components and capabilities.
```

 A minimal stack could therefore be:

```
             LLM
              │
              ▼
       Structured Output
              │
              ▼
         UI Schema
              │
              ▼
      Schema Validator
              │
              ▼
        SDUI Renderer
              │
              ▼
       Design System
```

 From there, the system can evolve toward adaptive layouts, multi-step interactions, personalized workflows, and eventually more autonomous UI orchestration.

---

 # 22\. Final Mental Model

 The overall paradigm can be summarized as:

```
Traditional UI

User
 ↓
Predefined workflow
 ↓
Predefined screens
 ↓
Components
```

 versus:

```
AI-native UI

User
 ↓
Intent
 ↓
Context
 ↓
LLM
 ↓
UI Plan
 ↓
Validated Schema
 ↓
SDUI
 ↓
Design System
 ↓
Native Interface
```

 The goal isn't to replace good product design with AI.

 The goal is to make the **interface itself responsive to what the user is trying to accomplish**, while keeping the underlying application deterministic, safe, consistent, and maintainable.

 In this model, **LLMs provide adaptability, SDUI provides composition, schemas provide control, and the design system provides consistency**.

 This can also be turned into a more technical RFC next, with a concrete JSON schema, component registry, prompt architecture, TypeScript types, and an example React/Next.js implementation.
