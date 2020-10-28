# Superior Proxy

It will exploit this bug:
https://solidity.ethereum.org/2020/10/07/solidity-dynamic-array-cleanup-bug/

## Details

This implementation exploits the bug above, since when gets resized down and then up the 3 position is not initialized.  

We downloaded OpenZepplin Proxy pattern implementation that was using solidity ^0.7.0 and reused the code. Here is the link: https://github.com/OpenZeppelin/openzeppelin-contracts/tree/v3.2.1-solc-0.7/contracts/proxy 

We only made a small enhancement to code from OpenZepplin. And we created 2 new files:
Superior.sol and SuperiorTransparentUpgradableProxy.sol our main simple contract is called PokeToken. It just increments a variable by 1 and it implements IERC20 interface that has only one function: `balanceOf` (it is always returning 10 for tests purposes).

Openzeppelin Proxy code is centralized. Only the admin can upgrade the proxy implementation at anytime. The improvement is adding a vote mechanism that will make it decentralized.

To be able to upgrade we added a democratic vote system. If you have a token in the ERC20 implementation, that alllows you to vote. It doesn't matter how many tokens you are holding, you can only vote once. Of course you can split your tokens in a bunch of address, but that will force to spend a lot of money in fees. We fixed a minimum value to 1.000 `YES` votes to be able to upgrade (1.000 is the minimum). Administrator can set the value > 1.000. But it should be at least 1.000. The voting period expires or gets cancelled after 7 days. The admisitrator can restart the process again. 

When it reaches 1.000 votes anyone can execute `upgradeTo` function. 

Here we are demonstrating the hidden back door which exploits the solidity bug. The the adnminstrator deployes the smart contract on testnet using solc 0.7.3 and on mainnet solc 0.7.2. The hidden back door can be expoited on mainnet but not on the testned.


The smart contract has an an array `voteDetails`. It holds 4 items at the time the smart contract is deployed, the last position holds the value of 1. It is used as an indicator for the `upgradeTo` function that it's the first deployment and the adminisstratot can execute this function and doesn't need to check votes. After executing `upgradeTo` for the first time, `voteDetails` array is resized to 1. That is how the code detects that it was already deployed.

Now adminisrator wants to upgrade the code. It is an ERC20 and we reused code from OpenZeppelin ERC20 implementation. It is secure. The  developer made a mistake and deployed it using only 17 decimals instead of 18. It was causing some issues when users are checking their tokens in Etherscan. They users requested an upgrade it. The administrator is executing the  `setUpgradeTo` function to setup a new ERC20 version that would have the correct 18 decimals. What a Shame.




