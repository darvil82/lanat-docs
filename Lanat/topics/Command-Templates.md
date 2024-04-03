# Command Templates

Command templates are classes that, with the help of annotations, allow you to define commands and arguments in a
type-safe and less verbose way.


## Define a command

To define a command, you need to create a new class that extends ``CommandTemplate`` and annotate it with
``@Command.Define``.

[Read more about defining a command](Commands.md#define-a-command-template).


## The `@Command.Define` annotation

Values may be provided to the annotation to specify the names and description of the command.
If no name is provided, the name of the class will be used instead.

The description is also optional, and it will be used to provide a summary of the purpose of the command in the
help message.

```Java
@Command.Define(
	names = { "myCmd", "myCommand" },
	description = "This is my cool command."
)
class MyCommand extends CommandTemplate { }
```


## The `@Argument.Define` annotation

To define an argument, you need to add a property to the command template class and annotate it with
``@Argument.Define``.

[Read more about defining arguments](Arguments.md#defining-arguments).

The annotation allows to specify most of the properties of the argument, such as the ``names()``,
``description()``, ``type()``, etc.


### How the argument type is set

If an argument type class (`Class<ArgumentType>`) is provided in the ``type()`` property of the annotation, an instance
of that class will be used as the type of the argument.

> The class specified must have a public no-args constructor for it to be properly instantiated by Lanat.
> {style="warning"}

If no type is provided, Lanat will attempt to infer the argument type from the type of the property that the argument is
assigned to. For example, if the property is of type ``int``, Lanat will use the ``IntegerArgumentType`` as the type of
the argument. See more about argument type inference [here](Type-inference.md).


### Setting a ``group()``

You can also specify a group in which this argument will be placed in. By using the ``group()`` property, you can
specify the name of the group. If the group does not exist, it will be created.

Multiple arguments can be placed in the same group by specifying the same name for the group.

```Java
// adding both arguments to the same group
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define(group = "stuff")
	public String arg1;
	
	@Argument.Define(group = "stuff")
	public String arg2;
}
```



## The ``@InitDef`` methods

Sometimes it is not possible to define all the properties of a command or argument by only using annotations, since in
some cases you may need to use a more complex logic to define them. For example, you may need to specify an argument
type that requires a constructor with parameters.

In these cases, you can use either the `beforeInit` or `afterInit` static methods, which are explained below.
When added to a command template class, these methods are called by Lanat when it is creating the command instance
from the command template class.
They must be annotated with `@InitDef`.


### The `beforeInit` method

This method is called before the arguments are added to the command. It receives a ``CommandBuildContext`` instance, which
allows to access the argument builders. At the point of this method being called, the argument builders have already
been created with the properties defined by the annotations, except for the `type()` property, which is not yet set.

This allows you to properly set the type of the argument by using the `ArgumentBuilder#type` method, which is
exactly what we need for the example mentioned above.

```Java
@InitDef
public static void beforeInit(CommandBuildContext ctx) {
	ctx.argWithType("especial", new MyArgumentType(5, "Hello"));
}
```

``ctx.argWithType(name, type)`` is logically equivalent to ``ctx.arg(name).type(type)``.

These methods allow you to select the argument builder by the name of the arguments defined in the command template.
As mentioned before, any property of them may be altered at this point.

> If no argument type was specified for an argument after `beforeInit` is called, Lanat will follow the steps to
> attempt to get the type [mentioned above](#how-the-argument-type-is-set).
> {style="note"}


### The `afterInit` method

This method is called right after all the argument builders have been built into argument instances, and they have been
added to the command instance. It receives the actual command object.

At this point it is possible to do things such as creating a group and adding arguments to it. Example:

```Java
@InitDef
public static void afterInit(Command cmd) {
	cmd.addGroup(new ArgumentGroup("stuff") {{
		addArgument(cmd.getArgument("especial"));
		addArgument(cmd.getArgument("another"));
		setRestricted(true);
	}});
}
```

Note that this can be greatly simplified by using the ``group()`` property of the ``@Argument.Define`` annotation:

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define(group = "stuff")
	public String especial;

	@Argument.Define(group = "stuff")
	public String another;
	
	@InitDef
	public static void afterInit(Command cmd) {
		cmd.getGroup("stuff").setRestricted(true);
	}
}
```


## The ``@CommandAccessor`` annotation

This annotation is used to specify a property that will hold an instance of a sub-command once it is parsed.

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@CommandAccessor
	public SubCommand subCmd;
	
	@Command.Define
	public static class SubCommand extends CommandTemplate { }
}
```

Once ``SubCommand`` is instantiated by Lanat, it will be automatically assigned to the ``subCmd`` property of
``MyCommand`` upon instantiation. This will later allow you to access the inner command's properties.

> If there is a sub-command template class defined inside another template class, but there's no property annotated
> with ``@CommandAccessor`` in the outer class, a ``CommandTemplateException`` will be thrown.
> {style="warning"}


<seealso>
	<category ref="related">
		<a href="Commands.md" anchor="define-a-command-template">How to define a Command</a>
		<a href="Arguments.md" anchor="defining-arguments">How to define an Argument</a>
		<a href="Argument-Types.md">Argument Types</a>
	</category>
</seealso>