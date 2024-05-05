---
switcher-label: Command creation method
---

# Receiving the values

Once you have defined a command, you can use it to parse input from the user.

To parse input, call the ``parse()`` method that is present on the `ArgumentParser` instance:

```Java
void main(String[] args) {
	// creation of the command instance
	var myCommand = ArgumentParser.from(MyCommand.class);
	
	// parsing the input
	var afterParseOptions = myCommand.parse(CLInput.from(args));
}
```
{switcher-key="Templates" collapsible="true" collapsed-title-line-number="6"}

```Java
void main(String[] args) {
	// creation of the command instance
	var myCommand = new ArgumentParser("MyCommand") {{
		addArgument(Argument.create(new StringArgumentType(), "userName"));
		addArgument(Argument.create(new IntegerArgumentType(), "age"));
	}}
	
	// parsing the input
	var afterParseOptions = myCommand.parse(CLInput.from(args));
}
```
{switcher-key="Traditional" collapsible="true" collapsed-title-line-number="9"}

> You can read more about the `CLInput` class [here](CLInput-class.md).

This will return a ``AfterParseOptions`` object, which allows you to do some post-parsing operations that can be
useful.



## The `AfterParseOptions` class

The `AfterParseOptions` class provides utility methods and defines a set of [_actions_](#actions) to be performed right before the
parsed values are given. These actions are only executed if a [_terminator_](#terminator-methods) method is called.

### Actions

Use the `withActions` method to define the actions that will be executed. The actions are defined by a lambda that receives
an `AfterParseActions` instance.

```Java
var afterParseOptions = myCommand.parse(CLInput.from(args));

// define the actions method chain
afterParseOptions.withActions(a -> a.printErrors().exitIfErrors());
```

These methods will be executed in the order they are chained. Here's a list of the available actions:

<deflist>
<def title="printErrors()" id="printErrors">

Prints all errors that occurred during parsing (Returned by `AfterParseOptions#getErrors()`) to `System.err`.

</def>

<def title="printHelpIfNoInput()" id="printHelpIfNoInput">

Prints the help message to `System.out` if no arguments were passed to the program.

</def>

<def title="exitIfErrors()" id="exitIfErrors">

Exits the program with the error code returned by `AfterParseOptions#getErrorCode()` if any errors occurred during parsing.

</def>

<def title="exitIfNoInput()" id="exitIfNoInput">

Exits the program with a code of `0` if no arguments were passed to the program.

</def>

</deflist>


<note>

The default actions are defined by the `AfterParseOptions#DEFAULT_ACTIONS` constant. Which is defined as:

```Java
actions -> actions
	.printErrors()
	.printHelpIfNoInput()
	.exitIfErrors()
	.exitIfNoInput();
```

</note>


### Terminator methods

Terminator methods are methods that will execute the actions defined in the `AfterParseOptions` once called before returning
the actual parsed values. The available terminator methods are:

<deflist>

<def title="getResult()" id="result-method">

Returns a [`ParseResultRoot`](ParseResult.md) object that contains all the parsed arguments.

</def>

<def title="into(Class)" id="into-method">

Instantiates the given Command Template class and sets all the fields annotated with
[`@Argument.Define`](Command-templates.md#the-argument-define-annotation) corresponding to their respective parsed arguments.
This method will also instantiate all the sub-commands recursively
[if defined in the template class properly](Instantiating-Command-Template.md).

</def>

</deflist>

> When called, terminator methods will also execute all the callbacks defined in the command structure.
> {style="note"}