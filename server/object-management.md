# Object Management

### 🧠 How It Works

On the server, object scripts are **instantiated manually or programmatically** using Lua class instantiation. These scripts are tightly integrated with **networked entities** created via `UtilityNet`.

#### When You Create a Server Entity

1. Class extending `BaseEntity` is instantiated (e.g. `new MyEntity(coords?, rotation?, options?)`)
2. `UtilityNet.CreateEntity(...)` is called under the hood.
3. The entity is given a `uNetId`, synced state, and added to the global [#entities-singleton](framework.md#entities-singleton "mention") list.
4. All plugins (declared via [#plugin-pluginname](../shared/decorators.md#plugin-pluginname "mention")) are attached and initialized.
5. Lifecycle methods are triggered in the following order:

```
constructor → create() [Only if coords is passed] → init() → OnAwake → OnSpawn → AfterSpawn
```

#### When You Destroy an Entity

Calling `self:destroy()` on a server entity will:

* Remove it from the [#entities-singleton](framework.md#entities-singleton "mention") list
* Call the [#ondestroy](../shared/hooks.md#ondestroy "mention") lifecycle hook (if defined)
* Call `UtilityNet.DeleteEntity(...)` to remove the object from the world

The client will automatically respond to this removal and clean up its instances.

***

### 📦 Object Script Tracking

All server-side object instances are stored in the **Entities singleton**:

```lua
Entities = new EntitiesSingleton()
```

Each object is uniquely identified by its `uNetId`, and tracked even when no players are near it.

You can fetch or query server-side entities at runtime:

```lua
local obj = Entities:get(id)                  -- By unique network ID
local crate = Entities:getBy("type", "Crate") -- By field match
```

This allows you to access or manipulate any object on the server, no matter where it is in the world.

***

### 🔁 No Render-Based Logic

Unlike the client, the server **does not** rely on `OnRender` / `OnUnrender` logic:

| Feature          | Server   | Client             |
| ---------------- | -------- | ------------------ |
| Render Detection | ❌        | ✅                  |
| Object Lifetime  | Manual   | Auto (on render)   |
| Memory Cleanup   | Explicit | Auto (on unrender) |

All object creation and destruction is fully controlled by your server-side logic.

***

### 🧰 UtilityNet Integration

Utility Objectify relies on `UtilityNet` for:

* Creating synced entities
* Managing state (`self.state:set(...)`)
* Getting/setting entity IDs (`self.id`)
* Deleting objects
* Resolving object existence (`UtilityNet.DoesUNetIdExist(id)`)

> You **must load `utility_lib` before `utility_objectify`** or the server will throw an error.

***

### 🔍 Debug & Tracking Tips

You can easily track which objects are active on the server via `Entities.list`.

Example: list all crates currently spawned:

```lua
for _, obj in pairs(Entities.list) do
    if obj is Crate then
        print("Active crate:", obj.id)
    end
end
```
