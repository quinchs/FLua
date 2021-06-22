<h1 align="center">FLua: A lua language extension</h1>

Welcome to Flua, the open source dynamic scripting language that compiles into Lua. It is heavily based on the 5.2 lua concepts. The initial idea of FLua came when I was trying to work with metatables and got so confused because I came from a C/C# background. The goal of FLua is to ease the writing of tables that are designed to represent entities. The compiled version of FLua is designed to be compatable with normal lua so you could use FLua created objects in lua.

<div align="center">
  <h1>New keywords</h1>

| Keyword   | Description                                                 |
| --------- | ----------------------------------------------------------- |
| `class`   | Classes are declared using the keyword `class`              |
| `public`  | Marks the current field or function public inside a class.  |
| `private` | Marks the current field or function private inside a class. |
| `import`  | Import a declaration from a file.                           |
| `export`  | Exports a declaration globally.                             |
| `get`     | Sets the `get` operator on a field in a class.              |
| `set`     | Sets the `set` operator on a field in a class               |
| `this`    | `this` is treated like the lua `self` keyword.              |
| `new`     | Used to create and call a classes constructor.              |
| `from`    | Used with `import` to define where to import from           |

  </div>
  
## Classes

Flue adds classes that compile to lua metatables, however with FLua its much easier to make encapsulation compliant tables.

With classes you can control what can be accessed as well as use some metatable magic.

### Defining a class

```lua
class MyClass {
    -- func's and fields go here
}
```

### Creating an instance of a class

In FLua you can create a new instance of a class with the `new` keyword:

```lua
local inst = new MyClass()
```

The new keyword acts like luas `:new` function and can be used as so:

```lua
local inst = MyClass:new()
```

Both will create a new instance of the class and call the constructor if there is one.

### Constructors

Making a constructor is easy in FLua. you make a **publically accessable** function with the same name of the class, you can have multiple constructors as long as they take a different amount of arguments.

```lua
class MyClass {
    function MyClass()
        print("Constructor was called.")
    end

    function MyClass(arg1)
        print("Got arg " .. tostring(arg1))
    end
}

local inst = new MyClass()  -- prints "Constructor was called."
inst = new MyClass("Hello") -- prints "Got arg Hello"
```

### `this` keyword

Within classes you can use the `this` keyword to access the instance of the current class. `self` does the same thing as `this`. currently in FLua classes are instance only, there is no static like implementation of classes _yet_.

**Example usage of `this`**

```lua
class MyClass {

    function MyClass()
        this.printHello()
    end

    function printHello()
        print("Hello!")
    end
}

local inst = new MyClass() -- prints hello.

```

## Public and Private Properties

With FLua you can specify properties or functions in classes to have access keywords. Currently there is `public` and `private`. By default. all properties without an access keyword are made public.

### Private
Only the class or an instance of the class can access these functions or properties. `private` behaives exactly like C#'s `private` keyword. The `private` keyword can be applied on funtions inside a class, fields inside a class, and on get and set functions.

### Public
Anyone can access the function or property with the `public` keyword. The `public` keyword can be applied on functions inside a class, fields inside a class, and get and set functions.

**Example**

```lua
class MyClass {
    public propA = nil
    private propB = nil

    function MyClass()
        self.propA = 1 -- valid.
        self.propB = 2
    end

    function getPropB()
        return self.propB -- valid.
    end
}

local inst = new MyClass()
print(inst.propA) -- prints 2.

print(inst.propB) -- invalid.
print(inst.getPropB()) -- valid.

inst.propA = 4 -- valid.
inst.propB = 5 -- invalid.

```

## Get and Set

The `get` and `set` keywords allow you to control encapsulation inside your classes.

### Get
The `get` keyword is used in properties to get the value of that property. they can be a function or a value. It is expected that the get function returns a value, but its not required.

### Set
The `set` function is used to set the properties value. The set function should take in a `value` parameter which is what the executer of the function wants to set the field to.

**Example**

```lua
class MyClass {
    public propA { get = propAPriv }
    private propAPriv = 1

    public propB { get = function() return math.random(10) end }

    public propC
    {
        get = propCPriv,
        set = function(value)
            assert(type(value) == "string", "value must be a string.")
            assert(value ~= nil, "value cannot be nil")
            assert(#value <= 25, "value must be 25 characters or less")
            self.propCPriv = value
        end
    }

    private propCPriv = ""

    function MyClass()

    end
}

local inst = new MyClass()
print(inst.propA)                         -- prints 1
inst.propA = 2                            -- error: cannot set a read only properties value.

print(inst.propB)                         -- prints a number between 1 and 10.

inst.propC = 1                            -- error: value must be a string.
inst.propC = "aaaaaaaaaaaaaaaaaaaaaaaaaa" -- error: value must be 25 characters or less
inst.propC = "valid"                      -- valid.
print(inst.propC)                         -- prints "valid"

```

## Import/Export

The `import` and `export` keywords are designed to help ease the usage of global variables.

### Importing

You can import specific functios/tables from globals like so

```lua
import pi from math

print(pi) -- prints 3.141...
```

### Exporting

```lua
class MyClass {
    function MyClass()
        print("MyClass created!")
    end
}

export MyClass
```

### Combining the two

```lua
-- File 1
randomGlobal = 10

class MyClass {
    function MyClass()
        print("MyClass created")
    end
}

export MyClass, randomGlobal

-- File 2
import MyClass from require "file1"

print(randomGlobal) -- nil, randomGlobal is undefined because we didnt import it yet.
print(MyClass)      -- prints the string version of the table, its defined.

import randomGlobal from require "file1"
print(randomGlobal) -- prints 10
```

# Whats in the works?

Once I write the core concepts listed above, im going to work on inheritance and static classes, methods, and fields. Currently not all of the things above work yet and theres no public version of a compiler for FLua yet, I am currently working on it though and will have somthing to publish in the next few weeks or so.
