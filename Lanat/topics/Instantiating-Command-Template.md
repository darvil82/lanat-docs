# Instantiating the command template

By using the terminator [`into(Class)`](Receiving-the-values.md#into-method) method in the
[`AfterParseOptions`](Receiving-the-values.md#the-afterparseoptions-class)
class, you can instantiate a command template class and set all the fields annotated with
[`@Argument.Define`](Command-templates.md#the-argument-define-annotation) with the parsed values given by the parse result.

<procedure title="Tutorial">
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


### Instantiating sub-commands as well

If the command template class has sub-commands defined, the `into` method will also instantiate them recursively if
properly defined:

<procedure title="Tutorial">
<step>

Let's assume we have a command template class with a sub-command like this:

```Java
@Command.Define
public class MyProgram extends CommandTemplate {
	@Argument.Define
	public double a;
	
	@Command.Define
	public class SubCommand extends CommandTemplate {
		@Argument.Define
		public double b;
	}
}
```

</step>

<step>

In order to be able to access the sub-command instance upon instantiation, you need to define a
[`@CommandAccesor`](Command-templates.md#the-commandaccessor-annotation) property:

```Java
@Command.Define
public class MyProgram extends CommandTemplate {
	...
	
	@CommandAccessor
	public SubCommand subCmd;
	
	...
}
```

> Lanat will automatically assign the instantiated sub-command to the `subCmd` property of `MyProgram` by looking up the
> type of the property.
> {style="note"}

</step>

<step>

Now, after parsing the arguments, call the `into` method with the class of the command template, and access the sub-command's
fields:

```Java
public static void main(String[] args) {
	var result = ArgumentParser.from(MyProgram.class)
		.parse(CLInput.from(args))
		.into(MyProgram.class);
		
	System.out.printf("a: %f, b: %f", result.a, result.subCmd.b);
}
```

</step>

</procedure>

> If there is a sub-command template class defined inside another template class, but there's no property annotated
> with ``@CommandAccessor`` in the outer class, a ``CommandTemplateException`` will be thrown when it is instantiated.
> {style="warning"}