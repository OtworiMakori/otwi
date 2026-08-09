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
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Status {
    string public status = "Building on Base";

    function update(string calldata _status) external {
        status = _status;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Note {
    string public note;

    function write(string calldata _note) external {
        note = _note;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AddressBook {
    mapping(address => address) public contacts;

    function setContact(address contact) external {
        contacts[msg.sender] = contact;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Points {
    mapping(address => uint256) public points;

    function addPoint() external {
        points[msg.sender]++;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Multiplier {
    uint256 public number = 1;

    function multiply(uint256 factor) external {
        number *= factor;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract EvenOdd {
    function isEven(uint256 number) external pure returns (bool) {
        return number % 2 == 0;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract OwnerSetter {
    address public owner;
    uint256 public value;

    constructor() {
        owner = msg.sender;
    }

    function set(uint256 _value) external {
        require(msg.sender == owner, "Not owner");
        value = _value;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ResetCounter {
    uint256 public count;

    function inc() external {
        count++;
    }

    function reset() external {
        count = 0;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NameStorage {
    string public name;

    function setName(string calldata _name) external {
        name = _name;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Comparator {
    function isBigger(uint256 a, uint256 b) external pure returns (bool) {
        return a > b;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MaxCounter {
    uint256 public count;
    uint256 public max = 100;

    function increment() external {
        require(count < max, "Max reached");
        count++;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Subtractor {
    function subtract(uint256 a, uint256 b) external pure returns (uint256) {
        require(a >= b, "Underflow");
        return a - b;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ValueInitializer {
    uint256 public value;
    bool public initialized;

    function init(uint256 _value) external {
        require(!initialized, "Already initialized");
        value = _value;
        initialized = true;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract GasPriceLogger {
    uint256 public lastGasPrice;

    function log() external {
        lastGasPrice = tx.gasprice;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ZeroAddress {
    function isZero(address addr) external pure returns (bool) {
        return addr == address(0);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CounterStarter {
    uint256 public count;

    function start(uint256 initial) external {
        count = initial;
    }

    function increment() external {
        count++;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ValueDoubler {
    uint256 public value;

    function setAndDouble(uint256 _value) external {
        value = _value * 2;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IncAndGet {
    uint256 public count;

    function inc() external returns (uint256) {
        count++;
        return count;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Resettable {
    uint256 public value;

    function set(uint256 _value) external {
        value = _value;
    }

    function reset() external {
        value = 0;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TimestampNow {
    function nowTime() external view returns (uint256) {
        return block.timestamp;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SenderIsOwner {
    address public owner = msg.sender;

    function check() external view returns (bool) {
        return msg.sender == owner;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CurrentBlock {
    function get() external view returns (uint256) {
        return block.number;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract GasLeft {
    uint256 public lastGasLeft;

    function log() external {
        lastGasLeft = gasleft();
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CalldataLength {
    function getLength() external pure returns (uint256) {
        return msg.data.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TxOrigin {
    function getOrigin() external view returns (address) {
        return tx.origin;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ThisAddress {
    function getThis() external view returns (address) {
        return address(this);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CodeSize {
    function getSize(address addr) external view returns (uint256) {
        return addr.code.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BaseSepolia {
    uint256 public constant CHAIN_ID = 84532;
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ImmutableNumber {
    uint256 public immutable number;

    constructor(uint256 _number) {
        number = _number;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ReceiveLogger {
    uint256 public received;

    receive() external payable {
        received += msg.value;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract FallbackLogger {
    uint256 public fallbackCount;

    fallback() external {
        fallbackCount++;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SelfBalance {
    function getBalance() external view returns (uint256) {
        return address(this).balance;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BitwiseOr {
    function or(uint256 a, uint256 b) external pure returns (uint256) {
        return a | b;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BitwiseNot {
    function not(uint256 value) external pure returns (uint256) {
        return ~value;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract UintToAddress {
    function convert(uint256 value) external pure returns (address) {
        return address(uint160(value));
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Encode {
    function encode(uint256 a, address b) external pure returns (bytes memory) {
        return abi.encode(a, b);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AssertExample {
    function check(uint256 value) external pure {
        assert(value != 0);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ConstructorExample {
    uint256 public value;

    constructor(uint256 _value) {
        value = _value;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ArrayPush {
    uint256[] public numbers;

    function push(uint256 number) external {
        numbers.push(number);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract DeleteExample {
    mapping(address => uint256) public values;

    function set(uint256 value) external {
        values[msg.sender] = value;
    }

    function remove() external {
        delete values[msg.sender];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IExample {
    function getValue() external view returns (uint256);
}

contract Example is IExample {
    function getValue() external pure returns (uint256) {
        return 42;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract IndexedEvent {
    event ValueSet(address indexed user, uint256 value);

    function set(uint256 value) external {
        emit ValueSet(msg.sender, value);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ConstantString {
    string public constant GREETING = "Hello Base";
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract AssemblyExample {
    function getChainId() external view returns (uint256 id) {
        assembly {
            id := chainid()
        }
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Coinbase {
    function getCoinbase() external view returns (address) {
        return block.coinbase;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BlobBaseFee {
    function getBlobBaseFee() external view returns (uint256) {
        return block.blobbasefee;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MsgSig {
    function getSig() external pure returns (bytes4) {
        return msg.sig;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SendETH {
    function send(address payable to) external payable {
        to.transfer(msg.value);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Selector {
    function getSelector(string calldata signature) external pure returns (bytes4) {
        return bytes4(keccak256(bytes(signature)));
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ArrayLength {
    function getLength(uint256[] calldata arr) external pure returns (uint256) {
        return arr.length;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract StructArray {
    struct Item {
        uint256 id;
        string name;
    }

    Item[] public items;

    function add(uint256 id, string calldata name) external {
        items.push(Item(id, name));
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract MultiReturn {
    function get() external pure returns (uint256, bool, string memory) {
        return (1, true, "Base");
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Ternary {
    function max(uint256 a, uint256 b) external pure returns (uint256) {
        return a > b ? a : b;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BreakExample {
    function find(uint256 target) external pure returns (uint256) {
        for (uint256 i = 0; i < 100; i++) {
            if (i == target) {
                break;
            }
        }
        return target;
    }
}
