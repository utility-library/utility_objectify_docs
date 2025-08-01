---
description: >-
  Take a raw entity and objectify it, make it manageable, programmable, and
  lifecycle-aware.
---

# Introduction

**Utility Objectify** is a simple but powerful system that lets you add custom behavior to entities in FiveM. you can easily attach logic to them and manage their lifecycle (spawn, destroy, state changes, etc.).

Dependencies:

* [utility\_lib](https://github.com/utility-library/utility_lib)
* [leap](https://github.com/utility-library/leap)

> **Note:** `utility_objectify` is a work in progress and is still in development.

***

### 🚀 **Features**

* **Entity-to-Class Binding**: Bind any UtilityNet entity to a lua class and manage it with custom logic.
* **Lifecycle Hooks**: Run functions when entities are spawned, destroyed, or updated.
* **State Management**: Track and react to changes in an entity's state.
* **Plugins**: Bind additional classes to the entity for modular functionality.

***

### 🎯 Objective

Currently, the design direction is to **unify the client and server APIs as much as possible**, making it easier to share logic between both environments.

The goal is not strict symmetry, but rather **practical alignment** where it counts:

* Writing class-based entity logic that works similarly on both sides
* Reusing method names, decorators, and structure
* Reducing the need to constantly switch files or duplicate code
* Minimizing boilerplate or conditional logic across environments

This makes development faster, more intuitive, and less error-prone, especially in complex networked systems where both client and server need to cooperate closely.

> For example, both sides expose `IsServer` / `IsClient` flags and use the same `@rpc`, `@model`, and `@plugin` decorators to keep logic consistent.

***

### 🛠 **How It Works**

The `BaseEntity` class is the core class that manages the lifecycle of your entities and handles state changes. \
It's designed to be extended by other entity classes, providing basic functionality like [#onspawn](shared/hooks.md#onspawn "mention"), [#ondestroy](shared/hooks.md#ondestroy "mention"), and state change handling.

```lua
class MyEntity extends BaseEntity {
    OnSpawn = function()
        -- Do something when the entity is spawned
    end,

    OnDestroy = function()
        -- Do something when the entity is destroyed
    end
}
```

***

### 💡 **Why Use This?**

* **Simplifies Entity Management**: No more messy global variables, scattered functions, manual entity spawning and management.
* **Life Cycle Hooks**: Automatically handle logic when entities spawn, destroy, or update.
* **Modular**: Easily add new functionality with plugins making everything reusable.
* **State-Driven Logic**: Track and respond to changes in your entities states without loops or complex logic.
