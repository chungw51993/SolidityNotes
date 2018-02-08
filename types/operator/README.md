## Operator on Types

#### Operators Involving LValues
  - ```a``` is an LValue, LValue is a variable or something that can be assigned to
  - Operators ```+=```, ```-=```, ```*=```, ```/=```, ```%=```, ```|=```, ```&=```, and ```^=``` can be used
  - ```a++``` and ```a--``` returns value before the change
  - ```++a``` and ```--a``` return value after the change
  - Delete
    - ```delete a``` assigns the initial value for the type so if ```a``` is an integer it will be ```a = 0```
    - It can be used on arrays, dynamic array will have length of zero and static array will have the same length but all the values reset
    - For struct it will reset all members value
    - It has no effect on whole mappings
    - Delete behaves like an assignment
```
pragma solidity ^0.4.0;

contract DeleteExample {
    uint data;
    uint[] dataArray;

    function f() public {
        uint x = data;
        delete x; // sets x to 0, does not affect data
        delete data; // sets data to 0, does not affect x which still holds a copy
        uint[] storage y = dataArray;
        delete dataArray; // this sets dataArray.length to zero, but as uint[] is a complex object, also
        // y is affected which is an alias to the storage object
        // On the other hand: "delete y" is not valid, as assignments to local variables
        // referencing storage objects can only be made from existing storage objects.
    }
}
```

#### Conversions between Types
  - Implicit Conversions
    - Compiler will try to convert if you applied operator to a different type
    - Implicit conversion only happens if it makes sense semantically and no information is lost
    - ```uint8``` is convertible to ```uint16``` and ```int128``` to ```int256``` but ```int8``` to ```uint256``` is not possible becuse ```uint256``` can't hold ```-1```
  - Explicit Conversions
    - If compiler doesn't convert you can do it yourself but it may have unexpected behaviors
```
int8 y = -3;
uint x = uint(y);
```
    - At the end of this code ```x``` will be ```0xfffff..fd``` (64 hex characters), which is -3 in representation of 256 bits
    - If the type is smaller it will cut higher-order bits off
```
uint32 a = 0x12345678;
uint16 b = uint16(a); // b will be 0x5678 now
```

#### Type Deduction
  - For convenience it is not always necessary to specify the type of a variables
  - Compiler will automatically infer to the type of the first expression
```
uint24 x = 0x123;
var y = x;
```
  - ```y``` will be ```uint24```
  - You can not use ```var``` for function parameters or return parameters


