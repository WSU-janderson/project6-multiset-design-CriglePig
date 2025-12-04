# Project 6 — MultiSet Design Document

## Introduction

### Problem
I am designing a MultiSet to support a game inventory system. This design is inspired by Minecraft’s stacked-item inventory, where identical items are grouped together and tracked by their name and quantity.

### Solution
My design models a Minecraft inventory system using an AVL multiset. Each node in the AVL tree stores a unique item name as a `string` key and its total quantity as an `unsigned int` value.

## Discussion


## Conclusion
