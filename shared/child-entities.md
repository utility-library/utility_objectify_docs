# Child Entities

Child entities give you a clean, logical way to bind related entities together so you can traverse and manage them as a tree. \
Add a child to a parent and you instantly get:

* On the child: `.parent` (and `.root`) to reach back up the tree.
* On the parent: `.children` plus helpers like <mark style="color:$warning;">`getChild`</mark>, <mark style="color:$warning;">`getChildBy`</mark>, <mark style="color:$warning;">`getChildrenBy`</mark>, and <mark style="color:red;">`removeChild`</mark>.

Child entities are **declared on the&#x20;**<mark style="color:red;">**server**</mark> and are **automatically converted as instances and available on the&#x20;**<mark style="color:$primary;">**client**</mark> via state.\
This keeps server authority while still giving the client read-friendly access to the structure.

***

### Why use child entities?

* **Simple traversal:** <mark style="color:$warning;">`car:getChild("engine")`</mark> or <mark style="color:$warning;">`car:getChild("wheels/frontLeft")`</mark>.
* **Strong locality:** Each feature (e.g., door/engine/wheel) lives as its own entity with its own lifecycle, methods and attributes, yet stays discoverable from its parent.
* **Reliable:** Accurately retrieve an entity child, create links, and ensuring access aligns with expectations.
* **Predictable sync:** Child links are written to the parent’s state (`state.children[name] = child.id`) so clients always see the same structure.
* **Safer code:** The API prevents adding children that aren’t created yet, catching mistakes early.

***

### Key concepts at a glance

* **Parent/Child/Root**
  * `child.parent` is set automatically by `addChild`.
  * `child.root` is the topmost ancestor (useful for cross-feature coordination).
  * `parent.children` is a name > entity map.
* **Server > Client**
  * Server uses `addChild` which updates `self.state.children`.
  * Clients read the structure from state when requested and resolve the client instances

***

### Quick start

Create entities (server)

```lua
local car = new Car(vector3(0,0,0))
local engine = new Engine(vector3(0,0,0))

local piston = new Piston(vec3(0,0,0))
local sparkPlugs = new SparkPlugsvector3(0,0,0))
local exhaust = new Exhaust(vector3(0,0,0))

```

#### Attach children

```lua
car:addChild("engine", engine)

engine:addChild("piston", piston)
engine:addChild("sparkPlugs", sparkPlugs)
engine:addChild("exhaust", exhaust)
```

Under the hood this sets:

* `engine.parent = car` and `engine.root = car`
* `car.children["engine"] = engine`
* `car.state.children["engine"] = engine.id` (so clients auto-see it)
* And the same for all engine components

#### Use it (client or server)

```lua
local engine = car:getChild("engine")
local piston = car:getChild("engine/piston")
-- OR
local piston = engine:getChild("piston")

-- Search helpers (examples)
local anyDoor = car:getChildBy("__type", "Door")
local damagedWheels = car:getChildrenBy("health", function(h) return h and h < 0.5 end)
```

***

### \</> API reference

#### <mark style="color:$success;">addChild(name: string, child: BaseEntity)</mark> <mark style="color:red;">`SERVER`</mark>

Add an entity as `child` of this entity under the specified `name`.\
children can be accessed using the methods or directly with `self.children`

#### <mark style="color:red;">removeChild(childOrName: BaseEntity | string)</mark> <mark style="color:red;">`SERVER`</mark>

Detach a child entity by instance or by name.

#### <mark style="color:$warning;">getChild(path: string)</mark> <mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Fetch a child entity by name or by slash-separated path:

```lua
car:getChild("engine")
car:getChild("engine/piston")
```

When you use a slash-separated path, it's just syntax sugar for chained calls:

```lua
car:getChild("engine/piston")
-- is equivalent to:
car:getChild("engine"):getChild("piston")

```

#### <mark style="color:$warning;">getChildBy(key: string, value)</mark> <mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Finds the first child by a custom field, value can also be a function working as a filter.\
whose `child[key] == value` or satisfies value as a function `value(child[key])`.

#### <mark style="color:$warning;">getChildrenBy(key: string, value)</mark> <mark style="color:blue;">`CLIENT`</mark> <mark style="color:red;">`SERVER`</mark>

Returns a name > child dictionary table of all matches, similar to [#getchildby-key-string-value](child-entities.md#getchildby-key-string-value "mention") but doesnt stop at first match

***

### &#x20;Tips

* **Attach during** [#onawake](hooks.md#onawake "mention")\
  Use it to attach children so the full tree is reliably ready when `OnSpawn` fires.
* **Root awareness**\
  If a child needs to coordinate with siblings in other branches, use `self.root` to get the top entity and query from there.
