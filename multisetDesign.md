# Project 6 - MultiSet Design Document

# Introduction

## Problem
I am designing a multiset to support a game inventory system. This design is inspired by Minecraft’s stacked-item inventory, where identical items are grouped together and tracked by their name and quantity.

## Solution
My design models a Minecraft inventory system built atop an AVL multiset. Each node in the AVL tree stores a unique item name as a `string` key and its total quantity as an `unsigned int` value.

# Discussion

## Design Philosophy
I am prioritizing Efficiency and Extensibility for Minecraft, its players, and its modding community. Efficiency in this context ensures that inventory operations such as adding, removing, and checking item quantities remains fast even as the number of distinct items grow. Extensibility allows new features, such as crafting, advanced inventory sorting, or other mechanics to be added without modifying the core AVL multiset structure.

AVL trees support efficiency because their height is always balanced, guaranteeing `O(log n)` time for insertions, deletions, and lookups regardless of inventory size. This ensures predictable and efficient performance for all core operations. Extensibility is supported by the ordered structure of the AVL tree which makes it easier to implement future features that rely on sorting, ranges, or set operations like inventory unions without changing the core data structure.

Although AVL trees involve internal balancing, I believe my design may also support simplicity in that each node represents a unique item key and its total quantity without getting into complex game mechanics, such as stack limits or item durability.

## Core Operations

### Insert
The `insert` operation increases the quantity of an item by `1` using its `string` key. If the key is found, the existing quantity is incremented. If the key is not found, a new node is created in the AVL tree with its quantity initialized to `1`. Adding a new node may require rebalancing if the insertion disrupts the AVL tree’s height balance. The `insert` operation can be used to add items being picked up, one by one.

#### Time Complexity
The time complexity of the `insert` operation is `O(log n)` because both searching for the key and any required rebalancing involve traversing the height of the AVL tree, which remains logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases for the `insert` operation include the triggered rebalancing, inserting many distinct items in rapid succession which increases tree height, and accumulating very large quantities over time which may approach the limits of a player's inventory.

#### Impact
The AVL structure supports the `insert` operation by maintaining a balanced height, allowing the tree to locate keys and determine whether to increment a quantity or create a new node in `O(log n)` time. Rebalancing ensures long-term performance but introduces additional rotation steps compared to simpler structures. Because the AVL relies on lexicographical ordering of item names, all `insert` behavior follows that comparison rule, which constrains how items are organized in the tree.

---

### Remove
The `remove` operation decreases the quantity of an existing item by `1` using its `string` key. If the key is found and the quantity is greater than `1`, the quantity is simply decremented. If the quantity reaches `0`, the node is removed entirely from the AVL tree. Removing a node may require rebalancing if the deletion disrupts the AVL tree’s height balance. This can be used in Minecraft to drop individual items or to simulate an item like potions or food being consumed.

#### Time Complexity
The time complexity of the `remove` operation is `O(log n)` because locating the key, performing the deletion, and carrying out any necessary rebalancing all involve traversing the height of the AVL tree, which remains logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases for the `remove` operation include attempting to remove an item that does not exist in the tree, removing the last remaining quantity of an item which requires deleting its node, and triggering AVL rebalancing when node removal alters subtree heights.

#### Impact
The AVL structure supports the `remove` operation by guaranteeing balanced height, allowing fast lookup and deletion in `O(log n)` time. Rebalancing maintains long-term efficiency but requires additional rotation steps when nodes are removed. Since AVL trees depend on lexicographical ordering, removal maintains this order even when nodes are deleted, ensuring consistent organization of the remaining items.

---

### Set Quantity
The `set quantity` operation assigns a specific quantity to an item using its `string` key. If the key is found, its stored quantity is replaced with the new value. If the new quantity is `0`, the node is removed from the AVL tree. If the key is not found and the new quantity is greater than `0`, a new node is created with that quantity. Creating or removing a node may require rebalancing if the update disrupts the AVL tree’s height balance. My vision for this operation in Minecraft involves using multiple items in crafting or dropping stacks of items.

#### Time Complexity
The time complexity of `set quantity` is `O(log n)` because locating the key, inserting or deleting a node when necessary, and any required rebalancing all traverse the AVL tree’s height, which is logarithmic relative to the number of nodes.

#### Edge Cases
Edge cases include setting the quantity of a non-existent item (should create a node if the quantity > 0), setting the quantity to `0` (which deletes the node), and assigning extremely large values that could exceed numeric limits.

#### Impact
The AVL structure supports `set quantity` by providing balanced, ordered storage so updates and existence checks run in `O(log n)` time. Creating or deleting nodes triggers the same rebalancing logic as insert/remove, preserving performance. Because keys are ordered lexicographically, `set quantity` behavior follows the same ordering constraints as other operations.

---

### Get Quantity
The `get quantity` operation retrieves the current quantity of an item using its `string` key. If the key exists in the AVL tree, the operation returns the stored quantity; if the key does not exist, it returns `0` to indicate that the item is not present in the inventory. This may be used in Minecraft to check the amount of an item a player has in their inventory for crafting.

#### Time Complexity
The time complexity of `get quantity` is `O(log n)` because the AVL tree allows searching for a key by traversing from the root down to a leaf in logarithmic time relative to the number of nodes.

#### Edge Cases
Edge cases include querying for a key that does not exist (should return `0`), or querying immediately after an insert or remove operation before rebalancing completes.

#### Impact
The AVL structure supports `get quantity` efficiently by maintaining a balanced height, allowing the operation to locate any key quickly. The lexicographical ordering of keys does not affect correctness but ensures that all lookups are consistent and predictable.

## Set Operations

### Union
The `union` operation combines two inventories by summing the quantities of each item. In gameplay, this could be used when a player loots all items from a chest with a hotkey ensuring that all items are accounted for without duplication of keys. Conceptually, the operation iterates over the items in the second inventory and inserts them into the first AVL multiset. If a key already exists, its quantity is incremented by the amount from the other inventory; otherwise, a new node is created. After each insertion, rebalancing may occur to maintain AVL tree balance.

#### Time Complexity
The operation has `O(n log m)` complexity, where `n` is the number of items in the second inventory and `m` is the number of items in the first, because each insertion involves a logarithmic AVL operation. 

#### Edge Cases
Edge cases include inventories with completely disjoint sets of items, inventories with extremely large quantities that could exceed storage limits, and empty inventories.

#### Impact
The AVL structure supports the `union` operation by maintaining fast lookups and insertions for each item in `O(log m)` time, allowing quantities from the second inventory to be combined efficiently. Rebalancing ensures that the combined inventory remains balanced, preserving predictable performance for future operations.

## Extension Feature

### Craft Recipe
The `craft recipe` operation allows the player to consume a set of ingredients from their inventory to create a new item. In gameplay, this models Minecraft’s crafting system: combining items like sticks and iron ingots to make a sword or combining wood planks to make a crafting table.

#### Design
This operation would take a recipe (a mapping of required item keys to quantities), the `string` key to the intended item to be crafted, and the quantity of items crafted. Next, it will check if the player’s inventory contains enough of each ingredient. If so, it decrements the quantities for each ingredient in the AVL multiset and adds the crafted item as a new node (or increments its quantity if it already exists). Rebalancing occurs as necessary after insertions or deletions.

#### Time Complexity
Checking and updating all ingredients requires `O(r log n)` time, where `r` is the number of items in the recipe and `n` is the number of distinct items in the inventory. Each lookup, decrement, or insertion is `O(log n)` due to AVL operations.

#### Edge Cases
Edge cases include insufficient ingredients to craft the recipe, recipes that create items already at maximum stack limits, and invalid recipes (nonexistent or malformed ingredient lists).

#### Impact
This feature directly integrates the multiset with core gameplay mechanics, making the inventory a dynamic tool for crafting. The AVL tree ensures predictable performance when checking ingredient quantities and updating the inventory after crafting.




# Conclusion
