<template>
  <div id="app">
    <el-upload
      class="upload-demo"
      drag
      action="donothing"
      :http-request="fileUpload"
      multiple>
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
    </el-upload>
    <div class="tableArea">
      <el-table
        :data="dataFilter()"
        >
        <el-table-column sortable
        v-for="item in key"
        :prop="item"
        :label="item">

        </el-table-column>

      </el-table>
      <vpn-pagination :total="total" :offset="oAssEquipmentDto.offset"
                      :pageSize="oAssEquipmentDto.pageNumber"
                      @handleSizeChangeSub="handleSizeChange"
                      @handleCurrentChangeSub="handleCurrentChange"></vpn-pagination>

    </div>
    <div v-if="dataList.length!==0" class="picArea">
      <div class="box">
        共{{this.total}}人
      </div>

    </div>


  </div>
</template>


<script>
  import XLSX from 'xlsx'
  import filter from './assets/config.json'
  import _ from 'lodash'
  import VpnPagination from "./components/vpnPagination";

  export default {
  name: 'App',
    components: {VpnPagination},
    data(){
    return{
      currentFile:{},
      dataList:[],
      key:['姓名','出生日期','性别','政治面貌','本科毕业学校','本科毕业专业','硕士毕业学校','硕士毕业专业','录取院系','录取专业'],
      total:0,
      oAssEquipmentDto: {
        offset: 1,
        pageNumber: 10,
      },
    }
  },
  methods:{
    dataFilter(){
      const result = this.dataList
      this.total = this.dataList.length
      if(this.dataList.length>=0)
        return result.slice((this.oAssEquipmentDto.offset - 1)*this.oAssEquipmentDto.pageNumber, this.oAssEquipmentDto.offset*this.oAssEquipmentDto.pageNumber)
    },
    fileUpload(file){
      let formData = new FormData()
      formData.append('file',file.file)
      console.log(file)
      this.currentFile={
        file : formData,
        name : file.file.name
      }
      this.readToList(file.file)
    },
    async readToList(currentFile){
      var fileReader = new FileReader();
      fileReader.onload = async (ev) =>{
        try {
          var data = ev.target.result
          var workbook = XLSX.read(data, {
            type: 'binary'
          }) // 以二进制流方式读取得到整份excel表格对象
          var persons = []; // 存储获取到的数据
        } catch (e) {
          console.log('文件类型不正确');
          return;
        }
        // 表格的表格范围，可用于判断表头数量是否正确
        var fromTo = '';
        // 遍历每张表读取
        for (var sheet in workbook.Sheets) {
          if (workbook.Sheets.hasOwnProperty(sheet)) {
            fromTo = workbook.Sheets[sheet]['!ref'];
            const sheet2JSONOpts = {
              defval: ''//给defval赋值为空的字符串
            }
            persons = persons.concat(XLSX.utils.sheet_to_json(workbook.Sheets[sheet],sheet2JSONOpts).map(item=>{
              return _.pick(item, this.key);
            }));
            // break; // 如果只取第一张表，就取消注释这行
          }
        }
        //在控制台打印出来表格中的数据
        this.dataList = persons
        console.log(fromTo,persons);

        var keyAry = []
        for(var key in persons[0]){
          keyAry.push(key);
        }
        this.tableHeader = keyAry
      };
      // 以二进制方式打开文件
      fileReader.readAsBinaryString(currentFile);
    },
    handleSizeChange(val) {
      this.oAssEquipmentDto.pageNumber = val;
    },
    //翻页
    handleCurrentChange(val) {
      this.oAssEquipmentDto.offset = val;

    },
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
  .tableArea{
    display: flex;
    flex-direction: column;
    padding: 10px 30px;
  }
  .picArea{
    padding: 20px;

  }
  .box{
    width: 50%;
    padding: 20px;
  }
</style>
