<template>
  <div class="page">
    <div class="chain">
      <label for="chain-selector">Chain</label>
      <select id="chain-selector" v-model="chainId">
        <option v-for="chain in chains" :key="chain.value" :value="chain.value">{{ chain.label }}</option>
      </select>
    </div>
    <div class="tx">
      <label for="tx-input">Transaction hash</label>
      <input id="tx-input" type="text" v-model="txHash">
    </div>
    <div>
      <button @click="inspect" :disabled="loading">Inspect</button>
    </div>
    <div class="mevs" v-if="!loading">
      <div class="mev" v-for="mevItem in mev">
        <div v-if="mevItem.swaps">
          <h3>Arbitrage</h3>
          <div>start amount: {{ mevItem.startAmount }}</div>
          <div>end amount: {{ mevItem.endAmount }}</div>
          <div>profit asset: {{ mevItem.profitAsset }}</div>
          <div class="swaps">
            <div class="swap" v-for="swap in mevItem.swaps">
              <div>from: {{ swap.from }} </div>
              <div>to: {{ swap.to }} </div>
              <div>amount in: {{ swap.amountIn }} </div>
              <div>amount out: {{ swap.amountOut }} </div>
              <div>asset in: {{ swap.assetIn }} </div>
              <div>asset out: {{ swap.assetOut }} </div>
              <div>contract: {{ swap.contract }} </div>
              <div>event: {{ swap.event }} </div>
              <div>transaction: {{ swap.transaction }} </div>
            </div>
          </div>
        </div>
        <div v-else-if="mevItem.liquidator">
          <h3>Liquidation</h3>
          <div>collateral amount: {{ mevItem.amountCollateral }}</div>
          <div>debt amount: {{ mevItem.amountDebt }}</div>
          <div>collateral asset: {{ mevItem.assetCollateral }}</div>
          <div>debt asset: {{ mevItem.assetDebt }}</div>
          <div>liquidator: {{ mevItem.liquidator }}</div>
          <div>borrower: {{ mevItem.borrower }}</div>
          <div>contract: {{ mevItem.contract }}</div>
          <div>event: {{ mevItem.event }}</div>
          <div>transaction: {{ mevItem.transaction }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { AlchemyProvider } from '@ethersproject/providers';
import { ref } from 'vue';
import { Inspector } from 'mev-inspect';


const chains = [{
  label: 'Ethereum',
  value: 1,
}, {
  label: 'Polygon',
  value: 137,
}, {
  label: 'Arbitrum',
  value: 42161
}];

const chainId = ref(1);

const txHash = ref('');

const loading = ref(false);

const mev = ref([]);

async function inspect() {
  loading.value = true;
  const provider = new AlchemyProvider(chainId.value, 'NEwRam_rhHUN2SJWDeHcU5Luij4Tcuxq');
  const inspect = new Inspector(chainId.value, provider);
  mev.value = await inspect.tx(txHash.value);
  loading.value = false;
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  margin-top: 60px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
}

.page {
  min-width: 600px;
  max-width: 800px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.chain,
.tx {
  display: flex;
  gap: 8px;
}

.mevs {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.mev {
  background: #eee;
  padding: 8px;
}

.swaps {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.swap {
  background: #ccc;
  padding:  8px;
}
</style>
