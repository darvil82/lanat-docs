# parseFromInto

We have learned how to create an argument parser from a command template
([ArgumentParser#from](Commands.md#creating-the-command-instance)), how to parse the input with it
([Command#parse](Receiving-the-values.md)), and finally, how to put the parsed values into the command template class
instance ([AfterParseOptions#into](Instantiating-Command-Template.md)).

For performing these three steps, we usually write this code:

```Java
void main(String[] args) {
	var result = ArgumentParser.from(MyProgram.class)
		.parse(CLInput.from(args))
		.into(MyProgram.class);
}
```

Since this pattern is so common, Lanat provides a utility method that combines these three steps into one:

```Java
void main(String[] args) {
	var result = ArgumentParser.parseFromInto(MyProgram.class, args);
}
```


## Providing custom after-parse actions

A more verbose overload of `parseFromInto` allows you to provide custom [after-parse actions](Receiving-the-values.md#actions) as well:

```Java
void main(String[] args) {
	var result = ArgumentParser.parseFromInto(
		MyProgram.class,
		CLInput.from(args),
		a -> a.printErrors().exitIfErrors()
	);
}
```