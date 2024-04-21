# Instantiating the command template

By using the terminator [`into(Class)`](Receiving-the-values.md#into-method) method in the
[`AfterParseOptions`](Receiving-the-values.md#the-afterparseoptions-class)
class, you can instantiate a command template class and set all the fields annotated with
[`@Argument.Define`](Command-templates.md#the-argument-define-annotation) with the parsed values given by the parse result.

<procedure title="Example">
<step>

Let's assume we have a command template class like this:

```Java
@Command.Define
public class MyProgram extends CommandTemplate {
	@Argument.Define
	public double a;
	
	@Argument.Define
	public double b;
}
```

</step>

<step>

Then, after parsing the arguments, call the `into` method with the class of the command template:

```Java
public static void main(String[] args) {
	var result = ArgumentParser.from(MyProgram.class)
		.parse(CLInput.from(args))
		.into(MyProgram.class);
}
```

</step>

<step>

`MyProgram` will be instantiated and returned with the fields `a` and `b` set to the parsed values:

```Java
System.out.printf("a: %f, b: %f", result.a, result.b);
```

</step>

</procedure>

> The `ArgumentParser` doesn't need to be created from a command template in order to be able to use the `into` method.
> `into` just looks up the fields (annotated with [`@Argument.Define`](Command-templates.md#the-argument-define-annotation))
> whose names match the argument names and sets them with the parsed values.
> {style="note"}