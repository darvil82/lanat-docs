---
switcher-label: Command creation method
---

# Argument Groups

[Commands](Commands.md) can contain argument groups. Groups allow you to organize the arguments in the command in a more
structured way so that the user can understand the purpose of each argument better.

Groups are mostly useful for the help message generation, where they can be seen visually affecting the representation of
how arguments are displayed.

![Group help representation](groups_help.png)


<chapter title="Creating a group and adding Arguments" switcher-key="Traditional">

Wrap the arguments you want to group in an ``Group``:

````Java
var cmd = new ArgumentParser("MyProgram") {{
	addGroup(new Group("stuff") {{
		addArgument(Argument.create(new StringArgumentType(), "arg1"));
		addArgument(Argument.create(new StringArgumentType(), "arg2"));
	}});
}}
````

In this case the arguments are added before the group is added to the command
_(Group instantiation > Arguments added to group > Group added to command)_.
However, this doesn't mean that the arguments have to be added before the group is added to the command. This is also
allowed:

````Java
var cmd = new ArgumentParser("MyProgram") {{
	var group = new Group("stuff");
	addGroup(group);
	group.addArgument(Argument.create(new StringArgumentType(), "arg1"));
	group.addArgument(Argument.create(new StringArgumentType(), "arg2"));
}}
````

> Lanat will automatically add the arguments added to the group to the command, so you don't have to worry about that.
> {style="note"}

</chapter>


<chapter title="Creating a group" switcher-key="Templates">

Use the [``group()``](Command-Templates.md#setting-a-group) attribute in the
[``@Argument.Define``](Command-Templates.md#the-argument-define-annotation) annotation to specify
the group that an argument belongs to.

<include from="Command-Templates.md" element-id="define-annotation_group"></include>

</chapter>


## Group hierarchy

Just like [commands](Commands.md), groups can be nested inside other groups. This allows you to create a hierarchy of groups:

````Java
var cmd = new ArgumentParser("MyProgram") {{
	addGroup(new Group("stuff") {{
		addArgument(Argument.create(new StringArgumentType(), "arg1"));
		addArgument(Argument.create(new StringArgumentType(), "arg2"));
		
		addGroup(new Group("more-stuff") {{
			addArgument(Argument.create(new StringArgumentType(), "another"));
		}});
	}});
}}
````

<tip>

For command templates, you will need to use the [``afterInit()``](Command-Templates.md#the-afterinit-method)
method to create nested groups.

For example:
```Java
@InitDef
public static void afterInit(Command cmd) {
	cmd.addGroup(new Group("parent-group") {{
		addGroup(cmd.getGroup("child-group"));
	}})
}
```

(In this case it is assumed that `child-group` was created using [`group()`](Command-Templates.md#setting-a-group))

</tip>



## Restrictive groups

Groups can be set to be restrictive. This means that only a single argument inside that group (or any of its sub-groups)
can be used at a time. This is useful for commands that have mutually exclusive arguments.

To make a group restrictive, set the ``restricted`` property of the group to ``true``:

````Java
var cmd = new ArgumentParser("MyProgram") {{
	addGroup(new Group("stuff") {{
		setRestricted(true);
		addArgument(Argument.create(new StringArgumentType(), "arg1"));
		addArgument(Argument.create(new StringArgumentType(), "arg2"));
	}});
}}
````
{switcher-key="Traditional"}

```Java
@InitDef
public static void afterInit(Command cmd) {
	cmd.getGroup("stuff").setRestricted(true);
}
```
{switcher-key="Templates"}


### Example

Given the following group tree **(lime colored groups are restrictive)**:

![Group hierarchy](groups_diagram.png)

* If `Argument 1` is used, then none of the arguments in the child groups can be used, because `Group 1` is restricted.
* If `Argument 3.1` is used, then none of the arguments in the rest of the tree can be used, because both
  `Group 3` and its parent `Group 1` are restricted.
* If `Argument 2.1` is used, `Argument 2.2` can still be used, because `Group 2` is not restricted. No other arguments
  in the tree can be used though.