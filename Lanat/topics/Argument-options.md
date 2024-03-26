# Options

Arguments have plenty of options to customize their behavior. All of these options can be set in either an
`Argument` instance, or in the `ArgumentBuilder` when creating an argument.


## Default value

Sets a value to use when the user does not provide one.

```Java
Argument.create(new IntegerArgumentType(), "number")
	.defaultValue(10);
```

<tip>

In [command templates](Command-Templates.md), you can also set a default value by setting the field's value:

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public int number = 10;
}
```

Note that this is not exactly the same as setting the default value in the argument. This value in the property is
only used when the command template is instantiated, after input has been parsed.

If both are set, **the argument's default value will have priority.**

</tip>



## Required

Marks the argument as required. If the user does not provide a value for this argument, an error will be thrown by Lanat
indicating that the argument is missing.

By default, arguments are not required.



## Positional

Marks the argument as a positional argument. Positional arguments are arguments that do not need to have a name specified
when being used. That is, the user can directly provide the value for the argument without specifying the argument's name.

```Java
@Argument.Define(positional = true)
public int number;
```

```Bash
myCommand 123
# or
myCommand --number 123
```

> The place of the positional argument in the command line input is determined by the order in which the arguments are
> defined in the command.



## Prefix

The prefix is the character that precedes the argument's name in the command line input. It must be a value defined in
the ``Argument.PrefixChar`` enum.

```Java
Argument.create(new IntegerArgumentType(), "number")
	.prefix(Argument.PrefixChar.SLASH);
```

```Bash
myCommand /number 123
```

> By default, the prefix is set to `Argument.PrefixChar.DEFAULT`, which is a value that can be altered by the user.
>
> Use ``Argument.PrefixChar.setDefaultValue(Argument.PrefixChar)`` to set the default prefix character used by all
> arguments.

> ``Argument.PrefixChar`` contains values such as `MINUS`, `DASH`, `PLUS`, etc. However, it also has an `AUTO`
> value which will automatically determine the prefix based on the operating system (e.g. `-` for Unix-like systems and
> `/` for Windows). This is the default value.
> {style="note"}