<template>
  <v-card color="card-shadow activity mb-4 pa-5" :ripple="false" @click="showDetails = !showDetails">
    <v-layout wrap>
      <v-flex
        :class="$vuetify.breakpoint.xsOnly ? 'xs6 order-2 pt-2' : 'xs2 order-0'"
        :style="{ paddingLeft: $vuetify.breakpoint.xsOnly ? '50px' : '0' }"
      >
        <div class="caption font-weight-medium">{{ transaction.dateFormatted }}</div>
        <div class="info font-weight-light">{{ transaction.timeFormatted }}</div>
      </v-flex>
      <v-flex :class="$vuetify.breakpoint.xsOnly ? 'xs8 order-0' : 'xs4 order-1'">
        <div class="icon-holder float-left">
          <img
            v-if="transaction.type === CONTRACT_TYPE_ERC20 || transaction.action === ACTIVITY_ACTION_TOPUP"
            :src="require(`../../../../public/images/${transaction.actionIcon}`)"
            :alt="transaction.from"
            class="mr-2"
            height="36"
          />
          <img v-else-if="transaction.type === CONTRACT_TYPE_ERC721" :src="transaction.actionIcon" class="mr-2" height="36" large color="primary" />
          <v-icon v-else class="float-left mx-3" large color="primary">{{ transaction.actionIcon }}</v-icon>
        </div>
        <div class="caption font-weight-medium">{{ transaction.actionText }}</div>
        <div class="info font-weight-light">
          {{ transaction.action === ACTIVITY_ACTION_SEND ? t('walletActivity.to') : t('walletActivity.from') }} {{ transaction.slicedTo }}
        </div>
      </v-flex>
      <v-flex class="text-right" :class="$vuetify.breakpoint.xsOnly ? 'xs4 order-1' : 'xs2 order-2'">
        <div class="caption font-weight-medium">
          <span v-if="transaction.type !== CONTRACT_TYPE_ERC721 && transaction.action === ACTIVITY_ACTION_SEND" class="error--text">-</span>
          {{ transaction.totalAmountString }}
        </div>
        <div class="info font-weight-light">{{ transaction.currencyAmountString }}</div>
      </v-flex>
      <v-flex class="order-3" xs2 v-if="!$vuetify.breakpoint.xsOnly"></v-flex>
      <v-flex :class="$vuetify.breakpoint.xsOnly ? 'xs6 text-right mt-3 order-3' : 'xs2 text-center order-4'">
        <v-chip class="status-chip black--text" :color="getChipColor(transaction.statusText)" small>
          {{ t(transaction.statusText) }}
        </v-chip>
      </v-flex>
    </v-layout>
    <v-divider v-if="this.showDetails" class="mt-2"></v-divider>
    <v-layout wrap v-if="this.showDetails">
      <v-flex xs12 class="activity-details">
        <v-list class="mx-n4 caption">
          <v-list-item>
            <v-list-item-content class="details-label">{{ t('walletActivity.startedAt') }}:</v-list-item-content>
            <v-list-item-content class="details-value text_2--text">
              <span>{{ transaction.timeFormatted }} - {{ transaction.dateFormatted }}</span>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content class="details-label">{{ t('walletActivity.sendTo') }}:</v-list-item-content>
            <v-list-item-content class="details-value text_2--text">
              <span>{{ transaction.to }}</span>
            </v-list-item-content>
          </v-list-item>
          <v-list-item v-if="transaction.type !== CONTRACT_TYPE_ERC721">
            <v-list-item-content class="details-label">{{ t('walletActivity.rate') }}:</v-list-item-content>
            <v-list-item-content class="details-value text_2--text">
              <span>{{ transaction.ethRate }} {{ transaction.currencyUsed }}</span>
            </v-list-item-content>
          </v-list-item>
          <v-list-item v-if="transaction.type !== CONTRACT_TYPE_ERC721">
            <v-list-item-content class="details-label">{{ t('walletActivity.amount') }}:</v-list-item-content>
            <v-list-item-content class="details-value text_2--text amount-text">
              {{ transaction.totalAmountString }} /{{ transaction.currencyAmountString }}
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content class="details-label">{{ t('walletActivity.network') }}:</v-list-item-content>
            <v-list-item-content class="details-value text_2--text">
              <network-display :network="transaction.networkType"></network-display>
            </v-list-item-content>
          </v-list-item>
          <v-list-item v-if="transaction.etherscanLink">
            <v-list-item-content class="details-value text_2--text text-right mt-1">
              <a class="etherscan-lnk" color="primary" :href="transaction.etherscanLink" target="_blank">{{ t('walletActivity.viewOnEtherscan') }}</a>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-flex>
    </v-layout>
  </v-card>
</template>

<script>
const {
  ACTIVITY_ACTION_SEND,
  ACTIVITY_ACTION_RECEIVE,
  ACTIVITY_STATUS_SUCCESSFUL,
  ACTIVITY_STATUS_UNSUCCESSFUL,
  ACTIVITY_STATUS_PENDING,
  ACTIVITY_ACTION_TOPUP,
  CONTRACT_TYPE_ERC20,
  CONTRACT_TYPE_ERC721
} = require('../../../utils/enums')

import NetworkDisplay from '../../helpers/NetworkDisplay'

export default {
  props: ['transaction'],
  components: {
    NetworkDisplay
  },
  data() {
    return {
      showDetails: false,
      ACTIVITY_ACTION_SEND,
      ACTIVITY_ACTION_RECEIVE,
      ACTIVITY_STATUS_SUCCESSFUL,
      ACTIVITY_STATUS_UNSUCCESSFUL,
      ACTIVITY_STATUS_PENDING,
      ACTIVITY_ACTION_TOPUP,
      CONTRACT_TYPE_ERC20,
      CONTRACT_TYPE_ERC721
    }
  },
  methods: {
    getChipColor(status) {
      return status === ACTIVITY_STATUS_SUCCESSFUL ? '#9BE8C7' : status === ACTIVITY_STATUS_UNSUCCESSFUL ? '#FEA29F' : '#E0E0E0'
    }
  }
}
</script>

<style lang="scss" scoped>
@import 'TransactionDetails.scss';
</style>
