They say that operators in both prefix and postfix evaluate from left to right, so you'd think that they would both work in a queue. [http://www.cs.man.ac.uk/~pjj/cs212/fix.html#L2R](http://www.cs.man.ac.uk/~pjj/cs212/fix.html#L2R)

But there's this stupid footnote in the article that says,

> Although Prefix "operators are evaluated left-to-right", they use values to their right, and if these values themselves involve computations then this changes the order that the operators have to be evaluated in. In the example above, although the division is the first operator on the left, it acts on the result of the multiplication, and so the multiplication has to happen before the division (and similarly the addition has to happen before the multiplication).
> Because Postfix operators use values to their left, any values involving computations will already have been calculated as we go left-to-right, and so the order of evaluation of the operators is not disrupted in the same way as in Prefix expressions. 

Any values involving computations you say?

Let's take this thing, `(1-2)+3` (or `1 2 - 3 +` in postfix) and turn it into prefix.

It becomes `+ 3 - 1 2`. And you know what's peculiar about that? It goes right to left.

But here's the kicker, if you want to do anything in a certain order in prefix, you have to do this. So in no cases that I've seen does prefix evaluate left to right.

Even in their sample expression `/ * A + B C D`, all operators get evaluated from left to right.

So I'm going to make some general assumptions

1. In postfix, **all** operators get evaluated left to right.
2. In prefix, **all** operators get evaluated from right to left.
3. In infix, operators can and/or have to **only** take a single value from their left.

If I make these generalizations, it would also be safe to assume prefix expressions always start with an operator and postfix expressions always start with a variable. However, I'm trying to see if I can tie all notations together, maybe even have an expression with multiple notations.

There's a lot of non issues with this one, like,

> What if I want an operator to be infix but there's a couple operators before it?

So what I assume is meant is like `4 5 - 7 /` and you mean `4/(5-7)`.

It's really strange because you can always shift the operators around for it to work, so it would become `/ 4 - 5 7`.

I know that's not infix anymore, but you can also do `5 - 7 / 4 ^ -1` since you like infix so much.

Basically it's like science signifigant figures. You think it isn't always satisfyable, but then there's some new trick like scientific notation that solves that.

## Easiest way to prove a theory is to disprove it.

In that sample question on that website `* A + B C` would get evaluated differently than both postfix and infix because `A + B` would always get called.

But we can turn that problem into our solution. That multiply operator gets greedy and takes the first variable made available to it. In any other notation, that doesn't work, but because it didn't have anything before it, we can assume that it's a prefix operator.

This leads to some mental gymnastics prepostinfix notation generalizations

1. If an operator gets inputted and everything it needs is already in the buffer, it is a postfix operator.
2. If an operator gets inputted and there is variables before it, but not enough to satisfy the operator, then it is an infix operator and it absorbs only the last variable in the buffer.
3. If an operator gets inputted and there is absolutely nothing, it is a postfix operator
4. There is really no difference between a postfix and a prefix operator(This was the idea that got me to start this project).

And here's how this would be done

* Make a buffer for the variables. When I say buffer, I mean easy access add and remove anywhere, and bidirectional. So a doubly linked list.
* Make a stack of operators objects.
* Operators have their own variable queues.
* Before a variable gets pushed to the buffer, the operator on top of the stack gets dibs. If that operator is satisfied, evaluate it then push the variable like you would an input.
* If an operator is postfix, slice the variables off the top of the buffer and evaluate them in order. Doesn't matter if they get put in the operator variable queue first.
* If an operator is infix, pop the last variable off the stack and into the operator's variable queue. This is fine because if there were any operators before it, they would have taken that variable. Then push it to the operator stack.
* If an operator is prefix, just push it to the operator stack.

Now that I am implementing it, I realized that I can just feed the infix and postfix operators the same because they are functionally the same and for the reason I listed above.

It works perfectly except for these downfalls

1. There's no precedence, just right to left except for prefix and postfix operators having to wait for computed values.
2. This can't be typed. I could try to store the typed variables by making a different buffer for each type of variable, but I couldn't promise that it would work like it should.
3. I don't think there will be gauranteed backwards compatibility with the other notations.
