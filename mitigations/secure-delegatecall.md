# Secure Usage of delegatecall

## Overview
`delegatecall` executes code from another contract **in the caller's storage context**.
While powerful, incorrect usage can lead to full contract takeover.

This document outlines best practices to safely use `delegatecall` in production smart contracts.

---

## Common Risks
- Storage slot collisions
- Unauthorised logic replacement
- State corruption
- Complete loss of contract ownership

---

## Mitigation Strategies

### 1. Use Standardized Storage Layouts (EIP-1967)
When using upgradeable contracts, always follow standardized storage slots.

```solidity
bytes32 internal constant IMPLEMENTATION_SLOT =
  0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;
```
This helps with preventing storage collision and avoiding arbitrary storage overwrite

### 2. Restrict Who Can Change Delegatecall Targets.
Never allow public or unprotected functions to modify implementation addresses.
```solidity
function upgrade(address newTmpl) external onlyOwner {
  implementation = newIpl;
}
```
This prevents malicious logic injection as public setters would equate to instant exploit.

### 3. Use Libraries over Delegatecall
If state sharing is not required, use `library` calls instead.

```solidity
using SafeMath for uint256;
```
No storage collision risk present as libraries are stateless.

### 4. Validate Implementation Code
Before upgrading, ensure:
- Code exists (`extcodesize`)
- Implementation is trusted
- No selfdestruct logic

```solidity
require(newImpl.code.length > 0, "Invalid implementation");
```

### 5. Use Established Frameworks
Prefer audited implementations:
- OpenZeppelin Upgradeable Contracts
- UUPS Pattern (EIP-1822)
Avoid rolling your own proxy logic unless necessary.
