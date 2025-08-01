# Installation

1. Download the `utility_objectify` resource and place it in your server's `resources` folder.
2.  In `server.cfg`, add the line to start the resource:

    ```bash
    start utility_objectify
    ```
3.  In the manifest of your choosen resource, add the following line:

    ```lua
     client_script "@utility_objectify/client/api.lua
     server_script "@utility_objectify/server/api.lua
    ```
