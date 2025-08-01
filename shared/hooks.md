# Hooks

Utility Objectify uses **hooks** to define and control the lifecycle of object scripts and to support plugin-style behaviors. Hooks are executed at specific moments in an entity’s lifecycle and can be overridden or extended.

***

### <mark style="color:$info;">OnRegister</mark>

<mark style="color:blue;">`CLIENT`</mark>

Triggered when an entity is registered (even if not rendered), this can be used for purposes such as blip creation.\
At this stage of execution, the entity and classes instances do not yet exist.\
see [#temp-object-properties](../client/object-management.md#temp-object-properties "mention") for more information

### <mark style="color:$info;">OnUnregister</mark>

<mark style="color:blue;">`CLIENT`</mark>

Triggered when an entity is about to be unregistered or removed from the system.\
Utilize this hook to execute any necessary cleanup of resources or de-initialization processes. \
This ensures that no residual data or active references remain that could lead to memory leaks or unexpected behavior.\
OnUnregister can access only [#temp-object-properties](../client/object-management.md#temp-object-properties "mention")!

### <mark style="color:$info;">OnAwake</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:$danger;">`SERVER`</mark>

Triggered just before an entity is spawned.\
this is useful for initialization or setup before `OnSpawn`.

### <mark style="color:$info;">OnSpawn</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:$danger;">`SERVER`</mark>

Triggered right after the entity is rendered/spawned. \
This occurs following the `OnAwake` event.

### <mark style="color:$info;">AfterSpawn</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:$danger;">`SERVER`</mark>

Triggered after all scripts and plugins have been loaded. \
Ideal for logic that relies on complete initialization.

### <mark style="color:$info;">OnDestroy</mark>

<mark style="color:blue;">`CLIENT`</mark> <mark style="color:$danger;">`SERVER`</mark>

Triggered when the entity is no longer rendered/deleted. \
Here you can run cleanup processes such as disabling listeners, removing UI components, and any other teardown logic.

### <mark style="color:$info;">OnStateChange(key, value)</mark>

<mark style="color:blue;">`CLIENT`</mark>

Triggered whenever **ANY** state value changes.\
Provides the key and value of the changed state as arguments

***

### 🧰 Hook Execution Order

1. `OnRegister` <mark style="color:blue;">`CLIENT ONLY`</mark>
2. `OnAwake`
3. `OnSpawn`
4. `AfterSpawn`
5. `OnDestroy` (on unrender or deletion)
6. `OnUnregister` <mark style="color:blue;">`CLIENT ONLY`</mark>

Plugins follow the same order but are initialized after the main script.
