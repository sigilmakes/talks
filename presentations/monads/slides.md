---
title: What the hell is a Monad?
info: |
  Code Club Talk — Willow Sparks
  2026-04-01

theme: seriph
themeConfig:
  primary: '#d4639a'
  secondary: '#9b72cf'
  background: '#1a1020'

drawings:
  persist: false

class: text-center
transition: fade-out
mdc: true
layout: cover
hideInToc: true

download: true
---

# What the hell is a Monad?

Willow Sparks · Code Club · 1 April 2026

<div class="mt-8 text-lg opacity-50">

*she/her*

</div>

---
layout: center
hideInToc: true
---

# A monad is just a monoid in the category of endofunctors, what's the problem?

<v-click>

<div class="mt-8 text-2xl" style="color: #d4639a">

...right. Let's start over.

</div>

</v-click>

---
layout: center
---

<Toc minDepth="1" maxDepth="1" />

---

# What Are We Even Talking About?

Monads are one of the most powerful tools in functional programming.

They're also one of the most *infamously* misunderstood.

<v-click>

<div class="mt-4">

### The reputation:
- Scary category theory
- Impenetrable Haskell tutorials
- "You'll understand monads once you've already understood monads"

</div>

</v-click>

<v-click>

<div class="mt-4" style="color: #d4639a">

### The reality:
**A monad is a box you can put things in.**

That's it. That's the starting point.

</div>

</v-click>

---

# Boxes We Already Know: Classes

You already use boxes every day. Consider a Python class:

<v-click>

```python
class Box:
    def __init__(self, value):
        self.value = value       # put a thing in the box

    def map(self, func):
        return Box(func(self.value))  # do something to the thing inside
```

</v-click>

<v-click>

```python
box = Box(5)                      # wrap 5 in a box
box.map(lambda x: x * 2)         # Box(10)
box.map(lambda x: x + 1)         # Box(6)
```

</v-click>

<v-click>

<div class="mt-4" style="color: #9b72cf">

A class wraps data and gives you methods to work with it.

A monad does the same thing — but with *rules* about how the wrapping and unwrapping works.

</div>

</v-click>

---

# The Null Problem

Things go wrong. Values might be missing. And we cope... badly.

<v-click>

```python
def get_user(id):        # might return None
def get_address(user):   # might return None
def get_zipcode(addr):   # might return None
```

</v-click>

<v-click>

```python
# The pyramid of doom
user = get_user(42)
if user is not None:
    addr = get_address(user)
    if addr is not None:
        zip = get_zipcode(addr)
        if zip is not None:
            print(zip)
```

</v-click>

<v-click>

<div class="mt-2" style="color: #d4639a">

Every. Single. Step. Needs. A. Check.

What if the box handled this for us?

</div>

</v-click>

---

# Maybe: A Box That Might Be Empty

<v-click>

```haskell
data Maybe a = Nothing | Just a
```

</v-click>

<v-click>

Two states:
- `Just 42` — there's something in the box
- `Nothing` — the box is empty

</v-click>

<v-click>

```haskell
getUser 42           -- Just someUser    (found one!)
getUser 999          -- Nothing          (doesn't exist)
```

</v-click>

<v-click>

<div class="mt-4" style="color: #9b72cf">

This is just a class with two variants. Like an `Optional` in Java, or `Option` in Rust.

Nothing scary yet.

</div>

</v-click>

---

# Making the Box Chainable

The magic of a monad is **bind** — a way to chain operations on boxes.

<v-click>

```haskell
(>>=) :: Maybe a -> (a -> Maybe b) -> Maybe b

Nothing  >>= f  = Nothing      -- box is empty? skip it
(Just x) >>= f  = f x          -- box has something? unwrap, apply, get new box
```

</v-click>

<v-click>

```haskell
-- Our nested null-check nightmare becomes:
getUser 42 >>= getAddress >>= getZipcode
```

</v-click>

<v-click>

<div class="mt-4" style="color: #d4639a">

✨ If any step returns `Nothing`, the whole chain stops. No nested ifs. No crashes. ✨

</div>

</v-click>

<v-click>

<div class="mt-2 text-sm opacity-60">

Compare: the class `Box.map()` returned a `Box`. Bind is the same idea, but the function itself decides what kind of box comes out.

</div>

</v-click>

---

# So What Actually Makes It a Monad?

Three ingredients:

<v-clicks>

1. **A box type** — wraps a value
   - `Maybe`, `Result`, `List`, `Promise`...

2. **A way to put things in** — usually called `return` or `unit` or `pure`
   - `Just 42`, `Ok(value)`, `Promise.resolve(x)`

3. **A way to chain** — `bind`, `>>=`, `flatMap`, `.then()`
   - Takes a box and a function → produces a new box
   - *Handles the plumbing between steps*

</v-clicks>

<v-click>

<div class="mt-4" style="color: #d4639a">

That's it. A box + a way in + a way to chain. That's a monad.

</div>

</v-click>

---

# Parallels with Classes

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

### A class gives you:
- A wrapper for data (`__init__`)
- Methods that operate on that data
- Encapsulation — hide the details

</div>

<div>

### A monad gives you:
- A wrapper for data (`return`)
- A chaining operation (`bind`)
- Encapsulation — *hide the plumbing*

</div>

</div>

<v-click>

<div class="mt-8 text-center">

| | **Class** | **Monad** |
|---|---|---|
| Wrapping | `Box(value)` | `return value` |
| Operating | `box.method()` | `box >>= func` |
| Hidden work | Internal state | Null checks, errors, async... |

</div>

</v-click>

<v-click>

<div class="mt-4 text-center" style="color: #9b72cf">

Classes hide *state*. Monads hide *control flow*.

</div>

</v-click>

---

# Monads You Already Use

<v-clicks>

| Monad | The Box | What's Hidden |
|-------|---------|--------------|
| **Maybe / Optional** | Value that might be absent | Null checking |
| **Result / Either** | Value that might be an error | Error propagation |
| **List** | Multiple possible values | Iteration |
| **Promise / Future** | Value that isn't here yet | Async scheduling |
| **IO** | A side effect waiting to happen | Purity tracking |

</v-clicks>

<v-click>

<div class="mt-4" style="color: #d4639a">

If you've ever written `.then()` on a Promise — congratulations, you've used a monad.

</div>

</v-click>

---
layout: two-cols-header
---

# Promise: A Monad You Definitely Know

::left::

```javascript
// .then() IS bind
fetch('/api/user')
  .then(res => res.json())
  .then(user => fetch(user.url))
  .then(res => res.json())
```

<v-click>

```javascript
// async/await is just sugar for it
async function getUser() {
  const res = await fetch('/api/user')
  const user = await res.json()
  const profile = await fetch(user.url)
  return profile.json()
}
```

</v-click>

::right::

<v-click>

<div class="ml-4 mt-4">

### The box: `Promise`
### Put things in: `Promise.resolve(x)`
### Chain: `.then(fn)`

<div class="mt-4" style="color: #9b72cf">

`async/await` is literally Haskell's `do`-notation reinvented for JavaScript.

The monad hides all the async scheduling so you can write code that *looks* synchronous.

</div>

</div>

</v-click>

---

# The Monad Laws

<div class="text-sm opacity-70 mb-4">

(Yes, there are rules. Only three. They're not that bad.)

</div>

<v-clicks>

1. **Left identity** — putting something in a box then chaining is the same as just calling the function
   - `return a >>= f` ≡ `f a`

2. **Right identity** — chaining with "just put it back in the box" does nothing
   - `m >>= return` ≡ `m`

3. **Associativity** — it doesn't matter how you group the chains
   - `(m >>= f) >>= g` ≡ `m >>= (\x -> f x >>= g)`

</v-clicks>

<v-click>

<div class="mt-4" style="color: #d4639a">

These guarantee your chains compose predictably. No weird order-dependent surprises.

Think of them like how `+` is associative: `(1+2)+3 = 1+(2+3)`. Same energy.

</div>

</v-click>

---
layout: center
---

# The Big Idea

<v-click>

<div class="text-2xl">

Monads let you **separate** *what* you're computing
<br/>from *how* the plumbing works.

</div>

</v-click>

<v-click>

<div class="mt-8 grid grid-cols-3 gap-4 text-center">

<div class="p-4 rounded" style="background: rgba(212, 99, 154, 0.15)">

**Maybe**
<br/>handles nulls

</div>

<div class="p-4 rounded" style="background: rgba(155, 114, 207, 0.15)">

**Result**
<br/>handles errors

</div>

<div class="p-4 rounded" style="background: rgba(212, 99, 154, 0.15)">

**Promise**
<br/>handles async

</div>

</div>

</v-click>

<v-click>

<div class="mt-8 text-lg opacity-70">

Same pattern. Different plumbing. You write the logic; the monad handles the rest.

</div>

</v-click>

---

# Mathematical Beauty or Fundamental Revulsion?

<v-clicks>

- Haskell formalised monads using **category theory** — a branch of pure maths
- The concepts are simple; the *vocabulary* is terrifying
- "Functor", "applicative", "endofunctor", "natural transformation"...
- These are just precise names for things you already do

</v-clicks>

<v-click>

<div class="mt-4 text-center">

| Scary Word | What It Actually Means |
|---|---|
| **Functor** | A box you can `map` over |
| **Applicative** | A box where you can also apply wrapped functions |
| **Monad** | A box you can chain (with `bind`) |
| **Endofunctor** | A mapping from one category to itself (please don't worry about this) |

</div>

</v-click>

<v-click>

<div class="mt-4 text-center" style="color: #d4639a">

Whether this is beautiful or revolting is a matter of taste. Either way, you now know what a monad is.

</div>

</v-click>

---

# Further Reading

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

### Approachable
- **"Functors, Applicatives, And Monads In Pictures"** — adit.io
- **"Railway Oriented Programming"** — Scott Wlaschin
- **"Learn You a Haskell"** — Miran Lipovača *(free online)*

</div>

<div>

### Going Deeper
- **"Monads for functional programming"** — Philip Wadler (1995)
- **Rust's `Result` and `Option` docs** — practical monads
- **"All About Monads"** — Haskell Wiki

</div>

</div>

---
layout: statement
hideInToc: true
---

# A monad is just a box you can put things in

<div class="mt-4 text-lg opacity-50">

(and chain operations on, in a lawful way, but you get it now)

</div>

---
hideInToc: true
---

# Thank you!

<div class="mt-8 text-center">

<div class="text-xl mb-4" style="color: #d4639a">

Questions?

</div>

<div class="opacity-60">

*"The purpose of abstraction is not to be vague, but to create a new semantic level in which one can be absolutely precise."*
<br/>— Edsger Dijkstra

</div>

</div>
