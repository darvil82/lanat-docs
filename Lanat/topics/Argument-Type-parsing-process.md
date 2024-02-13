# Understanding the parsing process

Let's take a look at how the parsing process works in Lanat with argument types.

Here's a regular command structure with 4 arguments:

![structure.png](structure.png)


## Parsing pipeline

### Initial values reception

Once the input is tokenized, the parsing process begins. Lanat goes through the input and forwards the values specified
on each argument to the proper sub-parsers, which are the argument types.

An argument may appear more than once in the input, and each time it appears, the argument type will receive the values.

![pipeline1.png](pipeline1.png)

The "sanitized" values that you can see between the Argument and Argument Type nodes are the values that the argument
type instance will receive in the parameter of its `parseValues` method.