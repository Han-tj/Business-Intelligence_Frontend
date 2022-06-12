<template>
  <div class="app-container">
    <el-card style="width:100%; margin:0 auto; padding-top: 10px; padding-bottom: 0px;">
      <el-form ref="form" :model="form" label-width="120px">
        <el-form-item label="">
          <el-row :gutter="10">
            <el-col :span="1">
              <div class="grid-content">
                实体1
              </div>
            </el-col>
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
          </el-row>
          <el-row :gutter="10" style="margin-top: 15px">
            <el-col :span="1">
              <div class="grid-content">
                实体2
              </div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content">
                <el-select v-model="formMark.entity2" placeholder="">
                  <el-option label="论文" value="paper" />
                  <el-option label="作者" value="author" />
                  <el-option label="研究主题" value="interest" />
                  <el-option label="研究机构" value="affiliation" />
                  <el-option label="学术刊物/会议" value="venue" />
                </el-select>
              </div>
            </el-col>
            <el-col :span="12" v-if="formPaperMark2">
              <div class="grid-content">
                <el-input v-model="form.entity2.paper" placeholder="请输入论文标题" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formAuthorMark2">
              <div class="grid-content">
                <el-input v-model="form.entity2.author" placeholder="请输入作者名称" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formInterestMark2">
              <div class="grid-content">
                <el-input v-model="form.entity2.interest" placeholder="请输入研究主题" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formAffiliationMark2">
              <div class="grid-content">
                <el-input v-model="form.entity2.affiliation" placeholder="请输入机构名称" />
              </div>
            </el-col>
            <el-col :span="12" v-if="formVenueMark2">
              <div class="grid-content">
                <el-input v-model="form.entity2.venue" placeholder="请输入刊物/会议名称" />
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
    <el-card style="width:100%; margin:0.7% auto; height: 400px;">
      <el-row>
        <el-col :span="24"><div class="grid-content">
          <div id="myChart" :style="{width: '100%', height: '400px'}"></div>
        </div></el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
import { boolean } from 'yargs';

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
        entity2:{
          paper:'',
          author:'',
          interest:'',
          affiliation:'',
          venue:''
        },
      },
      formMark:{
        entity: 'paper',
        entity2: 'paper',
      },
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
    formPaperMark2(){
      return this.formMark.entity2=="paper";
    },
    formAuthorMark2(){
      return this.formMark.entity2=="author";
    },
    formInterestMark2(){
      return this.formMark.entity2=="interest";
    },
    formAffiliationMark2(){
      return this.formMark.entity2=="affiliation";
    },
    formVenueMark2(){
      return this.formMark.entity2=="venue";
    },
  },
  methods: {
    onSubmit() {
      this.draw()
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

      let queryName2='';
      if(this.formPaperMark2){
        queryName2=this.form.entity2.paper
      }
      else if(this.formAuthorMark2){
        queryName2=this.form.entity2.author
      }
      else if(this.formInterestMark2){
        queryName2=this.form.entity2.interest
      }
      else if(this.formAffiliationMark2){
        queryName2=this.form.entity2.affiliation
      }
      else if(this.formVenueMark2){
        queryName2=this.form.entity2.venue
      }

      this.$axios
      .get("query/double", {
        params: {
          name1: queryName,
          category1: this.formMark.entity,
          name2: queryName2,
          category2: this.formMark.entity2,
          limit: 20000
        }
      })
      .then((response)=>{
        this.links=response.data.links;
        this.nodes=response.data.nodes;

        // // this.nodes[0].fixed = true;
        // this.nodes[0].x = 250;
        // this.nodes[0].y = 200;
        // // this.nodes[0].draggable=false;
        // this.nodes[0].symbolSize = 50;
        // this.nodes[0].gravity = 0;

        // // this.nodes[1].fixed = true;
        // this.nodes[1].x = 980;
        // this.nodes[1].y = 200;
        // // this.nodes[1].draggable=false;
        // this.nodes[1].symbolSize = 50;
        // this.nodes[1].gravity = 0;

        console.log(this.nodes);

        this.querySucceed();

        this.draw();
      })
      .catch(error => {
        this.queryFail();
        console.log(error)
      });
    },
    draw(){
      if(this.draw.myChart==null){ 
        // 初始化echarts实例
        this.draw.myChart = this.$echarts.init(document.getElementById('myChart'));
        this.draw.status=false;
      }
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
              edgeLength: [100, 200],
              repulsion: 500,
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
                  x: 250,
                  y: 200,
                  symbolSize: 50,
                  // fixed: true,
                  gravity: 0,
                }
              }
              else if(index==1){
                return{
                  name:node.index,
                  category:node.category,
                  value: node.name,
                  x: 980,
                  // fixed: true,
                  y: 200,
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
      this.draw.myChart.setOption(option);//设置option
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
