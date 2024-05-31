# State

Each element in a command hierarchy, when parsing begins, has a state, which can be altered
while the parsing process is ongoing.

Lanat allows you to parse a command hierarchy more than once. After the first parse, all states
are reset to their initial values so that the next parse can be done without any interference from
the previous one.

> To prevent unnecessary overhead, the states are reset only when a new second
> parse is about to start, rather than after the parsing process is complete.
> {style="note"}

```Java
var myCommand = ArgumentParser.from(MyCmd.class);
var parsed1 = myCommand.parse(CLInput.args(args)); // parse
var parsed2 = myCommand.parse(CLInput.args(args)); // reset -> parse
var parsed3 = myCommand.parse(CLInput.args(args)); // reset -> parse
```

## Resetting states on custom elements

Even if parsing multiple times is not common, we should always ensure that states are
properly reset.

Elements that have the ability to reset their states (such as argument types)
implement the `Resettable` interface.
This interface has a single method, `resetState()`, which resets the state of the element.

<procedure title="Example">
<step>

Let's take a look at a simple example of a custom [argument type](Argument-types.md).

```Java
class MyType implements ArgumentType<String> {
    private int times = 0;

    @Override
	public String parseValues(String[] args) {
        return args[0] + " " + ++times;
    }
    
    @Override
    public Range getUsageCountBounds() {
        return Range.AT_LEAST_ONE;
    }
}
```

</step>

<step>

An issue arises when we parse the command multiple times. The `times` field will not be reset
to its initial value, which is zero. In order to fix this, we need to implement our own `resetState()`:

```Java
@Override
public void resetState() {
    super.resetState();
    this.times = 0;
}
```

</step>
</procedure>

> Argument types already automatically reset things like possibly registered sub-types.
> {style="note"}