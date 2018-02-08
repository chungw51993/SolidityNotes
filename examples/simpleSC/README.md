## Simple Smart Contract

**A contract in the sense of Solidity is a collection of code (its functions) and data(its state) that resides at a specific address on the Ethereum blockchain.**

#### Storage

```
pragma solidity ^0.4.0;   <- Tells the source code is written for V 0.4.0 or newer

contract SimpleStorage {
    uint storedData;      <- Declares an unsigned integer variable called storedData

    function set(uint x) public {   <- Function to modify the storedData variable
        storedData = x;
    }

    function get() public constant returns (uint) {   <- Function to retrieve the variable
        return storedData;
    }
}
```

- Make sure to tell the source code which version to use to ensure that the contract does not suddenly behave differently with a new compiler version

- Pragma is instructions for the compiler about how to treat the source code

- The line *uint storedData* declares a state variable which you can think of as a single slot in a database

- Use the set and get functions to modify or retrieve the value

#### Subcurrency Example

```
pragma solidity ^0.4.0;

contract Coin {
    // The keyword "public" makes those variables
    // readable from outside.
    address public minter;    <- Declares an address that is publicly accessible
    mapping (address => uint) public balances;    <- Maps addresses to unsigned integers that is publicly accessible

    // Events allow light clients to react on
    // changes efficiently.
    event Sent(address from, address to, uint amount);    <- Creates an event that client can listen for

    // This is the constructor whose code is
    // run only when the contract is created.
    function Coin() public {
        minter = msg.sender;    <- msg is a global variable and sender is where function call came from
    }

    function mint(address receiver, uint amount) public {
        if (msg.sender != minter) return;
        balances[receiver] += amount;
    }

    function send(address receiver, uint amount) public {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Sent(msg.sender, receiver, amount);
    }
}
```

- Variable type address is a 160-bit value that does not allow any arithmetic operations

- Without **public** keyword other contracts have no way to access the variables

- The **public** keyword generates a function to access the current value of the state

```
function minter() returns (address) { return minter; }    <- Example of what function will look like
```

- The getter function created by **public** keyword for mapping is more complex

```
function balances(address _account) public view returns (uint) {
    return balances[_account];
}
```

- User interfaces can listen for an event and it will receive all the argument sent in the event

```
// To listen to the event above
Coin.Sent().watch({}, '', function(error, result) {
    if (!error) {
        console.log("Coin transfer: " + result.args.amount +
            " coins were sent from " + result.args.from +
            " to " + result.args.to + ".");
        console.log("Balances now:\n" +
            "Sender: " + Coin.balances.call(result.args.from) +
            "Receiver: " + Coin.balances.call(result.args.to));
    }
})
```

- The automatically generated functions, ```balances```, can be called from the user interface

- The functions that will actually end up with the contract and can be called by users and contracts are ```mint``` and ```send```
