# Framework

The client framework is the foundation of **Utility Objectify**. It provides the class-based system for working with entities and plugins, manages the connection to server-side logic, and enables advanced behavior through decorators.

This page breaks down how the client framework operates and how you can use it to build powerful, modular entity scripts.

***

## 🧱 BaseEntity

All object scripts on the client extend the `BaseEntity` class. This class provides:

* Entity lifecycle hooks ([#onspawn](../shared/hooks.md#onspawn "mention"), [#ondestroy](../shared/hooks.md#ondestroy "mention"), etc.)
* RPC access to the server (`self.server`)
* Plugin management (`self.plugins`)
* State listening via [#state-key-value](../shared/decorators.md#state-key-value "mention")
* Event binding via [#event-eventname-ignorerendering](../shared/decorators.md#event-eventname-ignorerendering "mention")

You should **never instantiate BaseEntity directly**. Instead, you define classes that inherit from it.

```lua
class Crate extends BaseEntity {
    OnSpawn = function(self)
        print("Crate spawned:", self.id)
    end
}
```

***

## 🪝 Custom Hook System

You can register **custom hooks** that run on all scripts when objects that define that method are spawned or destroyed.

#### Example: OnStateChange Hook

```lua
RegisterCustomHook("OnStateChange", {
    OnSpawn = function(env, call)
        env.changeHandler = UtilityNet.AddStateBagChangeHandler(env.id, function(key, value)
            call(key, value) -- propagate to the original method
        end)
    end,

    OnDestroy = function(env)
        UtilityNet.RemoveStateBagChangeHandler(env.changeHandler)
    end
})
```

This will be called only if the currently spawning/destroying entity has a method called `OnStateChange`\
The `call` parameter is a function to invoke the original entity method, triggering the hook call.

Custom hooks can include:

| Hook Method  | Purpose                                                                                                 |
| ------------ | ------------------------------------------------------------------------------------------------------- |
| `exec`       | Called when _**ANY**_ script instance is created (_also when it doesnt have the specified method name_) |
| `OnAwake`    | Exactly like [#onawake](../shared/hooks.md#onawake "mention")                                           |
| `OnSpawn`    | Exactly like [#onspawn](../shared/hooks.md#onspawn "mention")                                           |
| `AfterSpawn` | Exactly like [#afterspawn](../shared/hooks.md#afterspawn "mention")                                     |
| `OnDestroy`  | Exactly like[#ondestroy](../shared/hooks.md#ondestroy "mention")                                        |

Then in your entity class just add the hook method

```lua
@model("my_model")
class MyEntity extends BaseEntity {
    OnStateChange = function(key, value)
        print("State changed:", key, value)
    end
}
```

This lets you run custom logic when state changes for _any_ script that implements `OnStateChange`.

***

## 🌐 IsServer / IsClient Flags

To help with shared or conditional logic, **Utility Objectify** provides two constants:

| Constant   | Value   | Description                  |
| ---------- | ------- | ---------------------------- |
| `IsClient` | `true`  | Always `true` on the client  |
| `IsServer` | `false` | Always `false` on the client |

***

## 🔎 Framework Functions

### <mark style="color:purple;">SetRPCNamespace(namespace)</mark>

Change the prefix used internally for RPC event names\
by default is `Config.Namespace` or the `current resource name` followed by a colon (:)

### <mark style="color:purple;">Server.DisableTimeoutForNext()</mark>

Disable the 5s timeout on callbacks for the next RPC call
