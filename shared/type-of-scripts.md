# Type of scripts

## 🧱 Main script

Every class that extends `BaseEntity` registered with the [#model-modelname-abstract](decorators.md#model-modelname-abstract "mention") decorator is treated as the main script.

Every main script instance has:

{% tabs %}
{% tab title="Client side" %}
| Name           | Description                                                                       |
| -------------- | --------------------------------------------------------------------------------- |
| `self.id`      | The uNetId (UtilityNet id) of the current entity                                  |
| `self.obj`     | The GTA entity handle of the current entity                                       |
| `self.state`   | The object state bag, read-only (client)                                          |
| `self.plugins` | <p>A table with every plugin instance<br><code>[scriptName] = instance</code></p> |
| `self.server`  | Used to call server-side entity RPC functions                                     |
{% endtab %}

{% tab title="Server side" %}
| Name                                                            | Description                                                                       |
| --------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `self.id`                                                       | The uNetId (UtilityNet id) of the current entity                                  |
| `self.state`                                                    | The object state bag, read-only (client)                                          |
| `self.plugins`                                                  | <p>A table with every plugin instance<br><code>[scriptName] = instance</code></p> |
| `self.client` <mark style="color:red;">`NOT IMPLEMENTED`</mark> | Used to call client-side entity RPC functions                                     |
{% endtab %}
{% endtabs %}

## 🔌Plugin script

Every class (that can also extend `BaseEntity`) registered with the [#plugin-pluginname](decorators.md#plugin-pluginname "mention") decorator on a main script is treated as a plugin script.\
Plugin scripts are useful to modularize code/logic attaching only specific plugins and so making everything more robust and modular.

\
They are really similar to the main script, the only differences are:

| Name               | Description              |
| ------------------ | ------------------------ |
| ~~`self.plugins`~~ | Doesn't exist on plugins |
| `self.isPlugin`    | on **true**              |
| `self.main`        | The main script instance |
