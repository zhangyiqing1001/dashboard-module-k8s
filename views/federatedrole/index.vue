<template>
  <div>
    <page-header :title="$t('k8s.text_397')" />
    <page-body>
      <k8s-federatedrole-list :id="listId" :getParams="getParams" />
    </page-body>
  </div>
</template>

<script>
import K8sFederatedroleList from './components/List'

export default {
  name: 'K8SFederatedroleIndex',
  components: {
    K8sFederatedroleList,
  },
  data () {
    return {
      listId: 'K8SFederatedroleList',
      getParams: {
        details: true,
      },
    }
  },
}
</script>
