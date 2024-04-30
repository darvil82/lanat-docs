# ParseResult

When calling the [`getResult` terminator method](Receiving-the-values.md#result-method), you will receive a `ParseResult` object.
This object allows you to easily access the parsed values.


<tip id="cmd-templates-result">

For [instantiated command templates](Instantiating-Command-Template.md), you can access the `ParseResult` object by calling
the `getParseResult` method on the instantiated command template, or by overriding the `onUsed(ParseResult)` method in the
command template.

</tip>

<procedure title="Accessing the parsed values" id="access-values">
<step>

Call ``get`` and specify the argument whose value you want to access:

```Java
// retrieve the value of the argument "arg"
var argValue = result.<String>get("arg")
```

</step>

<step>

`argValue` is an `Optional<String>` object. You may have to check if the value is present or not by using
`Optional`'s methods.

```Java
argValue.ifPresent(
	value -> System.out.printf("Value of arg: %s%n", value)
);
```
{ignore-vars="true"}
```Java
String thing = argValue.orElse("unknown");
```

See the [Java Optional documentation](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Optional.html)
for more information.

</step>
</procedure>


<procedure title="Accessing sub-command values" id="access-subcmd-values">
<step>

To access the values of a sub-command, you can use the same `get` method. The parameter is a var-args array of strings
that represent the complete path of commands to reach the argument.

```Java
var subCmdValue = result.<String>get("main-cmd", "subcmd", "arg")
//                                   ^ cmd       ^ cmd     ^ arg
```

<tip>

You can also use the `getSubResult` method to get the `ParseResult` object of a sub-command.

```Java
var subResult = result.getSubResult("main-cmd")
	.getSubResult("subcmd")
	.<String>get("arg");
```

</tip>

</step>
</procedure>


<procedure title="See if a command was used" id="check-command-used">
<step>

You can, on any `ParseResult` in the hierarchy, check if the command it belongs to was used by calling the `wasUsed` method:

```Java
if (result.getSubResult("subcmd").wasUsed()) {
	System.out.println("Sub-command was used");
}
```

</step>
</procedure>


## ParseResultRoot

The `ParseResultRoot` object is the root of the `ParseResult` hierarchy. It inherits `ParseResult`, thus it has the methods
mentioned above, while also provides the following:

> The `ParseResultRoot` object is returned by the `getResult` terminator method.
> 
> In order to access this instance in
> command templates, you will need to [cast the `ParseResult` object](#cmd-templates-result) to `ParseResultRoot`
> when appropriate (that is, only when using it on the root command).

<deflist>
<def title="getForwardValue">

Returns an `Optional<String>` object that contains the [forward value](Input-syntax.md#forward-value)
(if it was provided by the user).

</def>

<def title="getUsedResults">

Returns a list of all the `ParseResult` objects that belong to a command that was used by the user. The list is ordered
from the root command to the last used command.

Here's an example of something you can do with this method:

````Java
result.getUsedResults()
	.forEach(res -> { switch (res.getCommand().getName()) {
		case "main" -> {
			System.out.println("main arg value: " + res.get("arg"));
		}
		case "sub-cmd" -> {
			System.out.println("arg num1 value: " + res.get("num1"));
			System.out.println("arg num2 value: " + res.get("num2"));
		}
		default -> {}
	}});
````

</def>
</deflist>