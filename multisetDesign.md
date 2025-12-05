# Project 6 - MultiSet Design Document

# Introduction

## Problem
I am designing a MultiSet to support a game inventory system. This design is inspired by Minecraft’s stacked-item inventory, where identical items are grouped together and tracked by their name and quantity.

## Solution
My design models a Minecraft inventory system built atop an AVL multiset. Each node in the AVL tree stores a unique item name as a `string` key and its total quantity as an `unsigned int` value.

# Discussion

## Design Philosophy
I am prioritizing Efficiency and Extensibility for Minecraft, its players, and its modding community. Efficiency in this context ensures that inventory operations such as adding, removing, and checking item quantities remains fast even as the number of distinct items grow. Extensibility allows new features, such as crafting, advanced inventory sorting, or other mechanics to be added without modifying the core AVL multiset structure.

AVL trees support efficiency because their height is always balanced, guaranteeing `O(log n)` time for insertions, deletions, and lookups regardless of inventory size. This ensures predictable and efficient performance for all core operations. Extensibility is supported by the ordered structure of the AVL tree which makes it easier to implement future features that rely on sorting, ranges, or set operations like inventory unions without changing the core data structure.

Although AVL trees involve internal balancing, I believe my design may also support simplicity in that each node represents a unique item key and its total quantity without getting into complex game mechanics, such as stack limits or item durability.

## Core Operations

### Insert
The insert operation increases the quantity of an item by `1` using its `string` key. If the key is found, the existing quantity is incremented. If the key is not found, a new node is created in the AVL tree with its quantity initialized to `1`. Adding a new node may require rebalancing if the insertion disrupts the AVL tree’s height balance.

#### Time Complexity
The time complexity of the insert operation is `O(log n)` because both searching for the key and any required rebalancing involve traversing the height of the AVL tree, which remains logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases for the insert operation include the triggered rebalancing, inserting many distinct items in rapid succession which increases tree height, and accumulating very large quantities over time which may approach the limits of a player's inventory.

#### Impact
The AVL structure supports the insert operation by maintaining a balanced height, allowing the tree to locate keys and determine whether to increment a quantity or create a new node in `O(log n)` time. Rebalancing ensures long-term performance but introduces additional rotation steps compared to simpler structures. Because the AVL relies on lexicographical ordering of item names, all insert behavior follows that comparison rule, which constrains how items are organized in the tree.

### Remove
The remove operation decreases the quantity of an existing item by `1` using its `string` key. If the key is found and the quantity is greater than `1`, the quantity is simply decremented. If the quantity reaches `0`, the node is removed entirely from the AVL tree. Removing a node may require rebalancing if the deletion disrupts the AVL tree’s height balance.

#### Time Complexity
The time complexity of the remove operation is `O(log n)` because locating the key, performing the deletion, and carrying out any necessary rebalancing all involve traversing the height of the AVL tree, which remains logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases for the remove operation include attempting to remove an item that does not exist in the tree, removing the last remaining quantity of an item which requires deleting its node, and triggering AVL rebalancing when node removal alters subtree heights.

#### Impact
The AVL structure supports the remove operation by guaranteeing balanced height, allowing fast lookup and deletion in `O(log n)` time. Rebalancing maintains long-term efficiency but requires additional rotation steps when nodes are removed. Since AVL trees depend on lexicographical ordering, removal maintains this order even when nodes are deleted, ensuring consistent organization of the remaining items.

### Set Quantity
The set quantity operation assigns a specific quantity to an item using its `string` key. If the key is found, its stored quantity is replaced with the new value. If the new quantity is `0`, the node is removed from the AVL tree. If the key is not found and the new quantity is greater than `0`, a new node is created with that quantity. Creating or removing a node may require rebalancing if the update disrupts the AVL tree’s height balance. My vision for this operation in Minecraft involves using multiple items in crafting or dropping stacks of items.

#### Time Complexity
The time complexity of set quantity is `O(log n)` because locating the key, inserting or deleting a node when necessary, and any required rebalancing all traverse the AVL tree’s height, which is logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases include setting the quantity of a non-existent item (should create a node if the quantity > 0), setting the quantity to `0` (which deletes the node), and assigning extremely large values that could exceed numeric limits.

#### Impact
The AVL structure supports set quantity by providing balanced, ordered storage so updates and existence checks run in `O(log n)` time. Creating or deleting nodes triggers the same rebalancing logic as insert/remove, preserving performance. Because keys are ordered lexicographically, set-quantity behavior follows the same ordering constraints as other operations.

# Conclusion
