<template>
  <base-side-page
    @cancel="cancelSidePage"
    :title="$t('k8s.text_234')"
    icon="res-k8s-daemonset"
    :res-name="detailData.name"
    :actions="params.actions"
    :current-tab="params.windowData.currentTab"
    :tabs="detailTabs"
    :loaded="loaded"
    @tab-change="handleTabChange">
    <template v-slot:actions>
      <actions :options="singleActions" :row="detailData" button-type="link" button-size="small" />
    </template>
    <component :is="params.windowData.currentTab" :id="listId" :res-id="data.id" :data="detailData" :onManager="onManager" resource="daemonset" :getParams="getParams" :showSearchbox="false" :showGroupActions="false" />
  </base-side-page>
</template>

<script>
import ColumnsMixin from '../mixins/columns'
import SingleActionsMixin from '../mixins/singleActions'
import Detail from './Detail'
import EventsSidepage from '@K8S/sections/EventsSidepage'
import SourceInformationSidepage from '@K8S/sections/SourceInformationSidepage'
import PodList from '@K8S/views/pod/components/List'
import SidePageMixin from '@/mixins/sidePage'
import WindowsMixin from '@/mixins/windows'
import Actions from '@/components/PageList/Actions'

export default {
  name: 'K8SDaemonsetSidePage',
  components: {
    Actions,
    Detail,
    EventsSidepage,
    SourceInformationSidepage,
    PodList,
  },
  mixins: [SidePageMixin, WindowsMixin, ColumnsMixin, SingleActionsMixin],
  data () {
    return {
      detailTabs: [
        { label: this.$t('k8s.text_217'), key: 'detail' },
        { label: this.$t('k8s.text_9'), key: 'pod-list' },
        { label: this.$t('k8s.text_218'), key: 'events-sidepage' },
        { label: this.$t('k8s.text_219'), key: 'source-information-sidepage' },
        { label: this.$t('compute.text_240'), key: 'event-drawer' },
      ],
    }
  },
  computed: {
    getParams () {
      return {
        owner_kind: this.detailData.kind,
        owner_name: this.detailData.name,
        namespace: this.detailData.namespace,
        cluster: this.detailData.clusterID,
      }
    },
    listId () {
      switch (this.params.windowData.currentTab) {
        case 'event-drawer':
          return 'EventListForK8SDaemonsetSidePage'
        case 'pod-list':
          return 'PodListForK8SDaemonsetSidePage'
        case 'events-sidepage':
          return 'EventsForK8SDaemonsetSidePage'
        default:
          return ''
      }
    },
  },
}
</script>
