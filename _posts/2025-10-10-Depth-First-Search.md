# Implementing Depth First Search

Decided to make a short and scrappy summary of implementing DFS to help cement the concept in my mind. 

The common boiler plate for DFS seems to consist of putting in a null check, traversing right and left, recursively calling the function, and adding to the root value. 

You can process the node's data in various ways. You can print it as well as process the node's value before, after, or between recursive calls.

Here's an example for calculating the max depth of a binary tree. It's a slight adaptation of the leetcode solution that I made for myself to make the concept clearer/more readable.

On a side note, most sites like leetcode start by defining a solution class but this is completely unnecessary for our implementation here.

Ok so we start by defining a function that takes in the root value of a tree node and -> int is telling python this will be an integer.

```
def maxDepth(root) -> int:
```

The we have our check to handle null values. If we have a null vale for our node, we will return 0. This isn't because 0 is a falsey value, it's cause we literally want to return that our tree depth = 0.

```
    if root is None:
        return 0
```

If our node is not null, we call the maxDepth function again, but this time for the left child of the current node. This finds the max depth of the left side fo the tree. Then we do the same thing for the right side of the tree. Afterwards, we compare the results of the two recursive calls and take the maximum of those two numbers. We add one to the max value. Next, we add one to the max value because we need to account for the node we are currently on as well. Lastly, we return this value up the tree to the node that called the recursive functions. 

```
    else:
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

This process repeats till we've gone through all nodes. Then the max depth of the tree will be returned from the original call to the maxDepth function.
