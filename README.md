# Geth Cheatsheet

## Starting up geth

The startup example below is based on the requirements: vultr.com 2048MB RAM, 2 virtual CPU environment. You can ramp up cache for better performance based on your environment.

`geth --cache=256 --jitvm --rpc console 2>> geth.log`

Initial load, fast blockchain sync:
`geth --cache=256 --jitvm --rpc --fast console 2>> geth.log`

Blockchain will sync in a few hours.

 * If using Avast on macOS, you will discover some quarantined files during sync due to false-positive "infections" in the blockchain.

## Simplify geth balance tracking:

Create this file in ~/gethfiles/gethload.js

```
function checkAllBalances() {
    var totalBal = 0;
    for (var acctNum in eth.accounts) {
        var acct = eth.accounts[acctNum];
        var acctBal = web3.fromWei(eth.getBalance(acct), "ether");
        totalBal += parseFloat(acctBal);
        console.log("  eth.accounts[" + acctNum + "]: \t" + acct + " \tbalance: " + acctBal + " ether");
    }
    console.log("  Total balance: " + totalBal + " ether");
};
```

Within geth console:

`> loadScript("/home/username/gethfiles/gethload.js")`

`> checkAllBalances()`

## Sending funds

Sending all funds in an account:

```
> var gasPrice = web3.eth.gasPrice;
undefined
> var gasLimit = 21000;
undefined
> var balance = web3.eth.getBalance(web3.eth.accounts[0]);
undefined
> balance
1000000000000000000
> personal.unlockAccount(eth.accounts[0], "<password_redacted>");
true
> var maxAmount=balance-(gasPrice*gasLimit);
undefined
> eth.sendTransaction({from:web3.eth.accounts[0], to:"0x8c8ff8a3f597d47d46876e3f4eefcdda41373311", value:maxAmount, gas:21000})
"0x63f0553be12b73e07e6f12e959e919a340fd691450d17ee009045f410a92e109"
> checkAllBalances()
  eth.accounts[0]:      0xfc927e32df07cb78587ec6dd668f1461bbde235e      balance: 0 ether
  eth.accounts[1]:      0x8c8ff8a3f597d47d46876e3f4eefcdda41373311      balance: 0.99958 ether
  Total balance: 1 ether
undefined
> 
```
Typical gas cost is 420000 gwei (0.00042 ether, 0.42 finney)

## Unlock to send:
```
> personal.unlockAccount(eth.accounts[0])
// Or
> personal.unlockAccount(eth.accounts[0], "password")
// Or
> personal.unlockAccount(eth.accounts[0], "password", 3600)
```

`personal.unlockAccount` does not save to geth console command history.
