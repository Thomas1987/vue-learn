<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
        <!-- <Select v-model="searchKey" class="search-col">
          <Option v-for="item in columns" v-if="item.key !== 'handle'" :value="item.key" :key="`search-col-${item.key}`">{{ item.title }}</Option>
        </Select> -->
        <!-- <Input v-model="searchValue" clearable placeholder="输入关键字搜索" class="search-input" @on-change="handleClear"> -->
        <Button class="search-btn" type="primary" @click="handleSearch"><Icon type="search"/>&nbsp;&nbsp;搜索</Button>
      </div>
      <Table :data="tableData1" :columns="tableColumns1" stripe/>
      <div style="margin: 10px;overflow: hidden">
        <div style="float: right;">
          <Page :total="100" :current="1" @on-change="changePage"/>
        </div>
      </div>
    </Card>
    <Modal v-model="userModal" :mask-closable="false" :loading="loading" width="900" title="添加用戶" class-name="vertical-center-modal" scrollable @on-ok="ok">
      <Form ref="form" :model="form" :rules="rule" label-width="100">
        <Row>
          <i-Col span="12">
            <Form-item prop="name" label="姓名：">
              <i-Input v-model="form.name"/>
            </Form-item>
          </i-Col>
          <i-Col span="12">
            <Form-item prop="name" label="姓名：">
              <i-Input v-model="form.name"/>
            </Form-item>
          </i-Col>
        </Row>
      </Form>
    </Modal>
  </div>
</template>
<script>
import './userTable.less'
export default {
  data() {
    return {
      UserName: false,
      userModal: false,
      loading: true,
      form: {
        name: ''
      },
      rule: {
        name: {
          required: true,
          min: 4
        }
      },
      tableData1: this.mockTableData1(),
      tableColumns1: [
        {
          title: 'Name',
          key: 'name'
        },
        {
          title: 'Status',
          key: 'status',
          render: (h, params) => {
            const row = params.row
            const color =
              row.status === 1
                ? 'primary'
                : row.status === 2
                  ? 'success'
                  : 'error'
            const text =
              row.status === 1
                ? 'Working'
                : row.status === 2
                  ? 'Success'
                  : 'Fail'

            return h(
              'Tag',
              {
                props: {
                  type: 'dot',
                  color: color
                }
              },
              text
            )
          }
        },
        {
          title: 'Portrayal',
          key: 'portrayal',
          render: (h, params) => {
            return h(
              'Poptip',
              {
                props: {
                  trigger: 'hover',
                  title: params.row.portrayal.length + 'portrayals',
                  placement: 'bottom'
                }
              },
              [
                h('Tag', params.row.portrayal.length),
                h(
                  'div',
                  {
                    slot: 'content'
                  },
                  [
                    h(
                      'ul',
                      this.tableData1[params.index].portrayal.map(item => {
                        return h(
                          'li',
                          {
                            style: {
                              textAlign: 'center',
                              padding: '4px'
                            }
                          },
                          item
                        )
                      })
                    )
                  ]
                )
              ]
            )
          }
        },
        {
          title: 'People',
          key: 'people',
          render: (h, params) => {
            return h(
              'Poptip',
              {
                props: {
                  trigger: 'hover',
                  title: params.row.people.length + 'customers',
                  placement: 'bottom'
                }
              },
              [
                h('Tag', params.row.people.length),
                h(
                  'div',
                  {
                    slot: 'content'
                  },
                  [
                    h(
                      'ul',
                      this.tableData1[params.index].people.map(item => {
                        return h(
                          'li',
                          {
                            style: {
                              textAlign: 'center',
                              padding: '4px'
                            }
                          },
                          item.n + '：' + item.c + 'People'
                        )
                      })
                    )
                  ]
                )
              ]
            )
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
            return h(
              'div',
              this.formatDate(this.tableData1[params.index].update)
            )
          }
        }
      ]
    }
  },
  methods: {
    mockTableData1() {
      const data = []
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
    formatDate(date) {
      const y = date.getFullYear()
      let m = date.getMonth() + 1
      m = m < 10 ? '0' + m : m
      let d = date.getDate()
      d = d < 10 ? '0' + d : d
      return y + '-' + m + '-' + d
    },
    changePage() {
      // The simulated data is changed directly here, and the actual usage scenario should fetch the data from the server
      this.tableData1 = this.mockTableData1()
    },
    changeLoading() {
      this.loading = false
      this.$nextTick(() => {
        this.loading = true
      })
    },
    ok() {
      this.$refs['form'].validate(valid => {
        if (!valid) {
          return this.changeLoading()
        }
        setTimeout(() => {
          this.changeLoading()
          this.userModal = false
          this.$Message.success('done')
        }, 1000)
      })
    }
  }
}
</script>
