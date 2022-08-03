## Create a private network using the Clique (proof of authority) consensus protocol
Note: This isnt for a production environment
### Prerequisites
 * [Hyperledger Besu](https://besu.hyperledger.org/en/stable/HowTo/Get-Started/Installation-Options/Install-Binaries/)

### Steps to run
 * No need to clone the repo, run `mkdir -p Clique-Network/{Node-1/{data},Node-2/{data},Node-3/{data}}` this serves as the basis of the chain with 3 nodes
 * Now we need to generate an address for node 1 which will be used as our bootloader
  ```
  cd Clique-Network/Node-1

  besu --data-path=data public-key export-address --to=data/node1Address
  ```
* Now we need to create a genesis file for the genesis block of the chain, in the root directory, run `touch cliqueGenesis.json` and paste in:
  ```
    {
  "config":{
  "chainId":1981,
  "constantinoplefixblock":
  0,
  "clique":{
  "blockperiodseconds":15,
  "epochlength":30000
  }
  },
  "coinbase":"0x0000000000000000000000000000000000000000",
  "difficulty":"0x1",
  "extraData":"0x0000000000000000000000000000000000000000000000000000000000000000<Node
  1 Address>0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit":"0xa00000",
  "mixHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "nonce":"0x0",
  "timestamp":"0x5c51a607",
  "alloc":
  {
  "fe3b557e8fb62b89f4916b721be55ceb828dbd73":
  {
  "privateKey":
  "8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63",
  "comment":
  "private key and this comment are ignored. In a real chain, the private key should NOT be stored",
  "balance":
  "0xad78ebc5ac6200000"
  },
  "627306090abaB3A6e1400e9345bC60c78a8BEf57":
  {
  "privateKey":
  "c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3",
  "comment":
  "private key and this comment are ignored. In a real chain, the private key should NOT be stored",
  "balance":
  "90000000000000000000000"
  },
  "f17f52151EbEF6C7334FAD080c5704D77216b732":
  {
  "privateKey":
  "ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f",
  "comment":
  "private key and this comment are ignored. In a real chain, the private key should NOT be stored",
  "balance":
  "90000000000000000000000"
  }
  },
  "number":"0x0",
  "gasUsed":"0x0",
  "parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000"
  }
```
  Note: replace `<Node-1 Address>` with the actual Node 1 address from the previous step, and paste it without the 0x extension

* In the `Node-1` folder, start the node-1 with `besu --data-path=data --genesis-file=../cliqueGenesis.json --network-id 123 --rpc-http-enabled --rpc-http-api=ETH,NET,CLIQUE --host-allowlist="*" --rpc-http-cors-origins="all"`
* In a seperate terminal shell, go the the `Node-2` folder, and in there run: `besu --data-path=data --genesis-file=../cliqueGenesis.json --bootnodes=<Node-1Enode URL> --network-id 123 --p2p-port=30304 --rpc-http-enabled --rpc-http-api=ETH,NET,CLIQUE --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8546` make sure to paste the Enode url from the terminal in which Node-1 is running
* Similarly, for Node 3 go the the `Node-3` folder, and in there run: `besu --data-path=data --genesis-file=../cliqueGenesis.json --bootnodes=<Node-1Enode URL> --network-id 123 --p2p-port=30305 --rpc-http-enabled --rpc-http-api=ETH,NET,CLIQUE --host-allowlist="*" --rpc-http-cors-origins="all" --rpc-http-port=8547` make sure to paste the Enode url from the terminal in which Node-1 is running
* Confirm the private network is working: `curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}' localhost:8545`, you should get back:
```
{
  "jsonrpc" : "2.0",
  "id" : 1,
  "result" : "0x2"
}
```

Note: Look at the logs displayed to confirm Node-1 is producing blocks and Node-2 and Node-3 are importing blocks.