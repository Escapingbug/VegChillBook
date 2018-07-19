# VegChill Plugin

## Plugin

The main thought of `VegChill` is to provide all functions in plugins instead of itself. It can provide some basic plugins, but plugins are the cores here as well. So plugins are first class objects.

A plugin can be a python package, as long as it is written considering `VegChill` protocol.

But actually plugins don't provide any functions, `extension`s do. So, they are only responsible to provide extensions. Since python is highly dynamic language, this is done by returning the class objects of `extension`s.

A simple plugin may look like this:

```python
class Plugin(VegChillPlugin):
    @staticmethod
    def init_ext():
        return EnvironInitExt

    @staticmethod
    def cmd_ext():
return []
```

Look here how `VegChillPlugin` is used, it looks like this:

```python
class VegChillPlugin(object):
    """VegChill Plugin"""

    @staticmethod
    def init_ext():
        """get the initalize extensions from the plugin
        Returns:
            object: subclass of init extension or list of that
        """
        raise NotImplementedError("init_ext not implemented")

    @staticmethod
    def cmd_ext():
        """get the command extensions from the plugin
        Returns:
            object: subclass of command extension or list of that
        """
raise NotImplementedError("cmd_ext not implemented")
```

This two methods return extension classes, so outside, `VegChill` can use `extension`s to do other jobs.

## Plugin Structure and Namespace

Plugins are highly extensible, thus namespaces are quite important in this situation when we have to know which plugin we are referring to. But first, we should talk about the structure.

A plugin can be a package, but exporting a plugin class implementing `VegChillPlugin` methods. The name `plugin` of some package but be directly exported as a subclass of `VegChillPlugin`.

For example, when you write a plugin that with no extensions, it looks like this:

```python
class Plugin(VegChillPlugin):
    @staticmethod
    def init_ext():
        return []

    @staticmethod
    def cmd_ext():
        return []
```

However you need to tell `VegChill` how to find this class, so, in `__init__.py`, you may do this:

```
from .my_plugin import Plugin as plugin
```

This is to ensure the name `plugin` is directly accessable to `VegChill` as a implemented `VegChillPlugin` subclass. You may use other python magic to do so, just be sure this constraint is fulfilled.

Now, when you specify the plugin name in your config file, like this:

```
[plugin]
my_plugin
```

This plugin will be loaded into `VegChill`, thus everything will work fine.

Or sometimes the name is complex, like this

```
[plugin]
pip_installed_package.my_plugin
```

Then `VegChill` will load `pip_installed_package.my_plugin.plugin`, use it as it is a `VegChillPlugin` subclass with implemented methods.
