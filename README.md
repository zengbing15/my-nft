# my-nft
The simple NFT token on Mantle Sepolia Testnet.

## Deploying the NFT
### Part 1: Creating an NFT

* Install both Node.js (>14) and npm on your local machine. To check your Node version, run the following command in your terminal:

```
node -v
```

#### Step 1: Create a Node Project

* Create an empty Node project 

```
mkdir my-nft && cd my-nft
npm init -y
```

#### Step 2: Create a Hardhat Project
```
npm install --save-dev hardhat
npx hardhat
```
You should then see a welcome message and options on what you can do. Use your “arrow keys” to navigate the small menu and Select Create a JavaScript project by clicking “Enter”.

Agree to all the prompts (project root, adding a .gitignore, and installing all sample project dependencies).

Now that we’re done with setting up Hardhat, let’s check if everything works properly.

```
npx hardhat test
```
#### Step 3: Install OpenZeppelin Contracts Package

```
npm install @openzeppelin/contracts
```

#### Step4: Create MyNFT.sol

Navigate to the contracts folder and create a new file called MyNFT.sol. Add the following code to the file.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyNFT is ERC721URIStorage, ERC721Burnable,Ownable {
constructor(address initialOwner)
        ERC721("MyNFT", "MNT")
        Ownable(initialOwner)
    {}
    function safeMint(address to, uint256 tokenId, string memory uri)
        public
        onlyOwner
    {
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }
// The following functions are overrides required by Solidity.

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```
[ref:create and deploy an ERC721 NFT](https://www.quicknode.com/guides/ethereum-development/nfts/how-to-create-and-deploy-an-erc-721-nft#creating-our-own-token)

Make sure that the version defined above (⁰.8.17) is the same as the version defined in the hardhat.config.js file.

#### Step 5: Connect MetaMask & Mantle to Your Project

Install the dotenv package in your project directory by running.

```
npm install dotenv --save
```
Then, create a .env file in the root directory (Main Folder) of your project, and add your MetaMask private key.

Note: The file name should be “.env” only, not “xyz.env” or “abc.env”
Your .env should look like this:

```
PRIV_KEY = 0x"your exported private key"
```
#### Step 6: Update hardhat.config.js

```
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.24",
  networks: {
    "mantle-testnet": {
      url: "https://rpc.sepolia.mantle.xyz/",
      accounts: [process.env.PRIV_KEY], // Uses the private key from the .env file
    }
  }
};

```

#### Step 7: Write the Deployment Script

Navigate to the scripts/ folder and replace the contents in the file deploy.js with the following:

```
async function main() {
   // Grab the contract factory 
   const MyNFT = await ethers.getContractFactory("MyNFT");

   // Start deployment, returning a promise that resolves to a contract object
   const myNFT = await MyNFT.deploy(); // Instance of the contract 
   console.log("Contract deployed to address:", myNFT.address);
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });

```
#### Step 8: Deploy the Contract

```
npx hardhat run scripts/deploy.js --network mantle-testnet
```

You should have an output similar to the one shown below

```
Lock with 0.001ETH and unlock timestamp 1709373094 deployed to 0x741F731F537E5A9402550C693B43c9e6e645b4fD
```
Now that our contract is deployed, we can go to [Mantle Sepolia Explorer](https://explorer.sepolia.mantle.xyz/) and check if our contract was deployed.


Ref:

* [How to create an NFT on Mantle Tutorial Part1](https://medium.com/0xmantle/how-to-create-an-nft-on-mantle-tutorial-part-i-deploying-the-nft-37c10cc87c5)
* [How to create an NFT on Mantle Tutorial Part2](https://medium.com/0xmantle/how-to-create-an-nft-on-mantle-tutorial-part-ii-minting-the-deployed-nft-8874aac266b3)
* [How to create an NFT on Mantle Tutorial Part3](https://medium.com/0xmantle/how-to-create-an-nft-on-mantle-tutorial-part-iii-how-to-view-the-deployed-nft-on-metamask-22b79a514fce)
