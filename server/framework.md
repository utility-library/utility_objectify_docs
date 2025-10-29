# Framework

The server framework of **Utility Objectify** is the core logic layer that manages the lifecycle of entity objects and their interaction with clients. It enables an **object-oriented, decorator-based** architecture that simplifies entity spawning, plugin composition, and client-server communication using RPC.

This page details how the server-side framework works and how to write server-side object scripts using the provided API.

***

## 🧱 BaseEntity Class

All server-side object scripts must **extend `BaseEntity`**. This class provides:

* Constructor with coords (vec3) rotation? (vec3) and options? (table)
* Entity creation, automatically called if passing coords in the constructor (`:create`)
* Entity deletion (`:destroy`)
* RPC access to the client (`self.client`)
* Plugin management (`self.plugins`)
* Child Entities methods and attributes [child-entities.md](../shared/child-entities.md "mention")
* Lifecycle hooks like [#onspawn](../shared/hooks.md#onspawn "mention"),[#ondestroy](../shared/hooks.md#ondestroy "mention") etc.

### Methods provided

<details>

<summary>General</summary>

#### <mark style="color:purple;">self:create(coords)</mark>

Spawns a networked object in the world, automatically called when passing coords during class instantiation.\
calls [#onawake](../shared/hooks.md#onawake "mention"), [#onspawn](../shared/hooks.md#onspawn "mention") and [#afterspawn](../shared/hooks.md#afterspawn "mention")

#### <mark style="color:purple;">self:destroy()</mark>

Deletes the entity and removes the instance calling [#ondestroy](../shared/hooks.md#ondestroy "mention")

</details>

### Example

```lua
@model("prop_box_wood02a")
class Crate extends BaseEntity {
    OnSpawn = function()
        print("Crate spawned with id", self.id)
    end
}

-- Spawning in the world
local myCrate = new Crate(vec3(0, 0, 72))
```

***

## 🌐 IsServer / IsClient Flags

To help with shared or conditional logic, **Utility Objectify** provides two constants:

| Constant   | Value   | Description                  |
| ---------- | ------- | ---------------------------- |
| `IsClient` | `false` | Always `false` on the server |
| `IsServer` | `true`  | Always `true` on the server  |

***

## 🔎 Framework Functions

### <mark style="color:purple;">SetRPCNamespace(namespace)</mark>

Change the prefix used internally for RPC event names\
by default is `Config.Namespace` or the `current resource name` followed by a colon (:)

### <mark style="color:purple;">Client.DisableTimeoutForNext()</mark>

Disable the 5s timeout on callbacks for the next RPC call
