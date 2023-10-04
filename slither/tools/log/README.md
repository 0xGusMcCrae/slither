# Slither-Log

Slither-Log is a tool to add stack tracing capabilities to Solidity's Hardhat development framework.

## Usage

To generate a git patch for a solidity file:

    slither-log $TARGET

To apply a patch (from same directory slither-log was called in):

    git apply --ignore-whitespace crytic-export/slither-log/$PATCHFILE

To remove a patch:

    git apply -R crytic-export/slither-log/$PATCHFILE

It's recommended to remove a patch before making edits, otherwise the patch will no longer be valid for removal.

If patch no longer applies, remove portions from the edited file from the patch file and it will still work on un-edited files.

`slither-log --help` for more options

    Positional arguments:
    filename              The filename of the contract to analyze.

    Patching options:
    --force-patch         Automatically apply the generated patch
    --allow-bytes-conversion
                            Hardhat console can't easily print bytes. This will cast any static bytes arrays to bytes32, then to uint256 to allow for easier logging. User can
                            convert back to the appropriate bytes size manually if desired. Default behavior is to not log any bytes. This option does not allow for logging of dynamic bytes arrays.
    --whitelist-function WHITELIST_FUNCTION
                            Edit only specified function(s). Call several times to add more than one function. Use canonical name - Contract.functionName(paramTypes).
    --blacklist-function BLACKLIST_FUNCTION
                            Exclude function(s) from editing. Call several times to add more than one function. Use canonical name - Contract.functionName(paramTypes).
    --whitelist-contract WHITELIST_CONTRACT
                            Edit only specified contract(s). Call several times to add more than one contract.
    --blacklist-contract BLACKLIST_CONTRACT
                            Exclude contract(s) from editing. Call several times to add more than one contract.


After applying the patch, run tests normally in Hardhat to have stack tracing logs printed to the console.

## Troubleshooting/FAQ

- If experiencing trouble with undetected imports, try running on the whole directory via `slither-log .` and whitelisting contracts as needed. This is an issue with Slither's recognition of Hardhat import mappings.

- If getting a "patch does not apply" error when attempting to remove a patch, delete lines related to the relevant files from the patch and then remove. Navigate manually to the files for which the patch did not apply and `ctrl+z` to remove the patch or delete console.log lines manually if needed.

## Sample Output:

    Enter MockStaking.calcRewardPerToken()
    Return from MockStaking.calcRewardPerToken(), returns (rewardPerToken + (rewardRate * (block.timestamp - updatedAt) * 1e18) / totalStaked) = 164121592693021
    Enter MockStaking.earned(address), (_account = 0x70997970c51812dc3a010c7d01b50e0d17dc79c8)
    Enter ERC20.totalSupply()
    Return from ERC20.totalSupply(), returns (_totalSupply) = 68320420
    Enter MockStaking.calcRewardPerToken()
    Return from MockStaking.calcRewardPerToken(), returns (rewardPerToken + (rewardRate * (block.timestamp - updatedAt) * 1e18) / totalStaked) = 164121592693021
    Enter ERC20.totalSupply()
    Return from ERC20.totalSupply(), returns (_totalSupply) = 68320420
    Return from MockStaking.earned(address), returns (earnedRewards + rewards[_account]) = 68
    Enter MockStaking.withdraw(uint256), (_amount = 320420)
    Enter SafeERC20.safeTransfer(IERC20,address,uint256), (token = unloggable_type, to = 0x70997970c51812dc3a010c7d01b50e0d17dc79c8, value = 320420)
    Enter SafeERC20._callOptionalReturn(IERC20,bytes), (token = unloggable_type, data = unlogged_bytes)
    Enter Address.functionCall(address,bytes,string), (target = 0x59b670e9fa9d0a427751af201d676719a970857b, data = unlogged_bytes, errorMessage = SafeERC20: low-level call failed)    
    Return from Address.functionCall(address,bytes,string), returns (functionCallWithValue(target, data, 0, errorMessage)) = unlogged_bytes
    Enter Address.functionCallWithValue(address,bytes,uint256,string), (target = 0x59b670e9fa9d0a427751af201d676719a970857b, data = unlogged_bytes, value = 0, errorMessage = %s)      
    SafeERC20: low-level call failed
    Enter Address.isContract(address), (account = 0x59b670e9fa9d0a427751af201d676719a970857b)
    Return from Address.isContract(address), returns (account.code.length > 0) = true
    Enter ERC20.transfer(address,uint256), (to = 0x70997970c51812dc3a010c7d01b50e0d17dc79c8, amount = 320420)
    Enter Context._msgSender()
    Return from Context._msgSender(), returns (msg.sender) = 0x4ed7c70f96b99c776995fb64377f0d4ab3b0e1c1
    Enter ERC20._transfer(address,address,uint256), (from = 0x4ed7c70f96b99c776995fb64377f0d4ab3b0e1c1, to = 0x70997970c51812dc3a010c7d01b50e0d17dc79c8, amount = 320420)
    Return from ERC20.transfer(address,uint256), returns (true) = true
    Return from Address.functionCallWithValue(address,bytes,uint256,string), returns (verifyCallResult(success, returndata, errorMessage)) = unlogged_bytes
    Enter Address.verifyCallResult(bool,bytes,string), (success = true, returndata = unlogged_bytes, errorMessage = SafeERC20: low-level call failed)
    Return from Address.verifyCallResult(bool,bytes,string), returns (returndata) = unlogged_bytes
    Enter MockToken.updateCirculatingSupply()
    Enter ERC20.balanceOf(address), (account = 0x4ed7c70f96b99c776995fb64377f0d4ab3b0e1c1)
    Return from ERC20.balanceOf(address), returns (_balances[account]) = 0
    Enter MockToken.getCurrentSupply()
    Enter ERC20.totalSupply()
    Return from ERC20.totalSupply(), returns (_totalSupply) = 68320420
    Return from MockToken.getCurrentSupply(), returns (totalSupply()) = 68320420
    Enter ERC20.totalSupply()
    Return from ERC20.totalSupply(), returns (_totalSupply) = 68320420
    Enter MockToken.getCirculatingSupply()
    Return from MockToken.getCirculatingSupply(), returns (circulatingSupply) = 68320420

