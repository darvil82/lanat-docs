---
switcher-label: Command creation method
---

# Sub-commands

Commands can contain sub-commands. This allows you to separate different logical parts of your program, which can
have different arguments.

For example, a program like `git` has different sub-commands like `git commit`, `git push`, `git pull`, etc.
These three are all different commands, but they do belong to the same parent command, `git`.

Let's take a look at how we can define sub-commands in Lanat.


<chapter switcher-key="Templates" title="Defining Sub-Commands">

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

We need to add another property to the parent command and annotate it with [`CommandAccesor`](Command-templates.md#the-commandaccessor-annotation)
which will hold an instance of the sub-command class once it is parsed:

````Java
@Command.Define
class MyCommand extends CommandTemplate {
	@CommandAccesor
    public SubCommand subCommand; // to access the subCommand upon instantiation
	
	@Command.Define
	static class SubCommand extends CommandTemplate {
		@Argument.Define
		public int number; // we also added an argument to the sub-command
	}
}
````

</chapter>


<chapter switcher-key="Traditional" title="Defining Sub-Commands">

You can add sub-commands to a command by using the ``Command#addCommand`` method:

````Java
var myCommand = new ArgumentParser("MyCommand") {{
	addCommand(new Command("SubCommand") {{
		// we also added an argument to the sub-command
		addArgument(Argument.create(new IntegerArgumentType(), "number"));
	}});
}};
````

</chapter>

> The root command instance shall always be of type ``ArgumentParser``, which contains a few more options
> specific to the root command.
> {style="note"}