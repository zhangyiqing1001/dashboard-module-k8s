<template>
  <div>
    <div class="d-flex" v-for="(item) in labelList" :key="item.key">
      <a-form-item :wrapperCol="{ span: 24 }">
        <a-input-group compact v-if="keyBaseSelectProps">
          <div class="d-flex">
            <a-input class="oc-addonBefore ant-input-group-addon" style="width: 80px;" :defaultValue="keyLabel" readonly />
            <base-select v-decorator="decorators.key(item.key)" v-bind="keyBaseSelectProps"  />
          </div>
        </a-input-group>
        <a-input v-else :addonBefore="keyLabel" v-decorator="decorators.key(item.key)" :placeholder="keyPlaceholder" />
      </a-form-item>
      <div class="mx-3"> = </div>
      <a-form-item :wrapperCol="{ span: 24 }">
        <a-input :addonBefore="valueLabel" v-decorator="decorators.value(item.key)" :placeholder="valuePlaceholder" />
      </a-form-item>
      <a-button v-if="firstCanDelete || labelList.length > 1" type="danger" shape="circle" icon="delete" @click="del(item)" class="mt-1 ml-3" />
    </div>
    <a-button type="primary" icon="plus" @click="add">{{$t('k8s.text_81', [ title ])}}</a-button>
  </div>
</template>

<script>
import * as R from 'ramda'
import { uuid } from '@/utils/utils'
import i18n from '@/locales'

export default {
  name: 'K8SLables',
  props: {
    title: {
      type: String,
      default: i18n.t('k8s.text_82'),
    },
    keyLabel: {
      type: String,
      default: i18n.t('k8s.text_83'),
    },
    valueLabel: {
      type: String,
      default: i18n.t('k8s.text_84'),
    },
    decorators: {
      type: Object,
      validator: val => R.is(Function, val.key) && R.is(Function, val.value),
    },
    keyPlaceholder: {
      type: String,
      default: i18n.t('k8s.text_85'),
    },
    valuePlaceholder: {
      type: String,
      default: i18n.t('k8s.text_85'),
    },
    keyBaseSelectProps: {
      type: Object,
    },
    firstCanDelete: {
      type: Boolean,
      default: true,
    },
  },
  data () {
    return {
      labelList: [],
    }
  },
  methods: {
    add () {
      this.labelList.push({ key: uuid() })
    },
    del (item) {
      const index = this.labelList.findIndex(val => val.key === item.key)
      this.labelList.splice(index, 1)
    },
  },
}
</script>
