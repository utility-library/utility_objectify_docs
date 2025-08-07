# Object Management

### 🧠 How It Works

On the server, object scripts are **instantiated manually or programmatically** using Lua class instantiation. These scripts are tightly integrated with **networked entities** created via `UtilityNet`.

#### <mark style="color:purple;">When You Create a Server Entity</mark>

1. Class extending `BaseEntity` is instantiated (e.g. `new MyEntity(coords?, rotation?, options?)`)
2. `UtilityNet.CreateEntity(...)` is called under the hood.
3. The entity is given a `uNetId`, synced state, and added to the global [#entities-singleton](../shared/entities-singleton.md#entities-singleton "mention") list.
4. All plugins (declared via [#plugin-pluginname](../shared/decorators.md#plugin-pluginname "mention")) are attached and initialized.
5. Lifecycle methods are triggered in the following order:

```
constructor → create() [Only if coords is passed] → init() → OnAwake → OnSpawn → AfterSpawn
```

#### <mark style="color:purple;">When You Destroy an Entity</mark>

Calling `self:destroy()` on a server entity will:

* Remove it from the [#entities-singleton](../shared/entities-singleton.md#entities-singleton "mention") list
* Call the [#ondestroy](../shared/hooks.md#ondestroy "mention") lifecycle hook (if defined)
* Call `UtilityNet.DeleteEntity(...)` to remove the object from the world

The client will automatically respond to this removal and clean up its instances.

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
* Managing state (`self.state`)
* Getting/setting entity IDs (`self.id`)
* Deleting objects
* Resolving object existence (`UtilityNet.DoesUNetIdExist(id)`)

> You **must load `utility_lib` before `utility_objectify`** or the server will throw an error.
