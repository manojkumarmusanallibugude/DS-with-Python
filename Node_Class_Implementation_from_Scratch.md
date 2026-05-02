# Node Class – Implementation from Scratch

---

## Before We Begin — What Problem Are We Even Solving?

Imagine you go to a railway station. You see a train. Each coach of the train is connected to the next one. If you want to add a new coach, you just attach it. If you want to remove one, you detach it and reconnect the rest. The train doesn't need to be rebuilt from scratch just because you added or removed a coach.

Now think about how your computer stores data.

When you create a Python list:

```python
numbers = [10, 20, 30, 40]
```

Python stores all these values **side by side in memory** — like seats in a row. To find element at index 2, Python just jumps directly to it. Fast? Yes. Flexible? Not really.

**The problem with arrays (lists):**
- If you want to insert a value at the beginning, every existing element has to **shift** to make room.
- If memory in that block is full, Python has to **move the entire list** to a bigger space.
- You are at the mercy of *contiguous* (side-by-side) memory.

This is where the **Node** was born.

---

## The Core Idea — What Is a Node?

A **Node** is the most fundamental building block of non-contiguous data structures.

Think of a Node as a **box** that holds two things:
1. **Data** — the actual value you want to store (a number, a name, anything).
2. **A reference (pointer)** — the address of the *next* box.

That's it. That's all a Node is.

```
┌──────────┬──────────┐
│  Data    │  Next ──────────► (points to next node)
└──────────┴──────────┘
```

Instead of storing data side by side in memory, each node can live **anywhere in memory** and simply *points* to where the next one is. This is like a treasure hunt — each clue tells you where the next clue is.

---

## Why Does This Matter? The Real-World Motivation

Let's be honest — why should you care about a Node when Python lists already exist?

Here's why:

| Situation | Python List | Node-Based Structure |
|---|---|---|
| Insert at beginning | Slow — shift everything | Fast — just update a pointer |
| Delete from middle | Slow — shift everything | Fast — just redirect a pointer |
| Memory layout | Must be contiguous | Can be scattered anywhere |
| Size known upfront? | Preferred | Not needed |

Nodes are the reason the following exist:
- **Linked Lists** — the backbone of queue and stack implementations
- **Trees** — every node in a tree (like a file system folder structure) is literally a Node
- **Graphs** — social networks, maps, and the internet itself are graphs built on nodes
- **Hash Tables** — collision chains are linked lists of nodes

Every time you use `git`, browse a webpage, or undo an action in a text editor — nodes are working behind the scenes.

---

## Implementing a Node from Scratch

Let's now build it, step by step, understanding every line.

### Step 1 — The Simplest Possible Node

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
```

**Why `self.data`?**
This stores the actual value. It's the "cargo" the node is carrying.

**Why `self.next = None`?**
When a node is first created, it doesn't point to anything. `None` means "this is currently the last node" or "this node is alone." It's like a box with an empty address slot.

Let's create a node:

```python
node1 = Node(10)
print(node1.data)   # Output: 10
print(node1.next)   # Output: None
```

---

### Step 2 — Connecting Two Nodes

```python
node1 = Node(10)
node2 = Node(20)

node1.next = node2  # node1 now points to node2
```

Now in memory:

```
node1                node2
┌────┬────┐         ┌────┬────┐
│ 10 │  ──────────► │ 20 │None│
└────┴────┘         └────┴────┘
```

**Why is this powerful?**
`node1` and `node2` can be stored at completely different memory addresses. The `next` pointer bridges them logically, not physically.

---

### Step 3 — Connecting Three Nodes

```python
node1 = Node(10)
node2 = Node(20)
node3 = Node(30)

node1.next = node2
node2.next = node3
```

```
┌────┬──┐    ┌────┬──┐    ┌────┬────┐
│ 10 │ ─────► │ 20 │ ─────► │ 30 │None│
└────┴──┘    └────┴──┘    └────┴────┘
```

Now we have a chain. This chain **is** a linked list at its core.

---

### Step 4 — Traversing (Walking Through) the Chain

To visit each node, you start at the first one and follow the `next` pointer until you hit `None`.

```python
current = node1

while current is not None:
    print(current.data)
    current = current.next
```

**Output:**
```
10
20
30
```

**Why `while current is not None`?**
Because `None` is our signal that we've reached the end. Without this check, you'd get an error trying to access `.next` on `None`.

---

## Adding Useful Behaviour to the Node

A bare Node class works, but let's make it developer-friendly.

### Adding `__repr__` — Making Nodes Readable

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

    def __repr__(self):
        return f"Node({self.data})"
```

Now when you print a node:

```python
n = Node(42)
print(n)   # Output: Node(42)
```

Without `__repr__`, Python would print something like `<__main__.Node object at 0x7f3c...>` — completely unreadable.

---

## The Node in Context — Why Computer Science Needed This

In the early days of computing, memory was scarce and rigid. Programs had to declare exactly how much memory they needed upfront. If you guessed wrong — too bad.

The Node concept (originating from **linked list** research in the 1950s–60s, notably by Allen Newell, Herbert Simon, and Cliff Shaw while building the **IPL language** for AI programs) solved this by allowing **dynamic memory allocation** — grow the structure as you need it, shrink it when you don't.

This idea rippled outward:

- **Operating Systems** — process scheduling queues are linked lists of nodes
- **Browsers** — your browser history is a doubly linked list (back/forward)
- **Text Editors** — undo/redo history is a stack of nodes
- **Compilers** — Abstract Syntax Trees (AST) are trees made of nodes
- **Databases** — B-Trees (the index structure inside MySQL, PostgreSQL) are node-based trees
- **Networking** — packet routing tables in routers use graph structures built on nodes

Without the Node concept, none of these would work the way they do.

---

## Common Mistakes Beginners Make

### Mistake 1 — Confusing the Node with the Data

```python
node = Node(100)

# Wrong thinking:
print(node)         # This is the NODE OBJECT, not the value

# Correct:
print(node.data)    # This is the VALUE: 100
```

The Node is the **container**. The data is what's **inside** the container.

---

### Mistake 2 — Losing the Chain

```python
node1 = Node(10)
node2 = Node(20)
node1.next = node2

# DON'T do this carelessly:
node1 = Node(99)   # You just lost the reference to the original node1!
                   # node2 still exists in memory but you can't reach it
```

This is a **memory leak** in lower-level languages (C, C++). In Python, the garbage collector handles it, but logically your chain is broken.

---

### Mistake 3 — Infinite Loop While Traversing

```python
node1 = Node(10)
node2 = Node(20)
node1.next = node2
node2.next = node1  # ← CIRCULAR! node2 points BACK to node1

current = node1
while current is not None:   # This loop NEVER ends!
    print(current.data)
    current = current.next
```

This is why **circular linked lists** need special handling (a topic of their own).

---

## The Full Node Class — Clean and Complete

```python
class Node:
    def __init__(self, data):
        self.data = data   # The value stored in this node
        self.next = None   # Pointer to the next node (default: no connection)

    def __repr__(self):
        return f"Node({self.data})"


# --- Usage ---

# Create nodes
a = Node("Apple")
b = Node("Banana")
c = Node("Cherry")

# Connect them
a.next = b
b.next = c

# Traverse
current = a
while current is not None:
    print(current.data)
    current = current.next
```

**Output:**
```
Apple
Banana
Cherry
```

---

## What Comes After the Node?

The Node class on its own is not very useful to end users. It is a **primitive** — like a brick. You don't live in a brick. But you build a house with bricks.

Here's how Node becomes the foundation of everything:

```
Node
 │
 ├──► Singly Linked List   (each node has one next pointer)
 ├──► Doubly Linked List   (each node has next AND previous pointer)
 ├──► Circular Linked List (last node's next points back to first)
 │
 ├──► Stack   (linked list with push/pop at one end)
 ├──► Queue   (linked list with enqueue at back, dequeue at front)
 │
 ├──► Binary Tree  (each node has left and right child node)
 ├──► BST, AVL Tree, Heap  (specialised trees — all nodes)
 │
 └──► Graph  (each node has a list of neighbour nodes)
```

Every single one of these structures is, at its heart, just **nodes pointing to other nodes**.

---

## Summary

| Concept | What It Means |
|---|---|
| `self.data` | The value the node carries |
| `self.next = None` | No connection yet; this node is isolated |
| `node1.next = node2` | Chain two nodes together |
| Traversal | Follow `.next` pointers until you hit `None` |
| Why Node exists | To store data dynamically without needing contiguous memory |

The Node class is deliberately simple. Its simplicity is its strength. You are not just learning a class — you are learning the **atomic unit of dynamic data structures** that powers compilers, operating systems, databases, browsers, and almost every piece of non-trivial software ever written.

Once you truly understand what a Node is and *why* it exists, every data structure you study after this will feel like a natural extension of this one idea.

---

*Next Topic → Singly Linked List – Insertion, Deletion, Traversal*
