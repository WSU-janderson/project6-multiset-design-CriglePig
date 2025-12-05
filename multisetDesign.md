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
The insert operation increases the quantity of an item by `1` using its string key. If the key is found, the existing quantity is incremented. If the key is not found, a new node is created in the AVL tree with its quantity initialized to `1`. Adding a new node may require rebalancing if the insertion disrupts the AVL tree’s height balance.

#### Time Complexity
The time complexity of the insert operation is `O(log n)` because both searching for the key and any required rebalancing involve traversing the height of the AVL tree, which remains logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases for the insert operation include the triggered rebalancing, inserting many distinct items in rapid succession which increases tree height, and accumulating very large quantities over time which may approach the limits of a player's inventory.

#### Impact
The AVL structure supports the insert operation by maintaining a balanced height, allowing the tree to locate keys and determine whether to increment a quantity or create a new node in `O(log n)` time. Rebalancing ensures long-term performance but introduces additional rotation steps compared to simpler structures. Because the AVL relies on lexicographical ordering of item names, all insert behavior follows that comparison rule, which constrains how items are organized in the tree.

### Remove
...

### Set Quantity
...

# Conclusion
