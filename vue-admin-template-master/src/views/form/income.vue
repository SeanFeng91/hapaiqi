<!-- Form -->
<template>
  <div>
  <div>
    <el-row>
      <!-- <el-button>默认按钮</el-button> -->
      <el-button type="primary" @click="dialogFormVisible = true">新增一笔收入</el-button>
      <!-- <el-button type="text" @click="dialogFormVisible = true">记录一笔收入</el-button> -->
    </el-row>
  <el-dialog title="收入" :visible.sync="dialogFormVisible">
    <el-form :model="form">
      <el-form-item label="金额" :label-width="formLabelWidth">
        <el-input v-model="form.name" autocomplete="off"></el-input>
      </el-form-item>

      <!-- <div class="block"> -->
          <!-- <span class="demonstration">默认</span> -->

        <!-- </div> -->

      <el-form-item label="日期" :label-width="formLabelWidth">

        <el-date-picker
          v-model="value1"
          type="date"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="类别" :label-width="formLabelWidth">
        <el-select v-model="form.region" placeholder="请选择类别">
          <el-option label="食品" value="food"></el-option>
          <el-option label="生活用品" value="dailyuse"></el-option>
          <el-option label="娱乐" value="entertainment"></el-option>
        </el-select>

      </el-form-item>
      <el-input
        type="textarea"
        :rows="2"
        placeholder="备注"
        v-model="textarea">
      </el-input>


    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
      <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
    </div>
  </el-dialog>
  </div>
  <div>
	   <el-table
	      :data="tableData"
	      border
	      style="width: 100%">
	      <el-table-column
	        prop="date"
	        label="日期"
	        width="180">
	      </el-table-column>
	      <el-table-column
	        prop="type"
	        label="类别"
	        width="180">
	      </el-table-column>
        <el-table-column
          prop="money"
          label="金额"
          width="180">
        </el-table-column>
	      <el-table-column
	        prop="ps"
	        label="备注">
	      </el-table-column>
	    </el-table>
  </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {

        // dialogTableVisible: false,
        dialogFormVisible: false,
        form: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        tableData: [{
                  date: '2019-02-02',
                  type: '娱乐',
                  money: '29.20',
                  ps: '看《星际穿越》'
                }, {
                  date: '2019-01-02',
                  type: '买菜',
                  money: '239.20',
                  ps: '洋葱、鸡肉'
                }],
        pickerOptions: {
                  disabledDate(time) {
                    return time.getTime() > Date.now();
                  },
                  value1: '',
                  // value2: '',
        },
        formLabelWidth: '120px'
      };
    }
  };
</script>
