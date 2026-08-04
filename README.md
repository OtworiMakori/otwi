This is the beginning of my public contributions focused on Base. Goal: learn, ship and participate in the Base Guild.
feat: add basic Hardhat/Foundry setup notes

Documented the recommended toolchain for developing on Base (Hardhat or Foundry + Base Sepolia). Next steps will include actual contract code.
feat: add basic ERC20 token skeleton

Started a minimal MyToken.sol based on OpenZeppelin standards. Preparing for testing token deployment on Base.
feat: add burn function to MyToken

Implemented a burn function so token holders can destroy their tokens. Completes basic ERC20 functionality for Base deployments.
feat: add totalSupply tracking to MyToken

Implemented proper totalSupply updates on mint and burn. Brings the token closer to standard ERC20 behavior on Base.
feat: implement approve and transferFrom in MyToken

Completed the core ERC20 interface by adding approve and transferFrom functions. Token is now more usable on Base.
feat: add allowance checking helpers to MyToken

Created view functions to easily check remaining allowances. Useful for frontends and testing on Base.
feat: add blacklist functionality to MyToken

Implemented a simple blacklist so the owner can restrict certain addresses. Extra security feature for tokens on Base.
feat: add snapshot feature to MyToken

Implemented basic balance snapshots so holders can prove balances at specific moments. Useful for airdrops and governance on Base.
feat: implement transfer with fee in MyToken

Added an optional transfer fee that can be enabled by the owner. Explores common token mechanics on Base.
feat: add max wallet limit to MyToken

Implemented a maximum balance per wallet to prevent concentration. Common anti-whale feature tested on Base.
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC20, Ownable {
    uint256 public transferFee = 0; // in basis points (100 = 1%)
    uint256 public maxWallet;
    mapping(address => bool) public blacklisted;

    constructor(uint256 _maxWallet) ERC20("MyToken", "MTK") Ownable(msg.sender) {
        maxWallet = _maxWallet;
        _mint(msg.sender, 1_000_000 * 10 ** decimals());
    }

    function setTransferFee(uint256 fee) external onlyOwner {
        require(fee <= 1000, "Max 10%");
        transferFee = fee;
    }

    function setBlacklist(address account, bool status) external onlyOwner {
        blacklisted[account] = status;
    }

    function _update(address from, address to, uint256 value) internal override {
        require(!blacklisted[from] && !blacklisted[to], "Blacklisted");
        if (from != address(0) && to != address(0)) {
            require(balanceOf(to) + value <= maxWallet, "Max wallet exceeded");
            uint256 fee = (value * transferFee) / 10000;
            if (fee > 0) {
                super._update(from, owner(), fee);
                value -= fee;
            }
        }
        super._update(from, to, value);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleToken {
    string public name = "SimpleToken";
    string public symbol = "STK";
    uint8 public decimals = 18;
    uint256 public totalSupply;
    mapping(address => uint256) public balanceOf;

    function mint(address to, uint256 amount) external {
        totalSupply += amount;
        balanceOf[to] += amount;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleVoting {
    uint256 public yesVotes;
    uint256 public noVotes;

    function voteYes() external {
        yesVotes++;
    }

    function voteNo() external {
        noVotes++;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EventCounter {
    uint256 public count;

    event Incremented(uint256 newCount);

    function increment() external {
        count++;
        emit Incremented(count);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Toggle {
    bool public isOn;

    function flip() external {
        isOn = !isOn;
    }
}
