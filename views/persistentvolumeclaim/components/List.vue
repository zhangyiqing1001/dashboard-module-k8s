
<template>
  <page-list
    :list="list"
    :columns="columns"
    :noDataText="noDataText"
    :group-actions="groupActions"
    :single-actions="singleActions">
    <template v-slot:group-actions-append>
      <cluster-namespace :getParams.sync="list.getParams" :namespaceMap="namespaceMap" @refresh="fetchData" class="ml-3" />
    </template>
  </page-list>
</template>

<script>
import * as R from 'ramda'
import ColumnsMixin from '../mixins/columns'
import SingleActionsMixin from '../mixins/singleActions'
import ClusterNamespace from '@K8S/sections/ClusterNamespace'
import clusterNamespaceMixin from '@K8S/mixins/clusterNamespace'
import expectStatus from '@/constants/expectStatus'
import WindowsMixin from '@/mixins/windows'
import ListMixin from '@/mixins/list'
import { getNameFilter } from '@/utils/common/tableFilter'

export default {
  name: 'K8SPersistentvolumeclaimList',
  components: {
    ClusterNamespace,
  },
  mixins: [WindowsMixin, ListMixin, ColumnsMixin, SingleActionsMixin, clusterNamespaceMixin],
  props: {
    id: String,
    getParams: {
      type: Object,
      default: () => ({}),
    },
  },
  data () {
    return {
      list: this.$list.createList(this, {
        id: this.id,
        resource: 'persistentvolumeclaims',
        apiVersion: 'v1',
        getParams: this.getParams,
        filterOptions: {
          name: getNameFilter(),
          unused: {
            label: this.$t('k8s.text_301'),
            dropdown: true,
            items: [
              { label: this.$t('k8s.text_302'), key: false },
              { label: this.$t('k8s.text_303'), key: true },
            ],
          },
        },
        steadyStatus: {
          status: Object.values(expectStatus.k8s_resource).flat(),
        },
        itemGetParams: {
          cluster: '',
          namespace: '',
        },
      }),
      groupActions: [
        {
          label: this.$t('k8s.create'),
          permission: 'k8s_persistentvolumeclaims_create',
          action: () => {
            this.$router.push({ path: '/k8s-persistentvolumeclaim/create' })
          },
          meta: () => ({
            buttonType: 'primary',
          }),
        },
        {
          label: this.$t('k8s.text_201'),
          permission: 'k8s_persistentvolumeclaims_delete',
          action: () => {
            const data = this.list.selectedItems
            const requestData = {
              cluster: data[0].clusterID,
            }
            const namespace = data[0].namespace
            if (namespace) requestData.namespace = namespace
            this.createDialog('DeleteResDialog', {
              vm: this,
              data,
              columns: this.columns,
              title: this.$t('k8s.text_201'),
              name: this.$t('k8s.text_10'),
              onManager: this.onManager,
              requestData,
            })
          },
          meta: () => {
            let validate = true
            let tooltip = ''
            if (this.list.selectedItems.length > 0) {
              const namespaces = this.list.selectedItems.map(v => v.namespace)
              const unique = Array.from(new Set(namespaces))
              if (unique.length > 1) {
                validate = false
                tooltip = this.$t('k8s.text_203')
              }
              if (this.list.selectedItems.some(item => item.mountedBy && item.mountedBy.length !== 0)) {
                validate = false
                tooltip = this.$t('k8s.text_304')
              }
            } else {
              validate = false
              tooltip = this.$t('k8s.text_204')
            }
            return {
              validate,
              tooltip,
            }
          },
        },
      ],
    }
  },
  methods: {
    handleOpenSidepage (row) {
      this.sidePageTriggerHandle(this, 'K8SPersistentvolumeclaimSidePage', {
        id: row.id,
        resource: 'persistentvolumeclaims',
        getParams: () => {
          const params = R.clone(this.list.getParams)
          if (row.namespace) {
            params.namespace = row.namespace
            if (params.all_namespace) delete params.all_namespace
          }
          return params
        },
        apiVersion: 'v1',
        steadyStatus: {
          status: Object.values(expectStatus.k8s_resource).flat(),
        },
      }, {
        list: this.list,
      })
    },
  },
}
</script>
