## Data Type Example

```
pragma solidity ^0.4.0;

contract DataTypes {

    bool myBool = true; // or false

    int8 myInt = -128; // Can be negative and positive
    uint8 myUInt = 255; // Can't be negative

    string myString; // String is basically array
    uint8[] myStringArr;

    byte myValue;
    bytes1 myBytes1;
    bytes32 myBytes32;

    // fixed8x1 myFixed;
    // ufixed myUFixed;

    enum Action {ADD, REMOVE, UPDATE}
    Action myAction = Action.ADD;

    address myAddress;

    function assignAddress() {
        myAddress = msg.sender;
        myAddress.balance;
        myAddress.transfer(10);
    }

    uint[] myIntArr = [1, 2, 3];

    function arrFunc() {
        myIntArr.push(1);
        myIntArr.length;
        myIntArr[0];
    }

    uint[10] myFixedArr;

    struct Account {
        uint balance;
        uint dailyLimit;
    }

    Account myAccount;

    function structFunc() {
        myAccount.balance = 100;
    }

    mapping (address => Account) _accounts;

    function () payable {
        _accounts[msg.sender].balance += msg.value;
    }

    function getBalance() returns (uint) {
        return _accounts[msg.sender].balance;
    }
}
```

- int8 to int256 in steps of 8
- uint8 to uint256 in steps of 8
- bytes1 to bytes32 in steps of 1