# Vendorizing

Drag and drop your resource folder onto `vendorize.bat`.

This will create hardlinks to the `api.lua` files from `utility_objectify` inside your resource’s `client/` and `server/` folders.\
It allows your code to use `utility_objectify`'s functionality without needing the resource to be installed.

> **Note:** When distributing a resource that uses `utility_objectify` you need to provide attribution or credit the original project accordingly.
>
> When distributing with escrow you need to ignore the files from it
