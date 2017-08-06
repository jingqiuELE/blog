+++
date = "2017-08-06T23:12:59+08:00"
draft = false
title = "Deploy smart contract on a private dev blockchain"

+++
## Install solidity
In the geth console, verify that solidity has been installed:
```javascript
>eth.getCompilers()
["Solidity"]
```
If the returned result doesn't contain Solidity, install the binary version:
http://solidity.readthedocs.io/en/develop/installing-solidity.html

Note: the nodejs version may not work with geth.

## The first smart contract
```javascript
contract mortal {
    /* Define variable owner of the type address*/
    address owner;

    /* this function is executed at initialization and sets the owner of the contract */
    function mortal() { owner = msg.sender; }

    /* Function to recover the funds on the contract */
    function kill() { if (msg.sender == owner) selfdestruct(owner); }
}

contract greeter is mortal {
    /* define variable greeting of the type string */
    string greeting;

    /* this runs when the contract is executed */
    function greeter(string _greeting) public {
        greeting = _greeting;
    }

    /* main function */
    function greet() constant returns (string) {
        return greeting;
    }
}
```

## Deploy smart contract in a dev private block chain
   Reference:

   https://ethereum.stackexchange.com/questions/2751/deploying-the-greeter-contract-via-the-geth-cli-is-not-registering-in-my-private

1. Start the dev private block chain:
```bash
$geth --datadir "./data" --dev account new //setup account
$geth --datadir "./data" --dev --unlock 0 --mine --minerthreads 1  //start miner in one window
```
Open another terminal, attach a new geth node to the first one:
```bash
$geth --datadir "./data" --dev attach ipc:data/geth.ipc
```

2. In the second geth node console mentioned above, run below command to deploy the hello_world contract:  
```javascript
>greeterSource = 'contract mortal { address owner; function mortal() { owner = msg.sender;  } function kill() { if (msg.sender == owner) suicide(owner);  }  } contract greeter is mortal { string greeting; function greeter(string _greeting) public { greeting = _greeting;  } function greet() constant returns (string) { return greeting;  }  }'
>greeterCompiled = web3.eth.compile.solidity(greeterSource)
>_greeting="Hello World!"
>greeterContract=web3.eth.contract(greeterCompiled["<stdin>:greeter"].info.abiDefinition);
>var greeter = greeterContract.new(_greeting,{from: eth.accounts[0], data: greeterCompiled["<stdin>:greeter"].code, gas: 1000000}, function(e, contract){
  if(!e) {
    if(!contract.address) {
      console.log("Contract transaction send: TransactionHash: " + contract.transactionHash + " waiting to be mined...");
    } else {
      console.log("Contract mined! Address: " + contract.address);
      console.log(contract);
    }
  } else {
    console.log("error: " + e);
  }
})
```
You should be able to see the contract being mined after a few seconds.

