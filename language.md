# WiringSim DSL Guide

The WiringSim DSL (Domain-Specific Language) provides a text-based way to define electrical circuits. This document explains the syntax and structure of the language.

## General Structure

A configuration file consists of three main parts:
1.  **Component Definitions**: Declares all the electrical components in the simulation.
2.  **Cable Definitions**: Declares the cables that run between components.
3.  **Wiring Blocks**: Describes the specific connections inside each component.

Comments can be added on any line using `//`.

---

## 1. Component Definition

Components are the primary elements on the workspace, like outlets, switches, and breakers.

**Syntax:**
`component <id>: <type> at (<x>, <y>)`

-   `<id>`: A unique identifier for the component (e.g., `c1`, `c10`).
-   `<type>`: The type of component. See the **Component Reference** below for available types.
-   `(<x>, <y>)`: The X and Y coordinates for the component's position on the main workspace.

**Example:**
```
component c1: breaker at (100, 100)
component c2: outlet at (347, 92)
```

---

## 2. Cable Definition

Cables connect two components on the main workspace.

**Syntax:**
`cable <id>: <type> from <component_id_1> to <component_id_2>`

-   `<id>`: A unique identifier for the cable (e.g., `cab-3`).
-   `<type>`: The type of cable, which determines its wires (`14/2` or `14/3`).
-   `<component_id_1>`: The ID of the source component.
-   `<component_id_2>`: The ID of the destination component.

**Example:**
```
cable cab-3: 14/2 from c1 to c2
cable cab-13: 14/3 from c10 to c11
```

---

## 3. Wiring Block

A wiring block defines the internal connections for a single component. This includes the position of cable entries, wire nuts, and the connections between various nodes.

**Syntax:**
`wiring <component_id> { ... }`

Inside the curly braces `{}`, you can have three types of statements: `entry`, `nut`, and `connect`.

### `entry`
Defines the position of a cable's entry point inside the wiring editor.

**Syntax:** `entry <cable_id> at (<x>, <y>)`

### `nut`
Defines a wire nut inside the wiring editor.

**Syntax:** `nut <alias> at (<x>, <y>)`
-   `<alias>`: A temporary name for the nut (e.g., `n1`, `n2`) that is only valid within the current `wiring` block.

### `connect`
This is the most important statement, defining a connection between two nodes.

**Syntax:** `connect <node_1> to <node_2>`

A `<node>` can be one of the following:
-   **Device Terminal**: `term:<terminal_id>` (e.g., `term:hot`, `term:g`). The available terminal IDs depend on the component type.
-   **Cable Wire**: `<cable_id>:<color>` (e.g., `cab-3:black`). Valid colors are `black`, `white`, `red`, `copper`.
-   **Wire Nut**: `<alias>` (e.g., `n1`). This must match an alias defined by a `nut` statement in the same block.

**Example:**
```
wiring c25 {
  nut n1 at (675, 108)
  entry cab-28 at (20, 300)
  entry cab-29 at (966, 96)
  
  connect cab-29:black to term:t1      // Connects a cable wire to a device terminal
  connect cab-29:white to n1          // Connects a cable wire to a wire nut
  connect cab-28:white to n1          // Connects another wire to the same nut
  connect n1 to term:t2               // Connects the wire nut to a device terminal
}
```

---

## Component Reference

Each component type has a specific set of terminals.

### `breaker`
-   `term:hot` (Gold)
-   `term:neutral` (Silver)
-   `term:ground` (Green)

### `outlet`
-   `term:h1`, `term:h2` (Hot, Gold)
-   `term:n1`, `term:n2` (Neutral, Silver)
-   `term:g` (Ground, Green)

### `switch` (Single-Pole)
-   `term:t1`, `term:t2` (Travelers, Gold)
-   `term:g` (Ground, Green)

### `switch3` (3-Way)
-   `term:common` (Common, Black)
-   `term:trav1`, `term:trav2` (Travelers, Gold)
-   `term:g` (Ground, Green)

### `bulb` (Light Fixture)
-   `term:hot` (Gold)
-   `term:neutral` (Silver)

---

## Wire Reference

The following wire colors can be used in `connect` statements when referring to a wire from a cable.

-   `black`: Typically used for hot wires.
-   `white`: Typically used for neutral wires.
-   `red`: Typically used as a traveler wire in `14/3` cables.
-   `copper`: The ground wire.
