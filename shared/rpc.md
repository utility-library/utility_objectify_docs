# RPC

## 🌐 Entity-Based RPC (via `self.server` or `self.client`)

{% tabs %}
{% tab title="Client side" %}
Every `BaseEntity` instance gets a `self.server` property that acts as a proxy to server-side methods.

#### Example:

```lua
self.server:OpenLid()
self.server.plugins.lootable:GiveLoot(playerId)
```

Internally, metatables route calls based on:

* The object’s `id`
* The method name
* The class or plugin type

RPC methods must be defined with [#rpc-return](decorators.md#rpc-return "mention") on the **server**.
{% endtab %}

{% tab title="Server side" %}
{% hint style="danger" %}
Currently not implemented
{% endhint %}

Every `BaseEntity` instance gets a `self.client` property that acts as a proxy to client-side methods.

#### Example:

```lua
self.client:OpenLid()
self.client.plugins.lootable:GiveLoot(playerId)
```

Internally, metatables route calls based on:

* The object’s `id`
* The method name
* The class or plugin type

RPC methods must be defined with [#rpc-return](decorators.md#rpc-return "mention") on the **client**.
{% endtab %}
{% endtabs %}

## 🧩 Global RPC (via `Server.<method>` or `Client.<method>`)

{% tabs %}
{% tab title="Client side" %}
You can also call **non-object-based RPCs** from the client using the global **Server** table. \
These are typically utility methods not bound to a specific entity.

```lua
local data = Server.GetPlayerStats()
```

They can still be a callback or fire-and-forget style like entity based RPCs
{% endtab %}

{% tab title="Server side" %}
You can also call **non-object-based RPCs** from the server using the global **Client** table. \
These are typically utility methods not bound to a specific entity.

```lua
local data = Client.GetPlayerStats(id, ...)
```

They can still be a callback or fire-and-forget style like entity based RPCs

{% hint style="info" %}
id can also be -1 to call the method on all clients
{% endhint %}
{% endtab %}
{% endtabs %}

***

## 🛡 RPC Internals

When you use `@rpc`, the system registers:

* An **event handler** with the method name under your resource namespace
* If used inside a class, a **entity wide** **dispatcher** that finds the correct entity instance by ID
* Optional **callback registration** for returning results

### Entity-based RPC Routing Logic

#### Example using a server rpc called from the client

<mark style="color:$danger;">Server</mark> define the rpc:

```lua
@model("my_model")
class MyEntity extends BaseEntity {
    @rpc
    DoThing = function()
        print("DoThing")
    end
}
```

<mark style="color:$primary;">Client</mark> calls the RPC from the spawned entity:

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

<mark style="color:$danger;">Server</mark> resolves:

1. Entity instance via `Entities:get(entityId)`
2. Verifies method exists and is exposed
3. Calls `DoThing` on that instance
4. Optionally, returns a value, the client will listen for he return value using callbacks

***

### 🔒 RPC Validation

The framework includes automatic safeguards to prevent clients/server from calling methods that:

* Don’t exist on the class
* Aren’t registered via `@rpc`
* Target the wrong type of class (e.g. wrong plugin or base class)

This keeps your RPC layer safe and predictable.
