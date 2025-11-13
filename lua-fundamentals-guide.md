# Lua Crash Course (For JavaScript Developers)

**Lua is like JavaScript's simpler cousin. Let's map everything 1:1.**

## 1. Variables & Data Types

### JavaScript:

```javascript
let name = "Adi";
const age = 25;
var isActive = true;
let nothing = null;
let notDefined = undefined;
```

### Lua:

```lua
local name = "Adi"
local age = 25
local isActive = true
local nothing = nil  -- Lua has only nil (no null/undefined distinction)

-- NO const in Lua (everything is mutable)
-- Always use 'local' (like let), avoid global variables
```

**Key differences:**

- ✅ No `const` - everything is mutable
- ✅ `nil` instead of `null`/`undefined`
- ✅ `local` keyword is CRITICAL (without it, variable is global)
- ✅ No semicolons needed (but allowed)

## 2. Data Types

| JavaScript           | Lua        | Notes                                              |
| -------------------- | ---------- | -------------------------------------------------- |
| `string`             | `string`   | Same                                               |
| `number`             | `number`   | Only one number type (no int/float distinction)    |
| `boolean`            | `boolean`  | Same                                               |
| `null` / `undefined` | `nil`      | Only one “nothing” value                           |
| `object`             | `table`    | Tables are everything (arrays + objects + classes) |
| `array`              | `table`    | Arrays are tables with numeric keys                |
| `function`           | `function` | Same                                               |

## 3. Strings

### JavaScript:

```javascript
const name = "Adi";
const greeting = `Hello, ${name}!`; // Template literals
const multi = `Line 1
Line 2`;
const length = name.length;
const upper = name.toUpperCase();
const concat = name + " is cool";
```

### Lua:

```lua
local name = "Adi"
local greeting = "Hello, " .. name .. "!"  -- .. is concatenation operator
local multi = [[Line 1
Line 2]]  -- [[ ]] for multi-line strings
local length = #name  -- # operator for length
local upper = string.upper(name)
local concat = name .. " is cool"

-- NO template literals in Lua
-- Must use concatenation with ..
```

**String methods:**

```lua
string.upper("hello")      -- "HELLO"
string.lower("HELLO")      -- "hello"
string.sub("hello", 2, 4)  -- "ell" (substring, 1-indexed!)
string.find("hello", "ll") -- 3, 4 (returns start, end indices)
string.gsub("hello", "l", "x") -- "hexxo" (global substitute)
string.len("hello")        -- 5 (same as #"hello")
```

## 4. Tables (Objects + Arrays)

**Tables are EVERYTHING in Lua. They're objects, arrays, dictionaries, classes, all in one.**

### As Object (Dictionary):

**JavaScript:**

```javascript
const player = {
  name: "Adi",
  level: 10,
  isAlive: true,
};

console.log(player.name); // "Adi"
console.log(player["level"]); // 10
player.health = 100; // Add property
```

**Lua:**

```lua
local player = {
    name = "Adi",  -- Use = not :
    level = 10,
    isAlive = true
}

print(player.name)       -- "Adi"
print(player["level"])   -- 10
player.health = 100      -- Add property
```

### As Array:

**JavaScript:**

```javascript
const items = ["sword", "shield", "potion"];
console.log(items[0]); // "sword" (0-indexed)
console.log(items.length); // 3
items.push("armor"); // Add to end
```

**Lua:**

```lua
local items = {"sword", "shield", "potion"}
print(items[1])      -- "sword" (1-INDEXED!!!)
print(#items)        -- 3 (# operator for length)
table.insert(items, "armor")  -- Add to end

-- Lua arrays start at 1, not 0!
-- items[0] is nil
```

**CRITICAL:** Lua is 1-indexed. Arrays start at 1.

### Table Methods:

```lua
local arr = {10, 20, 30}

table.insert(arr, 40)       -- [10, 20, 30, 40]
table.insert(arr, 2, 15)    -- [10, 15, 20, 30, 40] (insert at index 2)
table.remove(arr, 3)        -- [10, 15, 30, 40] (remove index 3)
table.sort(arr)             -- [10, 15, 30, 40] (sorts in place)
table.concat(arr, ", ")     -- "10, 15, 30, 40" (join array)

-- NO map, filter, reduce in standard Lua
-- You loop manually (see loops section)
```

## 5. Functions

### JavaScript:

```javascript
function greet(name) {
  return "Hello, " + name;
}

const greet2 = (name) => "Hello, " + name;

function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
```

### Lua:

```lua
function greet(name)
    return "Hello, " .. name
end

-- Or anonymous function
local greet2 = function(name)
    return "Hello, " .. name
end

-- Variadic functions (like ...args)
function sum(...)
    local args = {...}  -- Collect args into table
    local total = 0
    for i = 1, #args do
        total = total + args[i]
    end
    return total
end

print(sum(1, 2, 3, 4))  -- 10
```

**Multiple return values:**

**JavaScript:**

```javascript
function getDimensions() {
  return [100, 200]; // Return array
}
const [width, height] = getDimensions();
```

**Lua:**

```lua
function getDimensions()
    return 100, 200  -- Return multiple values directly
end

local width, height = getDimensions()
print(width, height)  -- 100  200
```

## 6. Conditionals

### JavaScript:

```javascript
if (x > 10) {
  console.log("big");
} else if (x > 5) {
  console.log("medium");
} else {
  console.log("small");
}

const result = x > 10 ? "big" : "small";
```

### Lua:

```lua
if x > 10 then
    print("big")
elseif x > 5 then  -- elseif is one word!
    print("medium")
else
    print("small")
end

-- NO ternary operator in Lua
-- Use 'and'/'or' hack instead:
local result = x > 10 and "big" or "small"
```

**Logical operators:**

| JavaScript | Lua                                |
| ---------- | ---------------------------------- |
| `&&`       | `and`                              |
| `\|\|`     | `or`                               |
| `!`        | `not`                              |
| `===`      | `==` (Lua only has `==`, no `===`) |
| `!==`      | `~=`                               |

```lua
if x > 5 and x < 10 then
    print("between 5 and 10")
end

if not isActive then
    print("inactive")
end

if name == "Adi" then  -- == for equality
    print("Hello Adi")
end

if age ~= 25 then  -- ~= for not equal
    print("Not 25")
end
```

**Truthy/Falsy:**

**JavaScript:** `false`, `0`, `""`, `null`, `undefined`, `NaN` are falsy

**Lua:** ONLY `false` and `nil` are falsy

```lua
if 0 then print("0 is truthy!") end        -- Prints (0 is truthy in Lua!)
if "" then print("empty string is truthy!") end  -- Prints
if false then print("nope") end            -- Doesn't print
if nil then print("nope") end              -- Doesn't print
```

## 7. Loops

### For Loop (Numeric):

**JavaScript:**

```javascript
for (let i = 1; i <= 10; i++) {
  console.log(i);
}
```

**Lua:**

```lua
for i = 1, 10 do  -- start, end (inclusive)
    print(i)
end

for i = 1, 10, 2 do  -- start, end, step
    print(i)  -- 1, 3, 5, 7, 9
end

for i = 10, 1, -1 do  -- Reverse
    print(i)  -- 10, 9, 8, ..., 1
end
```

### For Loop (Iterate Array):

**JavaScript:**

```javascript
const items = ["a", "b", "c"];
for (let i = 0; i < items.length; i++) {
  console.log(items[i]);
}

items.forEach((item) => console.log(item));
```

**Lua:**

```lua
local items = {"a", "b", "c"}

-- Method 1: Numeric for
for i = 1, #items do
    print(items[i])
end

-- Method 2: ipairs (for arrays)
for index, value in ipairs(items) do
    print(index, value)  -- 1 a, 2 b, 3 c
end
```

### For Loop (Iterate Object):

**JavaScript:**

```javascript
const player = { name: "Adi", level: 10 };
for (const key in player) {
  console.log(key, player[key]);
}
```

**Lua:**

```lua
local player = {name = "Adi", level = 10}

for key, value in pairs(player) do
    print(key, value)  -- name Adi, level 10
end
```

**ipairs vs. pairs:**

- `ipairs(table)` - For arrays (numeric keys), stops at first nil
- `pairs(table)` - For dictionaries (any keys), iterates all

### While Loop:

**JavaScript:**

```javascript
let i = 0;
while (i < 10) {
  console.log(i);
  i++;
}
```

**Lua:**

```lua
local i = 0
while i < 10 do
    print(i)
    i = i + 1  -- NO i++ in Lua!
end
```

### Repeat-Until (Do-While):

**JavaScript:**

```javascript
let i = 0;
do {
  console.log(i);
  i++;
} while (i < 10);
```

**Lua:**

```lua
local i = 0
repeat
    print(i)
    i = i + 1
until i >= 10  -- Condition is INVERTED (runs until true)
```

## 8. Operators

| Operation      | JavaScript          | Lua                 |
| -------------- | ------------------- | ------------------- |
| Addition       | `x + y`             | `x + y`             |
| Subtraction    | `x - y`             | `x - y`             |
| Multiplication | `x * y`             | `x * y`             |
| Division       | `x / y`             | `x / y`             |
| Modulo         | `x % y`             | `x % y`             |
| Exponent       | `x ** y`            | `x ^ y`             |
| Floor division | `Math.floor(x / y)` | `x // y` (Lua 5.3+) |
| Increment      | `x++`               | `x = x + 1` (NO ++) |
| Decrement      | `x--`               | `x = x - 1` (NO --) |
| String concat  | `x + y`             | `x .. y`            |
| Length         | `arr.length`        | `#arr`              |
| Not equal      | `x !== y`           | `x ~= y`            |
| And            | `x && y`            | `x and y`           |
| Or             | `x \|\| y`          | `x or y`            |
| Not            | `!x`                | `not x`             |

## 9. Scope

### JavaScript:

```javascript
let x = 10; // Block scope

function test() {
  let y = 20; // Function scope
  if (true) {
    let z = 30; // Block scope
  }
  // console.log(z);  // Error (z not accessible)
}
```

### Lua:

```lua
local x = 10  -- Local to current block/file

function test()
    local y = 20  -- Local to function
    if true then
        local z = 30  -- Local to if block
    end
    -- print(z)  -- Error (z not accessible)
end

-- Without 'local', variable is GLOBAL (bad!)
globalVar = 100  -- Accessible everywhere (avoid this!)
```

**Always use `local`.** Global variables cause bugs.

## 10. Metatables (Like Prototypes/Classes)

**Lua doesn't have classes. You fake them with metatables.**

### JavaScript:

```javascript
class Player {
  constructor(name, health) {
    this.name = name;
    this.health = health;
  }

  takeDamage(amount) {
    this.health -= amount;
  }
}

const player = new Player("Adi", 100);
player.takeDamage(20);
```

### Lua:

```lua
local Player = {}
Player.__index = Player  -- Metatable magic

function Player.new(name, health)
    local self = setmetatable({}, Player)  -- Create instance
    self.name = name
    self.health = health
    return self
end

function Player:takeDamage(amount)  -- : syntax adds implicit 'self'
    self.health = self.health - amount
end

local player = Player.new("Adi", 100)
player:takeDamage(20)  -- : calls with implicit self
print(player.health)  -- 80
```

**Breakdown:**

1. `Player = {}` - Table acts as class
2. `Player.__index = Player` - Makes table look up methods in itself
3. `Player.new()` - Constructor function
4. `setmetatable({}, Player)` - Creates instance with Player as prototype
5. `function Player:method()` - `:` adds implicit `self` parameter

## 11. Modules (Exports)

### JavaScript:

```javascript
// CombatTimings.js
export const M1_COOLDOWN = 0.5;
export function calculateDamage(power) {
  return power * 10;
}

// Or
module.exports = {
  M1_COOLDOWN: 0.5,
  calculateDamage: (power) => power * 10,
};

// Usage
import { M1_COOLDOWN, calculateDamage } from "./CombatTimings";
```

### Lua:

```lua
-- CombatTimings.lua (ModuleScript)
local CombatTimings = {}

CombatTimings.M1_COOLDOWN = 0.5

function CombatTimings.calculateDamage(power)
    return power * 10
end

return CombatTimings  -- Must return table

-- Usage in another script
local Timings = require(game.ReplicatedStorage.CombatTimings)
print(Timings.M1_COOLDOWN)  -- 0.5
local dmg = Timings.calculateDamage(5)  -- 50
```

## 12. Error Handling

### JavaScript:

```javascript
try {
  throw new Error("Something broke");
} catch (error) {
  console.error(error.message);
} finally {
  console.log("Cleanup");
}
```

### Lua:

```lua
local success, error = pcall(function()
    error("Something broke")  -- Throw error
end)

if not success then
    print("Error:", error)
end

-- NO finally in Lua
-- Do cleanup manually after pcall
```

**pcall = protected call (like try-catch)**

## 13. Common Patterns

### Map/Filter/Reduce (Manual):

**JavaScript:**

```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map((x) => x * 2);
const evens = numbers.filter((x) => x % 2 === 0);
const sum = numbers.reduce((a, b) => a + b, 0);
```

**Lua:**

```lua
local numbers = {1, 2, 3, 4}

-- Map
local doubled = {}
for i, v in ipairs(numbers) do
    doubled[i] = v * 2
end

-- Filter
local evens = {}
for i, v in ipairs(numbers) do
    if v % 2 == 0 then
        table.insert(evens, v)
    end
end

-- Reduce
local sum = 0
for i, v in ipairs(numbers) do
    sum = sum + v
end
```

### Find in Array:

**JavaScript:**

```javascript
const players = [{ name: "Adi" }, { name: "Bob" }];
const found = players.find((p) => p.name === "Adi");
```

**Lua:**

```lua
local players = {{name = "Adi"}, {name = "Bob"}}
local found = nil
for i, player in ipairs(players) do
    if player.name == "Adi" then
        found = player
        break
    end
end
```

## 14. The `:` vs `.` Distinction

### The Fundamental Rule

**In Lua/Luau, `:` is syntactic sugar for passing `self` as the first parameter.**

```lua
-- These are IDENTICAL:
object:method(arg1, arg2)
object.method(object, arg1, arg2)

-- The : automatically passes 'object' as first parameter
```

### When to Use `:` vs `.`

| Use `:`                                               | Use `.`                                             |
| ----------------------------------------------------- | --------------------------------------------------- |
| Calling instance methods (needs access to the object) | Calling static functions (doesn't need an instance) |
| Defining methods that operate on `self`               | Defining constructors or utility functions          |

**Examples:**

```lua
-- Module/Class definition
local Player = {}
Player.__index = Player

-- Use . for constructor (doesn't have 'self' yet, creates it)
function Player.new(name, health)
    local self = setmetatable({}, Player)
    self.name = name
    self.health = health
    return self
end

-- Use : for methods (needs 'self' to access instance data)
function Player:takeDamage(amount)
    self.health = self.health - amount  -- 'self' is passed automatically
end

function Player:isAlive()
    return self.health > 0
end

-- Use . for static utilities (no instance needed)
function Player.calculateMaxHealth(level)
    return 100 + (level * 10)  -- No 'self' involved
end

-- Usage
local player = Player.new("Adi", 100)  -- . because 'new' is static
player:takeDamage(20)                  -- : because method needs 'self'
print(player:isAlive())                -- : because method needs 'self'
local maxHP = Player.calculateMaxHealth(5)  -- . because static utility
```

### Behind the Scenes (What Really Happens)

```lua
-- When you write:
player:takeDamage(20)

-- Lua translates it to:
player.takeDamage(player, 20)
-- The first 'player' is looked up as a key
-- The second 'player' is passed as the first argument
```

**Mental model:** `:` = "call this function AND pass the left side as the first argument"

### Common Mistake

```lua
-- WRONG: Using : for constructor
function Player:new(name)
    -- 'self' here is undefined/wrong, you're CREATING self, not receiving it
    local self = setmetatable({}, Player)  -- Confusing!
    return self
end

-- CORRECT: Use . for constructor
function Player.new(name)
    local self = setmetatable({}, Player)  -- Clear: we're making self
    return self
end
```

**Rule of thumb:** If the function CREATES the object, use `.`. If it USES the object, use `:`.
