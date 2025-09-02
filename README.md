# Two-Step Transfer Object

A secure Move contract for Aptos blockchain that implements a two-step object transfer mechanism, providing enhanced safety for object ownership transfers.

## Overview

This contract enables secure object transfers between users through a two-step process:
1. **Initiate Transfer**: Object owner initiates a transfer to a pending recipient
2. **Claim Transfer**: Pending recipient claims ownership to complete the transfer

This pattern prevents accidental transfers to wrong addresses and gives recipients control over accepting ownership.

## Features

- **Safe Transfers**: Two-step process prevents accidental loss of objects
- **Revocable**: Original owner can revoke pending transfers before they're claimed
- **Event Tracking**: Comprehensive events for transfer lifecycle monitoring
- **Secure**: Built-in ownership verification and access controls

## Contract Functions

### `transfer_object`
```move
public entry fun transfer_object(owner: &signer, object_address: address, to: address)
```
Initiates a transfer of an object to a pending recipient.

**Parameters:**
- `owner`: Signer who currently owns the object
- `object_address`: Address of the object to transfer
- `to`: Address of the intended recipient

**Emits:** `ObjectExchangeCreated` event

### `claim_owner`
```move
public entry fun claim_owner(sender: &signer, object_address: address, previous_owner: address)
```
Claims ownership of a pending object transfer.

**Parameters:**
- `sender`: Signer claiming the object (must be the pending owner)
- `object_address`: Address of the object being claimed
- `previous_owner`: Address of the previous owner who initiated the transfer

**Emits:** `ObjectExchangeClaimed` event

### `revoke_transfer`
```move
public entry fun revoke_transfer(owner: &signer, object_address: address)
```
Revokes a pending object transfer, returning the object to the original owner.

**Parameters:**
- `owner`: Signer who initiated the original transfer
- `object_address`: Address of the object whose transfer should be revoked

**Emits:** `ObjectExchangeRevoked` event

## Events

### `ObjectExchangeCreated`
Emitted when a transfer is initiated.
- `object_address`: The object being transferred
- `previous_owner`: Current owner initiating the transfer
- `exchange_address`: Address of the exchange object holding the transfer
- `pending_owner`: Intended recipient

### `ObjectExchangeClaimed`
Emitted when a transfer is completed.
- `object_address`: The object that was transferred
- `old_owner`: Previous owner
- `exchange_address`: Address of the exchange object
- `new_owner`: New owner who claimed the object

### `ObjectExchangeRevoked`
Emitted when a transfer is revoked.
- `object_address`: The object whose transfer was revoked
- `current_owner`: Owner who revoked the transfer
- `exchange_address`: Address of the exchange object
- `pending_owner`: The recipient who didn't claim the object

## Error Codes

- `ENOT_OBJECT_OWNER (1)`: Caller is not the owner of the object
- `EWRONG_CLAIMER (2)`: Claimer is not the intended pending owner
- `EWRONG_OBJECT_ADDRESS (3)`: Object address mismatch

## Usage Example

1. **Initiate Transfer:**
   ```bash
   aptos move run --function-id "caas_framework::two_step_transfer_object::transfer_object" \
     --args address:0x123...abc address:0x456...def
   ```

2. **Claim Transfer:**
   ```bash
   aptos move run --function-id "caas_framework::two_step_transfer_object::claim_owner" \
     --args address:0x123...abc address:0x789...ghi
   ```

3. **Revoke Transfer (if needed):**
   ```bash
   aptos move run --function-id "caas_framework::two_step_transfer_object::revoke_transfer" \
     --args address:0x123...abc
   ```

## Development

### Prerequisites
- Aptos CLI
- Move compiler

### Building
```bash
aptos move compile
```

### Testing
```bash
aptos move test
```

## Security Considerations

- Always verify object addresses before initiating transfers
- Recipients should claim transfers promptly to avoid revocation
- Use events to monitor transfer status and detect any unexpected activity
- Ensure proper access controls are in place for your objects before using this transfer mechanism

## License

This project is provided as-is for educational and development purposes.