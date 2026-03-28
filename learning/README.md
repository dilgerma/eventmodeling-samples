# Event Modeling Learning Board

This board is a hands-on introduction to [Event Modeling](https://eventmodeling.org) — a lightweight method for designing information systems by describing them as a series of events over time.

The backup file `eventmodel-backup-2026-03-28.json` can be imported into [eventmodeling.org](https://app.eventmodeling.org) to view the interactive board.

---

## What the Board Contains

The board progresses from minimal examples to a complete real-world scenario:

| Timeline | Purpose |
|---|---|
| Simple examples (1–5) | Introduce each lane type and pattern in isolation |
| Shopping Cart (6) | Full e-commerce model applying all four patterns end-to-end |

The shopping cart scenario models: cart creation, item management, inventory & pricing, cart submission, and external integration — including a failure path.

---

## Core Concepts

### Lane Types

| Lane | Color | Represents |
|---|---|---|
| **Actor** | Blue | UI screens the user sees |
| **Interaction** | Orange | Commands the user or system issues |
| **Events** | Orange (dark) | Facts that happened — immutable, past-tense |
| **Internal System** | Purple | Automations within the domain |
| **External System** | Gray | Third-party integrations |
| **Spec Lane** | Yellow | Acceptance criteria and specs |
| **Feedback** | Red | Missing data or open questions |

### Elements

- **Screen** — a UI view in the Actor lane
- **Command** — an intent to change state (e.g. `Add Item`, `Submit Cart`)
- **Event** — a recorded fact (e.g. `Item Added`, `Cart Submitted`)
- **Read Model** — a projection built from one or more events; fields prefixed with `*` are key/partition fields
- **Automation** — a system trigger that issues commands based on a read model state
- **Slice** — a named vertical cut representing a single business capability

---

## The Four Patterns

All event models are composed of exactly four patterns:

### 1. State Change
> A user or system causes something to happen.

```
Screen → Command → Event
Automation → Command → Event
```

**Example:** Screen "Add Item" → Command `Add Item(aggregateId, itemId, ...)` → Event `Item Added`

---

### 2. State View
> Events are projected into a queryable read model.

```
Event(s) → Read Model
```

**Example:** `Item Added` + `Item Removed` → Read Model `cart items(*aggregateId, *itemId, description, price, totalPrice)`

---

### 3. Automation
> A read model triggers commands automatically — a combination of State View and State Change.

```
Event(s) → Read Model (TODO list) → Automation → Command → Event
```

**Example:** `Inventory Changed` → Read Model `Inventories` → Automation → `Change Inventory` command → `Inventory Changed`

---

### 4. Translation
> Information crosses a system boundary — an external event is translated into a command in the domain.

```
External Event → [Translation] → Command → Event
```

**Example:** External system publishes `External Cart Published` → translated into an internal `Cart Published` event

---

## Shopping Cart — Full Flow

```
User                         Domain                        External
──────────────────────────────────────────────────────────────────
Screen ──► Add Item ────────► Item Added
Screen ──► Remove Item ─────► Item Removed        cart items (Read Model)
Screen ──► Clear Cart ──────► Cart Cleared

                              Inventory Changed ──► Inventories (Read Model)
                              Price Changed ──────► Changed Prices (Read Model)

Screen ──► Submit Cart ─────► Cart Submitted ─────► Submitted Cart Data (Read Model)

Screen ──► Publish Cart ────► External Cart Published ──► (external system)
                           └► Cart Publication Failed
                              Cart Published
```

---

## Validating Information Completeness

A model is only correct when every command has access to all the data it needs. The Feedback lane is used to flag gaps:

- **Red arrows** point to missing data
- **Feedback nodes** describe what information is absent or unclear
- **Spec nodes** define acceptance criteria for a slice

Walk each slice column top-to-bottom: does the command have all fields it needs? Does the event carry enough data to build the read model? If not, add a feedback note.

---

## How to Apply the Patterns

1. Start with the **happy path** in time order — list events as they happen
2. Place **screens** above each event that a user triggers
3. Add **commands** between screens and events
4. Build **read models** from the events that feed into each command
5. Identify **automations** where a read model drives a follow-up command
6. Add **translations** at every system boundary
7. Use the **Spec lane** and **Feedback lane** to validate completeness

---

## References

- [eventmodeling.org](https://eventmodeling.org) — method overview and documentation
- [app.eventmodeling.org](https://app.eventmodeling.org) — online tool (import the `.json` file here)
- Original Miro board referenced in pattern documentation nodes on the board