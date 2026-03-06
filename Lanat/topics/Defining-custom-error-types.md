# Defining custom error types

You can create your own error types that you may then later throw in your own argument types, or anywhere else.
Let's take a look at the implementation of one of the built-in errors in lanat and see how it is done:

````Java
/**
 * Warning that occurs when an argument is used while there's a unique
 * argument that has been used.
 * @param indexAndOffset The indices of the tokens that caused the error. (start, end)
 * @param argument The argument that thrown the error.
 */
public record UniqueArgumentUsedError(
    Pair<Integer, Integer> indexAndOffset,
    Argument<?, ?> argument
) implements Error.ParseError
{
    @Override
    public void handle(ErrorFormattingContext fmt, ParseErrorContext ctx) {
        fmt.withContent(
            "Argument " + HelpFormatter.getRepresentation(this.argument)
                + " may not be used while "
                + (this.argument.isUnique() ? "another" : "a")
                + " unique argument is used."
        )
        .displayAndHighlightInput(
            new DisplayInput.Highlight(this.indexAndOffset, false)
        );
    }

    @Override
    public ErrorLevel getErrorLevel() {
        return ErrorLevel.WARNING;
    }
}
````

In this case, `UniqueArgumentUsedError` is a kind of error that is thrown after the tokenization process, thus, it
implements `ParseError`.

When using `ParseError` or `TokenizeError`, you must always provide an implementation for `handle()`. This is the method
that later will be internally called by the error collector in order to properly format your error on the terminal.

## `handle()`

Let's take a deeper look at `handle()`:

````Java
@Override
public void handle(ErrorFormattingContext fmt, ParseErrorContext ctx) {
    fmt.withContent(
        "Argument " + HelpFormatter.getRepresentation(this.argument)
            + " may not be used while "
            + (this.argument.isUnique() ? "another" : "a")
            + " unique argument is used."
    )
    .displayAndHighlightInput(
        new DisplayInput.Highlight(this.indexAndOffset, false)
    );
}
````

When invoked, we are provided with two contexts:

<deflist type="medium">
<def title="Formatting context">

Provides utilities to easily format errors to text.

</def>
<def title="Error context">

Provides a proxy to get data about the error. This will either be a `ParseErrorContext` or a `TokenizeErrorContext`,
depending on the kind of error we are creating.

</def>
</deflist>

### Display tools

As you may have noticed, this method is mostly in charge of generating the description of the error, explaining why it happened.
However, we also have available the `displayInput` methods. (In this case, the `displayAndHighlightInput` method). This basically
lets the error formatter that will manage this error, know that it is preferrable to highlight a certain range of tokens or text
when displaying it to the user (for instance, marking in red where the issue is in the input).

> The error formatter may later ignore whatever display options are supplied by the error.


## Specifying the error level

On the example we can see the error overriding the `getErrorLevel` method to define its [error level](Errors.md#error-levels):

````Java
@Override
public ErrorLevel getErrorLevel() {
    return ErrorLevel.WARNING;
}
````

> If not overridden, the default error level will be `ERROR`.


## Condiotionally remove other errors

Sometimes it is necessary to remove certain other errors if our error was created.

For example, in Lanat, if the user accidentally writes
the name of an argument with a different prefix, a warning will be shown mentioning that an argument with such name exists, but with a different
prefix (`SimilarArgumentError`). However, the warning which says "an unmatched token was supplied" (`UnmatchedTokenError`) is not shown in this specific case.

This is done by overriding the `shouldRemoveOther` method:

```Java
public record TestError(Argument<?, ?> argument) implements Error.ParseError {
    @Override
    public void handle(ErrorFormattingContext fmt, ParseErrorContext ctx) {
        fmt.withContent("A silly error.");
    }

	@Override
	public boolean shouldRemoveOther(@NotNull Error<?> other) {
		return other instanceof RequiredArgumentNotUsedError reqArgNotUsedError
			&& reqArgNotUsedError.argument == this.argument;
	}
}
```

In this example, if there's a `RequiredArgumentNotUsedError` with the same argument as our example error, it will be discarded and not shown to the user.

> Only errors of the **same command** are compared.
> This method is invoked for each error in the list of errors collected (except itself), and the error is removed if this method returns **true**.
> {style="note"}