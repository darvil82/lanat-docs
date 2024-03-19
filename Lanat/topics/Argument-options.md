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