# Introduction

`VegChill` is an open source `Xdb` plugin framework, tends to be a universal plugin for both `lldb` and `gdb`, and make features pluggable with just `pip`.

# Design
## What is considered? (skip this if you don't care how we choose to design the framework)

To be general over both `lldb` and `gdb`, one should first consider the differences between this two tools with reference to their `Python` language support.

`lldb` uses a interesting approach to support python language, within `lldb`, a compiled python is used, thus the python version is fixed according to `lldb` you are using. As far as I know, currently `lldb` only uses python2.

`gdb` however takes advantage of your system python, it uses your system python first, some systems are defaulted to use python3 instead of python2, thus the language differences between python2 and python3 must be made into consideration.

As for the usage of module, `lldb` and `gdb` are not very different, both use python modules and their functions, classes etc. But note that the two modules are totally different (of course they are different!), so the interfaces and usages maybe quite different, so we should generalize that when implementing the framework, right?

Now, after all these, since we tend to make all things pluggable, plugins seem to be a great option.

## Decision

Knowing what kind of things we should consider, now the decisions are all seems to be straight forward.

### Plugin

A `plugin` is a first-class object we use to extend our framework. A `plugin` is just a main object to provide the interface to retrieve `extension`s.

The interface is like this (from source code directly):

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

A `plugin` writer should implement these two methods to provide interfaces to retrieve `extension`, who is responsible for real jobs.

### Extension

As we know, `plugin` is just to provide interfaces for `extension` retrieves, which leaves the most interesting parts all in `extension`. So what is extension?

An `extension` is a interface to provide functionalities, like a function, or a `gdb`/`lldb` command. Thus we split `extension`s into multiple types.

Note that there are some common things sharing between all `extension`s, like main `vegchill` object, priority and dependency, we'll leave that to detailed description.

#### Init extension

An `init extension` is a kind of `extension`s who needs to be initialized right after the initialization of whole framework. This is needed since we sometimes need to provide functionalities with global information, thus global information will be initialized right after the framework.

And the interface for `init extension` is very simple:

```
class VegChillInitExt(VegChillExt):
    """VegChill Init Extension, this class will be instantiated when init the instance,
    and init extension can be accessed from main instance like `veg_chill['ext_path']`"""

    @staticmethod
    def name():
        """name of this extension
        With name, one can access via ext_path, which is the module.ext_name.
        """
        return ''
```

YES! Only names are needed! But where do we do all the jobs? Just do that in `__init__()` constructor functions. Other methods can be implemented as you wish with reference to how you want to use this `extension`. For further info, please refer to examples.

#### Cmd extension

A `cmd extension` are more straght-forward, since they represent a single command in `gdb`/`lldb`.

The interface is much more complex, so we leave it to tutorial parts. But all you need to know is a `cmd extension` represents a single command, like `hexdump`, or any other commands.

# Summarize

To sum up, in this chapter, we did a simple overview of the framework, but not in detail. We know that we take `plugin`s to be first-class ones, and `extension`s are used as providers, and there are kinds of them, and if you didn't skip anything, you'll know what we tend to solve with these solutions.
