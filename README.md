# bot1-EVM



const { ethers } = require("ethers");
// Provider setup
const provider = new ethers.providers.JsonRpcProvider("");
const contractAddress = ""; // Replace with your contract address
const abi = [
    "function mint(uint256 tokenId, uint256 deadline, bytes sig) external payable",
];
// Replace with your wallet's private key and address
const private_key = ""; 
const wallet = new ethers.Wallet(private_key, provider);
const contract = new ethers.Contract(contractAddress, abi, wallet);
// Contract details
const tokenId = 2
const deadline = Math.floor(Date.now() / 1000) + 3600;
    
    // EIP-712 Domain and Types
    const domain = {
        name: "",
        version: "",
        chainId:, // Replace with your chain ID
        verifyingContract: contractAddress,
    };
    const types = {
        mint: [
            { name: "to", type: "address" },
            { name: "tokenId", type: "uint256" },
            { name: "deadline", type: "uint256" },
        ],
    };
    
    // Values to hash
    const values = {
        to: wallet.address,
        tokenId,
        deadline,
    };
    async function generateSignature() {
        try {
            // Generate the digest
            const digest = ethers.utils._TypedDataEncoder.hash(domain, types, values);
            console.log("Digest:", digest);
    
            // Generate the signature
            const signature = await wallet.signMessage(ethers.utils.arrayify(digest));
            console.log("Generated Signature:", signature);
            return signature;
        } catch (error) {
            console.error("Error generating signature:", error);
        }
    }
    
async function mintToken(sig) {
    try {
        console.log("Deadline:", deadline);
        // Call the mint function
        const tx = await contract.mint(tokenId, deadline, sig, {
            value: ethers.utils.parseEther(""), // Mint price
        });
        console.log("Mint transaction sent:", tx.hash);
        // Wait for the transaction confirmation
        const receipt = await tx.wait();
        console.log("Mint transaction confirmed:", receipt);
    } catch (error) {
        console.error("Error during mint:", error.message);
    }
}
async function main() {
    try {
        const signature = await generateSignature();
        await mintToken(signature);
    } catch (error) {
        console.error("Error in main execution:", error.message);
    }
}
// Run the main function
main();