# Errors

Lanat displays error messages in the terminal when it encounters errors in the input. As explained previously
[here](Argument-Type-parsing-process.md#error-collection), Lanat collects all the errors that occurred during parsing.

They are thrown by _error containers_, such as [commands](Commands.md), [argument types](Argument-types.md) or the parser.

![an error](example-result.png)


## Error levels

Errors can have different severity levels, those being `ERROR`, `WARNING`, `INFO` and `DEBUG`.

The severity levels themselves do not provide any special behavior, but they can be used to filter errors
based on their severity. By default, errors with level `ERROR` will cause the program to exit with a non-zero
exit code, while other levels will not. This behavior can be changed by the user on any error container:

<deflist>
<def title="Minimum exit level">

The minimum level at which the program will exit with a non-zero exit code. The default value is `ERROR`, which
means that only errors with level `ERROR` will cause the program to exit.

For example, making `WARNING` also cause the program to exit:

````Java
command.setMinimumExitErrorLevel(ErrorLevel.WARNING);
````

> The program exists when the pertinent [after-parse action](Receiving-the-values.md#exitIfErrors) is executed.
> {style="note"}

</def>
<def title="Minimum display level">

The minimum level at which the error will be displayed to the user. The default value is `INFO`, which means that
only errors with level `INFO` or higher will be displayed (no `DEBUG` errors).

For example, making `DEBUG` errors also be displayed:

````Java
command.setMinimumDisplayErrorLevel(ErrorLevel.DEBUG);
````

</def>
</deflist>

> The minimum exit error level can never be lower than the minimum display error level, since errors that would cause
> the program to exit must also be displayed to the user.
> {style="warning"}


## Error containers nesting

When changing the minimum error levels on a command, this change will be propagated to all sub-commands and argument types
right before parsing. However, this doesn't mean you can't change the minimum error levels on each sub-command
individually.

<procedure title="Example">
<step>

Consider this example where we have the next command structure:

```Java
var cmd = new ArgumentParser("my-program") {{
	setMinimumExitErrorLevel(ErrorLevel.WARNING);

	addArgument(Argument.create(new MyType(), "arg1"));

	addCommand(new Command("sub-command") {{
		setMinimumExitErrorLevel(ErrorLevel.INFO);
		
		addArgument(Argument.create(new MyType(), "arg2"));
	}});
}};
```

Here, if `arg1` throws an error with a level of `INFO`, it will be shown, but will not cause the program to exit.
However, if `arg2` throws it, it will, because the minimum display error level is set to `INFO` on the sub-command.

</step>
</procedure>
