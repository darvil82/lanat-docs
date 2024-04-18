# Advanced creation of Commands

The aforementioned ``parseFromInto`` method in `ArgumentParser` has a pretty simple implementation. It abstracts
the usual method chain that you would have to write to:

- Initialize a command tree from a command template class.
- Parse the command line arguments.
- Put the received values into an instance of the command template class.

Here's that method chain:

````Java
void main(String[] args) {
	var result = ArgumentParser.from(MyCommand.class)
		.parse(CLInput.from(args))
		.into(MyCommand.class);
}
````

``ArgumentParser#from`` reads the command template class and creates a command tree from it (taking into account the
possible sub-commands defined).

In the case it is needed, it is possible to use the traditional way of creating a command tree from command templates.
Let's see how to do that.


## Command template constructor

``Command`` has a constructor overload that allows you to pass a command template class directly. When called,
it will create the new command instance based on the template.

> Note that by using this constructor, no sub-commands will be added automatically like how ``ArgumentParser#from``
> does.
> {style="warning"}

<procedure title="Example">
<step>

```Java
var cmd = new ArgumentParser(MyCommand.class) {{
	// adding a sub-command
	addCommand(new Command(MyCommand.MySubCommand.class));
	
	// adding an extra argument
	addArgument(Argument.create(new StringArgumentType(), "arg1"));
}};
```

For the parent command (`MyCommand.class`), the arguments present in it will be the ones defined in the template class,
and the single one added manually (`arg1` in this case).

``MySubCommand.class`` will be added as a sub-command of `MyCommand.class`.

</step>
</procedure>