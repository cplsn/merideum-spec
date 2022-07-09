# Merit
Merit is a programming language that comes bundled with a Merideum implementation. 
Merideum uses Merit for request syntax and execution. 
The language is designed to be simple, concise, and familiar.
Merit is intentionally unadvanced--this is to prevent misuse or abuse and keep requests and contracts from becoming too complex.

# Script Definition
The script definition block goes first on a Merit script. The script definition contains the script type `request` or `contract`, the name of the script, and, depending on the script type, a list of parameters.


## Request
A request definition does not have parameters.
```
request myRequest {
    // ...
}
```

## Contract

```
contract myContract {
    // ...
}
```

A contract definition can have a list of parameters. A parameter has a name and a Merit type.
```
contract myParametizedContract(name: string, birthYear: int) {
    // ...
}
```

___

# Variables
A variable is either a `const` or a `var`.

## const
A `const` variable cannot be reassigned. The request will fail and an error will be returned if a `const` variable is attempted to be reassigned.

```
const name = "Merideum"
```

A type declaration is only necessary if the value of the variable is set to `null` at declaration. Otherwise, the type is inferred from the value of the assignment.

```
const test: string? = null
```

## var
A `var` variable can be reassigned.
```
var age = 24
age = 25
```

A `var` variable may also be declared without an assignment, but the type declaration is then required. 
```
var age: int

age = 24
```

A type declaration is also required if the value would be null at declaration and assignment. 
```
var test: string? = null
```

The type of the reassigned value must match the original type of the variable. The request will fail and an error will be returned if a variable is attempted to be reassigned with a value of a different type.

A variable may be used in an expression.

```
const name = "Merideum"

const message = "Hello ${name}!"
```

## Types

### `int`
A 32-bit signed integer.
```
var age: int = 24
age = 25
```

### `string`
A string of characters.

```
var name: string
name = "Merideum"
```

The `length()` function can be used to return the `int` length of a `string`.

```
const name = "Merideum"
const nameLength = name.length()
```

### `object`
An `object` is a collection of key/value pairs. Values are accessed through dot-notation or indexed notation using its key. 

```
const car = {
    color = "red",
    doors = 4
}

const carColor = car.color
const carDoors = car["doors"]
```

If the modifier of the `object` variable is `var` instead of `const`, values can be added or removed to the object.

```
const car = {
    color = "red",
    doors = 4
}

car.driver = "Foo"
```

Modifiers may be included on a key assignment. If the modifier is excluded, the value is `const`. `var` values must still be assigned a value at declaration time.

```
const car = {
    color = "red",
    doors = 4,
    var gas = 100
}

car.gas -= 10

var car.driver = "Foo"
```

Just like with variables, a nullable value must have a type during declaration.

```
const car = {
    color = "red",
    doors = 4,
    var gas = 100,
    var driver: string? = null
}
```

### Nullability
Variables are non-nullable by default. The `?` is used as part of the type declaration to denote a variable as nullable. 
If a nullable variable would be set to `null` at declaration, the type declaration _must_ be included.

```
const test: string? = null

var test2: string?

test2 = null
```

___

# `request` object
The `request` object is a reserved variable that allows the user to get attributes of the request (like headers) or send errors and fail the request.

## `errors`
`errors` is an object that will return on the response if populated.

Usage:
```
request.errors["idNotFound"] = "The ID was not found."
```

Response body (in json):
```json
{
    "errors": {
        "idNotFound": "The ID was not found."
    }
}
```

## `fail()`
The `fail()` function is used to stop the code execution immediately and return any errors.

Usage:
```
request myRequest {
    request.fail()

    const message = "This will not run"
}
```

# `return`
The `return` keyword is used to stop execution and return a value on the response. The value of the `return` must be an `object`.

Usage:
```
request myRequest {
    return { message = "Hello World!" }
    
    const message = "This will not run"
}
```

Response body (in json):
```json
{
    "output": {
        "message": "Hello World!"
    }
}
```

___

# Resources
Resources are objects with functions that allow a Merit script to call code external to the script.

## `import`
A resource is referenced by a dot-delimited path and its name and assigned to a variable using the `import` keyword. `import` statements must occur after the script definition and before any other statements. A resource name starts with a capital letter.

```
request myRequest {
    
    import greeter: org.merideum.Greeter
}
```

Import statements can be folded into an `import` block.

```
request myRequest {

    import personRepository: org.person.PersonRepository
    import locationService: org.location.LocationService
    import calendar: com.google.Calendar
}
```

can be written as:

```
request myRequest {

    import {
        personRepository: org.person.PersonRepository
        locationService: org.location.LocationService
        calendar: com.google.Calendar
    }
}
```

## Functions

A resource function may have parameters and return a value:
```
request myRequest {
    import greeter: org.merideum.Greeter
    
    const name = "Merideum"
    
    const message = greeter.sayHello(name)
}
```

The return value must be a Merit value (`int`, `string`, `object` etc.)
