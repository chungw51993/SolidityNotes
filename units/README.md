## Units and Globally Available Variables

#### Ether Units
  - Literal number can have suffix of ```wei```, ```finney```, ```szabo```, or ```ether``` to convert between the subdenominations of Ether
  - Ether currency numbers without a postfix will be assumed as ```wei```
  - ```2 ether == 2000 finney``` equals ```true```

#### Time Units
  - Suffixes ```seconds```, ```minutes```, ```hours```, ```days```, ```weeks```, and ```years``` can be used after literal numbers to convert between units of time
  - ```1 == 1 seconds```, ```1 minutes === 60 seconds``` and so on
  - Don't perform calendar calculations using these units because not every year equals 365 days and not every day has 24 hours because of leap seconds
  - These suffixes cannot be applied to variables but if you want to interpret some input variable
```
function f(uint start, uint daysAfter) public {
    if (now >= start + daysAfter * 1 days) {
      // ...
    }
}
```

#### Special Variables and Functions
  - These special variables and functions are mainly used to provide info about the blockchain
  - Block and Transaction Properties
    - ```block.blockhash(uint blockNumber) returns (bytes32)```: hash of the given block - only works for only 256 recent blocks
    - ```block.coinbase(address)```: current miner's address
    - ```block.difficulty(uint)```: current difficulty
    - ```block.gaslimit(uint)```: current gaslimit
    - ```block.number(uint)```: current number
    - ```block.timestamp(uint)```: current timestamp as seconds
    - ```msg.data(bytes)```: complete calldata
    - ```msg.gas(uint)```: remaining gas
    - ```msg.sender(address)```: current sender
    - ```msg.sig(bytes4)```: first four bytes of the calldata (function identifier)
    - ```msg.value(uint)```: number of wei sent
    - ```now(uint)```: current block timestamp (alias for ```block.timestamp```)
    - ```tx.gasprice(uint)```: gas price of the transaction
    - ```tx.origin(address)```: sender of the transaction (full call chain)
  - Error Handling
    - ```assert(bool condition)```: throws if the condition is not met - used for internal errors
    - ```require(bool condition)```: throws if the condition is not met - used for inputs or external errors
    - ```revert()```: abort execution and revert
  - Mathematical and Cryptographic Functions
    - ```addmod(uint x, uint y, uint k) returns (uint)```: compute ```(x + y) % k``` where the addition is performed with arbitrary precision and does not wrap around at ```2**256```
    - ```mulmod(uint x, uint y, uint k) returns (uint)```: compute ```(x + y) % k``` where the multiplication is performed with arbitrary precision and does not wrap around at ```2**256```
    - ```keccak256(...) returns (bytes32)```: compute Ethereum-SHA-3 (Keccak-256) hash
    - ```sha256(...) returns (bytes32)```: compute SHA-256 hash
    - ```sha3(...) returns (bytes32)``` : alias to ```keccak256```
    - ```ripemd160(...) returns (bytes32)```: compute RIPEMD-160 hash
    - ```ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)```: recover the address associated with the public key or return zero on error
    - All hash functions concatenates arguments without any paddings
    - If padding is necessary you can use ```keccak256("\x00\x12")``` is the same as ```keccak256(uint16(0x12))```
```
// These examples are all identical
keccak256("ab", "c")
keccak256("abc")
keccak256(0x616263)
keccak256(6382179)
keccak256(97, 98, 99)
```
  - Address Related
    - ```<address>.balance(uint256)```: balance of the address in Wei
    - ```<address>.transfer(uint256 amount)```: send given amount of Wei to address
    - ```<address>.send(uint256 amount) returns (bool)```: send given amount of Wei to address
    - ```<address>.call(...) returns (bool)```: issue low-level ```CALL```
    - ```<address>.callcode(...) returns (bool)```: issue low-level ```CALLCODE```
    - ```<address>.delegatecall(...) returns (bool)```: issue low-level ```DELEGATECALL```
  - Contract Related
    - ```this```: current contract, explicitly convertible to address
    - ```selfdestruct(address recipient)```: destroy the current contract, sending its funds to the given address
    - ```suicide(address recipient)```: alias to ```selfdestruct```
