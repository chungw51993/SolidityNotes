## Modifier and Interface Example

```
pragma solidity ^0.4.0;

interface Regulator {
    function checkValue(uint amount) returns (bool);
    function loan() returns (bool);
}

// Bank contract uses Regulator interface
contract Bank is Regulator {
    uint private value;
    address private owner;

    // Custom modifier for functions
    modifier ownerFunc {
        require(owner == msg.sender);
        // Underscore specifies when function should run
        _;
    }

    // Constructor for Bank Contract
    function Bank(uint amount) {
        value = amount;
        owner = msg.sender;
    }

    // deposit function has ownerFunc modifier on it
    function deposit(uint amount) ownerFunc {
        value += amount;
    }

    // withdraw function has ownerFunc modifier on it
    function withdraw(uint amount) ownerFunc {
        if (checkValue(amount)) {
            value -= amount;
        }
    }

    function balance() returns (uint) {
        return value;
    }

    function checkValue(uint amount) returns (bool) {
        return value >= amount;
    }

    function loan() returns (bool) {
        return value > 0;
    }
}

// MyFirstContract using Bank constructor to inherit functions
contract MyFirstContract is Bank(10) {
    string private name;
    uint private age;

    function setName(string newName) {
        name = newName;
    }

    function getName() returns (string) {
        return name;
    }

    function setAge(uint newAge) {
        age = newAge;
    }

    function getAge() returns (uint) {
        return age;
    }
}
```

- MyFirstContract will have all the functions in Bank but it will not have access to it's variables
- Only owner of the contract will be able to deposit or withdraw since modifier checks to see if sender is owner
- Underscore in modifier is when the function modifier is applied to runs so you can run it before require if you wanted to
