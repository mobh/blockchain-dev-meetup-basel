# Meetup 1 : 26.10.2017
### Ethereum

<img src="images/EtherumStack_MeetupBasel_s.png" style="height:558px">

----

## Hands On Session Part 1:Geth Client

----
# Install Geth client & connect to Ethereum Networks


Mac:
https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Mac
(for Windows instructions under:
https://github.com/ethereum/go-ethereum/wiki/Installation-instructions-for-Windows)

*start terminal

*Install homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

* Install geth
brew tap ethereum/ethereum
brew install ethereum
( if already installed execute "brew upgrade ethereum")


* execute geth on Main net (downlads live chain)
$ geth version
$ geth


*Check Nodes of Main Net
https://www.ethernodes.org/network/1


* Cancel after 1 or 2 minutes ( Ctrl + C ) and check downloaded ethereum chain data
/Users/XXXXX/Library/Ethereum/geth/ 

* execute on Test net (downlads test chain)
$ geth --testnet 

*  Cancel after 1 or 2 minutes ( Ctrl + C ) and check downloaded ethereum test chain data
/Users/XXXXX/Library/Ethereum/geth/ 


*Check Nodes of Test Net
https://www.ethernodes.org/network/2

# build a private blockchain

* Create an account to use as the coinbase for your blockchain node
Example:
macbook-pro:ethereum  geth account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase: 
Repeat passphrase: 
Address: {1762cf22fde7928d3a84e812e009887e152e4e96}

* create a private Blockchain

cd ~
mkdir ethereum
(add Path in ENV variables to reduce typing)
echo 'export ethereum_home=/Users/myUserName/ethereum' >> ~/.bash_profile
Example: echo 'export ethereum_home=/Users/mbenhajla/Documents/Blockchain_Meetup/work/ethereum'>> ~/.bash_profile
(use vi or any another editor to create the genesis block)
vi $ethereum_home/genesis26.json

-copy following json contents in file "genesis26.json"
{
  "config": {
        "chainId": 26,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "alloc": {},
  "difficulty" : "0x4000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000026",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00"
}

NOTE:
coinbase: is the Address that mining rewards are sent; set your new created account there 
example: "0x1762cf22fde7928d3a84e812e009887e152e4e96"


-save "genesis26.json" ( with esc !wq)

-init the first block in the chain on first node 
geth --datadir $ethereum_home/node1 init $ethereum_home/genesis26.json


- launch with console & interact with the node 1
geth --datadir $ethereum_home/node1 --networkid 26 console
( port can be set -port "another Port" example "35555")

-Get the list of the available commands
eth

# execute commands to interact with the private Blockchain using Console

net.version
eth.accounts
eth.mining
eth.blockNumber
eth.getBlock(0)

-Create an Account
personal.newAccount()


eth.accounts
eth.getBalance(eth.accounts[0])

-start mining
Mining uses eth.coinbase for the mining rewards. eth.coinbase, when not specified it defaults to eth.accounts[0]
eth.coinbase
personal.unlockAccount(eth.accounts[0])
miner.start(1)
( NOTE: DAG Generation can take some time depending on the difficulty. 
see https://ethereum.stackexchange.com/questions/1993/what-actually-is-a-dag for more info)
miner.stop()
eth.blockNumber ( how many blocks were generated)

-create a second account and transfer ether from account1 ( after mining)
eth.sendTransaction({ from:eth.accounts[0], to:eth.accounts[1], value: web3.toWei(2,"ether")})


# execute commands on Node1 using JSON over IPC 

* communicate with the node through IPC (Inter Process Communication) 
nc -U $ethereum_home/node1/geth.ipc
(example nc -U /Users/XXXX/Documents/Blockchain_Meetup/work/ethereum/node1/geth.ipc)

-send json command 
{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}
example result:
{"jsonrpc":"2.0","id":1,"result":["0x90dba0eba8395faf244e0e06b989a4f340541876"]}


# execute commands on private Blockchain using JSON over RPC ( for remote access)

-Restart node 1 ( 8545 is the default)
geth --datadir $ethereum_home/node1  --rpc --rpcport 8545 --rpcaddr 0.0.0.0 --rpccorsdomain "*" --rpcapi "eth,net,web3" console

# execute eth.coinbase 
$ curl -X POST --data '{"jsonrpc":"2.0","method":"eth_coinbase","params":
[],"id":1}' localhost:30304
# Which returns:
{"jsonrpc":"2.0","id":1,"result":"0x6c503786685f23abae76976fe0aa843f75ea9e71"}


# execute commands on private Blockchain using JSON over Web3 library ( Dapps)

* get Balance
web3.eth.getBalance(web3.eth.accounts[0])

* list accounts
web3.eth.accounts

* Convert from ether to Wei /from Wei to ether
web3.toWei(1, "ether")
web3.fromWei( eth.getBalance(eth.accounts[0]))


# Test on testRPC 
testRPC  is a Virtual Chain ( InMemory Chain) 
download: https://github.com/ethereumjs/testrpc  (will be used later for development with truffle)
* execute "exit" command in console

* Install  testrpc
npm install -g ethereumjs-testrpc

* launch testrpc with 3 accounts ( pre-filled with test Ethers)
$ testrpc --accounts="3"

*attach geth to testrpc
geth attach http://localhost:8545

*execute commands as above with private blockchain
example
> eth.accounts
["0xef737cec8093a4df6442c832cea83501b8442e20", "0x809be9539a58562d3907894dc8101eb60673b5fe", "0x83949fb1fbe8991fe0cb55783ea0d15f9f5dec88"]

*instead of geth , use web3 to communicate with testrpc
-install web3
npm install web3
-install Repl 
npm install node-repl -g
-start node
$ node
-execute commands on  http://localhost:8545 from web3
Example:
const Web3 = require('web3')
const web3 = new Web3(new Web3.providers.HttpProvider())
const eth = web3.eth
> eth.accounts

-stop testrpc und geth bei killing the process
exmaple kill -TERM [PID]

## Hands On Session Part 2: Solidity & SmartContracts

----

# Introduction to solidity
Link Documentation Solidity:
http://solidity.readthedocs.io/en/develop/solidity-by-example.html
https://github.com/ConsenSys/smart-contract-best-practices

* Install Remix Editor locally 

https://github.com/ethereum/browser-solidity

or use remote Remix Editor 

https://ethereum.github.io/browser-solidity


* Solidity by example

- Restart node1 from local blockchain 
geth --datadir $ethereum_home/node1  --rpc --rpcport 8545 --rpcaddr 0.0.0.0 --rpccorsdomain "*" --rpcapi "eth,net,web3" console

-Unlock Account
personal.unlockAccount(eth.accounts[0])

- start remix

-Paste the following example contracts into the remix window

-select web3.provider and type http://localhost:8545

-Compile (with version 0.4.18 in this tutorial), 
-Fix warnings and errors if you are using another version of remix, and deploy samples on local node1 

-start mining
miner.start(1)

- check Balance 
eth.getBalance(eth.accounts[0])

Example 1 (simple):
+++++++++++++++++
pragma solidity ^0.4.18;
contract Bank{
    
    address public creator;
    mapping (address => uint) balances;
    
    function Bank()
    {
        creator = msg.sender;
    }
    
    function deposit() payable
    {
        balances[msg.sender] = balances[msg.sender] + msg.value;
    }
    
    function withdraw(uint amount)
    {
        if(balances[msg.sender] < amount)
        {
            revert();
        }
        
        balances[msg.sender] = balances[msg.sender] - amount;
        
        msg.sender.transfer(amount);
        
    }
}

Example 2 is optional :(advanced, needs rework to fix warnings in 0.4.18):
+++++++++++++++++

( example from: https://gist.github.com/bellaj/f04f1c2dc5e24dd753a22338519555ad)


pragma solidity ^0.4.18;
contract AnotherBank { // CamelCase
    // Declare state variables outside function, persist through life of contract

    // dictionary that maps addresses to balances
    mapping (address => uint) private balances;

    // "private" means that other contracts can't directly query balances
    // but data is still viewable to other parties on blockchain

    address public owner;
    // 'public' makes externally readable (not writeable) by users or contracts

    // Events - publicize actions to external listeners
    event DepositMade(address accountAddress, uint amount);

    // Constructor, can receive one or many variables here; only one allowed
    function AnotherBank() {
        // msg provides details about the message that's sent to the contract
        // msg.sender is contract caller (address of contract creator)
        owner = msg.sender;
    }

    /// @notice Deposit ether into bank
    /// @return The balance of the user after the deposit is made
    function deposit() public returns (uint) {
        balances[msg.sender] += msg.value;
        // no "this." or "self." required with state variable
        // all values set to data type's initial value by default

        DepositMade(msg.sender, msg.value); // fire event

        return balances[msg.sender];
    }

    /// @notice Withdraw ether from bank
    /// @dev This does not return any excess ether sent to it
    /// @param withdrawAmount amount you want to withdraw
    /// @return The balance remaining for the user
    function withdraw(uint withdrawAmount) public returns (uint remainingBal) {
        if(balances[msg.sender] >= withdrawAmount) {
            balances[msg.sender] -= withdrawAmount;

            if (!msg.sender.send(withdrawAmount)) {
                // to be safe, may be sending to contract that
                // has overridden 'send' which may then fail
                balances[msg.sender] += withdrawAmount;
            }
        }

        return balances[msg.sender];
    }

    /// @notice Get balance
    /// @return The balance of the user
    // 'constant' prevents function from editing state variables;
    // allows function to run locally/off blockchain
    function balance() constant returns (uint) {
        return balances[msg.sender];
    }

    // Fallback function - Called if other functions don't match call or
    // sent ether without data
    // Typically, called when invalid data is sent
    // Added so ether sent to this contract is reverted if the contract fails
    // otherwise, the sender's money is transferred to contract
    function () {
        throw; // throw reverts state to before call
    }
}

Note: set gaz limit to 3000000 ( in remix)

*************************************

# Create your first smart contract ( for example a simple SHOP) and deploy it on your 
private BlockChain

contract Shop {

	struct Product {
        string name;
        uint uid;
        uint price;
        uint stock;
    } 

    // Init
    function Shop(..){
    
    }

    mapping(uint => Product) public products;

    function addProduct(string _name, uint _uid, uint _price, uint _stock )  fromOwner returns (bool success) {
    	
    }

	// check transaction costs and optimize, reading and writing to the storage costs more

   	function buyProduct(uint productId)  payable {
     
     }	

     function buyWithToken (uint productId)  payable returns (bool success) {
    
     }

     function withdraw(){
     	        
     }	
}


*deploy SHOP contract 

-paste SHOP contract into the remix window

-reconnect the browser to web3 
-unlock account ( if not yet done)
personal.unlockAccount(eth.accounts[0])
-select web3.provider and type http://localhost:8545


# (optional) create your own coin for the shop 
 