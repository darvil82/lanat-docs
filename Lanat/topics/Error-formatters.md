# Error formatters

Before being shown on the terminal, errors are forwarded to _error formatters_, which esentially convert
the instance of the error into a printable string. They are able to format errors that were generated in the
process of either tokenisation or parsing


## Available formatters

Lanat provides the next error formatters:

<deflist>
<def title="PrettyErrorFormatter">
The default formatter. Displays on the terminal with the next format:

```bash
​ ┌─$ERRORLEVEL
$INPUT
 │ $CONTENTS
 └─────── ───── ── ─
```

With the values being:
- **$ERRORLEVEL:** The error level. ("ERROR", "WARNING", etc)
- **$INPUT:** The whole input. Either all the tokens (colored), or the whole input string.
- **$CONTENTS:** The content/description of the error. Properly wraps the text.

The generated text is colored according to the error level.

</def>

<def title="SimpleErrorFormatter">
Displays on the terminal within a single line, with the next format:

```Bash
[$ERRORLEVEL (at $IN_TYPE $POS, "$INPUT"]: $CONTENT
```

With the values being:
- **$ERRORLEVEL:** The error level. ("ERROR", "WARNING", etc)
- **$IN_TYPE:** The type of the input where the error occurred. (either 'token' or 'char')
- **$POS:** The position of the error in the input. (number)
- **$INPUT:** The token at the position of the error, or the characters near the position.
- **$CONTENT:** The content/description of the error.

The contents inside square brackets are colored according to the error level.

</def>
</deflist>


## Creating a custom formatter

You can create a custom error formatter by creating a new class that extends `ErrorFormatter` and implements the
required methods. An error formatter must be capable of formatting errors that ocurred when tokenizing or after so.
This is an important note because, a parser error works with **tokens**, while a tokenizer error works with raw
**input text**.

Here are the methods you are expected to implement:

<deflist>
<def title="generateTokensView(ParseErrorContext ctx)">

Returns a string for the representation of the input given in tokens. This method will only be invoked if the
error this formatter is handling inherits `ParseError` (most errors are of this kind)

</def>

<def title="generateInputView(TokenizeErrorContext ctx)">

Returns a string for the representation of the input given in raw text. This method will only be invoked if the
error this formatter is handling inherits `TokenizeError`.

</def>

<def title="generate()">

Generates the final formatted error message string that will be displayed to the user.

This method may use the `generateXView` methods previously mentioned, but **not directly**. Instead,
call the `getGeneratedView()` method, which will call the appropiate method, supplying the matching
error context to each.

</def>
</deflist>


The formatter is populated with the data given by the current error we are handling, such as its description,
error level or the highlight options. However, the contexts supplied on the `generateXView()`, provide a finer access
and manipulation of error type specific properties.

`ErrorFormatter` provides you with a few useful methods such as `getContent()`, `getDisplayOptions()`,
`getErrorLevelFormatter()`, etc, to make it easier for you to generate the output.



```Java
public static class TestFormatter extends ErrorFormatter {
	public TestFormatter(ErrorContext currentErrorContext) {
		super(currentErrorContext);
	}

	@Override
	protected String generate() {
		return this.getContentSingleLine() + System.lineSeparator()
			+ "\t(" + this.getErrorLevel().name()
			+ " caused by '" + this.getGeneratedView() + "').";
	}

	@Override
	protected TextFormatter generateTokensView(ParseErrorContext ctx) {
		return ctx.getTokenAt(ctx.getAbsoluteIndex(this.getIndex()))
            .getFormatter();
	}

	@Override
	protected TextFormatter generateInputView(TokenizeErrorContext ctx) {
		return TextFormatter.of(
			ctx.getInputNear(this.getIndex(), 4),
            SimpleColor.BRIGHT_CYAN
        );
	}

	private int getIndex() {
		return this.getHighlightOptions()
			.map(DisplayInput.Highlight::range)
			.map(Range::start)
			.get();
	}
}
```



![ErrorContexts](errorcontexts.png)