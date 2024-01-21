---
switcher-label: Command creation method
---

# Sub-Commands

Commands can contain sub-commands. This allows you to separate different logical parts of your program, which can
have different arguments.

For example, a program like `git` has different sub-commands like `git commit`, `git push`, `git pull`, etc.
These three are all different commands, but they do belong to the same parent command, `git`.

Let's take a look at how we can define sub-commands in Lanat.


## Sub-Commands in Command Templates { switcher-key="Templates" }

Simply add a new nested command template class to your command template class:

````Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Command.Define
	static class SubCommand extends CommandTemplate { }
}
````

We are still missing something, as this code will make Lanat throw a ``CommandTemplateException``.
This is because at the time of gathering the parsed values, we have no way of actually accessing an instance
of the sub-command class.

We need to add another property which will hold an instance of the sub-command class once it is parsed:

````Java
@Command.Define
class MyCommand extends CommandTemplate {
	@CommandAccessor
	public Test subCmd;

	@Command.Define
	static class SubCommand extends CommandTemplate {
		@Argument.Define
		public int number; // we also added an argument to the sub-command
	}
}
````


## Defining Sub-Commands Imperatively { switcher-key="Traditional" }

You can add sub-commands to a command by using the ``Command#addCommand`` method:

````Java
var myCommand = new ArgumentParser("MyCommand") {{
	addCommand(new Command("SubCommand") {{
		// we also added an argument to the sub-command
		addArgument(Argument.create(new IntegerArgumentType(), "number"));
	}});
}};
````


## Using Sub-Commands

Now that we have defined a sub-command, the user can use it like this:

````Bash
myCommand subCmd --number 123
````

> The user can also use the arguments of the parent command without any issues.

The developer can gather the parsed values like this:

````Java
MyCommand input = ArgumentParser.parseFromInto(MyCommand.class, args);

int number = input.subCmd.number; // 123
````
{switcher-key="Templates"}


````Java
int number = myCommand.parse(CLInput.from(args))
	.getResult() // get the results container
	.<Integer>get("SubCommand.number") // get the sub-command's argument value
	.get(); // unwrap the optional
````
{switcher-key="Traditional"}

<tip switcher-key="Traditional">

``ParseResult#get()`` also has an overload which allows you to specify the route to the argument as multiple strings;

```Java
result.<Integer>get("SubCommand", "number");
```

Or, you can use the ``ParseResult#getSubResult`` method to get the sub-command's ``ParseResult`` instance:

```Java
result.getSubResult("SubCommand").<Integer>get("number");
```

</tip>
