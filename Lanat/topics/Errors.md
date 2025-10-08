# Errors

Lanat displays error messages in the terminal when it encounters errors in the input. As explained previously
[here](Argument-Type-parsing-process.md#error-collection), Lanat collects all the errors that occurred during parsing.

They are thrown by _error containers_, such as [commands](Commands.md), [argument types](Argument-types.md) or the parser, and later
collected and displayed to the user.

![an error](example-result.png){width=700}


## Error levels

Errors can have different severity levels, those being `ERROR`, `WARNING`, `INFO` and `DEBUG`.

The severity levels themselves do not provide any special behavior, but they can be used to filter errors
based on their severity. By default, errors with level `ERROR` will cause the program to exit with a non-zero
exit code, while other levels will not. This behavior can be changed by the user on any error container:

<deflist>
<def title="Error exit threshold">

The threshold at which the program will exit with a non-zero exit code. The default value is `ERROR`, which
means that only errors with level `ERROR` will cause the program to exit.

For example, if this is set to `WARNING`, then errors including `WARNING` and `ERROR` will cause the program to exit, but
`INFO` and below will not.

Here we are making `WARNING` also cause the program to exit:

````Java
command.setErrorExitThreshold(ErrorLevel.WARNING);
````

> The program exists when the pertinent [after-parse action](Receiving-the-values.md#exitIfErrors) is executed.
> {style="note"}

</def>
<def title="Error display threshold">

The threshold at which the error will be displayed to the user. The default value is `INFO`, which means that
only errors with level `INFO` or higher will be displayed (no `DEBUG` errors).

For example, if this is set to `INFO`, then errors including `INFO`, `WARNING` and `ERROR` will be displayed, but
`DEBUG` will not.

Here we are making `DEBUG` errors also be displayed:

````Java
command.setErrorDisplayThreshold(ErrorLevel.DEBUG);
````

</def>
</deflist>

> The error exit threshold level can never be lower than the display threshold, since errors that would cause
> the program to exit must also be displayed to the user.
> {style="warning"}


## Error containers nesting

When changing the error thresholds on a command, this change will be propagated to all sub-commands and argument types
right before parsing. However, this doesn't mean you can't change the thresholds on each sub-command
individually.

<procedure title="Example">
<step>

Consider this example where we have the next command structure:

```Java
var cmd = new ArgumentParser("my-program") {{
	setErrorExitThreshold(ErrorLevel.WARNING);

	addArgument(Argument.create(new MyType(), "arg1"));

	addCommand(new Command("sub-command") {{
		setErrorExitThreshold(ErrorLevel.INFO);
		
		addArgument(Argument.create(new MyType(), "arg2"));
	}});
}};
```

Here, if `arg1` throws an error with a level of `INFO`, it will be shown, but will not cause the program to exit.
However, if `arg2` throws it, it will, because the exit error threshold is set to `INFO` on the sub-command.

</step>
</procedure>


## Exit error code

When the program exits due to an error, by default, it returns with a code of `1`. This value can be easily changed by
calling the `setErrorCode` method on the command:

````Java
command.setErrorCode(3);
````

Commands in the hierarchy can have different error codes. The one that will be used to exit the program is actually
the result of the `OR` operation between all the error codes of the commands that thrown errors during parsing.

<procedure title="Example">
<step>

Assume we have the following command structure:

- Command `foo` has an error code value of `2`. (`0b010`)
- Command `bar` has an error code value of `5`. (`0b101`)

Both commands failed, so in this case the program will exit with a return value of `7`. (`0b111`)

</step>
</procedure>