<template>
    <div>
        <q-input outlined style="height: 100%" type="textarea" v-model="newjson" label="Edit your metadata directly here or use the tags below" />
        <q-btn label="Submit changes" color="orange" icon="warning" @click="submitChanges()" v-if="newjson !== json && !edited" />
        <div v-if="edited" class="text-caption">
            Your posting JSON metadata has been edited. Refresh the page in a moment to see the changes
        </div>
        <div v-if="log !== ''"><q-icon name="info" color="primary" v-if="!error" /><q-icon name="error" color="red" v-if="error" /> {{ log }}</div>
        <div>
            Commonly Supported Props:
            <q-chip v-for="tag in commonTags" :key="tag.index" outline dense clickable color="grey" :selected=(doesKeyExist(tag)) :removable=(doesKeyExist(tag)) @remove="removeKey(tag)" @click="showKeyPrompt(tag)">{{ tag }}</q-chip>
        </div>
        <div>
            Extra Props recognised by this site:
            <q-chip v-for="tag in myTags" :key="tag.index" outline dense clickable color="grey" :selected=(doesKeyExist(tag)) :removable=(doesKeyExist(tag)) @remove="removeKey(tag)" @click="showKeyPrompt(tag)">{{ tag }}</q-chip>
        </div>
        <json-viewer :data="JSON.parse(newjson)" />
        <q-dialog v-model="keyDialog" persistent>
        <q-card style="min-width: 350px">
            <q-card-section>
            <div class="text-h6">Edit {{ keyDialogCurrentKey }}</div>
            </q-card-section>
            <q-card-section class="q-pt-none">
            <q-input dense v-model="keyDialogCurrentData" outlined autofocus type="textarea" />
            </q-card-section>
            <q-card-actions align="right" class="text-primary">
            <q-btn flat label="Cancel" v-close-popup />
            <q-btn flat label="Ok" @click="addKey(keyDialogCurrentKey, keyDialogCurrentData)" v-close-popup />
            </q-card-actions>
        </q-card>
        </q-dialog>
    </div>
</template>
<script>
import jsonViewer from 'components/jsonViewer.vue'
export default {
  name: 'propsEditor',
  props: ['json', 'username', 'type'],
  components: { jsonViewer },
  data () {
    return {
      newjson: this.json,
      edited: false,
      error: false,
      keyDialog: false,
      keyDialogCurrentKey: '',
      keyDialogCurrentData: '',
      log: '',
      myTags: ['twitter', 'twitch', 'github', 'discord', 'telegram', 'email', 'bitcoin', 'litecoin', 'ethereum', 'ipfs', 'psn', 'steam', 'xbox', 'flickr', 'facebook', 'tumblr', 'linkedin', 'instagram', 'reddit', 'tiktok', 'soundcloud', 'vimeo', 'spotify', 'youtube', 'bandcamp', 'medium', 'keybase', 'vimm', 'threespeak', 'mastodon', 'gab', 'dbuzz', 'wordpress', 'nftshowroom', 'palnet', 'leofinance'],
      commonTags: ['name', 'about', 'location', 'profile_image', 'cover_image', 'website', 'witness_description', 'dtube_pub', 'pinned', 'portfolio', 'trail']
    }
  },
  methods: {
    submitChanges () {
      if (this.type === 'postingMeta') {
        var params = {
          account: this.username,
          json_metadata: '',
          posting_json_metadata: this.newjson
        }
        var op = [
          'account_update2',
          params
        ]
        window.hive_keychain.requestBroadcast(this.username, [op], 'Posting', function (response, err) {
          if (response.success === true) {
            this.error = false
            this.log = response.message
            this.edited = true
          }
          if (response.success === false) {
            this.error = true
            this.log = response.message
          }
        }.bind(this))
      }
    },
    doesKeyExist (key) {
      if (key in JSON.parse(this.newjson).profile) {
        return true
      } else {
        return false
      }
    },
    removeKey (key) {
      // remove key
      console.log('remove key ' + key)
      var obj = JSON.parse(this.newjson)
      delete obj.profile[key]
      this.newjson = JSON.stringify(obj)
    },
    addKey (key, data) {
      // add key
      if (key !== undefined && data !== undefined) {
        console.log('add key: ' + key + ' with data ' + data)
        var obj = JSON.parse(this.newjson)
        obj.profile[key] = data
        this.newjson = JSON.stringify(obj)
      } else {
        this.log = 'Not adding empty key'
      }
    },
    showKeyPrompt (key) {
      this.keyDialogCurrentKey = key
      this.keyDialogCurrentData = JSON.parse(this.newjson).profile[key]
      this.keyDialog = true
    },
    mounted () {
    }
  }
}
</script>
