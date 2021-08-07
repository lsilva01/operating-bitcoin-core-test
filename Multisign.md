# Multisign

## 1. Specter Desktop Approach

### 1.1 Creating The Wallet

It the Bitcoin Core version is equals or higher than 0.21, Specter creates a descriptor wallet. Otherwise, a legacy wallet.

```python
use_descriptors = core_version >= 210000
if use_descriptors:
    # Use descriptor wallet
    rpc.createwallet(os.path.join(rpc_path, alias), True, True, "", False, True)
else:
    rpc.createwallet(os.path.join(rpc_path, alias), True)
```

Note that the second param, which is disable_private_keys, is true, so wallet is created in watchonly mode.

