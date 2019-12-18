<template lang="pug">
  div
    header.bg-primary
      p.p-6.tx-link.font-xl.align-center My Tasks

    section.h-container.mx-auto
      // 新增
      div.p-add.my-6.p-4.bg-white.font-xl.tx-gray-300.cursor-pointer(v-if="!isAdd",@click="isAdd = true")
        <i class="fas fa-plus"></i>
        span.ml-2.md-width-75 Add Task
      div.p-add.my-6.font-xl.tx-gray-300(v-else,style="border:0")
        div.p-add__title.p-4
          input(type="text",placeholder="Type Something Here…",v-model="addData.title")
          div
            <i class="far fa-star cursor-pointer" :class="{'tx-warning':addData.stars,'fas':addData.stars}" @click="addData.stars = !addData.stars"></i>

        div.py-6.px-4
          div.mb-3
            p.tx-black
              <i class="fas fa-calendar-alt"></i>
              span.ml-2 Deadline
            input.ml-8.mr-1(type="date",v-model="addData.date")
            input(type="time",v-model="addData.time")
          div
            p.tx-black
              <i class="far fa-comment-dots"></i>
              span.ml-2 Comment
            textarea.ml-8(cols="50",rows="8",v-model="addData.content")

        div.d-flex.cursor-pointer
          p.p-4.flex-1.d-flex.justify-content-center.align-items-center.bg-white.tx-danger(@click="clear()") Cancel
          p.p-4.flex-1.d-flex.justify-content-center.align-items-center.bg-primary.tx-white(@click="add()") Add Task

      // 列表
      ul
        li.p-3.mb-2.bg-gray-100.round-s(v-for="(item,index) in list") 
          div.d-flex.justify-content-between
            p.weight-l {{item.title}}
            div
              <i class="far fa-star mr-2" :class="{'tx-warning':item.stars,'fas':item.stars}" ></i>
              <i class="fas fa-trash-alt tx-link cursor-pointer" @click="del(item.id,index)"></i>
          p.font-m.tx-gray-400
            span 內容:
            span {{ item.content }}
          p.font-m.tx-gray-400.align-right {{ item.date }}{{ item.time }}

</template>

<script>
let addData = {
  title: '',
  date: '',
  time: '',
  content: '',
  stars: false
}

export default {
  data() {
    return {
      isAdd: false,
      addData: { ...addData },
      list: []
    }
  },
  created() {
    this.getData()
  },
  methods: {
    clear() {
      this.isAdd = false
      this.addData = { ...addData }
    },
    async add() {
      await this.$axios.post('/data', this.addData)
      this.clear()
      this.getData()
    },
    async del(id, index) {
      this.list.splice(index, 1)
      await this.$axios.delete(`/data/${id}`)
    },
    getData() {
      this.$axios.get('/data').then(res => {
        this.list = res.data.sort(function(a, b) {
          return a.stars < b.stars ? 1 : -1
        })
      })
    }
  }
}
</script>
