## Assembly

```
Functional Assembly
pragma solidity ^0.4.0;

contract Assembly {
    function nativeLoops() public returns (uint _r) {
        for (uint i = 0; i < 10; i++) {
            _r++;
        }
    }

    function asmLoops() public returns (uint _r) {
        // inline assembly
        assembly {
            let i := 0
            loop:
            i := add(i, 1)
            _r := add(_r, 1)
            jumpi(loop, lt(i, 10))
        }
    }

    function nativeConditional(uint _v) public returns (uint) {
        if (5 == _v) {
            return 55;
        } else if (6 == _v) {
            return 66;
        }

        return 11;
    }

    function asmConditional(uint _v) public returns (uint _r) {
        assembly {
            switch _v
            case 5 {
                _r := 55
            }
            case 6 {
                _r := 66
            }
            default {
                _r := 11
            }
        }
    }

    function asmReturns(uint _v) public returns (uint) {
        assembly {
            let _ptr := add(msize(), 1)
            mstore(_ptr, _v)
            return(_ptr, 0x20)
        }
    }
}
```

```
Instructional Assembly

  function inlineAsmLoops() public returns (uint _r) {
      assembly {
          0 // i
          10 // max

          loop:
          // i := add(i, 1)
          dup2
          1
          add
          swap2 // swap i with dup2
          pop

          // _r := add(_r, 1)
          dup3
          1
          add
          swap3
          pop

          // lt(i, 10)
          dup1
          dup3
          lt

          // jumpi(loop, lt(i, 10))
          loop
          jumpi

          pop
          pop
      }
  }
```

- Don't use inline assembly when you don't have to
- Use functional assembly since it is easier to understand