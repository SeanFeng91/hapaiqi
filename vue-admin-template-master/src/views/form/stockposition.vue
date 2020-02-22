<template>
  <div class="template-manage">
    <div class="temp-title" align="center">
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
        >新增股票</el-button
      >
      <el-button size="small" type="primary" @click="resetDateFilter"
        >清除日期过滤器</el-button
      >
      <el-button size="small" type="primary" @click="clearFilter"
        >清除所有过滤器</el-button
      >
      <el-button size="small" type="primary" @click="centerDialogVisible = true" icon="el-icon-plus"> 新增 </el-button>
    </div>

    <el-dialog title="新增股票资产" :visible.sync="centerDialogVisible" width="35%">
      <el-input class="dialog-iput"
        placeholder="请输入代码"
        v-model="inputCode"
        clearable>
      </el-input>
      <el-input
        placeholder="请输入名称"
        v-model="inputName"
        clearable>
      </el-input>
      <el-input
        placeholder="请输入市价"
        v-model="inputPrice"
        clearable>
      </el-input>
      <el-input
        placeholder="请输入成本"
        v-model="inputCost"
        clearable>
      </el-input>
      <el-input
        placeholder="请输入持仓"
        v-model="inputPosition"
        clearable>
      </el-input>

      <el-dropdown>
        <span class="el-dropdown-link">
          类别<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item v-for="item in Market"> {{ item.value }}</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>

      <el-dropdown>
        <span class="el-dropdown-link">
          标签<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item v-for="item in AccountType"> {{ item.value }}</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>

      <el-button type="warning">重置</el-button>
      <el-button type="primary" @click="dialogAddTemp">确认</el-button>
      <el-button plain @click="centerDialogVisible = false">取消</el-button>

    </el-dialog>

  <!-- <el-form :rules="model.rules" ref="form"> -->
    <el-table
      :data="stockPosition"
      border
      stripe
      show-summary
      :summary-method="getSummaries"
      style="width: 100%"
      @filter-change="handleFilterChange"
      :default-sort="{ prop: 'date', order: 'ascending' }"
    >
      <el-table-column label="股票代码" prop="Code" align="center">
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Code }}</span>
          </div>
          <div v-else>
              <el-input
                v-model="scope.row.Code"
                placeholder="输入代码"
              ></el-input>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="股票名称" prop="Name" align="center">
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Name }}</span>
          </div>
          <div v-else>
              <el-input
                v-model="scope.row.Name"
                placeholder="输入代码"
              ></el-input>
          </div>
        </template>
      </el-table-column>

      <el-table-column label="市值" prop="MarketValue" align="center">
        <template slot-scope="scope">
            <span>{{ scope.row.MarketValue }}</span>
        </template>
      </el-table-column>

      <el-table-column label="总盈亏" prop="Profit" align="center">
        <template slot-scope="scope">
            <span>{{ scope.row.Profit }}</span>
        </template>
      </el-table-column>

      <el-table-column label="总盈亏率" prop="ProfitMargin" align="center">
        <template slot-scope="scope">
            <span>{{ scope.row.ProfitMargin }}</span>
        </template>
      </el-table-column>

      <el-table-column label="当日盈亏" prop="TodayProfit" align="center">
        <template slot-scope="scope">
            <span>{{ scope.row.TodayProfit }}</span>
        </template>
      </el-table-column>

      <el-table-column label="当日盈亏率" prop="TodayMargin" align="center">
        <template slot-scope="scope">
            <span>{{ scope.row.TodayMargin }}</span>
        </template>
      </el-table-column>

      <el-table-column
        label="市价"
        align="center"
        prop="Price"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Price }}</span>
          </div>
          <div v-else>
            <el-input
              v-model="scope.row.Price"
              placeholder="请填价格"
              onkeyup="this.value=this.value.replace(/[^\d.]/g,'');"
              maxlength="15"
            ></el-input>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        label="成本"
        align="center"
        prop="Cost"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Cost }}</span>
          </div>
          <div v-else>
            <el-input
              v-model="scope.row.Cost"
              placeholder="请填价格"
              onkeyup="this.value=this.value.replace(/[^\d.-]/g,'');"
              maxlength="15"
            ></el-input>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        label="持仓"
        align="center"
        prop="Position"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Position }}</span>
          </div>
          <div v-else>
            <el-input
              v-model="scope.row.Position"
              placeholder="请填数量"
              onkeyup="this.value=this.value.replace(/[^\d.]/g,'');"
              maxlength="15"
            ></el-input>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        label="类别"
        align="center"
        prop="Category"
        column-key="Category"
        :filters="Market"
        :filter-method="filterCategory"
        :filter-multiple="false"
        filter-placement="bottom-end"
      >
        <template slot-scope="scope">
          <div v-if="!scope.row.editing">
            <span>{{ scope.row.Category }}</span>
          </div>
          <div v-else>
            <el-select v-model="scope.row.Category" placeholder="类别">
              <el-option
                v-for="item in Market"
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
    <!-- </el-form> -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      stockPosition: [
        {
          Code: "000858",
          Name: "五粮液",
          MarketValue: "",
          Profit: "1000",
          ProfitMargin: "10%",
          TodayProfit:"1000",
          TodayMargin:"10%",
          Price:"110",
          Cost:"100",
          Position:"100",
          Category: "深证",
          Tag: "中国银行"
        }
      ],
      inputCode: "",
      inputName: "",
      inputPrice: "",
      inputCost: "",
      inputPosition: "",
      inputCategory: "",
      inputTag: "",
      centerDialogVisible: false,
      rules: {},
      Market: [
        {
          label: "上证",
          text: "上证",
          value: "上证"
        },
        {
          label: "深证",
          text: "深证",
          value: "深证"
        },
        {
          label: "美股",
          text: "美股",
          value: "美股"
        },
        {
          label: "港股",
          text: "港股",
          value: "港股"
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
    this.stockPosition = JSON.parse(localStorage.getItem("stockPosition"));
  },
  methods: {
    resetDateFilter() {
      this.$refs.filterTable.clearFilter("Market");
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
    filterCategory(value, row) {
      return row.Category === value;
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
      this.$set(this.stockPosition[$index], "editing", true);
    },
    // 保存
    handleSave($index, row) {
      // fn 字符串转数字 不满足就提示
      // fi 字符串转整数 不满足就提示
      function fn(str, t) {
        return str.match(/^-?\d*\.?\d*$/) ? str.match(/^-?\d*\.?\d*$/)[0] : "0";
      };
      function fi(str, t) {
        if (str.match(/^\d*$/)) {
          return str.match(/^\d*$/)[0]
        } else {
          t.$message('不是整数，已经改为0');
          return 0;
        }

      };

      // 除掉所有字母
      row.Price = row.Price.replace(/[^\d.]/g, "");
      row.Cost = row.Cost.replace(/[^\d.-]/g, "");
      row.Position = row.Position.replace(/[^\d.]/g, "");


      // 变为数字字符串或者0
      row.Price = fn(row.Price, this);
      row.Cost = fn(row.Cost, this);
      row.Position = fi(row.Position, this);

      // 市值、总盈亏、总盈亏率计算
      row.MarketValue = Number(row.Price) * Number(row.Position);
      row.Profit = (Number(row.Price) - Number(row.Cost)) * Number(row.Position);
      const profitmargin = (Number(row.Price) / Number(row.Cost) - 1)*100
      row.ProfitMargin = profitmargin >= 0 ? profitmargin.toFixed(2) + "%" : "N/A",

      // TODO: daily profit and margin
      //
      row.TodayProfit = "TODO";
      row.TodayMargin = "TODO";
      //

      this.$set(this.stockPosition[$index], "editing", false);
      localStorage.setItem(
        "stockPosition",
        JSON.stringify(this.stockPosition)
      );
    },
    // 取消
    handleCancel($index, row) {
      this.$set(this.stockPosition[$index], "editing", false);
    },
    // 新增一条模板数据
    addTemp() {
      this.stockPosition = this.stockPosition || [];
      this.stockPosition.push({
        MarketValue: "",
        Category: "",
        Datetime: "",
        Tag: "",
        editing: true,
        center: true
      });
    },
    dialogAddTemp() {
      this.stockPosition = this.stockPosition || [];
      this.stockPosition.push({
          Code: this.inputCode,
          Name: this.inputName,
          MarketValue: "",
          Profit: "",
          ProfitMargin: "%",
          TodayProfit:"",
          TodayMargin:"%",
          Price: this.inputPrice,
          Cost: this.inputCost,
          Position: this.inputPosition,
          Category: this.inputCategory,
          Tag: this.inputTag
      });
      this.centerDialogVisible = false;

    },
    handleDelete($index, row) {
      this.$confirm("此操作将永久删除该条模板, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.stockPosition.splice($index, 1);
          localStorage.setItem(
            "stockPosition",
            JSON.stringify(this.stockPosition)
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
          sums[index] = "合计";
          return;
        }
        const values = data.map(item => Number(item[column.property]));
        const sumsindex = [2,3]
        if (index === 2 || index === 3 ){
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);

            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          // TO DO
          // for dashboard use only
          // 此处存在本地数据没有保存在服务器
          // 得到总支出：
          // const totalIncome = localStorage.getItem("totalIncome")

          sums[index] = Number(sums[index]).toFixed(2);
          sums[index] += "";
        } else {
          if(index === 4){
              sums[index] = (100*(Number(sums[3]/(Number(sums[2])-Number(sums[3])))-1)).toFixed(2)+"%";
          } else{
            sums[index] = "";
          }

        }
      });
      const stockSummary = {
        marketValue : sums[2],
        profit : sums [3],
        profitMargin : sums[4]
      }
      console.log
      localStorage.setItem("stockSummary",JSON.stringify(stockSummary));
      return sums;
    }
  }
};
</script>

<style>
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }

</style>
