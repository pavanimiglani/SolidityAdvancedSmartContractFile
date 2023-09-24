# SolidityAdvancedSmartContractFile 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// TargetContract is the contract that holds the state variables.
contract TargetContract {
    uint256 public value;

    function setValue(uint256 _newValue) external {
        value = _newValue;
    }
}

// CallerContract delegates calls to TargetContract to update state variables.
contract CallerContract {
    address public targetContractAddress;
    address public owner;

    constructor(address _targetContractAddress) {
        targetContractAddress = _targetContractAddress;
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the owner can call this function");
        _;
    }

    // Delegate call to set the value in TargetContract
    function setValueInTargetContract(uint256 _newValue) external onlyOwner {
        // Perform a delegate call to TargetContract's setValue function
        (bool success, ) = targetContractAddress.delegatecall(
            abi.encodeWithSignature("setValue(uint256)", _newValue)
        );

        require(success, "Delegate call to TargetContract failed");
    }

    // Get the value from TargetContract
    function getValueFromTargetContract() external view returns (uint256) {
        (bool success, bytes memory data) = targetContractAddress.staticcall(
            abi.encodeWithSignature("value()")
        );

        require(success, "Static call to TargetContract failed");

        // Decode and return the result
        return abi.decode(data, (uint256));
    }
}


Steps for executing the code- 

1. TargetContract has a single state variable value, which can be set using the setValue function.

2. CallerContract is the contract that delegates calls to TargetContract. It has a constructor that accepts the address of the TargetContract and an owner variable to restrict access to certain functions.

3. The setValueInTargetContract function in CallerContract uses delegate call to invoke the setValue function in TargetContract with the provided value.

4. The getValueFromTargetContract function in CallerContract uses a static call to read the value from TargetContract.
   
5. Specifically we can check the value of the value variable in TargetContract after calling setValueInTargetContract, and we can retrieve it using getValueFromTargetContract. 
