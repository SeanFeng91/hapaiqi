<template>
  <div class="template-manage">
    <div class="temp-title" align="right">
      <!--
        <p class="icon-box">
          <!-- <img class="icon-img" src="@/assets/images/set.svg" />
          <span class="name">模板管理</span>
        </p>
        -->
      <el-button
        size="small"
        type="primary"
        icon="el-icon-circle-plus-outline"
        @click="addTemp"
        >新增收入</el-button
      >
      <el-button size="small" type="primary" @click="resetDateFilter"
        >清除日期过滤器</el-button
      >
      <el-button size="small" type="primary" @click="clearFilter"
        >清除所有过滤器</el-button
      >
    </div>

    <el-table
      :data="incomeTableData"
      border
      show-summary
      :summary-method="getSummaries"
      style="width: 100%"
      @filter-change="handleFilterChange"
      :default-sort="{ prop: 'date', order: 'ascending' }"
    >
      <el-table-column label="日期" prop="date" sortable align="center">
        <template slot-scope="scope">
          <!-- <div class="block"> -->
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Datetime }}</span>
          </div>
          <div v-else>
            <!-- <span class="demonstration">默认</span> -->
            <el-date-picker
              v-model="scope.row.Datetime"
              type="date"
              placeholder="选择日期时间"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
            >
            </el-date-picker>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        label="金额"
        align="center"
        width="150"
        prop="MoneyAmount"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.MoneyAmount }}</span>
          </div>
          <div v-else>
            <el-input
              v-model="scope.row.MoneyAmount"
              placeholder="请填金额"
              onkeyup="this.value=this.value.replace(/[^\d.]/g,'');"
              maxlength="9"
            ></el-input>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        label="类别"
        align="center"
        width="150"
        prop="Categoary"
        column-key="Categoary"
        :filters="consumeType"
        :filter-method="filterCategoary"
        :filter-multiple="false"
        filter-placement="bottom-end"
      >
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
                :value="item.value"
              >
              </el-option>
            </el-select>
          </div>
        </template>
      </el-table-column>

      <!-- :filter-method="filterTag"  -->
      <el-table-column
        label="标签"
        prop="Tag"
        align="center"
        width="150"
        column-key="Tag"
        :filters="AccountType"
        :filter-method="filterTag"
        :filter-multiple="false"
        filter-placement="bottom-end"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Tag }}</span>
          </div>
          <div v-else>
            <el-select v-model="scope.row.Tag" placeholder="标签">
              <el-option
                v-for="item in AccountType"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </div>
        </template>
      </el-table-column>

      <!-- <el-table-column
      prop="Tag"
      label="标签"
      width="150"
      :filters="[{ text: '中国银行', value: '中国银行' }, { text: '建设银行', value: '建设银行' }]"
      :filter-method="filterTag"
      filter-placement="bottom-end">
      <template slot-scope="scope">
        <el-tag
          :type="scope.row.Tag === '中国银行' ? 'primary' : 'success'"
          disable-transitions>{{scope.row.Tag}}</el-tag>
      </template>
    </el-table-column> -->
      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
          <div class="operate-groups">
            <el-button
              type="primary"
              size="mini"
              v-if="!scope.row.editing"
              icon="el-icon-edit-outline"
              @click="handleEdit(scope.$index, scope.row)"
              >编辑
            </el-button>
            <el-button
              type="primary"
              size="mini"
              v-if="scope.row.editing"
              icon="el-icon-success"
              @click="handleSave(scope.$index, scope.row)"
              >保存
            </el-button>
            <el-button
              size="mini"
              type="danger"
              v-if="!scope.row.editing"
              icon="el-icon-delete"
              @click="handleDelete(scope.$index, scope.row)"
              >删除
            </el-button>
            <el-button
              size="mini"
              type="warning"
              v-if="scope.row.editing"
              icon="el-icon-warning"
              @click="handleCancel(scope.$index, scope.row)"
              >取消
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
      incomeTableData: [
        {
          MoneyAmount: "19.33",
          Categoary: "食物",
          Datetime: "2020-02-12",
          Tag: "中国银行"
        }
      ],
      consumeType: [
        {
          label: "食物",
          text: "食物",
          value: "食物"
        },
        {
          label: "娱乐",
          text: "娱乐",
          value: "娱乐"
        },
        {
          label: "水果",
          text: "水果",
          value: "水果"
        }
      ],
      AccountType: [
        {
          label: "中国银行",
          text: "中国银行",
          value: "中国银行"
        },
        {
          label: "建设银行",
          text: "建设银行",
          value: "建设银行"
        },
        {
          label: "招商银行",
          text: "招商银行",
          value: "招商银行"
        }
      ],
      value1: ""
    };
  },
  created() {
    this.incomeTableData = JSON.parse(localStorage.getItem("incomeTableData"));
  },
  methods: {
    resetDateFilter() {
      this.$refs.filterTable.clearFilter("date");
    },
    clearFilter() {
      this.$refs.filterTable.clearFilter();
    },
    handleFilterChange(filters) {
      console.log(filters);
      console.log("筛选条件变化");
    },
    formatter(row, column) {
      return row.address;
    },
    filterTag(value, row) {
      return row.Tag === value;
    },
    filterCategoary(value, row) {
      return row.Categoary === value;
    },
    filterHandler(value, row, column) {
      const property = column["property"];
      return row[property] === value;
    },
    //限制字母输入
    tonumbers() {
      this.value = this.value.replace(/[^\d.]/g, "");
    },
    // 编辑
    handleEdit($index, row) {
      this.$set(this.incomeTableData[$index], "editing", true);
    },
    // 保存
    handleSave($index, row) {
      row.MoneyAmount = row.MoneyAmount.replace(/[^\d.]/g, "");
      const fn = str => {
        return str.match(/^\d*\.?\d*$/) ? str.match(/^\d*\.?\d*$/)[0] : "0";
      };
      row.MoneyAmount = fn(row.MoneyAmount);
      this.$set(this.incomeTableData[$index], "editing", false);
      localStorage.setItem(
        "incomeTableData",
        JSON.stringify(this.incomeTableData)
      );
    },
    // 取消
    handleCancel($index, row) {
      this.$set(this.incomeTableData[$index], "editing", false);
    },
    // 新增一条模板数据
    addTemp() {
      this.incomeTableData = this.incomeTableData || [];
      this.incomeTableData.push({
        MoneyAmount: "",
        Categoary: "",
        Datetime: "",
        Tag: "",
        editing: true,
        center: true
      });
    },
    handleDelete($index, row) {
      this.$confirm("此操作将永久删除该条模板, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.incomeTableData.splice($index, 1);
          localStorage.setItem(
            "incomeTableData",
            JSON.stringify(this.incomeTableData)
          );
          this.$message({
            type: "success",
            message: "删除成功!"
          });
        })
        .catch(err => {
          this.$message({
            type: "error",
            message: err
          });
        });
    },
    getSummaries(param) {
      const { columns, data } = param;
      const sums = [];
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = "总收入";
          return;
        }
        const values = data.map(item => Number(item[column.property]));
        if (index === 1) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);

            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          sums[index] = Number(sums[index]).toFixed(2);
          sums[index] += " 元";
        } else {
          sums[index] = "";
        }
      });

      return sums;
    }
  }
};
</script>
