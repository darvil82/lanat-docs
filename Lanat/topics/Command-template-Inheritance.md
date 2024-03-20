# Command Template Inheritance

As previously mentioned, a command template must always inherit from `CommandTemplate`. However, it must be noted that
a class can inherit from any other class as long as in the end of the inheritance chain there is a class that extends
`CommandTemplate`. This can be useful for creating more "generic" command templates that **can be used as a base** for other
command templates.


### Example

Here's a command template that just defines a command with two arguments, related to a user:

```Java
@Command.Define
class UserCommand extends CommandTemplate {
	@Argument.Define
	public String userName;
	
	@Argument.Define
	public int email;
}
```

We can now create a brand new command template that inherits from ``UserCommand`` and adds a new argument to it:

```Java
@Command.Define
class StudentCommand extends UserCommand {
	@Argument.Define
	public String favoriteSubject;
}
```

The `StudentCommand` class now has all the arguments that `UserCommand` has, plus a new one.

> **Remember** that this is still just a template. This is different from creating sub-commands.
> {style="note"}


## Default command templates

Lanat provides a few default command templates that you can inherit from;

### ``CommandTemplate.Default``

Very common command template as it adds the ``help`` and ``version`` arguments to the command.
This essentially just calls ``Command#addHelpArgument`` and ``Command#addVersionArgument`` in the ``after`` method.

Since these arguments are added **only** in the ``afterInit`` method, they can not be accessed directly in the instance
of the class.

### ``CommandTemplate.Default.WithVerbose``

Inherits from ``CommandTemplate.Default`` and adds a ``verbose`` argument to the command as well.

The ``verbose``/`v` argument has a type of `CounterArgumentType`, which means that it can be used multiple times to increase
the verbosity of the command.