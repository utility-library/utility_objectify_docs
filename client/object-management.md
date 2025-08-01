---
description: >-
  This page explains how object scripts are created, registered, managed, and
  destroyed dynamically on the client.
---

# Object Management

Utility Objectify includes a powerful system for managing **object script instances** tied to entities in the world.

When entities are spawned or unspawned, the framework dynamically creates or destroys script instances based on model-to-class mappings. This allows for automatic behavior assignment and modular logic.

***

## 🧠 How It Works

#### <mark style="color:purple;">OnRender</mark>

When an entity becomes visible to the player:

1. Its model is checked against registered scripts.
2. A new instance of the class extending `BaseEntity` is created for each script (`main` and any plugins).
3. Lifecycle methods ([#onawake](../shared/hooks.md#onawake "mention"), [#onspawn](../shared/hooks.md#onspawn "mention"), [#afterspawn](../shared/hooks.md#afterspawn "mention")) are called.
4. The instance is stored and tracked.

#### <mark style="color:purple;">OnUnrender</mark>

When the entity is no longer visible:

* [#ondestroy](../shared/hooks.md#ondestroy "mention") is called on each script.
* All associated script instances are destroyed.
* Object memory is freed.

This logic is handled via internal UtilityNet events:

```lua
UtilityNet.OnRender(...)       -- Creates script instances
UtilityNet.OnUnrender(...)     -- Destroys script instances
```

***

## 📦 Temp Object Properties

Before the object is rendered, properties set during [#onregister](../shared/hooks.md#onregister "mention") are stored in a **temporary table**:

```lua
self.someData = 123
```

These are stored and later injected to the real instance when the object is rendered.\
This ensures that [#onregister](../shared/hooks.md#onregister "mention") can prepare data even before the object is visible, like creating a blip at the entity coords

#### [#onregister](../shared/hooks.md#onregister "mention") and [#onunregister](../shared/hooks.md#onunregister "mention")

can interact only with the **temporary instance properties**, not real instance runtime changed properties.\
have access only to `self.id` and `self.state`

#### All other hooks ([#onspawn](../shared/hooks.md#onspawn "mention"), [#ondestroy](../shared/hooks.md#ondestroy "mention"), etc...)

can interact with the temporary instance properties, as they will be injected before the [#onawake](../shared/hooks.md#onawake "mention") hook

***

## 🔎 Object Management Functions

### <mark style="color:purple;">IsObjectScriptRegistered(model, name)</mark>

Checks if a specific model as a script registered to im, can be useful if a plugin needs another plugin to work properly

### <mark style="color:purple;">RegisterCustomHook(hookMethod, hookData)</mark>

See [#custom-hook-system](framework.md#custom-hook-system "mention")

### <mark style="color:purple;">GetObjectScriptInstance(obj, name, nocheck?)</mark>

Fetch a specific script instance by name from an object.\
All main scripts are exposed as `main`

* `nocheck = true` skips rendering checks.

### <mark style="color:purple;">GetExternalObjectScriptStatic(model, name)</mark>

Returns the raw script table (class) without creating an instance.

> ⚠️ This is unsafe for live operations, use for inspection only.

### <mark style="color:purple;">AreObjectScriptsFullyLoaded(obj)</mark>

Returns `true` if all scripts have been fully created and attached to the object.
