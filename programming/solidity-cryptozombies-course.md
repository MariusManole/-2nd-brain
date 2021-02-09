---
description: Notes from following the Solidity cryptozombies course
---

# Solidity - CryptoZombies course

## Lesson 1

Solidity's code is encapsulated in **contracts**. A `contract` is the fundamental building block of Ethereum applications — all variables and functions belong to a contract, and this will be the starting point of all your projects.

An empty contract named `HelloWorld` would look like this:

```text
contract HelloWorld {

}
```

All solidity source code should start with a "version pragma" — a declaration of the version of the Solidity compiler this code should use.

### **Variables**

**State variables** are permanently stored in contract storage. This means they're written to the Ethereum blockchain. Think of them like writing to a DB.

The `uint` data type is an unsigned integer, meaning **its value must be non-negative**. There's also an `int` data type for signed integers.

Sometimes you need a more complex data type. For this, Solidity provides _**structs**_:

```text
struct Person {
  uint age;
  string name;
}
```

When you want a collection of something, you can use an _**array**_. There are two types of arrays in Solidity: _**fixed**_ arrays and _**dynamic**_ arrays:

> ```text
> // Array with a fixed length of 2 elements:
> uint[2] fixedArray;
> // another fixed Array, can contain 5 strings:
> string[5] stringArray;
> // a dynamic Array - has no fixed size, can keep growing:
> uint[] dynamicArray;
> ```

We can also create an array of _**structs**_. Using the previous chapter's `Person` struct:

```text
Person[] people; // dynamic Array, we can keep adding to it
```

Remember that **state variables** are **stored permanently** in the blockchain? So creating a dynamic array of structs like this can be useful for storing structured data in your contract, kind of like a database.

We can declare an array as `public`, and Solidity will automatically create a _**getter**_ method for it. The syntax looks like:

```text
Person[] public people;
```

Other contracts would then be able to read from, but not write to, this array. So this is a useful pattern for storing public data in your contract.

### **Functions**

A function declaration in solidity looks like the following:

```text
function eatHamburgers(string memory _name, uint _amount) public {

}
```

This is a function named `eatHamburgers` that takes 2 parameters: a `string` and a `uint`.  Note that we're specifying the function visibility as `public`. 

**!!** We're also providing instructions about where the `_name` variable should be stored- in `memory`. This is required for all **reference types** such as arrays, structs, mappings, and strings. Without the memory keyword, Solidity tries to declare variables in storage.

There are two ways in which you can pass an argument to a Solidity function:

* By **value**, which means that the Solidity compiler creates a new copy of the parameter's value and passes it to your function. This allows your function to modify the value without worrying that the value of the initial parameter gets changed.
* By **reference**, which means that your function is called with a reference to the original variable. Thus, if your function changes the value of the variable it receives, the value of the original variable gets changed.

**??** In the example above, is `string memory _name` passed by value or by reference? Or is that an unrelated concept?

> Note: It's convention \(but not required\) to start function parameter variable names with an underscore in order to differentiate them from global variables.

Function similar to list.append\(\) in Python &gt; struct.push\(\)

Note -&gt;  `array.push()` returns a `uint` of the new length of the array.

```text
// create a New Person:
Person satoshi = Person(172, "Satoshi");

// Add that person to the Array:
people.push(satoshi);

// or - in one line
people.push(Person(16, "Vitalik"));
```

 In Solidity, functions are `public` by default. This means anyone \(or any other contract\) can call your contract's function and execute its code.

Obviously this isn't always desirable, and can make your contract vulnerable to attacks. Thus it's good practice to mark your functions as `private` by default, and then only make `public` the functions you want to expose to the world.

Let's look at how to declare a **private function**:

```text
uint[] numbers;

function _addToArray(uint _number) private {
  numbers.push(_number);
}
```

This means only other functions within our contract will be able to call this function and add to the `numbers` array.  As you can see, we use the keyword `private` after the function name. And as with function parameters, it's convention to start private function names with an underscore \(`_`\).

### Return Values

To return a value from a function, the declaration looks like this:

```text
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}
```

In Solidity, the function declaration contains the type of the return value \(in this case `string`\).  


### Function modifiers \(view and pure\) <a id="function-modifiers"></a>

The above function doesn't actually change state in Solidity — e.g. it doesn't change any values or write anything. So in this case we could declare it as a _**view**_ function, meaning it's only viewing the data but not modifying it:

```text
function sayHello() public view returns (string memory) {
```

Solidity also contains _**pure**_ functions, which means you're not even accessing any data in the app. Consider the following:

```text
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

This function doesn't even read from the state of the app — its return value depends only on its function parameters. So in this case we would declare the function as _**pure**_.

> Note: It may be hard to remember when to mark functions as pure/view. Luckily the Solidity compiler is good about issuing warnings to let you know when you should use one of these modifiers.

### Typecasting

Sometimes you need to convert between data types. Take the following example:

```text
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:
uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:
uint8 c = a * uint8(b);
```

In the above, `a * b` returns a `uint`, but we were trying to store it as a `uint8`, which could cause potential problems. By casting it as a `uint8`, it works and the compiler won't throw an error.  


### Events

_**Events**_ are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.

```text
// declare the event
event IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
  // fire an event to let the app know the function was called:
  emit IntegersAdded(_x, _y, result);
  return result;
}
```

Your app front-end could then listen for the event. A javascript implementation would look something like:

```text
YourContract.IntegersAdded(function(error, result) {
  // do something with result
})
```

-- end of lesson 1 --

Code:

```text
pragma solidity ^0.4.25;

contract ZombieFactory {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string _name, uint _dna) private {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        emit NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(abi.encodePacked(_str)));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        uint randDna = _generateRandomDna(_name);
        _createZombie(_name, randDna);
    }

}
```

## Lesson 2

### Chapter 2: Mappings and Addresses

Two new **data types**: mapping and address.

The Ethereum blockchain is made up of accounts, which you can think of like bank accounts. An account has a balance of Ether \(the currency used on the Ethereum blockchain\), and you can send and receive Ether payments to other accounts, just like your bank account can wire transfer money to other bank accounts.

Each account has an address, which you can think of like a bank account number. It's a unique identifier that points to that account, and it looks like this:

0x0cE446255506E92DF41614C46F1d6df9Cc969183

### Mappings <a id="mappings"></a>

In Lesson 1 we looked at _**structs**_ and _**arrays**_. _**Mappings**_ are another way of storing organized data in Solidity.

Defining a `mapping` looks like this:

```text
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

A mapping is essentially a key-value store for storing and looking up data. In the first example, the key is an `address` and the value is a `uint`, and in the second example the key is a `uint` and the value a`string`.

### Chapter 3: Msg.sender



