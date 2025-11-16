---
switcher-label: Command creation method
---

# Seeing if command was used

It is often useful to know if the user interacted with some specific command. Lanat offers a few ways to check this,
each with its own use case.

## Provide a callback

When defining a command, you can provide a callback that will be executed if the command is used by the user.
The callback will receive the [`ParseResult`](ParseResult.md) object that belongs to the command as a parameter.

```Java
var myCommand = new ArgumentParser("MyCommand") {{
	// more code here
	
	setOnUsedCallback(result -> {
		System.out.println("MyCommand was used!");
	});
}};
```
{ switcher-key="Traditional" }

````Java
@Command.Define
class MyCommand extends CommandTemplate {
    // more code here
    
    @Override
    public void onUsed(ParseResult parseResult) {
        System.out.println("MyCommand was used!");
    }
}
````
{ switcher-key="Templates" }


> The [`ParseResultRoot`](ParseResult.md#parseresultroot) object is returned by the `getResult` terminator method.
>
> In order to access this instance within the callbacks, you will need to cast the `ParseResult`
> object to `ParseResultRoot` when appropriate (that is, only when using it on the root command). Here's an example:
> ````Java
> @Override
> public void onUsed(@NotNull ParseResult parseResult) {
> 	System.out.println("MyCommand was used!");
> 
> 	if (parseResult instanceof ParseResultRoot rootResult)
> 		System.out.println("Forward value: " + rootResult.getForwardValue());
> }
> ````
{ switcher-key="Templates" }

> The [`ParseResultRoot`](ParseResult.md#parseresultroot) object is returned by the `getResult` terminator method.
>
> In order to access this instance within the callbacks, you will need to cast the `ParseResult`
> object to `ParseResultRoot` when appropriate (that is, only when using it on the root command). Here's an example:
> ````Java
> var myCommand = new ArgumentParser("MyCommand") {{
>     // more code here
> 
> 	setOnUsedCallback(result -> {
> 		System.out.println("MyCommand was used!");
> 
>      	if (parseResult instanceof ParseResultRoot rootResult)
>      		System.out.println("Forward value: " + rootResult.getForwardValue());
> 	});
> }};
> ````
{ switcher-key="Traditional" }


## Using `ParseResult`

The [`ParseResult`](ParseResult.md) object allows you to check if the command was used with the `wasUsed()` method:

```Java
if (result.wasUsed()) {
	System.out.println("Command was used");
}

if (result.getSubResult("subcmd").wasUsed()) {
	System.out.println("Sub-command was used");
}
```