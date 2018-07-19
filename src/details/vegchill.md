# VegChill Main Object

`VegChill` main object is the overall object containing all other objects, works as an environment, manages plugins and extensions (extensions mostly).

When `VegChill` first initializes, it construct a `VegChill` object using config file, a config file looks like this:

```
[plugin]

[option]
verbose = true
```

The path of config file can be changed, in your loading script of `lldb` or `gdb`, while the loading script path is currently fixed using `Python`'s `user_data_dir`, with app name `VegChill` and app author `Anciety`. Path under linux is `~/.local/share/VegChill/Anciety/`, and `load_vegchill.py` is the loading script, you can change the path of config file here.

Now for the config itself, two sections are needed, one for plugin loading, one for option choice. Plugin section is needed when you tend to run external plugins, like when you `pip` installed some plugins, and specify the package name, for example `vegchill.plugins.SomePlugin`. For more information of how to specify the correct name, please refer to [plugin details](./plugin.md).

Option part is just options, some installed plugins may refer to this section for more configurations. Like we use `verbose` option here, and set it to `true` for plugins to refer to.

With this config file, `VegChill` will add all plugins then intialize all `init extension` but with dependencies and priorities considered, dependencies and priorities will be talked about in [extension details](./extension.md), and `cmd extension` right after that.

When initialize all extensions, this object will be transfered to them in case they will use it to refer to other extensions, thus all extensions are able to find others using this global environment object.

Other than all these, not much more things are done by this object, all functionals are provided by providers here, so called `extension`s, this object just works like a manager.
