<template>
  <div>
    <label :for="item.id">
      <input type="checkbox" :id="item.id" v-model="item.isCompleted" @click="onClick" />
      {{ item.date | toFormat }}
    </label>
  </div>
</template>

<script>
import moment from 'moment'
export default {
  props: {
    item: Object
  },
  filters: {
    toFormat(value) {
      return moment(value).format('MM/DD')
    }
  },
  methods: {
    onClick() {
      this.$emit('update')
    }
  }
}
</script>

<style scoped>
div {
  display: inline-block;
}
</style>
