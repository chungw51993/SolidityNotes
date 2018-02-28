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

```
// Time based event
pragma solidity ^0.4.0;

contract TimeBased {

    mapping(address => uint) public _balanceOf;
    mapping(address => uint) public _expiryOf;
    uint private leaseTime = 600;

    modifier expire(address _addr) {
        if (_expiryOf[_addr] >= block.timestamp) {
            _expiryOf[_addr] = 0;
            _balanceOf[_addr] = 0;
        }
        _;
    }

    function lease()
        public
        payable
        expire(msg.sender)
        returns (bool) {
        require(msg.value == 1 ether);
        require(_balanceOf[msg.sender] == 0);
        _balanceOf[msg.sender] = 1;
        _expiryOf[msg.sender] = block.timestamp + leaseTime;
        return true;
    }

    function balanceOf()
        public
        returns (uint) {
        return balanceOf(msg.sender);
    }

    function balanceOf(address _addr)
        public
        expire(_addr)
        returns (uint) {
        return _balanceOf[_addr];
    }

    function expiryOf()
        public
        returns (uint) {
        return expiryOf(msg.sender);
    }

    function expiryOf(address _addr)
        public
        expire(_addr)
        returns (uint) {
        return _expiryOf[_addr];
    }
}
```

```
// Alarm based event
pragma solidity ^0.4.0;

interface AlarmWakeUp {
    function callback(bytes data) public;
}

contract AlarmService {

    struct TimeEvent {
        address addr;
        bytes data;
    }

    mapping(uint => TimeEvent[]) private _events;

    function set(uint _time) public returns (bool) {
        TimeEvent _timeEvent;
        _timeEvent.addr = msg.sender;
        _timeEvent.data = msg.data;
        _events[_time].push(_timeEvent);
    }

    function call(uint _time) public {
        TimeEvent[] timeEvents = _events[_time];
        for(uint i = 0; i < timeEvents.length; i++) {
            AlarmWakeUp(timeEvents[i].addr).callback(timeEvents[i].data);
        }
    }

}

contract AlarmTrigger is AlarmWakeUp {

    AlarmService private _alarmService;

    function AlarmTrigger() {
        _alarmService = new AlarmService();
    }

    function callback() public {

    }

    function setAlarm() public {
        _alarmService.set(block.timestamp + 60);
    }

}
```

- Use time based event when you have to use event