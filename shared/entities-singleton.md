# Entities Singleton

### 🧠 Entities Singleton

All live entity instances are tracked in the `Entities` singleton.

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

```lua
local crates = Entities:getAllBy("__type", "Crate")

if crates and #crates > 0 then
    print(crates.id.." is a Crate!")
end
```

***

## 📦 Object Script Tracking

All object instances are stored in the **Entities singleton**:

```lua
Entities = new EntitiesSingleton()
```

Each object is uniquely identified by its `uNetId`

You can fetch or query entities at runtime:

```lua
local obj = Entities:get(id)                  -- By unique network ID
local crate = Entities:getBy("type", "Crate") -- By field match
```

<mark style="color:blue;">`CLIENT`</mark> for an object to be available in the entities list, it must be rendered.\
<mark style="color:red;">`SERVER`</mark> always fully populated, allow to access or manipulate any object, no matter where it is in the world.

***

## 🔍 Debug & Tracking Tips

You can easily track which objects are active on the server via `Entities.list`.

Example: list all crates currently spawned:

```lua
for _, obj in pairs(Entities.list) do
    if obj is Crate then
        print("Active crate:", obj.id)
    end
end
```
