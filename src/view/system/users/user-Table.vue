<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
        <Select v-model="searchKey" class="search-col">
          <Option v-for="item in tableColumns" v-if="item.key !== 'handle' && item.key !=='checked'" :value="item.key" :key="`search-col-${item.key}`">{{ item.title }}</Option>
        </Select>
        <Input v-model="searchValue"  clearable placeholder="输入关键字搜索" class="search-input" @on-change="handleClear"/>
        <Button  @click="handleSearch" class="search-btn">查询</Button>
        <Button  @click="handleSearch" class="search-btn">添加</Button>
        <Button  @click="handleSearch" class="search-btn">编辑</Button>
        <Button  @click="handleSearch" class="search-btn">导出</Button>
      </div>
      <Table :data="tableData" :columns="tableColumns" stripe/>
      <div style="margin: 10px;overflow: hidden">
        <div style="float: right;">
          <Page :total="100" :current="1" @on-change="changePage"/>
        </div>
      </div>
    </Card>
    <Modal v-model="addModal" :title="dialogStatus">

    </Modal>
  </div>
</template>
<script>
import './userTable.less'
export default {
  // 数据
  data() {
    return {
      tableData: [],
      tableColumns: [
        {
          key: 'checked',
          type: 'selection',
          width: 60,
          align: 'center'
        },
        {
          title: '姓名',
          key: 'name'
        },
        {
          title: '状态',
          key: 'status',
          render: (h, params) => {
            const row = params.row
            const color = row.status === 1 ? 'primary' : row.status === 2 ? 'success' : 'error'
            const text = row.status === 1 ? 'Working' : row.status === 2 ? 'Success' : 'Fail'

            return h('Tag', {
              props: {
                type: 'dot',
                color: color
              }
            }, text)
          }
        },
        {
          title: 'Portrayal',
          key: 'portrayal',
          render: (h, params) => {
            return h('Poptip', {
              props: {
                trigger: 'hover',
                title: params.row.portrayal.length + 'portrayals',
                placement: 'bottom'
              }
            }, [
              h('Tag', params.row.portrayal.length),
              h('div', {
                slot: 'content'
              }, [
                h('ul', this.tableData[params.index].portrayal.map(item => {
                  return h('li', {
                    style: {
                      textAlign: 'center',
                      padding: '4px'
                    }
                  }, item)
                }))
              ])
            ])
          }
        },
        {
          title: 'People',
          key: 'people',
          render: (h, params) => {
            return h('Poptip', {
              props: {
                trigger: 'hover',
                title: params.row.people.length + 'customers',
                placement: 'bottom'
              }
            }, [
              h('Tag', params.row.people.length),
              h('div', {
                slot: 'content'
              }, [
                h('ul', this.tableData[params.index].people.map(item => {
                  return h('li', {
                    style: {
                      textAlign: 'center',
                      padding: '4px'
                    }
                  }, item.n + '：' + item.c + 'People')
                }))
              ])
            ])
          }
        },
        {
          title: 'Sampling Time',
          key: 'time',
          render: (h, params) => {
            return h('div', 'Almost' + params.row.time + 'days')
          }
        },
        {
          title: 'Updated Time',
          key: 'update',
          render: (h, params) => {
            return h('div', this.formatDate(this.tableData[params.index].update))
          }
        }
      ],
      searchKey: '',
      searchValue: ''
    }
  },
  // 编译好的HTML 挂载到页面完成后执行的事件钩子，
  // 此钩子函数中一般会做一些ajax请求获取数据进行数据初始化
  // mounted在整个实例中只执行一次
  mounted() {
    this.tableData = this.mockTableData1()
  },
  // 方法
  methods: {
    // mock表格数据
    mockTableData1() {
      let data = []
      for (let i = 0; i < 10; i++) {
        data.push({
          name: 'Business' + Math.floor(Math.random() * 100 + 1),
          status: Math.floor(Math.random() * 3 + 1),
          portrayal: ['City', 'People', 'Cost', 'Life', 'Entertainment'],
          people: [
            {
              n: 'People' + Math.floor(Math.random() * 100 + 1),
              c: Math.floor(Math.random() * 1000000 + 100000)
            },
            {
              n: 'People' + Math.floor(Math.random() * 100 + 1),
              c: Math.floor(Math.random() * 1000000 + 100000)
            },
            {
              n: 'People' + Math.floor(Math.random() * 100 + 1),
              c: Math.floor(Math.random() * 1000000 + 100000)
            }
          ],
          time: Math.floor(Math.random() * 7 + 1),
          update: new Date()
        })
      }
      return data
    },
    // 日期格式化
    formatDate(date) {
      const y = date.getFullYear()
      let m = date.getMonth() + 1
      m = m < 10 ? '0' + m : m
      let d = date.getDate()
      d = d < 10 ? ('0' + d) : d
      return y + '-' + m + '-' + d
    },
    // 分页方法，真实环境需要调用API
    changePage() {
      // The simulated data is changed directly here, and the actual usage scenario should fetch the data from the server
      this.tableData = this.mockTableData1()
    },
    // 清空后触发事件
    handleClear(e) {
      if (e.target.value === '') { this.tableData = this.tableData }
    },
    // 搜索按钮绑定事件
    handleSearch() {
      this.tableData = this.tableData.filter(item => item[this.searchKey].indexOf(this.searchValue) > -1)
    }
  }
}
</script>
