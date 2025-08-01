# Overview

The client-side portion of **Utility Objectify** is responsible for managing the behavior, lifecycle, and communication of object instances rendered on the player's machine.

It handles:

* Creating and destroying local script instances tied to entities
* Managing plugins and lifecycle hooks
* Handling remote procedure calls ([rpc.md](../shared/rpc.md "mention")) from server to client

This section provides a high-level overview of what happens on the client and how to work with it.

***

### 🧠 Core Responsibilities

#### ✅ 1. Object Instantiation

When an entity is rendered (`OnSpawn`), the library:

* Looks up all scripts registered to the entity’s model (via the `@model` or `@plugin` decorators)
* Creates a new class instance and binds it to the entity
* Initializes the lifecycle: `OnAwake`, `OnSpawn`, `AfterSpawn`

#### ❌ When an entity is unrendered (`OnDestroy`):

* All bound script instances are destroyed
* The lifecycle method `OnDestroy` is called
* Memory and state tracking is cleared

***

### 🔧 Script Registration

[#main-script](../shared/type-of-scripts.md#main-script "mention")

### 🧩 Plugin System

[#plugin-script](../shared/type-of-scripts.md#plugin-script "mention")

***

### 📡 RPC Client → Server

[#entity-based-rpc-via-self.server-or-self.client](../shared/rpc.md#entity-based-rpc-via-self.server-or-self.client "mention")

[#global-rpc-via-server.less-than-method-greater-than-or-client.less-than-method-greater-than](../shared/rpc.md#global-rpc-via-server.less-than-method-greater-than-or-client.less-than-method-greater-than "mention")

***

### 🔄 State Synchronization

[#state-key-value](../shared/decorators.md#state-key-value "mention")

***

### 📣 Event Handling

[#event-eventname-ignorerendering](../shared/decorators.md#event-eventname-ignorerendering "mention")

***

### 🛠 Utility Functions

[#functions](framework.md#functions "mention")

[#functions](object-management.md#functions "mention")

***

### 🧪 Debug Mode

If `DevModeStatus` is enabled, object script instances can be visualized while aiming:

* Shows all registered scripts for an entity
* Drawn in 3D using `DrawText3Ds`
