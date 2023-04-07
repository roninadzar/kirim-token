import { providers, Wallet } from "ethers";
import { FlashbotsBundleProvider } from "@flashbots/ethers-provider-bundle";

async function main() {
  // Standard json rpc provider directly from ethers.js (NOT Flashbots)
  // create the base provider
  let base = new ethers.providers.JsonRpcProvider({url: "http://<polygonbor-ip>:8545"}, 137);
  await base.ready;
  
  // Set up the sponsor wallet
  const sponsor = new ethers.Wallet('SponsorPrivateKey', base);

  // Set up the recipient wallet
  const recipient = new ethers.Wallet('RecipientPrivateKey', base);

  // Set up the wallet to transfer from
  const fromWallet = new ethers.Wallet('FromPrivateKey', base);

  // wrap it with the marlin relay provider
  let provider = new FlashbotsBundleProvider(base, sponsor, {url: 'http://bor.txrelay.marlin.org/'}, 137);


  const CONTRACT_ADDRESS = "0x0a..<contract-address>"
  const ABI = ["function transferFrom(address _from, address _to, uint256 _value)"]
  const contract = new ethers.Contract(CONTRACT_ADDRESS, ABI, fromWallet);

  const txs = [
    {
      signer: fromWallet,
      transaction: await contract.populateTransaction.transferFrom(
        fromWallet.address, // Set the wallet that will transfer the tokens
        recipient.address, // Set the recipient's wallet address
        ethers.utils.parseUnits("10", "ether") // Set the amount of token to transfer (in this case, 10 tokens)
      )
    }
  ];

  const blk = await base.getBlockNumber();

  // send bundle to marlin relay
  const result = await provider.sendBundle(txs, blk + 1);
  console.log(result);
}

main().catch(console.error);
