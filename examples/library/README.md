## Library and Import Example

```
pragma solidity ^0.4.0;

library IntExtended {

    function increment(uint _self) returns (uint) {
        return _self + 1;
    }

    function decrement(uint _self) returns (uint) {
        return _self - 1;
    }

    function incrementByValue(uint _self, uint value) returns (uint) {
        return _self + value;
    }

    function decrementByValue(uint _self, uint value) returns (uint) {
        return _self - value;
    }
}
```

```
pragma solidity ^0.4.0;

// This imports the library
import "browser/library.sol";

contract TestLibrary {
    // Extends uint functions with IntExtended functions
    using IntExtended for uint;

    function testIncrement(uint _base) returns (uint) {
        // You can call the library after import
        return IntExtended.increment(_base);
    }

    function testDecrement(uint _base) returns (uint) {
        return IntExtended.decrement(_base);
    }

    function testIncrementByValue(uint _base, uint _value) returns (uint) {
        // You can use any functions from the library on uint since you extended its functions
        return _base.incrementByValue(_value);
    }

    function testDecrementByValue(uint _base, uint _value) returns (uint) {
        return _base.decrementByValue(_value);
    }
}
```

- You can import Solidity files that are remote (import "github.com/chungw51993/test.sol")
