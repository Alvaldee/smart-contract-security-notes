# Reentrancy vulnerability

## Summary
Reentrancy is a vulnerability that occurs when a contract performs an external call before updating its internal state. Because external calls transfer control flow to potentially untrusted code, an attacker can re-enter the vulnerable function while the contract’s storage is still in its pre-call state, allowing the same logic to execute multiple times within a single transaction. This commonly results in repeated withdrawals of funds.

---

## Root Cause
At the EVM level, external calls such as `call` transfer execution to another contract without enforcing any single-entry or locking mechanism. If critical state updates are performed after the external call, the contract remains in an inconsistent state during execution of the callee, enabling reentrant execution paths.

---

## Vulnerable Pattern
A common vulnerable pattern is a function makes an external call before updating its own important state, allowing the function to be called repetitively.

```solidity
function withdraw() external {
        uint256 amount = balances[msg.sender];
        require(amount > 0);

        (bool ok, ) = msg.sender.call{value: amount}("");
        require(ok, "Send failed");

        balances[msg.sender] = 0;
    }
```

