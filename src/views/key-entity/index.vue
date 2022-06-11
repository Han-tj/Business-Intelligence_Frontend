<template>
  <div class="app-container">
    <el-card style="width:100%; margin:0 auto; padding-top: 20px">
      <el-form ref="form" :model="form" label-width="120px">
        <el-form-item label="">
          <el-row :gutter="10">
            <el-col :span="1">
              <div class="grid-content">
                查询
              </div>
            </el-col>
            <el-col :span="8" style="margin-left:-5px">
              <div class="grid-content">
                <el-input v-model="interest" placeholder="请输入领域名称" />
              </div>
            </el-col>
            <el-col :span="2" style="margin-left:5px">
              <div class="grid-content" >
                中的关键
              </div>
            </el-col>
            <el-col :span="6" style="margin-left:-25px">
              <div class="grid-content">
                <el-select v-model="formMark" placeholder="">
                  <el-option label="作者" value="author" />
                  <el-option label="单位" value="affiliation" />
                  <el-option label="学术刊物/会议" value="venue" />
                </el-select>
              </div>
            </el-col>
            <el-col :span="2" :push="3" >
              <div class="grid-content">
                <el-button type="primary" @click="onSubmit">查询</el-button>
              </div>
            </el-col>
          </el-row>     
        </el-form-item>
      </el-form>
    </el-card>
    <el-card style="width:100%; margin:0.7% auto; height: 440px;">
      <el-row>
        <el-col :span="11"><div class="grid-content">
          <el-table
            :data="tableData"
            :stripe="true"
            height="370"
            :highlight-current-row='true'
            style="width: 95%; margin: 0; height: 330px;">
            <el-table-column
              prop="name"
              label="名称"
              align='center'
              width="400">
            </el-table-column>
            <el-table-column
              prop="value"
              label="重要性"
              align='center'>
            </el-table-column>
          </el-table>
          <el-pagination
            layout="total"
            style="margin: 0 auto;"
            :total="tableData.length">
          </el-pagination>
        </div></el-col>

        <el-col :span="1"><div class="grid-content">
          <el-divider direction="vertical" style="height: 420px;"></el-divider>
        </div></el-col>

        <el-col :span="12"><div class="grid-content">
          <div id="myChart" :style="{width: '100%', height: '400px'}"></div>
        </div></el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      interest:'',
      formMark: 'author',
      tableData: [],
    }
  },
  methods: {
    onSubmit() {
      let queryUrl='';
      if(this.formMark=='author'){
        queryUrl='query/author_rank'
      }
      else if(this.formMark=='affiliation'){
        queryUrl='query/affiliation_rank'
      }
      else if(this.formMark=='venue'){
        queryUrl='query/publication_rank'
      }
      this.$axios
      .get(queryUrl, {
        params: {
          interest: this.interest
        }
      })
      .then((response)=>{
        this.tableData=response.data;

        this.querySucceed();

        this.draw();
      })
      .catch(error => {
        this.queryFail();
        console.log(error)
      });
    },
    draw(){
      // 初始化echarts实例
      let myChart = this.$echarts.init(document.getElementById('myChart'))
      // 指定图表的配置项和数据
      var option = {
        tooltip: {
          trigger: 'item'
        },
        // legend: {
        //   orient: 'vertical',
        //   left: 'left'
        // },
        series: [
          {
            type: 'pie',
            radius: '50%',
            data: this.tableData,
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      }
      //防止越界，重绘canvas
      // window.onresize = myChart.resize;
      myChart.setOption(option);//设置option
    },
    querySucceed() {
      this.$notify({
        title: '成功',
        message: "成功获取查询结果",
        type: 'success'
      });
    },
    queryFail() {
      this.$notify.error({
        title: '错误',
        message: "未能获取查询结果",
      });
    },
    runtimeFormatter(row,column){
      let runtime = row.runtime;
      if(runtime==0){
        return '-'
      }
      return runtime;
    },
  },
  mounted(){
    this.draw();
  }
}
</script>

<style lang="scss" scoped>
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .el-divider--vertical{
    display: inline-block;
    width: 1px;
    height: 31em;
    margin: 0 8px;
    vertical-align: middle;
    position: relative;
  } 
</style>
