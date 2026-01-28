# Ethernaut — Level 1: Fallback

## Summary
The contract's receive() function changes ownership to anyone who has made even a trivial contribution, without verifying if they should have administrative rights. 
This happens because the fallback function lacks proper authorization checks while modifying critical state variables. The vulnerability allows complete contract takeover through what appears to be an innocuous Ether transfer.

## Vulnerability
Improper access control via fallback function allowing ownership takeover.

## Root Cause
The contract updates ownership based on msg.value without verifying caller intent.

## Exploit Strategy
1. Contribute minimal ETH
2. Trigger fallback via direct transfer
3. Call withdraw as owner

## Impact
Attacker gains full ownership and drains contract balance.

## Mitigation
Avoid logic in fallback/receive functions; enforce explicit access checks.

## Key Takeaway
Never modify sensitive state variables (especially ownership/authorization) in fallback or receive functions without explicit, robust access controls. 
These automatic functions should handle simple Ether transfers only, while all privilege changes belong in separate, well-protected functions.
