<template>
  <div id="app">
    <div>
      <Item v-for="item in items" :key="item.id" :item="item" />
    </div>
    <p>達成した日数: {{ completedItemsLength }}</p>
    <div>
      <Item v-for="item in completedItemsArray" :key="item.id" :item="item"></Item>
    </div>
    <div v-if="completedItemsLength > 0">
      <button @click="isPreviousDayCompleted">一日前を表示する</button>
    </div>
  </div>
</template>

<script>
/**
 * 課題の継続日数を出力する。
 * - 直近で課題をやったのは、いつか？
 * - 連続して課題をやった一番古い日は、いつか？
 *   - 連続しているかどうかをどうやって計算するか？
 */
import moment from 'moment'
import Item from '@/components/Item'
const now = new Date()
const year = now.getFullYear()
const month = now.getMonth()
const date = now.getDate()
const dateArray = []
for (let i = 0; i < 30; i++) {
  dateArray.push({
    id: i + 1,
    date: new Date(year, month, date + i),
    isCompleted: false
  })
}

export default {
  components: {
    Item
  },
  data() {
    return {
      /* dummy data */
      items: dateArray
    }
  },
  filters: {
    toFormat(value) {
      return moment(value).format('MM/DD')
    }
  },
  computed: {
    // 完了した課題リスト。
    completedItemsArray() {
      return this.getCompletedItemsArray(this.items)
    },
    // 完了した課題数。
    completedItemsLength() {
      return this.getCompletedItemsArray(this.items).length
    },
    // 直近で完了した課題
    latestCompletedItem() {
      return this.getLatestCompletedItem(this.items)
    }
  },
  methods: {
    /**
     * 直近で完了した課題を取得する。
     * @param {Object[]} items
     * @returns {Object}
     */
    getLatestCompletedItem(items) {
      const completedItemsArray = this.getCompletedItemsArray(items)
      let result
      if (completedItemsArray.length === 0) {
        return false
      }
      for (const item of completedItemsArray) {
        if (!result || result.date < item.date) {
          result = item
        }
      }
      return result
    },
    /**
     * 完了した課題の一覧を取得する。
     * @param {Object[]} items
     * @returns {Object[]}
     */
    getCompletedItemsArray(items) {
      if (!items || items.length === 0) {
        return []
      }
      return items.filter(item => item.isCompleted === true)
    },
    /**
     * 前日も完了しているかどうか。
     * @returns {boolean}
     */
    isPreviousDayCompleted() {
      const previousDay = this.getPreviousDay(this.getLatestCompletedItem(this.items).date)
      const result = !!this.completedItemsArray.find(item => {
        return item.date.getTime() === previousDay.getTime()
      }) || false
      return result
    },
    /**
     * 前日を取得する。
     * @param {Object} day
     * @returns {Object}
     */
    getPreviousDay(day) {
      return new Date(day.getFullYear(), day.getMonth(), day.getDate() - 1)
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 300px;
}
</style>
