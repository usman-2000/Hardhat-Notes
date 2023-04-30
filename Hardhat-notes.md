Hardhat is the development environment

-------- For Creating New Project.

yarn init
yar add --dev hardhat
yarn hardhat

--- For compiling a contract

yarn hardhat compile


---- hardhar-ethers

const {ethers} = require("hardhat")
                 in this we directly import ethers from hardhat because hardhat has built-in ethers

---- For deploying

	yarn hardhat run scripts/deploy.js

---- Hardhat netwroks

	hardhat automatically consider the network "hardhat". If you want to give a network explicitly given to code , you can give by going to the hardhat.config.js file and in the miodule.export , write a line defaultNetwork="hardhat". i can also add other networks as well.

	or you can also give through the command while deploying

	yarn hardhat run scripts/deploy.js --network hardhat


	when we are pulling a value from environment variable(.env), first you should install dotenv package.
		yarn add --dev dotenv

---- Programmatic Verification

	create an account on etherscan. Create a new api, copy the api key and paste on .env file.
	install @nomiclabs/hardhat-etherscan
	Now your hardhat has new task "verify"
	You can create new function verify and give it the parameters of contract address and constructor args.
	use run to verify the contract.
	await run("verify")

---- Artifacts Error

	Jab artifact ka error aaye to artifact ki or cache ki file ko delete kr do or phr sy run krny ki command chalao to wo automatically dobara sy install kr leta hai or compile krta hai phr error chala jata hai

---- Adding Tasks

	we can add tasks by just creating a task folder and inside that folder create a js file for the task which you want to create. 
	the syntax is:
	first 
	
	const {task} = require("hardhat/config")
	task("task name" , "task description").setAction(
		async (taskArgs, hre)=>{
			const "task name" = await hre.ethers.provider.getBlockNumber();
			console.log("sdasd")

	scripts and tasks both can be used for deploy and run code . tasks are better for plugin and scripts are better for your own local development

---- Hardhat node 
	yarn hardhat node 
	it will provide an interface like a ganache but in terminal

---- Console

	yarn hardhat console --network localhost

------------------------------------------------------------ TESTING ----------------------------------------------------------------

mocha framework used for testing.
we have describe function in which it takes two things one is the name(could be any name for now) and the second is anonymous function.
In this anonymous function it has two things BeforeEach and couple of it. BeforeEach is the code which tells us that it should be run before every it. we can also have describe inside the describe which also have more before each and more it.

Testing is the main thing of the project. Writing a good test will level up your understanding as well as project security.

command for run test
	yarn hardhat test

if we want to run only a specific test we have two options
	we use : yarn hardhat run test --grep store
	we can also use : it.only(......... logic here) ............... in this we use only keyword with the it function.


-------------------------------------------------------- GAS TESTING ---------------------------------------------------------------

hardhat-gas-reporter

in this first we install and then in hardhat.config file, enter the tasks which you want.

------- SOLIDITY COVERAGE
	it is a hardhat plugin , it goes to all our contract and sees how many lines of code actually covered.

	yarn add --dev solidity-coverage

------- Waffle 

It is also a tool for testing . We will use it later . We imported it as @nomiclabs/hardhat-waffle. To confirm this , visit the code of blockchain.

------------------------------------------------------- Solhint -------------------------------------------------------------------

It is used for detecting errors.
the command is :
	yarn solhint contracts/*.sol

---------------------------------------------------Importing from NPM -------------------------------------------------------------

ERROR :   The library @chainlink/contracts, imported from contracts/FundMe.sol, is not installed. Try installing it using npm.

when this error come we import a library from npm. We download this using the following command:

	yarn add --dev @chainlink/contracts

---------------------------------------------------- Deploy using hardhat-deploy plugin -------------------------------------------

It is used for the deployement using plugin (not using scripts)
first install the plugin using command:
		yarn add --dev hardhat-deploy

After downloading , add a require statement to hardhat-config file. 
require("hardhat-deploy")

Now, for checking write "yarn hardhat" , you will see that new tasks were added.
After this you can see the deploy task, which you can use now.
Before this you should already add hardhat-deploy-ethers. For which write this command:
	npm install --save-dev  @nomiclabs/hardhat-ethers@npm:hardhat-deploy-ethers ethers
or
	yarn add --dev @nomiclabs/hardhat-ethers@npm:hardhat-deploy-ethers ethers

Now create a new folder name as deploy. In this folder create a file for deployment of contract. We create an anonymous asyn function.

when deploying:

module.exports = async ({ getNamedAccounts, deployments }) => {
  const { deploy, log } = deployments;
  const { deployer } = await getNamedAccounts; // these are the deployers from which account the contract is deploying
  const chainId = network.config.chainId; // from here we will get the chain id

  //   well what happens when we want to change chains?
  // when going for local host or hardhat network we want to use mock

  const fundMe = await deploy("FundMe", { //FundMe : yeh hamary contract ka name hota hai
    from: deployer, // kon deploy kr raha hai contract ko
    args: [ /* address */], // price feed address like constructor arguments
    log: true, // console.log kranay k liye
  });
};

-----------------Now for adding some more if/else functionality we are using aave github.




-------------------------------------------------- Helper Hardhat Config ----------------------------------------------------------

it is in aave v3 , It will do like:
	hey if you are network A use this address, if you are network B use this address.
For the ADDRESS we will copy the pricefeed address for that network. In our case we will use goerli or sepolia , then go to the chainlink and find the address of eth/usd of goerli or sepolia.	If we want to deploy on sepolia we will create a new file called helper-hardhat-config.js, in this file we will create an object of network config and in that we will use chain id and then name and address of pricefeed which we get from chainlink. After that just export it so that other files can use it.


-------------------------------------------------- Deploy Mock -------------------------------------------------------------------

// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@chainlink/contracts/src/v0.6/tests/MockV3Aggregator.sol";


when you are going to compile , this error will occur:
------------------------------------
The Solidity version pragma statement in these files doesn't match any of the configured compilers in your config. Change the pragma or configure additional compiler versions in your hardhat config.

  * contracts/test/MockV3Aggregator.sol (^0.6.0)
  * @chainlink/contracts/src/v0.6/tests/MockV3Aggregator.sol (^0.6.0)
  * @chainlink/contracts/src/v0.6/interfaces/AggregatorV2V3Interface.sol (^0.6.0)
  * @chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol (^0.6.0)
  * @chainlink/contracts/src/v0.6/interfaces/AggregatorInterface.sol (^0.6.0)

To learn more, run the command again with --verbose
------------------------------------

So in order to get rid of it , you can go to hardhat config file and make an array of versions. like this:

  solidity: {
    compilers: [{ version: "0.8.8" }, { version: "0.6.6" }],
  }


The way we only run our deploy scripts:

module.exports.tags = ["all", "mocks"];


