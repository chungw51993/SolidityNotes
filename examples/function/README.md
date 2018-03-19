## Contract Function

```
pragma ^0.4.0;

contract Inbox {
  string public message;

  function Inbox (string initialMessage) public {   // Calling a function
    message = initialMessage;
  }

  function setMessage(string newMessage) public {   // Sending a transaction to a function
    message = newMessage;
  }
}
```

- If the function is modifying contract's data it will be sending transaction to the function
  - Transaction takes time to execute
  - Returns the transaction hash
  - It costs money to send transaction
  - Transaction will NEVER return data only hash
- If the funciton is not modifying any data it will just call the function
  - It executes instantly
  - Returns contract data
  - Free