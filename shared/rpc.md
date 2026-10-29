# RPC

## 🌐 Entity-Based RPC (via `self.server` or `self.client`)

{% tabs %}
{% tab title="Client side" %}
Every `BaseEntity` instance gets a `self.server` property that acts as a proxy to server-side methods.

**Example:**

```lua
self.server:OpenLid()
self.server.plugins.lootable:GiveLoot(playerId)
```

Internally, metatables route calls based on:

* The object’s `id`
* The method name
* The class or plugin type

RPC methods must be defined with [#rpc-return](decorators.md#rpc-return "mention") on the **server**.

{% hint style="info" %}
If an RPC reaches the client before the entity is registered, the client waits up to **5 seconds** for it before raising an error.
{% endhint %}
{% endtab %}

{% tab title="Server side" %}
Every `BaseEntity` instance gets a `self.client` property that acts as a proxy to client-side methods.

**Example:**

```lua
self.client:OpenLid(clientId)
self.client.plugins.lootable:GiveLoot(clientId, playerId)


self.client.await:OpenLid(clientId)
self.client.await.plugins.lootable:GiveLoot(clientId, playerId)
```

Internally, metatables route calls based on:

* The object’s `id`
* The method name
* The class or plugin type

RPC methods must be defined with [#rpc-return](decorators.md#rpc-return "mention") on the **client**.

{% hint style="info" %}
`clientId` can also be **-1** to call the method for **all currently rendering clients**

for callbacks (await) this will return a dictionary of `clientId = {returns}`
{% endhint %}

{% hint style="info" %}
**Reliable delivery:** the server sends an entity RPC only once the target client has the entity ready (spawned and `OnSpawn` finished). If it is not ready yet the call is held for up to **5 seconds** and sent as soon as the client is ready, so RPCs sent from `OnSpawn`/`AfterSpawn` on the server are not lost.

* Normal calls wait in the background and don't block the caller
* `await` calls block until the client is ready, and return nothing if it times out
* With `-1` the call is sent to the clients that are ready, waiting until at least one is
* Calls held this way can arrive after a later call that was sent immediately
{% endhint %}
{% endtab %}
{% endtabs %}

## 🧩 Global RPC (via `Server.<method>` or `Client.<method>`)

{% tabs %}
{% tab title="Client side" %}
You can also call **non-object-based RPCs** from the client using the global **Server** table.\
These are typically utility methods not bound to a specific entity.

```lua
local data = Server.GetPlayerStats()
```

They can still be a callback or fire-and-forget style like entity based RPCs
{% endtab %}

{% tab title="Server side" %}
You can also call **non-object-based RPCs** from the server using the global **Client** table.\
These are typically utility methods not bound to a specific entity.

```lua
Client.GetPlayerStats(clientId, ...)

local data = Client.await.GetPlayerStats(clientId, ...)
```

They can still be a callback or fire-and-forget style like entity based RPCs

{% hint style="info" %}
`clientId` can also be **-1** to call the method on **all clients**

for callbacks (await) this will return a dictionary of `clientId = {returns}`
{% endhint %}
{% endtab %}
{% endtabs %}

***

## 🛡 RPC Internals

When you use [#rpc-return](decorators.md#rpc-return "mention"), the system registers:

* An **event handler** with the method name under your resource namespace
* If used inside a class, a **entity wide** **dispatcher** that finds the correct entity instance by ID
* Optional **callback registration** for returning results

### Entity-based RPC Routing Logic

#### Example using a server rpc called from the client

<mark style="color:red;">Server</mark> define the rpc:

```lua
@model("my_model")
class MyEntity extends BaseEntity {
    @rpc
    DoThing = function()
        print("DoThing")
    end
}
```

<mark style="color:blue;">Client</mark> calls the RPC from the spawned entity:

```lua
@model("my_model")
class MyEntity extends BaseEntity {
    OnSpawn = function()
        self.server:DoThing()
    end
}
```

That is the same as:

```lua
Server["MyEntity.DoThing"](entityId, ...)
```

And then will be converted to:

```lua
TriggerServerEvent("namespace:MyEntity.DoThing", entityId, ...)
```

<mark style="color:red;">Server</mark> resolves:

1. Entity instance via `Entities:get(entityId)`
2. Verifies method exists and is exposed
3. Calls `DoThing` on that instance
4. Optionally, returns a value, the client will listen for he return value using callbacks

***

### 🔒 RPC Validation

The framework includes automatic safeguards to prevent clients/server from calling methods that:

* Don’t exist on the class
* Aren’t registered via [#rpc-return](decorators.md#rpc-return "mention")
* Target the wrong type of class (e.g. wrong plugin or base class)

This keeps your RPC layer safe and predictable.
