# A Beginner's Crash Course to Lua

<div align="center">
  <img src="./assets/lua_banner.png" alt="Project Banner" width="100%" style="max-width: 900px; border-radius: 8px;">
</div>

<br/>

## 👋 What is Lua?

Welcome to Lua! Lua is a powerful but simple programming language. The name "Lua" (pronounced **LOO-ah**) means "Moon" in Portuguese, which is fitting because it's light and fast.

It was designed to be easy to learn and use. You'll find Lua used in many popular applications, especially for scripting:

- **Game Development:** Famous games like **Roblox**, **World of Warcraft**, and **Angry Birds** use Lua for scripting game events, character actions, and user interfaces.
- **Application Scripting:** Tools like the **Neovim** text editor and the **Redis** database use Lua to let users write custom plugins and scripts.
- **Embedded Systems:** Because it's so lightweight, it's great for small devices and systems.

Think of it as a friendly, powerful tool for adding simple logic and behavior to bigger programs.

## 1. Getting Started

Before we can write code, we need to install the Lua "interpreter." This is a program that reads your Lua code and runs it.

### 💻 How to Install Lua

The best way to install Lua depends on your operating system.

#### For Windows

1. **Install Lua using Chocolatey:**

```powershell
choco install lua -y
```

2. **Verify the installation:**

Close and reopen your terminal, then run:

```powershell
lua -v
```

You should see something like `Lua 5.4.x` — confirming that Lua is installed and available in your system PATH automatically.

#### For macOS (using Homebrew)

If you don't have the Homebrew package manager, you can install it from [brew.sh](https://brew.sh/).

1. Open the **Terminal** app.
2. Run the command: `brew install lua`
3. **Verify:** In the same terminal, type `lua -v`.

#### For Linux (Ubuntu/Debian)

1. Open your **Terminal**.
2. Run the commands:

```bash
sudo apt update
sudo apt install lua5.4
```

(You may need to use `lua5.3` depending on your version of Ubuntu). 3. **Verify:** In the same terminal, type `lua -v`.

## 2. Your First Program (Hello, World!)

The traditional first program in any language is to print "Hello, World!" to the screen.

1. Create a new file named `hello.lua` using any plain text editor (like Notepad, VS Code, or TextEdit in "plain text" mode).
2. Type the following line into the file:

```lua
print("Hello, World!")
```

3. Save the file.
4. Open your terminal or command prompt and navigate to the folder where you saved `hello.lua`.
5. Run your program by typing:

```bash
lua hello.lua
```

You should see `Hello, World!` printed to your screen. The `print()` function is your main tool for seeing what's happening in your code.

## 3. Adding Comments

Comments are notes for humans that the computer will ignore. They are essential for explaining what your code does.

```lua
-- This is a single-line comment. Anything after the -- is ignored.

--[[
This is a
multi-line comment.
Everything between the [[ and ]] is ignored.
]]
```

## 4. Variables & Data Types

A **variable** is like a labeled box where you can store a piece of information. You give it a name and put a value inside it.

In Lua, you create variables using the `local` keyword. **You should _always_ use** `local` —it keeps your variables organized and prevents bugs.

```lua
local name = "Adi"          -- Stores a piece of text
local age = 25              -- Stores a number
local isActive = true       -- Stores a true/false value
local nothing = nil         -- Stores "nothing"
```

**Key rules for variables:**

- `local` **is critical:** Using `local` makes the variable "local" to its section of code. Without it, you create a "global" variable, which is considered bad practice.
- **Mutable:** Variables in Lua can change. You can assign a new value to an existing variable.
  ```lua
  local score = 100
  score = 150  -- This is perfectly fine
  ```
- `nil` **for "nothing":** Lua has one value for "nothing" or "empty": **`nil`**. It's used to represent the absence of a value.
- **No semicolons:** You don't need semicolons (`;`) at the end of your lines.

## 5. Data Types

Every value in Lua has a "type." Here are the most important ones:

- **string:** For text. You create strings with double quotes (`"Hello"`) or single quotes (`'Hello'`).
- **number:** For numbers. Lua doesn't separate integers (like `10`) and floats (like `10.5`). They are all just "numbers."
- **boolean:** For `true` or `false` values. Used for making decisions.
- **nil:** A special type that only has one value, `nil`. It represents "nothing."
- **function:** A block of reusable code (we'll cover this soon!).
- **table:** The most important data type in Lua! It's a "do-it-all" structure used for lists, dictionaries, and more.

## 6. Strings (Working with Text)

Strings are for holding text. Here's how to work with them.

```lua
local name = "Adi"

-- To join strings, Lua uses two dots (..)
local greeting = "Hello, " .. name .. "!"
print(greeting) -- Prints: Hello, Adi!

-- To get the length of a string, use the # operator
local length = #name
print(length) -- Prints: 3

-- For multi-line strings, use double square brackets [[ ]]
local multi = [[
This is a
long string
on multiple lines.
]]
```

### 🧵 Common String Functions

Lua has a built-in `string` library for more advanced operations.

```lua
string.upper("hello")      -- "HELLO"
string.lower("HELLO")      -- "hello"

-- Substring: (string, start_index, end_index)
-- Note: Strings are 1-indexed in Lua! 'h' is at 1, 'e' is at 2.
string.sub("hello", 2, 4)  -- "ell"

-- Find (returns the start and end index)
string.find("hello", "ll") -- 3, 4

-- Global substitute (replace "l" with "x")
string.gsub("hello", "l", "x") -- "hexxo"

string.len("hello")        -- 5 (this is the same as #"hello")
```

## 7. Tables (The All-in-One Data Structure)

**Tables are the most important concept in Lua.** They are the only built-in data structure, and they're used to create everything from simple lists to complex objects.

Think of a table as a smart container.

### Using Tables as Lists (Arrays)

A list is an ordered collection of items.

```lua
-- A table used as a list (array)
local items = {"sword", "shield", "potion"}

-- GETTING VALUES:
-- Lua is 1-INDEXED! The first item is at 1.
print(items[1])  -- "sword"
print(items[2])  -- "shield"
print(items[0])  -- nil (there is no item at 0)

-- GETTING LENGTH:
-- The # operator gets the length of a list-like table
print(#items)    -- 3

-- ADDING VALUES:
-- table.insert adds an item to the end
table.insert(items, "armor")
print(items[4])  -- "armor"
```

> **Warning: Lua is 1-Indexed!**
> This is the most common trip-up for people coming from other languages. The first element in a list is at index `[1]`, not `[0]`.

### Using Tables as Dictionaries (Key-Value Pairs)

You can also use tables to store values with custom string keys, like a dictionary or an "object."

```lua
-- A table used as a dictionary
local player = {
   name = "Adi",    -- Use = instead of :
   level = 10,
   isAlive = true
}

-- GETTING VALUES:
-- You can use dot notation
print(player.name)    -- "Adi"

-- Or bracket notation (useful if the key has spaces)
print(player["level"])  -- 10

-- ADDING/UPDATING VALUES:
-- Just assign a value to a new key
player.health = 100
print(player.health)  -- 100
```

### 🧰 Common Table Functions

Lua provides a `table` library to help you manage tables (mostly list-style ones).

```lua
local arr = {10, 20, 30}

-- Insert 40 at the end
table.insert(arr, 40)        -- arr is now {10, 20, 30, 40}

-- Insert 15 at index 2
table.insert(arr, 2, 15)     -- arr is now {10, 15, 20, 30, 40}

-- Remove the item at index 3
table.remove(arr, 3)         -- arr is now {10, 15, 30, 40}

-- Sorts the table in place
table.sort(arr)              -- arr is now {10, 15, 30, 40}

-- Join all items with a separator
local str = table.concat(arr, ", ")
print(str)                   -- "10, 15, 30, 40"
```

## 8. Functions (Reusable Code)

Functions are named blocks of code that you can "call" (run) whenever you need them. They are the main way to organize your code.

```lua
-- Define a function
function greet(name)
   return "Hello, " .. name .. "!"
end

-- Call the function
local message = greet("Adi")
print(message) -- "Hello, Adi!"
```

### Storing Functions in Variables

You can also create an "anonymous function" and store it in a variable. This is just a different way of doing the same thing.

```lua
local greet2 = function(name)
   return "Hello, " .. name .. "!"
end

print(greet2("Bob")) -- "Hello, Bob!"
```

### Functions with Multiple Arguments

You can accept a variable number of arguments using `...` (three dots).

```lua
function sum(...)
   -- {...} collects all arguments into a table
   local args = {...}
   local total = 0
   -- We loop through the args table (see Loops section)
   for i = 1, #args do
      total = total + args[i]
   end
   return total
end

print(sum(1, 2, 3, 4))  -- 10
```

### Multiple Return Values

A special feature of Lua is that functions can return **multiple values**.

```lua
function getDimensions()
   -- Return two values, separated by a comma
   return 100, 200
end

-- We can "catch" both values in local variables
local width, height = getDimensions()
print(width, height)  -- 100    200
```

## 9. Making Decisions (Conditionals)

To make your code do different things based on different situations, you use `if` statements.

```lua
if x > 10 then
   print("big")
elseif x > 5 then  -- Note: "elseif" is one word!
   print("medium")
else
   print("small")
end
```

### Logical Operators

You use logical operators to combine conditions.

| Operator | Meaning                                   |
| -------- | ----------------------------------------- |
| `and`    | Both sides must be true                   |
| `or`     | At least one side must be true            |
| `not`    | Inverts the value (`not true` is `false`) |

### Comparison Operators

| Operator | Meaning                                |
| -------- | -------------------------------------- |
| `==`     | Equal to (Note: **two** equals signs!) |
| `~=`     | **Not equal to**                       |
| `>`      | Greater than                           |
| `<`      | Less than                              |
| `>=`     | Greater than or equal to               |
| `<=`     | Less than or equal to                  |

<br/>

```lua
-- 'and' example
if x > 5 and x < 10 then
   print("between 5 and 10")
end

-- 'not' example
if not isActive then
   print("inactive")
end

-- 'not equal' example
if name ~= "Adi" then
   print("Not Adi")
end
```

### "Truthy" and "Falsy" Values

This is very important! When Lua checks a condition, it only considers two values "falsy" (or "false").

- **`false`**
- **`nil`**

**Everything else is "truthy" (or "true").** This includes the number `0` and an empty string `""`!

```lua
if 0 then print("0 is truthy!") end         -- This will print!
if "" then print("Empty string is truthy!") end -- This will also print!
if false then print("nope") end            -- Will not print
if nil then print("nope") end              -- Will not print
```

## 10. Repeating Code (Loops)

Loops are for running a block of code multiple times.

### Numeric `for` Loop

Use this when you want to count from one number to another.

```lua
-- format: for variable = start, end, step
-- (step is optional, defaults to 1)

-- Counts 1, 2, 3, ..., 10
for i = 1, 10 do
   print(i)
end

-- Counts 1, 3, 5, 7, 9 (steps by 2)
for i = 1, 10, 2 do
   print(i)
end

-- Counts 10, 9, 8, ..., 1 (counts down)
for i = 10, 1, -1 do
   print(i)
end
```

### `while` Loop

This loop will run _while_ a condition is true.

```lua
local i = 1
while i <= 5 do
   print(i)
   i = i + 1  -- CRITICAL: You must change the variable!
          -- (Lua has no i++ or i--)
end
-- Prints: 1, 2, 3, 4, 5
```

### `repeat ... until` Loop

This is similar to a `while` loop, but the condition is checked at the _end_. This means **it always runs at least once**.

```lua
local i = 1
repeat
   print(i)
   i = i + 1
until i > 5  -- Note: The condition is "when to stop"
         -- (the opposite of a 'while' loop)

-- Prints: 1, 2, 3, 4, 5
```

### Looping Through Tables (Lists)

To go through every item in a list, you can use `ipairs`.

- `ipairs` = "index pairs"
- It gives you the **index** and the **value** for each item.

```lua
local items = {"a", "b", "c"}

-- Method 1: Numeric for (good if you only need the value)
for i = 1, #items do
   print(items[i])
end
-- Prints: a, b, c

-- Method 2: ipairs (use this when you need index and value)
for index, value in ipairs(items) do
   print(index, value)
end
-- Prints:
-- 1    a
-- 2    b
-- 3    c
```

### Looping Through Tables (Dictionaries)

To go through every key-value pair in a dictionary, you use `pairs`.

```lua
local player = {name = "Adi", level = 10}

for key, value in pairs(player) do
   print(key, value)
end
-- Prints:
-- name    Adi
-- level   10
-- (The order is not guaranteed!)
```

**`ipairs` vs. `pairs`:**

- `ipairs(table)`: Use for **Lists**. Iterates over numeric keys (1, 2, 3...).
- `pairs(table)`: Use for **Dictionaries**. Iterates over _all_ keys (like "name", "level").

## 11. Getting User Input

You can ask the user to type something into the terminal using `io.read()`.

```lua
print("What's your name?")
local name = io.read()
print("Hello, " .. name .. "!")

print("How old are you?")
-- "*n" tells io.read to expect a number
local age = io.read("*n")
print("You will be " .. (age + 1) .. " next year.")
```

## 12. Operators Quick Reference

Here's a quick cheat-sheet of common operators.

### Arithmetic Operators

| Operator    | Operation                 |
| ----------- | ------------------------- |
| `+`         | Addition                  |
| `-`         | Subtraction               |
| `*`         | Multiplication            |
| `/`         | Division                  |
| `%`         | Modulo (remainder)        |
| `^`         | Exponent (power of)       |
| `//`        | Floor division (Lua 5.3+) |
| `x = x + 1` | Increment (no `++`)       |
| `x = x - 1` | Decrement (no `--`)       |

### String Operators

| Operator | Operation                        |
| -------- | -------------------------------- |
| `..`     | String concatenation             |
| `#`      | Length of string (or list table) |

### Comparison & Logical Operators

| Operator | Operation    |
| -------- | ------------ |
| `==`     | Equal to     |
| `~=`     | Not equal to |
| `and`    | Logical AND  |
| `or`     | Logical OR   |
| `not`    | Logical NOT  |

## 13. Code Scope (local vs. global)

"Scope" refers to where a variable is accessible. In Lua, blocks of code (like `if` statements, loops, or functions) create new "scopes."

**This is why `local` is so important.**

```lua
local x = 10  -- This 'x' is local to the whole file

function test()
   local y = 20  -- This 'y' is local ONLY to this function
   if true then
      local z = 30  -- This 'z' is local ONLY to this 'if' block
      print(x)      -- 10 (can see 'x')
      print(y)      -- 20 (can see 'y')
   end
   -- print(z) -- This would cause an error! 'z' doesn't exist here.
end

-- print(y) -- This would also cause an error! 'y' doesn't exist here.

-- Without 'local', a variable becomes GLOBAL (bad!)
globalVar = 100  -- This is accessible EVERYWHERE. Avoid this!
```

**Rule of thumb: Always use `local`** unless you have a very specific reason not to. It prevents bugs and keeps your code clean.

## 14. Advanced: Metatables (Creating "Classes")

Lua doesn't have "classes" in the way other languages do. But it has something even more flexible: **Metatables**.

A metatable is a table that "describes" the behavior of _another_ table. It lets you do powerful things, like "object-oriented programming."

Here's a common pattern for creating a "Player" object.

```lua
-- 1. Create a "class" table. This will hold our methods.
local Player = {}
Player.__index = Player -- This is the metatable magic!
                -- It tells Lua: "If you can't find a
                -- function in an instance, look for it here."

-- 2. Create a "constructor" function to make new players
function Player.new(name, health)
   -- Create a new, empty table for our instance
   local self = {}
   -- Set its metatable to be our Player "class"
   setmetatable(self, Player)

   -- 3. Set the instance's data
   self.name = name
   self.health = health

   -- 4. Return the new instance
   return self
end

-- 5. Define a "method"
-- Note the colon (:)
-- It automatically passes 'self' as the first argument
function Player:takeDamage(amount)
   self.health = self.health - amount
   print(self.name .. " took " .. amount .. " damage!")
end

-- --- USAGE ---
local player = Player.new("Adi", 100) -- Create a new player
player:takeDamage(20)                 -- Call its method
print(player.health)                  -- 80
```

This is complex, so don't worry if it doesn't make sense at first. The key idea is:

1. `Player = {}` is our "blueprint" table.
2. `Player.new()` is our "factory" that builds new player tables.
3. `setmetatable()` links the new tables to the blueprint.
4. The colon (`:`) is special syntax for object methods. (See next section!)

## 15. Organizing Code: Modules

When your code gets big, you'll want to split it into multiple files. Lua calls these **modules**.

A module is just a Lua file that **returns a table**.

**File: `CombatTimings.lua`**

```lua
-- 1. Create a table to hold our public functions/data
local CombatTimings = {}

CombatTimings.M1_COOLDOWN = 0.5

function CombatTimings.calculateDamage(power)
   return power * 10
end

-- 2. Return the table at the end
return CombatTimings
```

**File: `main.lua`** (in the same folder)

```lua
-- 1. Use require() to "import" the module.
--    (Don't add the .lua)
local Timings = require("CombatTimings")

print(Timings.M1_COOLDOWN)  -- 0.5
local dmg = Timings.calculateDamage(5)
print(dmg)                  -- 50
```

## 16. Error Handling (pcall)

What if a part of your code might fail, like trying to load a file that doesn't exist? You don't want it to crash your whole program.

You can "wrap" the dangerous code in a `pcall` (protected call).

`pcall` runs the function and returns two things:

1. A **success** boolean (`true` if it worked, `false` if it crashed).
2. The **result** (if it succeeded) or the **error message** (if it failed).

```lua
local function mightFail()
   -- ... some code ...
   error("Something broke!") -- 'error()' is how you throw an error
   -- ... more code ...
end

local success, result = pcall(mightFail)

if success then
   print("It worked! Result:", result)
else
   print("It failed! Error:", result)
end

-- This will print:
-- It failed! Error: [string "local function..."]:3: Something broke!
```

## 17. Common Patterns (Manual)

Lua has a small standard library. You'll often write your own helper functions for common tasks.

### "Map" a table (Creating a new table based on another)

```lua
local numbers = {1, 2, 3, 4}
local doubled = {}

for i, v in ipairs(numbers) do
   doubled[i] = v * 2
end
-- 'doubled' is now {2, 4, 6, 8}
```

### "Filter" a table (Keeping only some items)

```lua
local numbers = {1, 2, 3, 4}
local evens = {}

for i, v in ipairs(numbers) do
   if v % 2 == 0 then
      -- We must use table.insert because the
      -- keys won't be sequential (1, 2, 3...)
      table.insert(evens, v)
   end
end
-- 'evens' is now {2, 4}
```

### "Reduce" a table (Calculating a single value)

```lua
local numbers = {1, 2, 3, 4}
local sum = 0

for i, v in ipairs(numbers) do
   sum = sum + v
end
-- 'sum' is now 10
```

### Find in a List of Dictionaries

```lua
local players = {{name = "Adi"}, {name = "Bob"}}
local found = nil

for i, player in ipairs(players) do
   if player.name == "Adi" then
      found = player
      break  -- Stop looping once we find it
   end
end

if found then
   print("Found: " .. found.name)
end
```

## 18. The Colon (:) vs. The Dot (.)

This is a very common point of confusion, but the rule is simple.

**In Lua, `:` is just "syntactic sugar" (a shortcut) for passing `self` as the first parameter.**

These two lines are **100% IDENTICAL** to Lua:

```lua
object:method(arg1, arg2)
object.method(object, arg1, arg2)
```

The colon (`:`) automatically takes the `object` on the left and passes it as the first argument to the function.

### When to Use `:` vs `.`

| Use `:` (Colon)                                                  | Use `.` (Dot)                                           |
| ---------------------------------------------------------------- | ------------------------------------------------------- |
| **Calling** instance methods (needs access to the object's data) | **Calling** static functions (doesn't need an instance) |
| **Defining** methods that operate on `self`                      | **Defining** constructors or utility functions          |

**Examples:**

```lua
-- Module/Class definition
local Player = {}
Player.__index = Player

-- Use . for constructor (it doesn't have 'self' yet, it CREATES self)
function Player.new(name)
   local self = setmetatable({}, Player)
   self.name = name
   return self
end

-- Use : for methods (it needs 'self' to access the instance data)
function Player:sayName()
   print(self.name)  -- 'self' is passed automatically
end

-- Use . for static utilities (no instance/self is needed)
function Player.calculateLevelBonus(level)
   return level * 10
end

-- --- USAGE ---

-- Call with . because 'new' is a static "factory" function
local player = Player.new("Adi")

-- Call with : because 'sayName' is an instance method
player:sayName()  -- Prints "Adi"

-- Call with . because it's a static utility
local bonus = Player.calculateLevelBonus(5)
print(bonus)      -- 50
```

**Rule of thumb:** If the function **CREATES** the object (like `new()`), use **`.`**. If the function **USES** the object's instance data (like `takeDamage()` or `sayName()`), use **`:`**.

> The logo used in the banner belongs to https://www.lua.org/ and is included here for educational, non-commercial purposes. For licensing and usage details, please refer to the official Lua website.
