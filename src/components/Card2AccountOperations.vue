<template>
  <q-card v-if="this.accountHistory.length !== 0" style="max-height: 90%; width: 80vw" bordered>
    <q-splitter v-model="card2Split" unit="px" separator-class="bg-teal-7">
      <template v-slot:before>
        <q-tabs v-model="card2Tab" vertical indicator-color="secondary">
          <q-tab name="0" label="Today" />
          <q-tab name="1" label="Yesterday" />
          <q-tab name="2" label="2 Days Ago" />
          <q-tab name="3" label="3 Days Ago" />
          <q-tab name="4" label="4 Days Ago" />
          <q-tab name="5" label="5 Days Ago" />
          <q-tab name="6" label="6 Days Ago" />
          <q-tab name="7" label="7 Days Ago" />
        </q-tabs>
        <div v-if="false">
          <q-btn-toggle disable v-model="filter.direction" no-caps rounded unelevated toggle-color="secondary" color="dark" text-color="white" :options="[{ label: 'In & Out', value: 'both' }, { label: 'In', value: 'in' }, { label: 'Out', value: 'out' }]" />
        </div>
        <div v-if="false">
          <q-select disable outlined v-model="filter.exclusive" :options="filter.exclusiveOptions" />
        </div>
        <div class="justify-center row">
          <div class="text-bold text-h5">Hide</div>
        </div>
        <q-card class="q-gutter-sm row">
          <div class="row"><q-checkbox dense v-model="filter.customJson" label="Custom JSON" /></div>
          <div class="row"><q-checkbox dense v-model="filter.benefactorRewards" label="Benefactor Rewards" /></div>
          <div class="row"><q-checkbox dense v-model="filter.curationRewards" label="Curation Rewards" /></div>
          <div class="row"><q-checkbox dense v-model="filter.claimRewards" label="Claim Rewards" /></div>
          <div class="row"><q-checkbox dense v-model="filter.producerRewards" label="Producer Rewards" /></div>
          <div class="row"><q-checkbox dense v-model="filter.hpsRewards" label="HPS Rewards" /></div>
          <div class="row"><q-checkbox dense v-model="filter.marketOrders" label="Market Orders" /></div>
          <div class="row"><q-checkbox dense v-model="filter.witnessRelated" label="Witness Related" /></div>
          <div class="row"><q-checkbox dense v-model="filter.comments" label="Comments" /></div>
          <div class="row"><q-checkbox dense v-model="filter.votes" label="Votes" /></div>
          <div class="row"><q-checkbox dense v-model="filter.transfers" label="Transfers" /></div>
        </q-card>
      </template>
      <template v-slot:after>
        <q-tab-panels v-model="card2Tab" animated vertical transition-prev="jump-up" transition-next="jump-down">
          <q-tab-panel name="0" label="a">
            <card2AccountOperationsTab :day=0 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="1">
            <card2AccountOperationsTab :day=1 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="2">
            <card2AccountOperationsTab :day=2 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="3">
            <card2AccountOperationsTab :day=3 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="4">
            <card2AccountOperationsTab :day=4 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="5">
            <card2AccountOperationsTab :day=5 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="6">
            <card2AccountOperationsTab :day=6 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
          <q-tab-panel name="7">
            <card2AccountOperationsTab :day=7 :accountHistory="accountHistory" :username="username" :filter="filter" :globalPropsHive="globalPropsHive" />
            <q-btn label="Load More" @click="getMoreHistory()" color="secondary" v-if="allowLoadMore" />
            <q-spinner-pie color="secondary" size="5em" v-if="loading" />
          </q-tab-panel>
        </q-tab-panels>
      </template>
    </q-splitter>
  </q-card>
</template>

<script>
// import hive from '@hiveio/hive-js'
import card2AccountOperationsTab from 'components/Card2AccountOperationsTab.vue'
import hive from '@hiveio/hive-js'
import moment from 'moment'
export default {
  name: 'Card2AccountOperations',
  props: ['username', 'globalPropsHive'],
  data () {
    return {
      card2Tab: '0',
      card2Split: 200,
      accountHistory: [],
      limit: 1000,
      allowLoadMore: true,
      loading: true,
      filter: {
        direction: 'both',
        producerRewards: true,
        customJson: false,
        benefactorRewards: false,
        curationRewards: false,
        claimRewards: false,
        hpsRewards: false,
        marketOrders: false,
        witnessRelated: false,
        transfers: false,
        votes: false,
        comments: false,
        exclusive: 'All',
        exclusiveOptions: ['All', 'Comments', 'Comments & Votes', 'Custom JSON', 'Delegations', 'Downvotes', 'Market Orders', 'Rewards', 'HPS Related', 'Transfers', 'Upvotes', 'Votes', 'Witness Related']
      }
    }
  },
  components: {
    card2AccountOperationsTab: card2AccountOperationsTab
  },
  methods: {
    getAccountHistory (account, from, limit) {
      console.log('Fetching last ' + limit + ' transactions')
      hive.api.getAccountHistory(account, from, limit, function (err, result) {
        if (err) { console.log(err) }
        this.loading = false
        this.accountHistory = result.reverse()
        var oldestDaysAgo = this.getDaysAgo(this.accountHistory[this.accountHistory.length - 1][1].timestamp)
        if (this.accountHistory.length < this.limit || this.oldestDaysAgo > 7) { this.allowLoadMore = false }
        if (this.accountHistory.length < 10000 && oldestDaysAgo < 8 && this.allowLoadMore) {
          this.getMoreHistory()
        }
      }.bind(this))
    },
    getMoreHistory () {
      this.allowLoadMore = false
      this.loading = true
      // var from = this.accountHistory.length - 1
      var from = -1
      this.getAccountHistory(this.username, from, 10000)
    },
    getDaysAgo (timestamp) {
      var now = moment()
      var then = moment.utc(timestamp)
      var delta = now.diff(then, 'days')
      return delta
    },
    filterDaysAgo (tx) {
      var daysago = parseInt(this.getDaysAgo(tx[1].timestamp))
      if (daysago === parseInt(this.day)) {
        return true
      } else {
        return false
      }
    }
  },
  mounted () {
    this.getAccountHistory(this.username, -1, this.limit)
  }
}
</script>
