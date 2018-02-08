## Variable Types

**A contract in the sense of Solidity is a collection of code (its functions) and data(its state) that resides at a specific address on the Ethereum blockchain.**

#### Booleans
- ```bool```: Possible values are ```true``` or ```false```
- Operators
  - ```!``` logical negation
  - ```&&``` logical conjuntion, "and"
  - ```||``` logical disjunction, "or"
  - ```==``` equality
  - ```!=``` inequality

#### Integers
- ```int```/```uint```: Signed and unsigned integers of various sizes
- ```uint8``` to ```uint256``` in steps of 8
- ```int8``` to ```int256``` as well
- ```int``` and ```uint``` are aliases for ```int256``` and ```uint256```
- Operators
  - Comparisons: ```<=```, ```<```, ```==```, ```!=```, ```>=```, ```>``` (evaluate to bool)
  - Bit operators: ```&```, ```|```, ```^``` (bitwise exclusive), ```~``` (bitwise negation)
  - Arithmetic operators: ```+```, ```-```, unary ```-```, unary ```+```, ```*```, ```/```, ```%```, ```**``` (exponentiation), ```<<``` (left shift), ```>>``` (right shift)
- Division always truncates and division by zero will throw a runtime exception
- The result of a shift operation is the type of the left operand. The expression ```x << y``` is equivalent to ```x * 2**y```, and ```x >>> y``` is equivalent to ```x / 2**y```. This means that shifting negative numbers sign extends. Shifting by a negative amount throws a runtime exception

#### Address
- ```address```: Holds a 20 byte value (size of an Ethereum address)
- Address also have members and serve as a base for all contracts
- Operators
  - ```<=```, ```<```, ```==```, ```!=```, ```>=``` and ```>```

##### Members(Prototypes) of Addresses
  - ```balance``` and ```transfer```
  - It is possible to query the balace of an address using the property ```balance``` and to send Ether (in units of wei) to an address using the ```transfer``` function
```
address x = 0x123;
address myAddress = this;
if (x.balance < 10 && myAddress.balance >= 10) x.transfer(10);
```
  - ```send```: Low-level counterpart of ```transfer``` but if the execution fails the current contract will not stop with an exception but return ```false```
  - There are some dangers in using ```send```, the transfer fails if the call stack depth is at 1024 and it also fails if the recipient runs out of gas. Try to use ```transfer``` to make transactions.
  - ```call```, ```callcode``` and ```delegatecall```
  - To interact with contracts that do not adhere to the Application Binary Interface, ```call``` is provided which takes an arbitrary number of arguments of any types
```
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.call("register", "MyName");
nameReg.call(bytes4(keccak256("fun(uint256)")), a);
```
  - ```call``` returns a boolena indicating whether the invoked function terminated (true) or caused an EVM exception (false)
  - You can adjust the gas and Ether value when using call
```
nameReg.call.gas(1000000)("register", "MyName");
nameReg.call.value(1 ether)("register", "MyName");
nameReg.call.gas(1000000).value(1 ether)("register", "MyName");
```
  - ```delegatecall``` can be used similarly but it only uses the code of the given address and all other aspects are taken from the current contract. The purpose of this is to use library code which is stored in another contract.
  - ```callcode``` is a limited variant and it does not provide access to the original ```msg.sender``` and ```msg.value```
  - **All three functions are low-level functions and should be used with care. Specifically, any unknown contract might be malicious and if you call it, you hand over control to that contract which could in turn call back into your contract, so be prepared for changes to your state variables when the call returns.**

#### Fixed-size byte arrays
  - ```bytes1``` to ```bytes32```
  - ```byte``` is an alias for ```bytes1```
  - Operators
    - Comparisons: ```<=```, ```<```, ```==```, ```!=```, ```>=```, ```>``` (evaluate to ```bool```)
    - Bit operators: ```&```, ```|```, ```^``` (bitwise exclusive or), ```~``` (bitwise negation), ```<<``` (left shift), ```>>``` (right shift)
    - Index access: If ```x``` is of type ```bytes1```, then ```x[k]``` for ```0 <= k < 1``` returns the ```k```th byte (read-only)
  - The shift operator works with any integer type as right operand but will return the type of the left operand

##### Members(Prototypes) of Fixed-size byte arrays
  - ```length```: returns the fixed length of the byte array

#### Dynamically-size byte array
  - ```bytes```: Dyamic byte array
  - ```string```: Dynamic UTF-8-encoded string
  - If you can limit the length to a certain bytes always use one of ```bytes1``` to ```bytes32``` because they are cheaper

#### Functions
  - _Internal_ and _External_ functions
  - _Internal_ functions can only be called inside the current contract (current code block)
  - _External_ functions consist of an address and a function signature and they can be called from external function calls
```
function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]
```
  - Parameter types can be empty but return types cannot be empty and if function should not return anything omit ```returns (<return types>)```
  - By default function types are internal so ```internal``` can be omitted
```
// Example of using internal functions
pragma solidity ^0.4.16;

library ArrayUtils {
  // internal functions can be used in internal library functions because
  // they will be part of the same code context
  function map(uint[] memory self, function (uint) pure returns (uint) f)
    internal
    pure
    returns (uint[] memory r)
  {
    r = new uint[](self.length);
    for (uint i = 0; i < self.length; i++) {
      r[i] = f(self[i]);
    }
  }
  function reduce(
    uint[] memory self,
    function (uint, uint) pure returns (uint) f
  )
    internal
    pure
    returns (uint r)
  {
    r = self[0];
    for (uint i = 1; i < self.length; i++) {
      r = f(r, self[i]);
    }
  }
  function range(uint length) internal pure returns (uint[] memory r) {
    r = new uint[](length);
    for (uint i = 0; i < r.length; i++) {
      r[i] = i;
    }
  }
}

contract Pyramid {
  using ArrayUtils for *;
  function pyramid(uint l) public pure returns (uint) {
    return ArrayUtils.range(l).map(square).reduce(sum);
  }
  function square(uint x) internal pure returns (uint) {
    return x * x;
  }
  function sum(uint x, uint y) internal pure returns (uint) {
    return x + y;
  }
}
```
```
// Example of using external functions
pragma solidity ^0.4.11;

contract Oracle {
  struct Request {
    bytes data;
    function(bytes memory) external callback;
  }
  Request[] requests;
  event NewRequest(uint);
  function query(bytes data, function(bytes memory) external callback) public {
    requests.push(Request(data, callback));
    NewRequest(requests.length - 1);
  }
  function reply(uint requestID, bytes response) public {
    // Here goes the check that the reply comes from a trusted source
    requests[requestID].callback(response);
  }
}

contract OracleUser {
  Oracle constant oracle = Oracle(0x1234567); // known contract
  function buySomething() {
    oracle.query("USD", this.oracleResponse);
  }
  function oracleResponse(bytes response) public {
    require(msg.sender == address(oracle));
    // Use the data
  }
}
```