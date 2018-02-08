## Event and Transaction Example

```
pragma solidity ^0.4.0;

contract Transaction {

    // This set up a event that can be triggered to log the values
    event SenderLogger(address);
    event ValueLogger(uint);

    address private owner;

    modifier isOwner {
        require(owner == msg.sender);
        _;
    }

    modifier validValue {
        assert(msg.value >= 1 ether);
        _;
    }

    function Transaction() {
        owner = msg.sender;
    }

    function () payable isOwner validValue {
        // This triggers the event and logs the values
        SenderLogger(msg.sender);
        ValueLogger(msg.value);
    }
}
```