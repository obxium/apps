<template>
  <div>
    <!-- {{ validators[0] }}
    {{ resolved }} -->
    <EmptyGuard :array="validators" label="Validators">
      <ValidatorRow v-for="(validator, index) in validators" :key="index" :validator="validator" :index="index" />
    </EmptyGuard>
  </div>
</template>
<script lang="ts" >
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import Connector from '@vue-polkadot/vue-api';
import { hexToNumber, hexToString } from '@polkadot/util';
import { AccountId } from '@polkadot/types/interfaces';
import EmptyGuard from '@/components/shared/wrapper/EmptyGuard.vue'
import ValidatorRow from './ValidatorRow.vue'

const components = {
  EmptyGuard,
  ValidatorRow
}

@Component({ components })
export default class TableOverview extends Vue {
  @Prop() private validators!: AccountId[];

  private resolved: any = '';

  private async getAddressInsight(address: any) {
    const stakerInfo = await this.getStakerInfo(address)
    const accountInfo = await this.getAccountsInfo(address)
    return { stakerInfo, accountInfo }
  }

  private async getStakerInfo(address: string) {
    const { api } = Connector.getInstance();
    const query = await api.derive.staking.query(address);
    // const totalHex = query.exposure.total.unwrap()
    // console.log('TableOverview -> getStakerInfo -> query.exposure.total', query.exposure.total.unwrap());
    // console.log('TableOverview -> getStakerInfo -> totalHex', totalHex);
    return { query }
  }

  private async getAccountsInfo(address: string) {
    const { api } = Connector.getInstance();
    return await api.derive.accounts.info(address);
  }

  private async mounted() {
    this.resolved = await this.getAddressInsight(this.validators[0])
  }
}
</script>
