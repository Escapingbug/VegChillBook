# Extension

Extensions are a huge role in `VegChill`'s framework structure. It works like providers, providing functionalities for others to use.

## Priority and Dependency

Sometimes, an extension will depend on others. Like there is a default extension called `environ`, it provides a global environment to add global variables alike functionality. Since most of the time it should be intialized **before** any other extensions, and some other extensions may depend on it, unless it is loaded, other extensions may not be able to initialize, we use priority and dependency to solve this problem.

Each extension is associated with two class-accessible variables `priority` and `dependency`, a extension prototype is like this:

```python
class VegChillExt(object):
    priority = 100
    dependency = []

    @classmethod
    def set_vegchill(cls, vegchill):
        cls.vegchill = vegchill

    @staticmethod
    def gdb_support():
        """is this extension support gdb or not"""
        return True

    @staticmethod
    def lldb_support():
        """is this extension support lldb or not"""
return True
```

Priority ranges from 0 to 100, or any higher numbers. 0 is the highest priority. And dependencies are a list of strings with `extension_python_path.extension_name`. Like `vegchill.plugins.util.util`, here `vegchill.plugins.util` is the import name of the extension, and the final `util` is the name of a extension, which we depend on.

Note that not all types of extensions are sensible to denpendencies and priorities.

## Extension types

There are types of extensions which provide different usage scenerios.

### Init Extension (Sensible to prior and depend)

An `init extension` is a extension that is intialized right after `VegChill` main object and is sensible to priority and denpdency. 

A name method must be implemented, this is important for other extension to refer to us, since we use `import_path.name` to refer to some extensions.

The idea of init extensions is to provide universal function interfaces instead of writing functions since we want to generalize both `lldb` and `gdb` features and their use of libraries.

### Cmd Extension (not sensible)

The other type supported for now is `cmd extension`, which is much more simpler to understand since each extension represents a command in `lldb` or `gdb`.
