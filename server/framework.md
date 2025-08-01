# Framework

The server framework of **Utility Objectify** is the core logic layer that manages the lifecycle of entity objects and their interaction with clients. It enables an **object-oriented, decorator-based** architecture that simplifies entity spawning, plugin composition, and client-server communication using RPC.

This page details how the server-side framework works and how to write server-side object scripts using the provided API.

***

### 🧱 BaseEntity Class

All server-side object scripts must **extend `BaseEntity`**. This class provides:

* Constructor with coords (vec3) rotation? (vec3) and options? (table)
* Entity creation, automatically called if passing coords in the constructor (`:create`)
* Entity deletion (`:destroy`)
* RPC access to the client (`self.client`) `NOT IMPLEMENTED`
* Plugin management (`self.plugins`)
* Lifecycle hooks like [#onspawn](../shared/hooks.md#onspawn "mention"),[#ondestroy](../shared/hooks.md#ondestroy "mention") etc.

#### Example

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

Functions provided by `BaseEntity`

| Function               | Description                                                                                                   |
| ---------------------- | ------------------------------------------------------------------------------------------------------------- |
| `self:create(coords)`  | Spawns a networked object in the world, automatically called when passing coords when instantiating the class |
| `self:destroy()`       | Deletes the entity and removes the instance                                                                   |
| `self:init(id, state)` | `INTERNAL` Initializes lifecycle methods on existing entity                                                   |

***

## 🌐 IsServer / IsClient Flags

To help with shared or conditional logic, **Utility Objectify** provides two constants:

| Constant   | Value   | Description                  |
| ---------- | ------- | ---------------------------- |
| `IsClient` | `false` | Always `false` on the server |
| `IsServer` | `true`  | Always `true` on the server  |

***

### 🧠 Entities Singleton

All live server-side entity instances are tracked in the `Entities` singleton.

```lua
Entities = new EntitiesSingleton()
```

#### Methods provided

| Function                        | Description                                                                                                        |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `Entities:get(id)`              | Fetch a object instance from its uNetId                                                                            |
| `Entities:getBy(key, value)`    | Find an instance by a custom field, value can also be a function working as a filter                               |
| `Entities:getAllBy(key, value)` | Returns a table containing all instances that respect the filter, value can also be a function working as a filter |
| `Entities:createByName(name)`   | Instantiate a new object of a class with that name                                                                 |

Example:

```lua
local open = Entities:getAllBy("state", function(state) return state.open end)

if open and #open > 0 then
    print(open[1].id.." is an open crate")
end
```

```lua
local specific = Entities:getBy("someCustomId", 100)

if specific then
    print(specific.id.." has the attribute someCustomId setted to 100")
end
```
