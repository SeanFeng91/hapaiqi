<template>
  <div class="template-manage">
    <!-- //创建对话会功能 -->
    <el-dialog title="title" :visible.sync="centerDialogVisible" @close="closeDialogVisible" width="35%">
		<el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="StockBuyIn_RuleForm">
		  <el-form-item label="股票代码" prop="InputCode">
        <el-input class="dialog-iput"
          placeholder="请输入交易代码"
          v-model="ruleForm.InputCode"
          clearable>
        </el-input>
		  </el-form-item>
      <el-form-item label="股票名称" prop="InputName">
        <el-input
      	placeholder="请输入股票名称"
      	v-model="ruleForm.InputName"
      	clearable>
        </el-input>
      </el-form-item>
		  <el-form-item label="买入价格" prop="BuyInPrice">
			  <el-input
				placeholder="请输入买入价格"
				v-model="ruleForm.BuyInPrice"
				clearable>
			  </el-input>
		  </el-form-item>
		  <el-form-item label="买入数量" prop="BuyInAmount">
			  <el-input
				placeholder="请输入购买数量"
				v-model="ruleForm.BuyInAmount"
				clearable>
			  </el-input>
		  </el-form-item>
      <el-form-item label="买入日期" prop="BuyInDate">
		  <el-date-picker
        v-model="ruleForm.BuyInDate"
        type="datetime"
        placeholder="选择日期时间"
        value-format="yyyy-MM-dd HH:mm:ss">
		  </el-date-picker>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="submitForm">确认</el-button>
        <el-button plain @click="centerDialogVisible = false">取消</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
		  <!-- <el-button type="warning">重置</el-button> -->
		  <!-- <el-button type="primary" @click="dialogAddTemp">确认</el-button> -->
		  <!-- <el-button plain @click="centerDialogVisible = false">取消</el-button> -->
		</el-form>
    </el-dialog>
    <!-- //表1：用于汇总股票投资后各指标数据，需要考虑增加历史数据标签页，用于记录清仓持仓为0的股票盈亏数据。 -->
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
<!-- 表1不建议保留编辑功能，需要输入信息全部来自表2，价格信息尝试自动获取接入 -->
    </el-table>
    <!-- </el-form> -->
    <!-- //表2：用于录入历史交易信息，包括买入和卖出。 -->
    <!-- //新增和编辑公用一个弹出对话框。 -->
    <div class="temp-title" >
        <!-- <p class="icon-box" align="center"> -->
           <!-- <img class="icon-img" src="@/assets/images/set.svg" /> -->
      <span class="name" width="80%">股票交易明细</span>
        <!-- </p> -->
      <el-button
      size="small"
      type="primary"
      @click="OpenAddNewTradeDialog"
      icon="el-icon-plus"> 新增 </el-button>
<!-- @click="centerDialogVisible = true" -->
    </div>
    <el-table
    :data="StockTradeData"
    height="250"
    border
    style="width: 100%">
      <el-table-column
        prop="TradeCode"
        label="股票代码"
        width="150"
        align="center">
      </el-table-column>
      <el-table-column
        prop="TradeName"
        label="股票名称"
        width="150"
        align="center">
      </el-table-column>
      <el-table-column
        prop="TradeAmount"
        label="交易数量"
        width="150"
        align="center">
      </el-table-column>
      <el-table-column
        prop="TradePrice"
        label="交易价格"
        width="150"
        align="center">
      </el-table-column>
      <el-table-column
        prop="TradeDate"
        label="日期"
        align="center"
        width="200"
        value-format="yyyy-MM-dd HH:mm:ss"
        >
      </el-table-column>
      <el-table-column label="操作" align="center">
          <template slot-scope="scope">
            <div class="operate-groups">
              <el-button
                type="primary"
                size="mini"
                icon="el-icon-edit-outline"
                @click="handleEdit(scope.$index, scope.row)"
                >编辑
              </el-button>
              <el-button
                size="mini"
                type="danger"
                icon="el-icon-delete"
                @click="handleDelete(scope.$index, scope.row)"
                >删除
              </el-button>
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
      StockTradeData:[{
        TradeCode: "",
        TradeName: "",
        TradeAmount: "",
        TradePrice: "",
        TradeDate: "",
      }],
      // dialog弹框名称
      // titleName: {
      //   add: '新增',
      //   edit: '编辑'
      // },
      dialogStatus: '',
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
      centerDialogVisible: false,
      title :'',
      ruleForm: {
          InputCode: '',
          InputName:'',
          BuyInPrice: '',
          BuyInAmount: '',
          BuyInDate:'',

        },
        // rules:{},弹框内容填写规则
        rules: {
          InputCode: [
            { required: true, message: '请输入代码', trigger: 'blur' },
            { min: 6, max: 6, message: '6位代码', trigger: 'blur' }
          ],
          BuyInPrice: [
            { required: true, message: '请输入价格', trigger: 'change' }
          ],
          BuyInAmount: [
            { required: true, message: '请输入数量', trigger: 'change' }
          ],
          date1: [
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
        },

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
  //页面切换时从数据库刷新表单数据
  created() {
   // stockPosition=[];
    // this.stockPosition = JSON.parse(localStorage.getItem("stockPosition"));
    this.StockTradeData = JSON.parse(localStorage.getItem("StockTradeData"));
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
    // 打开新增对话框
    OpenAddNewTradeDialog(){
      // this.ruleForm.InputCode="",
      // this.ruleForm.InputName="",
      // this.ruleForm.BuyInAmount="",
      // this.ruleForm.BuyInPrice="",
      this.ruleForm.BuyInDate= new Date();
      // console.log(new Date())
      // this.dialogStatus = "add";
      // this.centerDialogVisible = this.centerDialogVisible || [];
      this.centerDialogVisible = true
      // this.ruleForm.title="新增股票交易"
    },
    // 新增一条模板数据
    AddNewTrade() {
      // this.StockTradeData = this.StockTradeData || [];
      this.StockTradeData.push({
        TradeCode: this.ruleForm.InputCode,
        TradeName: this.ruleForm.InputName,
        TradeAmount:this.ruleForm.BuyInAmount,
        TradePrice:this.ruleForm.BuyInPrice,
        TradeDate:this.ruleForm.BuyInDate,
      });
      // console.log(TradeCode);
      // 把交易界面数据存入localData

      localStorage.setItem(
        "StockTradeData",
        JSON.stringify(this.StockTradeData)
      );
      this.centerDialogVisible = false;
    },
    dialogAddTemp() {
      this.stockPosition = this.stockPosition || [];
      this.stockPosition.push({
          Code: this.InputCode,
          Name: this.InputName,
          Price: this.BuyInPrice,
          Amount: this.BuyInAmount,
          MarketValue: "",
          Profit: "",
          ProfitMargin: "%",
          TodayProfit:"",
          TodayMargin:"%",
          Cost: this.inputCost,
          Position: this.inputPosition,
          Category: this.inputCategory,
          Tag: this.inputTag
      });
      this.centerDialogVisible = false;

    },
    // 编辑录入交易记录表格的数据
    handleEdit($index, row) {
      // this.$set(this.StockTradeData[$index], "editing", true);
      console.log($index,row);//这里可打印出每行的内容
      this.centerDialogVisible = true;
      let _row = row;
      console.log("row is:",row)
      // this.ruleForm = Object.assign({},row); // ruleForm是Dialog弹框的data，该方法没用
      this.title=$index;
      // this.ruleForm.title=$index
      // this.ruleForm = row;
      this.ruleForm.InputCode = row.TradeCode;
      this.ruleForm.InputName = row.TradeName;
      this.ruleForm.BuyInAmount = row.TradeAmount;
      this.ruleForm.BuyInPrice = row.TradePrice;
      this.ruleForm.BuyInDate = row.TradeDate;
      this.ruleForm.id=0 //传递id这个很重要
      console.log("row id:",row.id);
    },
    //删除录入交易记录表格的数据
    handleDelete($index, row) {
      this.$confirm("此操作将永久删除该条模板, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.StockTradeData.splice($index, 1);
          localStorage.setItem(
            "StockTradeData",
            JSON.stringify(this.StockTradeData)
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
    //关闭或者取消弹框
    closeDialogVisible(){
      this.$refs.ruleForm.resetFields();//element封装的方法,清空模态框的值
      this.title="" //初始化title的值
      this.ruleForm={//初始化addForm中的值
      InputCode: '',
      InputName:'',
      BuyInPrice: '',
      BuyInAmount: '',
      BuyInDate:'',
      id:'',
      }
      this.row=[]
    },
    //表单提交,点击确定按钮(确定添加或编辑)
    submitForm() {
      this.$refs.ruleForm.validate(valid => {
        if (valid) {
          let params = {
            TradeCode: this.ruleForm.InputCode,
            TradeName: this.ruleForm.InputName,
            TradeAmount: this.ruleForm.BuyInAmount,
            TradePrice: this.ruleForm.BuyInPrice,
            TradeDate:this.ruleForm.BuyInDate,
          };
          //现在笨办法，通过设置ruleform.id的富余变量来判断是新增还是编辑，但是无法获取row.id,现在统一
          if(this.ruleForm.id!==0){//当没有传过来id的时候,说明是添加,所以发送添加请求
            this.AddNewTrade();
            data => {
                  // console.log(data, 1122);
                  this.$message.success("新增成功！")
                  this.dialogAddgsVisible = false
                  this.handleCurrentChange(1);
            }
          }else{//发送编辑请求
          // console.log(this.ruleForm.id)
            this.row=params;
            //现在的笨办法是靠this.title这个不用的变量来传递了index参数，实现把row的数据传递回表格的功能
            //数据双向绑定功能抓紧研究。
            this.$set(this.StockTradeData,this.title,this.row)
            this.centerDialogVisible = false;
            //还需要试验一下是否需要先存储在读取来实现刷新。
            localStorage.setItem(
              "StockTradeData",
              JSON.stringify(this.StockTradeData)
            );
            console.log(this.StockTradeData)
            this.StockTradeData = JSON.parse(localStorage.getItem("StockTradeData"));
          }
        }
       });
    },
    resetForm(formName) {
        this.$refs[formName].resetFields();
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

//需要更新调整页面布局。
<style>
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>
