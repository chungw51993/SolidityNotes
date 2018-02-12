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

```
pragma solidity ^0.4.0;

library Strings {

    function concat(string _base, string _value) internal returns (string) {
        // Convert string into byte array
        bytes memory _baseBytes = bytes(_base);
        bytes memory _valueBytes = bytes(_value);

        // Specify memory to not store values in the storage
        string memory _tmpValue = new string(_baseBytes.length + _valueBytes.length);
        bytes memory _newValue = bytes(_tmpValue);

        uint i;
        uint j;

        // When you increment undefined uint value it will start from 0
        for (i = 0; i < _baseBytes.length; i++) {
            _newValue[j++] = _baseBytes[i];
        }

        for (i = 0; i < _valueBytes.length; i++) {
            _newValue[j++] = _valueBytes[i];
        }

        return string(_newValue);
    }

    function strpos(string _base, string _value) internal returns (int) {
        bytes memory _baseBytes = bytes(_base);
        bytes memory _valueBytes = bytes(_value);

        assert(_valueBytes.length == 1);

        for (uint i = 0; i < _baseBytes.length; i++) {
            if (_baseBytes[i] == _valueBytes[0]) {
                return int(i);
            }
        }

        return -1;
    }
}

contract TestStrings {

    // Extend the string functions with Strings library
    using Strings for string;

    function testConcat(string _base) returns (string) {
        return _base.concat("_suffix");
    }

    function needleInHaystack(string _base) returns (int) {
        return _base.strpos("t");
    }

}
```

- int8 to int256 in steps of 8
- uint8 to uint256 in steps of 8
- bytes1 to bytes32 in steps of 1
- strings are arrays so convert it to bytes to manipulate it
- you have to specify **memory**, **storage** or **stack** when converting