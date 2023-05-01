import { SigningCosmosClient } from "@cosmjs/launchpad";


const celestiaEndpoint = "celestia-endpoint-url";
const celestiaMnemonic = "celestia-mnemonic-phrase";

const recipientAddress = "recipient-cosmos-address";

async function sendPayForBlobTransaction(client, memo, amount) {
  const walletAddress = (await client.getAccounts())[0].address;

  const senderAddress = walletAddress;
  const receiverAddress = recipientAddress;

  const msg = {
    type: "celestia/PayForBlob",
    value: {
      sender_address: senderAddress,
      receiver_address: receiverAddress,
      amount: amount.toString(),
    },
  };
  const fee = {
    amount: [{ amount: "5000", denom: "ubld" }],
    gas: "200000",
  };
  const { accountNumber, sequence } = await client.getNonce();

  const signedTx = await client.signAndBroadcast(
    [msg],
    fee,
    memo,
    accountNumber,
    sequence
  );

  return signedTx;
}

async function sendPayForBlob() {
  const amount = "1000000"; 
  const memo = "PayForBlob işlemi gönderildi"; 

  const client = await SigningCosmosClient.connectWithMnemonic(
    celestiaEndpoint,
    celestiaMnemonic
  );

  const signedTx = await sendPayForBlobTransaction(client, memo, amount);

  console.log(`Transaction hash: ${signedTx.transactionHash}`);
}

sendPayForBlob();
