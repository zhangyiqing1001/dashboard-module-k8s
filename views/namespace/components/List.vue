
<template>
  <page-list
    :list="list"
    :columns="columns"
    :group-actions="groupActions"
    :single-actions="singleActions">
    <template v-slot:group-actions-append>
      <cluster-namespace :getParams.sync="list.getParams" :ignoreNamespace="true" @refresh="fetchData" class="ml-3" />
    </template>
    </page-list>
</template>

<script>
import ClusterNamespace from '@K8S/sections/ClusterNamespace'
import ColumnsMixin from '../mixins/columns'
import SingleActionsMixin from '../mixins/singleActions'
import { getNameFilter } from '@/utils/common/tableFilter'
import WindowsMixin from '@/mixins/windows'
import ListMixin from '@/mixins/list'

export default {
  name: 'K8SNamespaceList',
  components: {
    ClusterNamespace,
  },
  mixins: [WindowsMixin, ListMixin, ColumnsMixin, SingleActionsMixin],
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
        resource: 'namespaces',
        apiVersion: 'v1',
        getParams: this.getParams,
        idKey: 'name',
        filterOptions: {
          name: getNameFilter(),
        },
      }),
      groupActions: [
        {
          label: '新建',
          permission: 'k8s_namespaces_create',
          action: () => {
            this.$router.push({ path: '/k8s-namespace/create' })
          },
          meta: () => ({
            buttonType: 'primary',
          }),
        },
        {
          label: '删除',
          permission: 'k8s_namespaces_delete',
          action: () => {
            this.createDialog('DeleteResDialog', {
              vm: this,
              data: this.list.selectedItems,
              columns: this.columns,
              title: '删除命名空间',
              name: '命名空间',
              onManager: this.onManager,
              requestData: {
                cluster: this.list.selectedItems[0]['clusterID'],
              },
              requestParams: {
                id: this.list.selectedItems.map(item => { return item.name }),
              },
            })
          },
          meta: () => {
            if (this.list.selectedItems.length > 0) {
              let namespaces = this.list.selectedItems.map(v => v.namespace)
              let unique = Array.from(new Set(namespaces))
              if (unique.length > 1) {
                return {
                  validate: false,
                  tooltip: '请选择同一个命名空间下的资源',
                }
              }
              return {
                validate: true,
                tooltip: '',
              }
            } else {
              return {
                validate: false,
                tooltip: '请选择需要删除的资源，且为同一命名空间下的资源',
              }
            }
          },
        },
      ],
    }
  },
  created () {
    this.fetchData()
  },
  methods: {
    fetchData () {
      if (this.list.getParams.cluster) {
        this.list.fetchData()
      }
    },
    handleOpenSidepage (row) {
      this.sidePageTriggerHandle(this, 'K8SNamespaceSidePage', {
        id: row.name,
        resource: 'namespaces',
        apiVersion: 'v1',
        getParams: this.list.getParams,
      }, {
        list: this.list,
      })
    },
  },
}
</script>