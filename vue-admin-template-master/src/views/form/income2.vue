<template>
  <div class="template-manage">

      <div class="temp-title">
        <p class="icon-box">
          <!-- <img class="icon-img" src="@/assets/images/set.svg" /> -->
          <span class="name">模板管理</span>
        </p>
        <el-button
          size="small"
          type="primary"
          icon="el-icon-circle-plus-outline"
          @click="addTemp">新增模板</el-button>
      </div>
  <el-button @click="resetDateFilter">清除日期过滤器</el-button>
  <el-button @click="clearFilter">清除所有过滤器</el-button>
  <el-table
    ref="filterTable"
    :data="tableData"
    style="width: 100%">

    <el-table-column
      label="金额">
      <template slot-scope="scope">
        <div v-if="!scope.row.editing">
          <span>{{ scope.row.MoneyAmount }}</span>
        </div>
        <div v-else>
          <el-input v-model="scope.row.name" placeholder="请填金额"></el-input>
        </div>
      </template>
    </el-table-column>

    <el-table-column
      label="类别">
      <template slot-scope="scope">
        <div v-if="!scope.row.editing">
          <span>{{ scope.row.Categoary }}</span>
        </div>
        <div v-else>
          <el-select v-model="scope.row.Categoary" placeholder="类别">
            <el-option
              v-for="item in consumeType"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </div>
      </template>
    </el-table-column>

    <el-table-column
      prop="Datetime"
      label="日期"
      sortable
      width="180"
      column-key="date"
      :filters="[{text: '2016-05-01', value: '2016-05-01'}, {text: '2016-05-02', value: '2016-05-02'}, {text: '2016-05-03', value: '2016-05-03'}, {text: '2016-05-04', value: '2016-05-04'}]"
      :filter-method="filterHandler"
    >
    </el-table-column>


    <el-table-column
      prop="Tag"
      label="标签"
      width="100"
      :filters="[{ text: '中国银行', value: '中国银行' }, { text: '建设银行', value: '建设银行' }]"
      :filter-method="filterTag"
      filter-placement="bottom-end">
      <template slot-scope="scope">
        <el-tag
          :type="scope.row.Tag === '家' ? 'primary' : 'success'"
          disable-transitions>{{scope.row.Tag}}</el-tag>
      </template>
    </el-table-column>
    <el-table-column
      label="操作">
      <template slot-scope="scope">
        <div class="operate-groups">
          <el-button
            type="primary"
            size="mini"
            v-if="!scope.row.editing"
            icon="el-icon-edit-outline"
            @click="handleEdit(scope.$index, scope.row)">编辑
          </el-button>
          <el-button
            type="primary"
            size="mini"
            v-if="scope.row.editing"
            icon="el-icon-success"
            @click="handleSave(scope.$index, scope.row)">保存
          </el-button>
          <el-button
            size="mini"
            type="danger"
            v-if="!scope.row.editing"
            icon="el-icon-delete"
            @click="handleDelete(scope.$index, scope.row)">删除
          </el-button>
          <el-button
            size="mini"
            type="warning"
            v-if="scope.row.editing"
            icon="el-icon-warning"
            @click="handleCancel(scope.$index, scope.row)">取消
          </el-button>
          <!-- <div class="upAndDown">
            <el-button
              plain
              class="up"
              type="primary"
              size="mini"
              icon="el-icon-arrow-up"
              @click="handleUp(scope.$index, scope.row)">
            </el-button>
            <el-button
              plain
              class="down"
              type="primary"
              size="mini"
              icon="el-icon-arrow-down"
              @click="handleDown(scope.$index, scope.row)">
            </el-button>
          </div> -->
        </div>
      </template>
    </el-table-column>

  </el-table>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        tableData: [{
          MoneyAmount: '19.33',
          Categoary: '食物',
          Datetime: '2020-02-12',
          Tag:'中国银行'
        }],
        consumeType: [{
          label: '食物',
          value: '食物'
        }, {
          label: '娱乐',
          value: '娱乐'
        }, {
          label: '水果',
          value: '水果'
        }]
      }
    },
    created () {
      this.tableData = JSON.parse(localStorage.getItem('tableData'))
    },
    methods: {
      resetDateFilter() {
        this.$refs.filterTable.clearFilter('date');
      },
      clearFilter() {
        this.$refs.filterTable.clearFilter();
      },
      formatter(row, column) {
        return row.address;
      },
      filterTag(value, row) {
        return row.tag === value;
      },
      filterHandler(value, row, column) {
        const property = column['property'];
        return row[property] === value;
      },
      // 编辑
      handleEdit ($index, row) {
        this.$set(this.tableData[$index], 'editing', true)
      },
      // 保存
      handleSave ($index, row) {
        this.$set(this.tableData[$index], 'editing', false)
        localStorage.setItem('tableData', JSON.stringify(this.tableData))
      },
      // 取消
      handleCancel ($index, row) {
        this.$set(this.tableData[$index], 'editing', false)
      },
      // 新增一条模板数据
      addTemp () {
        debugger
        this.tableData = this.tableData || []
        this.tableData.push({
          MoneyAmount: '',
          Categoary: '',
          Datetime: '',
          Tag:'',
          editing: true
        })
      }
    }
  }
</script>
