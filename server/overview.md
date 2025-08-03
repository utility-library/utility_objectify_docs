# Overview

The **server-side** portion of **Utility Objectify** is responsible for **owning**, **spawning**, and **networking** all entity logic in a centralized, authoritative way. While the client reacts to objects being rendered, the server creates and manages these objects in the world and coordinates interactions via RPC and plugins.

This section provides a high-level overview of what happens on the **server**, and how to work with it.

***

### 🧠 Core Responsibilities

✅ 1. **Entity Creation & Management**\
Server-side code is responsible for **creating and destroying entity instances** using `UtilityNet`.\
When a class extending `BaseEntity` is instantiated:

* A networked entity is created in the world.
* A corresponding server-side script instance is stored in memory.
* Plugins are instantiated and attached to the main object.

✅ 2. **Plugin Instantiation**\
If the entity class has plugins (via [#plugin-pluginname](../shared/decorators.md#plugin-pluginname "mention")), those are automatically initialized and linked to the main instance.

❌ The server **does not react** to object rendering or visibility like the client. It owns the logic and data, while the client reacts to what’s visible.

***

### 🔧 Script Registration

[#main-script](../shared/type-of-scripts.md#main-script "mention")

### 🧩 Plugin System

[#plugin-script](../shared/type-of-scripts.md#plugin-script "mention")

***

### 📡 RPC Server → Client

[#entity-based-rpc-via-self.server-or-self.client](../shared/rpc.md#entity-based-rpc-via-self.server-or-self.client "mention")

[#global-rpc-via-server.less-than-method-greater-than-or-client.less-than-method-greater-than](../shared/rpc.md#global-rpc-via-server.less-than-method-greater-than-or-client.less-than-method-greater-than "mention")

***

### 🔄 State Change Handling

[#state-key-value](../shared/decorators.md#state-key-value "mention")

### 📣 Event Handling

[#event-eventname-ignorerendering](../shared/decorators.md#event-eventname-ignorerendering "mention")

***

### 📦 Object Registry: `Entities`

[#entities-singleton](framework.md#entities-singleton "mention")

***

### 🚫 Currently Not Supported on Server

The following features are currently **client-only** and not applicable to server-side code:

* [#onregister](../shared/hooks.md#onregister "mention")/[#onunregister](../shared/hooks.md#onunregister "mention") — Only run with temporary client objects
