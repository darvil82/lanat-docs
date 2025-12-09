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