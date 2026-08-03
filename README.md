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
