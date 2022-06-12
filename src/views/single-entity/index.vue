<template>
  <div class="app-container">
    <el-card style="width:100%; margin:0 auto; padding-top: 20px">
      <el-form ref="form" :model="form" label-width="120px">
        <el-form-item label="实体">
          <el-row :gutter="10">
            <el-col :span="6">
              <div class="grid-content">
                <el-select v-model="formMark.entity" placeholder="">
                  <el-option label="论文" value="paper" />
                  <el-option label="作者" value="author" />
                  <el-option label="研究主题" value="interest" />
                  <el-option label="研究机构" value="affiliation" />
                  <el-option label="学术刊物/会议" value="venue" />
                </el-select>
              </div>
            </el-col>
            <el-col :span="12" v-if="formPaperMark">
              <div class="grid-content">
                <el-input v-model="form.entity.paper" placeholder="请输入论文标题" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formAuthorMark">
              <div class="grid-content">
                <el-input v-model="form.entity.author" placeholder="请输入作者名称" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formInterestMark">
              <div class="grid-content">
                <el-input v-model="form.entity.interest" placeholder="请输入研究主题" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formAffiliationMark">
              <div class="grid-content">
                <el-input v-model="form.entity.affiliation" placeholder="请输入机构名称" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formVenueMark">
              <div class="grid-content">
                <el-input v-model="form.entity.venue" placeholder="请输入刊物/会议名称" />
              </div>
            </el-col>
            <el-col :span="2" :push="1" >
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
              width="300">
            </el-table-column>
            <el-table-column
              prop="category"
              label="类别"
              align='center'
              width="90">
            </el-table-column>
            <el-table-column
              prop="relationship"
              label="与实体的关系"
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
      nodes:[],
      links:[],
      form: {
        entity:{
          paper:'',
          author:'',
          interest:'',
          affiliation:'',
          venue:''
        },
      },
      formMark:{
        entity: 'paper',
      },
      tableData: [],
    }
  },
  computed:{
    formPaperMark(){
      return this.formMark.entity=="paper";
    },
    formAuthorMark(){
      return this.formMark.entity=="author";
    },
    formInterestMark(){
      return this.formMark.entity=="interest";
    },
    formAffiliationMark(){
      return this.formMark.entity=="affiliation";
    },
    formVenueMark(){
      return this.formMark.entity=="venue";
    },
  },
  methods: {
    onSubmit() {
      let queryName='';
      if(this.formPaperMark){
        queryName=this.form.entity.paper
      }
      else if(this.formAuthorMark){
        queryName=this.form.entity.author
      }
      else if(this.formInterestMark){
        queryName=this.form.entity.interest
      }
      else if(this.formAffiliationMark){
        queryName=this.form.entity.affiliation
      }
      else if(this.formVenueMark){
        queryName=this.form.entity.venue
      }
      this.$axios
      .get("query/single", {
        params: {
          name: queryName,
          category: this.formMark.entity,
          limit: 200000
        }
      })
      .then((response)=>{
        this.links=response.data.links;
        this.nodes=response.data.nodes;

        // this.nodes[0].fixed = true;
        this.nodes[0].x = 250;
        this.nodes[0].y = 200;
        // this.nodes[0].draggable=false;
        this.nodes[0].symbolSize = 50;
        this.nodes[0].gravity = 0;

        this.tableData=[];
        for(let i=0; i<this.links.length; ++i){
          this.tableData.push({
            name: this.nodes[this.links[i].target+this.links[i].source].name,
            category: this.nodes[this.links[i].target+this.links[i].source].category,
            relationship: this.links[i].label
          })
        }

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
        legend: {
          data: ['paper', 'author', 'affiliation', 'interest', 'publication']
        },
        series: [
          {
            type: 'graph',
            layout: 'force',
            symbolSize:30,
            force: {
              edgeLength: [50, 75],
              repulsion: 300,
              gravity: 0.1
            },
            animation: false,
            // labelLayout: {
            //   hideOverlap: true
            // },
            draggable: true,
            data: this.nodes.map((node,index)=>{
              if(index==0){
                return{
                  name:node.index,
                  category:node.category,
                  value: node.name,
                  symbolSize: 50,
                  gravity: 0,
                }
              }
              return {
                name:node.index,
                category:node.category,
                value: node.name,
              }
            }),
            categories: [
              {
                "name": "paper",
              },
              {
                "name": "author",
                "keyword": {},
                "base": ""
              },
              {
                "name": "affiliation",
                "keyword": {},
                "base": ""
              },
              {
                "name": "interest",
                "keyword": {},
                "base": ""
              },
              {
                "name": "publication",
                "keyword": {}
              }
            ],
            emphasis: {
              scale: true,
              focus: 'adjacency',
              blurScope: 'series',
              label: {
                position: 'right',
                formatter: '{c}',
                show: true
              },
              edgeLabel:{
                show: true,
                formatter: '{c}',
                align:'center',
              },
            },
            edgeSymbol: ['circle', 'arrow'],
            edgeSymbolSize: [4, 10],
            edges: this.links.map((link)=>{
              return {
                source:link.source,
                target:link.target,
                value: link.label,
              }
            }),
          }
        ]
      };
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
