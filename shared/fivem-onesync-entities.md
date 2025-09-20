# BaseEntityOneSync

The core of **Utility Objectify** is built around **UtilityNet**, but we also support creating and attaching to **OneSync entities**.\
\
This means you can attach scripts to vehicles, peds, or objects that are already networked by FiveM OneSync.

{% hint style="info" %}
Although supported, we strongly recommend using UtilityNet entities whenever possible, especially for objects, as they offer greater predictability and reliability.\
OneSync objects are capped at 80 locally created entities and a significant portion of that limit is already consumed by the base game.
{% endhint %}

## BaseEntityOneSync

To work with OneSync entities, you’ll extend the **`BaseEntityOneSync`** class and add a onesync specific decorator ([#vehicle-modelname](decorators.md#vehicle-modelname "mention"), [#ped-modelname](decorators.md#ped-modelname "mention"), [#object-modelname](decorators.md#object-modelname "mention")).

`BaseEntityOneSync` extends `BaseEntity`, meaning it inherits all of its functionality (e.g., model constructors, states, childs, hooks, rpc, etc.).

| Name                                              | Description                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------ |
| `self.id`                                         | The uNetId (UtilityNet id) of the current entity, not FiveM NetId! |
| <mark style="color:$success;">`self.netId`</mark> | The FiveM NetId                                                    |
| `self.obj`                                        | The GTA entity handle of the OneSync entity                        |
| `self.model`                                      | Will reflect the actual OneSync entity model                       |

{% hint style="warning" %}
You NEED to attach also a client script to make BaseEntityOneSync entities works
{% endhint %}

Lets create an entity that use the `t20` as model:

```lua
-- In both, client and server
@vehicle("t20")
class T20 extends BaseEntityOneSync {
    OnSpawn = function()
        print("Spawned car t20!")
    end,
}

--- Spawn the car at the first player coords
local playerId = GetPlayerFromIndex(0)
local player = GetPlayerPed(playerId)

local t20 = new T20(GetEntityCoords(player))
```

This will create a vehicle that will works using UtilityNet rendering techniques, meaning it will be 100% persistent and load only if someone is actively rendering it, saving resources and being reliable.

{% hint style="info" %}
Only position and rotation are saved when unrendering, this means that vehicle properties, physical damage or any other thing is not stored
{% endhint %}

To create a ped just:

```lua
-- In both, client and server
@ped("a_f_m_beach_01")
class BeachFemalePed extends BaseEntityOneSync {
    OnSpawn = function()
        print("Spawned ped a_f_m_beach_01!")
    end,
}

--- Spawn the car at the first player coords
local playerId = GetPlayerFromIndex(0)
local player = GetPlayerPed(playerId)

local t20 = new BeachFemalePed(GetEntityCoords(player))
```

{% hint style="info" %}
The same goes for peds, only position and rotation are saved when unrendering, this means that weapons, health or any other thing is not stored
{% endhint %}
