# Vesting-Contract
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Vesting {
    IERC20 public token;
    address public beneficiary;
    uint256 public start;
    uint256 public duration;
    uint256 public released;

    constructor(address _token, address _beneficiary, uint256 _duration) {
        token = IERC20(_token);
        beneficiary = _beneficiary;
        start = block.timestamp;
        duration = _duration;
    }

    function release() public {
        uint256 vested = vestedAmount();
        uint256 unreleased = vested - released;
        require(unreleased > 0, "No tokens to release");
        released += unreleased;
        token.transfer(beneficiary, unreleased);
    }

    function vestedAmount() public view returns (uint256) {
        if (block.timestamp < start) return 0;
        if (block.timestamp >= start + duration) return token.balanceOf(address(this));
        return (token.balanceOf(address(this)) * (block.timestamp - start)) / duration;
    }
}

interface IERC20 {
    function transfer(address to, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}
