# Decorators

### <mark style="color:purple;">@model(modelName, abstract?)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Register the following class as [#main-script](../client/object-management.md#main-script "mention") of the specified model

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {}
```

You can also pass a table of models:

```lua
@model({ "prop_crate_11e", "prop_boxpile_07d" })
class Crate extends BaseEntity {}
```

#### Model Constructors <mark style="color:red;">`SERVER`</mark>

On the server, passing multiple models generates model-specific constructors.\
This allows explicit control over which model is used when creating the entity:

```lua
-- ✅ Explicit constructors
new Crate.prop_crate_11e(...)
new Crate.prop_boxpile_07d(...)

-- ✖️ Invalid: no model provided
new Crate(...)
```

#### Abstract parameter <mark style="color:red;">`SERVER`</mark>

Passing `abstract = true` marks the class as abstract.\
Useful for logic-only objects or custom entity types, for example, NPC spawn zones or render/unrender managers:

```lua
@model("npc_spawn_zone", true)
class SpawnZone extends BaseEntity {}
```

`utility_objectify`, from the provided model name generates the model and its corresponding object, while keeping full entity lifecycle management.

Client logic works automatically, no special setup needed.

***

### <mark style="color:purple;">@vehicle(modelName)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Register the following class as [#main-script](../client/object-management.md#main-script "mention") of the specified model.\
can be used only on classes that extends [#baseentityonesync](baseentityonesync.md#baseentityonesync "mention")

```lua
@vehicle("t20")
class MyCar extends BaseEntityOneSync {}
```

You can also pass a table of models:&#x20;

```lua
@vehicle({ "t20", "bati" })
class MyCar extends BaseEntityOneSync {}
```

#### Model Constructors <mark style="color:red;">`SERVER`</mark>

Are supported like [#model-modelname-abstract](decorators.md#model-modelname-abstract "mention")

***

### <mark style="color:purple;">@ped(modelName)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Register the following class as [#main-script](../client/object-management.md#main-script "mention") of the specified model.\
can be used only on classes that extends [#baseentityonesync](baseentityonesync.md#baseentityonesync "mention")

```lua
@ped("a_f_m_beach_01")
class MyPed extends BaseEntityOneSync {}
```

You can also pass a table of models:&#x20;

```lua
@vehicle({ "a_f_m_beach_01", "a_f_m_bevhills_01" })
class MyPed extends BaseEntityOneSync {}
```

#### Model Constructors <mark style="color:red;">`SERVER`</mark>

Are supported like [#model-modelname-abstract](decorators.md#model-modelname-abstract "mention")

***

### <mark style="color:purple;">@object(modelName)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Register the following class as [#main-script](../client/object-management.md#main-script "mention") of the specified model.\
can be used only on classes that extends [#baseentityonesync](baseentityonesync.md#baseentityonesync "mention")

```lua
@object("prop_crate_11e")
class MyObject extends BaseEntityOneSync {}
```

You can also pass a table of models:&#x20;

```lua
@vehicle({ "prop_crate_11e", "prop_boxpile_07d" })
class MyObject extends BaseEntityOneSync {}
```

#### Model Constructors <mark style="color:red;">`SERVER`</mark>

Are supported like [#model-modelname-abstract](decorators.md#model-modelname-abstract "mention")

{% hint style="warning" %}
OneSync only supports up to 80 locally created entities at once, and the base game already uses a large share of that limit.\
Because of this, relying on this kind of entity decorator isn’t very reliable.\
Unless you specifically require this type of entity, i strongly recommend using UtilityNet entities as usual (with [#model-modelname-abstract](decorators.md#model-modelname-abstract "mention"))
{% endhint %}

***

### <mark style="color:purple;">@plugin(pluginName)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Registers a [#plugin-script](../client/object-management.md#plugin-script "mention") to be loaded alongside a main class. Plugins allow you to modularize logic.

```lua
@plugin("lootable")
```

***

### <mark style="color:purple;">@state(key, value?)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Watches for changes to a specific `self.state` key on the object.\
Executes the decorated method whenever the key changes.

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    @state("isOpen")
    OnOpenChange = function(value, load)
        print("State changed:", value)
    end
}
```

You can specify a value to only trigger on specific state changes:

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    @state("mode", "lootable")
    OnLootable = function(value, load)
        print("Crate is now lootable")
    end
}
```

The load parameter is a boolean indicating if that function was called from a state change or from the initial state loading, this can be useful to reuse and differentiate logic/code

`load`: **true** = the function was called why the entity loaded/rendered and so the value as not changed\
`load`: **false** = the function was called by a state change

***

### <mark style="color:purple;">@event("eventName", ignoreRendering?)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Registers a listener for a net event and binds it to the object.\
If `ignoreRendering` is not set, the event handler will only run when the object is fully loaded[#areobjectscriptsfullyloaded-obj](../client/object-management.md#areobjectscriptsfullyloaded-obj "mention")

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    @event("Crate:ForceOpen")
    HandleForceOpen = function()
        print("Received event to open crate")
        self:Open()
    end,
    
    Open = function()
        -- Crate opening logic!
    end
}
```

Set `ignoreRendering = true` to disable rendering checks:

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    -- This will call this function also if
    -- the entity isnt currently rendering
    @event("Crate:ForceOpen", true)
    HandleForceOpen = function()
        print("Received event to open crate, i can also not be rendering!")
        self:Open()
    end,
    
    Open = function()
        -- Crate opening logic!
    end
}
```

***

### <mark style="color:purple;">@rpc(return?)</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Marks a function as callable from the **server** side.\
If the function uses the lua `return` keyword or the `return` argument is set to **true**,\
it will register a callback and return the value to the caller.

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    @rpc(true)
    GetHealth = function()
        return self.state.health
    end
}
```

is the same of

```lua
@model("prop_crate_11e")
class Crate extends BaseEntity {
    @rpc
    GetHealth = function()
        return self.state.health
    end
}
```

this decorator can be used also on normal functions (not entity)

```lua
@rpc
function IsFreeAiming(self)
    return IsPlayerFreeAiming(PlayerId())
end
```

### <mark style="color:purple;">@srpc(return?)</mark>

<mark style="color:red;">`SERVER`</mark>

Same as [#rpc-return](decorators.md#rpc-return "mention") but register only if `IsServer` is true,\
useful when writing shared code and you want to register the RPC only on the server without doing manual if checks

### <mark style="color:purple;">@crpc(return?)</mark>

<mark style="color:blue;">`CLIENT`</mark>

Same as [#rpc-return](decorators.md#rpc-return "mention") but register only if `IsClient` is true,\
useful when writing shared code and you want to register the RPC only on the client without doing manual if checks
