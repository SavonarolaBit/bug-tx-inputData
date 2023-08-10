<script lang="ts">
  import {
    TransactionBuilder,
    OutputBuilder,
  } from "@fleet-sdk/core";
  import { onMount } from "svelte";

  onMount(async () => {
    await ergoConnector.nautilus.connect();
  });

  function boxToStrVal(box) {
    let newBox = JSON.parse(JSON.stringify(box));
    newBox.value = newBox.value.toString();
    if (newBox.assets === undefined) newBox.assets = [];
    for (let i = 0; i < newBox.assets.length; i++) {
      newBox.assets[i].amount = newBox.assets[i].amount.toString();
    }
    return newBox;
  }

  async function getOracleBox() {
    const resp = await fetch(
      `https://api.ergoplatform.com/api/v1/boxes/unspent/byTokenId/011d3364de07e5a26f0c4eef0852cddb387039a921b7154ef3cab22c6eda887f`
    );
    let data = await resp.json();
    let oracleBox = data.items[0];
    oracleBox = boxToStrVal(oracleBox);
    return oracleBox;
  }

  async function createUnsignedTx() {
    const wallet_address = await ergo.get_change_address();
    const unsignedTransaction = new TransactionBuilder(
      await ergo.get_current_height()
    )
      .from(await ergo.get_utxos())
      .to(new OutputBuilder(1000000000n, wallet_address))
      .sendChangeTo(wallet_address)
      .payMinFee()
      .build()
      .toEIP12Object();

    return unsignedTransaction
  }

  async function sendWithOracleBox() {
    const oracleBox = await getOracleBox();
    const unsignedTransaction = await createUnsignedTx();

    unsignedTransaction.dataInputs = [oracleBox];
    console.log({ unsignedTransaction });

    const signedTransaction = await ergo.sign_tx(unsignedTransaction);
    console.log({ signedTransaction });

    transactionId = await ergo.submit_tx(signedTransaction);
    console.log({ transactionId });
  }

  async function send() {
    
    const unsignedTransaction = await createUnsignedTx();
    console.log({ unsignedTransaction });

    const signedTransaction = await ergo.sign_tx(unsignedTransaction);
    console.log({ signedTransaction });

    transactionId = await ergo.submit_tx(signedTransaction);
    console.log({ transactionId });
  }

  let transactionId:string|undefined
</script>

<button on:click={sendWithOracleBox}>
  send 1 erg to self with oracle box (ERROR)
</button> <br />

<button on:click={send}> send 1 erg to self </button>

<p>
  <a href={`https://testnet.ergoplatform.com/en/transactions/${transactionId}`}
    >txId = {transactionId ?? ""}</a
  >
</p>
