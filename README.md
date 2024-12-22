# Metacrafters Avalanche Advanced Proof - Avalanche Subnet

Embark on an exciting journey to create a decentralized financial (DeFi) gaming experience inspired by DeFi Kingdoms, built on Avalanche. Immerse yourself in a world where players collect, build, and battle with digital assets while earning rewards through engaging gameplay mechanics.

## Overview

This guide focuses on the technical aspects of setting up an Ethereum Virtual Machine (EVM) subnet on Avalanche and deploying foundational smart contracts to replicate a DeFi Kingdom-like experience. For simplicity, we won’t explore the broader details of DeFi economics or value generation but instead concentrate on the steps necessary to establish your DeFi gaming ecosystem.

## Key Steps

1. **Set Up Your EVM Subnet**
   - Follow the Avalanche documentation and this guide to create a custom EVM subnet.

2. **Define Your Native Currency**
   - Create a unique native currency that serves as the in-game currency for your DeFi Kingdom clone.

3. **Connect to MetaMask**
   - Integrate your EVM subnet with MetaMask using the steps outlined in this guide.

4. **Deploy Smart Contracts**
   - Use Solidity and Remix to deploy essential contracts for game mechanics, such as battling, exploring, and trading. These contracts will define game rules, tokens, liquidity pools, and more.

---

## Avalanche Subnet Overview

An Avalanche subnet is a custom, independent blockchain network within the Avalanche ecosystem. It operates as a standalone network with its own rules, consensus mechanism, and token economics. Subnets are scalable and tailored to specific use cases, making them ideal for creating gaming ecosystems.

### Setting Up an Avalanche Subnet

#### Install Avalanche-CLI
Run the following command to install the latest Avalanche-CLI binary:
```bash
curl -sSfL https://raw.githubusercontent.com/ava-labs/avalanche-cli/main/scripts/install.sh | sh -s
```

#### Create an EVM Subnet
Avalanche’s Subnet-EVM supports features like custom fee tokens, airdrops, configurable gas parameters, and stateful precompiles. To create an EVM subnet:

1. Run the command:
   ```bash
   avalanche subnet create mySubnet
   ```
2. Configure the subnet by selecting the `Subnet-EVM` option and setting parameters such as:
   - **Chain ID:** A unique positive integer (e.g., `12345567`).
   - **Token Symbol:** Specify a native token symbol (e.g., `MYSUBNET`).
   - **Gas Settings:** Choose the default low-disk use/low-throughput option (1.5 mil gas/s).
   - **Airdrops:** Optionally airdrop tokens to a default address (not for production use).

3. Deploy the subnet:
   ```bash
   avalanche subnet deploy mySubnet
   ```
   This deploys your subnet locally and displays subnet details for further interaction.

---

## Smart Contracts

### ERC20 Contract Example

The ERC20 contract facilitates a fungible token standard widely used in blockchain applications.

#### Code:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Solidity by Example";
    string public symbol = "SOLBYEX";
    uint8 public decimals = 18;

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address sender, address recipient, uint amount) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

### Vault Contract Example

The Vault contract facilitates deposits and withdrawals of ERC20 tokens.

#### Code:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint amount) external returns (bool);
}

contract Vault {
    IERC20 public immutable token;
    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function deposit(uint _amount) external {
        uint shares = totalSupply == 0
            ? _amount
            : (_amount * totalSupply) / token.balanceOf(address(this));

        balanceOf[msg.sender] += shares;
        totalSupply += shares;
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        balanceOf[msg.sender] -= _shares;
        totalSupply -= _shares;
        token.transfer(msg.sender, amount);
    }
}
```

### Summary

These contracts form the foundation for managing your DeFi gaming assets, including token issuance and secure storage. Use these examples to customize and expand functionality to meet your specific gaming requirements.
