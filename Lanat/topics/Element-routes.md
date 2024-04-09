# Element routes

As previously seen, description tags can receive a route to an element as a value.
This route is a string that represents the path to the element in the command structure.


## Syntax

The syntax for a route is very simple; it is a dot-separated list of names indicating the path to the object to be
referenced.

For example, consider the following command structure:

```Java
@Command.Define
class MyCommand extends CommandTemplate {
	@Argument.Define
	public String arg1;

	@Argument.Define
	public String arg2;
}
```

In order to reach the ``arg2`` argument from the `MyCommand` command, the route would be `args.arg2`.

When writing a route, the names of the elements available depend on the _current target_ in the route.
This is similar to accessing properties in objects. For instance, if the current target is a command, you can access
the ``args`` path to reach an argument, just like with groups, for example.

> By default, the initial target is the command the element belongs to. If ``!`` is the first element in the route, then the
> element that requested to generate the route will be the initial target instead.

> If the route is empty, the current target is the command the element belongs to.


## Selectable objects

{type="narrow"}
args
: The arguments the current target contains. The next element in the route must be the name of one of these arguments.

groups
: The groups the current target contains. The next element in the route must be the name of one of these groups.

cmds
: The sub-commands the current target contains. The next element in the route must be the name of one of these sub-commands.

type
: The [argument type](Argument-Types.md) of the current target (only available when the target is an argument).


## Examples

1. Select the [type](Argument-Types.md) of the argument `myArg1` of the current command: ``args.myArg1.type``
2. Select the Sub-Command ``myCmd`` of the current command: ``cmds.myCmd``
3. Select the [type](Argument-Types.md) of the argument ``myArg1`` of the Sub-Command `myCmd` in the current command: ``cmds.myCmd.args.myArg1.type``
4. Select the [type](Argument-Types.md) of the argument that is requesting to parse this description by using the `!` prefix: `!.type`