# DApp "Casino Ethereum"
Based on https://medium.com/@merunasgrincalaitis/the-ultimate-end-to-end-tutorial-to-create-and-deploy-a-fully-descentralized-dapp-in-ethereum-18f0cf6d7e0e

To get access just go to
https://gateway.ipfs.io/ipns/QmawAyhL433WQDQDLPChzm989fW9sqnjp718rrPu9qFwq8
when and if my IPFS daemon is running )

How to build and deploy from scratch.
Nodejs and truffle should already be installed.

## Install dependencies
```bash
mkdir ethereum-casino
cd ethereum-casino
truffle init
npm init
npm i --save react react-dom babel-core babel-loader babel-preset-react babel-preset-env css-loader style-loader json-loader web3@0.20.0 babel-polyfill babel-register babel-preset-stage-2 babel-preset-es2015
npm i -g http-server webpack webpack-cli json-loader 
```

## Update source code
Make dirs in project root:
```bash
mkdir src
mkdir src/js
mkdir src/css
mkdir dist
```
Replace file in project root with one from https://github.com/zidorov/bf-week-5/tree/master/casino-ethereum-demo
```
truffle-config.js
```

Add files to project from ../casino-ethereum-demo:
```
webpack.config.js
migrations/2_deploy_casino.js
contracts/Casino.sol
dist/index.html
src/css/index.css
src/js/index.js
```

### Test and deploy contracts
Compile contracts using truffle:
```bash
truffle compile
```

Deploy contracts in ganache (run in separate window):
```bash
ganache-cli -l 8000000
```

```bash
truffle migrate
```

Test contracts:
```bash
truffle test
```

### Deploy contracts in Goerli ###
Download and install geth (if not yet installed).
Скачать и установить geth (если еще не установлен).
https://geth.ethereum.org/downloads/

Sync goerli testnet (wait until sync will be completed ~15 min)
```bash
geth --goerli --v5disc --syncmode light
```

Finish geth and restart with parameters:
```bash
geth --goerli --v5disc --syncmode light --http --http.port 8547 --verbosity 2 --allow-insecure-unlock console
```

Check is accounts already exists (in geth console):
```
eth.accounts
```
Check the account balance in ETH (in geth console):
```
web3.fromWei(eth.getBalance(eth.accounts[0]),"ether")
```

Create account in geth console:
```
personal.newAccount()
```
For the first time a passphrase for local wallet in geth will be asked.
Copy generated account number and get ETH from goerli faucet
https://goerlifaucet.com/
https://goerli-faucet.mudit.blog/

Unlock account in geth console:
```
personal.unlockAccount(eth.accounts[0],"<password>",1e9)
```
Check balance of account:
web3.fromWei(eth.getBalance(eth.accounts[0]),"ether")

Deploy contracts to Goerli (account with enough ETH should be unlocked!):
```bash
truffle migrate --reset --network goerli
```
Write down Casino contract number, e.g. 0x68afD32894AECb1A7c73616bcEDA91F78962838b
(from "2_deploy_contract.js" section)

Get Casino.sol contract ABI Casino.json (available in build/contracts after 'truffle compile').

### Update code and build using webpack
Update ABI and contract address in file src/js/index.js
```js
const MyContract = web3.eth.contract(<put contract ABI here>);
this.state.ContractInstance = MyContract.at("<put contract address here>")
```

Build dist/build.js:
```bash
webpack
```

### Test locally
```
http-server dist/
```
Go to http://localhost:8080 to test.

### Deploy to IPFS
Download and install (unpack) IPFS with command line
https://docs.ipfs.io/install/command-line/
Add path to ipfs.exe to PATH environment variable.

Check IPFS is accessible:
```bash
ipfs --version
```

Init IPFS node:
```bash
ipfs init
```
Run IPFS daemon (it should run in a separate terminal/cmd window):
```bash
ipfs daemon
```

Now publish ```dist/``` dir to ipfs:
```bash
ipfs add -r dist/
```
example output (for windows cmd.exe is cutting the hash so please use Power Shell or Git Bash):
```bash
added QmQBRQAb8viARryN8kMZaaj2SgrEJyMbMRQZ5XD4WbdUXu dist/build.js
added Qme3zqhXKouQaMpQ4aXHJ3gNMozfvVGX1wn9HtbEnFKukB dist/build.js.LICENSE.txt
added QmZXr4vfJK7GzNBMkeuN5RX7j2yz9EKZ1mmv5boojaJ8X8 dist/favicon.ico
added QmW1zu8pRP1Cb9yiRz5z2HqtYhmzZuGMyoopZKxvyGPuSG dist/index.html
added QmPhUjUMToZBBcuHksSCZAevTsFZU4KHEK6dZCQRzzVF2h dist
 382.87 KiB / 382.87 KiB  100.00%
```

Publish in IPNS using ```dist/``` hash:
```bash
ipfs name publish QmZJy7nbfx2EXfRfHuWcMyNft7afDNnsEo6JvB7vvYb34q
```
example output:
```bash
Published to k51qzi5uqu5dlqk3qfjam1fgubh5l0ujtx2pb1a4y7e0oodqgevmkmb8bxf5b9: /ipfs/QmPhUjUMToZBBcuHksSCZAevTsFZU4KHEK6dZCQRzzVF2h
```
#### Now DApp is accessible via IPFS
###### IPFS addresses (changes every time if file/dir is changed)
Public gateway:
```https://gateway.ipfs.io/ipfs/QmPhUjUMToZBBcuHksSCZAevTsFZU4KHEK6dZCQRzzVF2h```

Local gateway:
```http://localhost:8080/ipfs/QmPhUjUMToZBBcuHksSCZAevTsFZU4KHEK6dZCQRzzVF2h```

###### IPNS addresses (constant even if file/dir is changed)
Public gateway:
```https://gateway.ipfs.io/ipns/k51qzi5uqu5dlqk3qfjam1fgubh5l0ujtx2pb1a4y7e0oodqgevmkmb8bxf5b9```

Local gateway:
```http://localhost:8080/ipns/k51qzi5uqu5dlqk3qfjam1fgubh5l0ujtx2pb1a4y7e0oodqgevmkmb8bxf5b9```


