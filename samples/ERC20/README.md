```
pragma solidity ^0.4.0;

// Constraint for ERC20 compliant Token
interface ERC20 {
    // Returns total supply of token
    function totalSupply() constant returns (uint _totalSupply);

    // Returns the balance of the address
    function balanceOf(address _owner) constant returns (uint balance);

    // Allows token to be sent
    function transfer(address _to, uint _value) returns (bool success);

    // Allows approved address to transfer
    function transferFrom(address _from, address _to, uint _value) returns (bool success);

    // Allows owner to approve third party address to transfer tokens
    function approve(address _spender, uint _value) returns (bool success);

    // Set how much approved address get
    function allowance(address _owner, address _spender) constant returns (uint remainer);

    event Transfer(address indexed _from, address indexed _to, uint _value);

    event Approval(address indexed _owner, address indexed _spender, uint _value);
}
```

```
pragma solidity ^0.4.0;

import "browser/ERC20.sol";

contract MyFirstToken is ERC20 {
    // Specify symbol for your token ex. BTC, ETH, SIG
    string public constant symbol = "MFT";

    // Specify name for your token ex. Bitcoin, Ethereum
    string public constant name = "My First Token";

    // Specify how many decimal points your token is going to use
    uint8 public constant decimals = 18;

    // Specify total supply of your token
    uint private constant __totalSupply = 1000;

    // Map all the address so it is accessible
    mapping (address => uint) private __balanceOf;
    mapping (address => mapping (address => uint)) private __allowances;

    function MyFirstToken() {
        __balanceOf[msg.sender] = __totalSupply;
    }

    function totalSupply() constant returns (uint _totalSupply) {
        _totalSupply = __totalSupply;
    }

    function balanceOf(address _addr) constant returns (uint balance) {
        return __balanceOf[_addr];
    }

    function transfer(address _to, uint _value) returns (bool success) {
        if (_value > 0 && _value <= balanceOf(msg.sender)) {
            __balanceOf[msg.sender] -= _value;
            __balanceOf[_to] += _value;
            Transfer(msg.sender, _to, _value);
            return true;
        }

        return false;
    }

    function transferFrom(address _from, address _to, uint _value) returns (bool success) {
        if (__allowances[_from][msg.sender] > 0 &&
            _value > 0 &&
            __allowances[_from][msg.sender] >= _value &&
            __balanceOf[_from] >= _value) {
            __balanceOf[_from] -= _value;
            __balanceOf[_to] += _value;
            __allowances[_from][msg.sender] -= _value;
            return true;
        }
        return false;
    }

    function approve(address _spender, uint _value) returns (bool success) {
        __allowances[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }

    function allowance(address _owner, address _spender) constant returns (uint remaining) {
           return __allowances[_owner][_spender];
    }
}
```

- This is the basic ERC20 Token
- ERC20 requires all the functions and events listed for the interface
- Without all the function the Token is not ERC20 compliant
