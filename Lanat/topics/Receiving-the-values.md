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