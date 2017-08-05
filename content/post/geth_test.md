+++
date = "2017-08-05T08:19:59+08:00"
draft = true
title = "Use geth to create private ethereum blockchain"

+++

I decided to experience ethereum blockchain and smart contract by building a private blockchain using geth, 
because this is the most controllable way for me to understand the details.
Official documents:
https://github.com/ethereum/go-ethereum
Steps:
    * Initialize the blockchain:
    ```
    $geth --datadir=/private_data_dir init genesis.json
    $cat genesis.json
    {
      "config": {
            "chainId": 0,
            "homesteadBlock": 0,
            "eip155Block": 0,
            "eip158Block": 0
        },
    	"alloc"      : {
    		"64f65d182f5a944814402ce645fcee380b3ed461": {
    			"balance": "10000000000000000000"
    		},
    		"f237f5b662d5938c5e516bcc42c5f94e625f8fb9": {
    			"balance": "00000000000000001234"
    		}
    	},
      "coinbase"   : "0x0000000000000000000000000000000000000000",
      "difficulty" : "0x20000",
      "extraData"  : "",
      "gasLimit"   : "0x2fefd8",
      "nonce"      : "0x0000000000000042",
      "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
      "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
      "timestamp"  : "0x00"
    }
    ```

    * Generate a boot.key:
    ```
    $bootnode --genkey=boot.key
    ```

    * Start the bootnode:
    ```
    $bootnode --nodekey=boot.key
    ```
    Above command would output the enode url.

    * Start the geth node with console:
    ```
    $geth --datadir=private_data_dir --bootnodes=enode://enode_url console
    ```

    * Start the miner:
    in the geth console, start CPU miner which uses 1 thread:
    ```
    >miner.start(1)
    ```
    Once the miner is started, it will mine blocks to your primary account. Veryfy that the account balance 
    is increasing with miner mined new blocks:
    ```
    >web3.fromWei(eth.getBalance(eth.coinbase), "ether")
    ```
    You can stop the miner after it has mined some blocks:
    ```
    >miner.stop()
    ```

    * Check the block chain after mining:
        1. Get the latest block number:
        ```
        >eth.blockNumber()
        ```
        2. Show the block information for any particular block:
        ```
        >eth.getBlock(blockNum)
        ```
        example:
        ```
        {
          difficulty: 133900,
          extraData: "0xce9e5448ce9ed0af535048ce9ed0afce9e",
          gasLimit: 3298641,
          gasUsed: 0,
          hash: "0xac13cdcb7e8f3613c0dfd1369d726ebf3b16a3f1705dc3baae3586b79c08f241",
          logsBloom: "0x000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
        0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
        0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
          miner: "0x64f65d182f5a944814402ce645fcee380b3ed461",
          mixHash: "0x406a266c0cfd820e4257056cc1619ca3863063096a42144f84f3350448dc33e9",
          nonce: "0x6d01c6f02d4c493f",
          number: 50,                                                                                                                                                                         [1/1197]
          parentHash: "0xd4fe323983329729d67a7656c9008577adfcc88bb587fcdd51db1f591e045903",
          receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
          sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
          size: 528,
          stateRoot: "0xff3438c7a6c8b56d9f6c5af95df428a8faf1136553bdc6184656d72698f69e08",
          timestamp: 1501896834,
          totalDifficulty: 6756914,
          transactions: [],
          transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
          uncles: []
        }
        ```

    * Unlock primary account for gas to trasact:
    ```
    personal.unlockAccount(eth.coinbase)
    ```
