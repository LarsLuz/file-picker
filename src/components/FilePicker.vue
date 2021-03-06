<template>
  <div class="uk-height-1-1 uk-flex uk-flex-column uk-overflow-hidden">
    <list-header
      :current-folder="currentFolder"
      :is-select-btn-enabled="isSelectBtnEnabled"
      :is-location-picker="isLocationPicker"
      :are-resources-selected="areResourcesSelected"
      @openFolder="loadFolder"
      @select="emitSelectedResources"
    />
    <div
      v-if="state === 'loading'"
      key="loading-message"
      class="uk-flex uk-flex-1 uk-flex-middle uk-flex-center"
    >
      <oc-spinner aria-label="Loading resources" />
    </div>
    <list-resources
      v-if="state === 'loaded'"
      key="resources-list"
      class="uk-flex-1 oc-border"
      :resources="resources"
      :current-folder="currentFolder"
      :is-location-picker="isLocationPicker"
      @openFolder="loadFolder"
      @selectResources="selectResources"
    />
  </div>
</template>

<script>
import { buildResource } from '../helpers/resources'
import ListResources from './ListResources.vue'
import ListHeader from './ListHeader.vue'

export default {
  name: 'FilePicker',

  components: {
    ListHeader,
    ListResources
  },

  props: {
    variation: {
      type: String,
      required: true,
      validator: value => value === 'resource' || 'location'
    }
  },

  data: () => ({
    state: 'loading',
    resources: [],
    currentFolder: null,
    selectedResources: [],
    davProperties: [
      '{http://owncloud.org/ns}permissions',
      '{http://owncloud.org/ns}favorite',
      '{http://owncloud.org/ns}fileid',
      '{http://owncloud.org/ns}owner-id',
      '{http://owncloud.org/ns}owner-display-name',
      '{http://owncloud.org/ns}share-types',
      '{http://owncloud.org/ns}privatelink',
      '{DAV:}getcontentlength',
      '{http://owncloud.org/ns}size',
      '{DAV:}getlastmodified',
      '{DAV:}getetag',
      '{DAV:}resourcetype'
    ]
  }),

  computed: {
    isSelectBtnEnabled() {
      return this.isLocationPicker || this.areResourcesSelected
    },

    isLocationPicker() {
      return this.variation === 'location'
    },

    areResourcesSelected() {
      return this.selectedResources.length > 0
    }
  },

  created() {
    this.loadFolder('/')
  },

  methods: {
    loadFolder(path) {
      this.state = 'loading'
      this.$client.files
        .list(path, 1, this.davProperties)
        .then(resources => {
          resources = resources.map(resource => buildResource(resource))
          this.resources = resources.splice(1)
          this.currentFolder = resources[0]
          this.state = 'loaded'
        })
        .catch(error => {
          console.error(error)
          this.state = 'failed'
        })
    },

    selectResources(resources) {
      this.selectedResources = resources
    },

    emitSelectedResources() {
      this.$emit('selectResources', this.selectedResources)
    }
  }
}
</script>
