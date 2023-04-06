<template>
    <div id="test2">
        <el-row>
        
        <el-col :span="6">
          
          <el-button type="success" @click="handleNew" plain>添加规则</el-button>
          <el-button type="warning" @click="handleRestart" plain>重启服务</el-button>
          
          </el-col>

          <el-col :span="6">
            当前服务状态：{{ status }}
          </el-col>
          <el-col :span="6"></el-col>
        </el-row>
          <!-- 这里设置表 -->
          <el-table :data="tableDataBrRule" border
      style="width: 100%"
      :row-class-name="tableRowClassName">
       
      <el-table-column fixed prop="id" label="行号" >
      </el-table-column>
      
      <el-table-column prop="alias" label="流入端口"> </el-table-column>  
      <el-table-column prop="destAlias" label="流出端口"> </el-table-column>

      <!--
      <el-table-column prop="prot" label="协议"> </el-table-column>
      <el-table-column prop="action" label="端口"> </el-table-column>
      -->
      
      <!--
      <el-table-column prop="action" label="动作"> </el-table-column>
-->
      <el-table-column label="操作" width="200">
        <template slot-scope="scope">
        <!--
            <el-button @click="handleEdit(scope.row)"  type="warning" size="small">编辑</el-button>
          -->
          <el-button @click="handleDel(scope.row)" type="danger" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>


    <div>
    <!-- 测试对话框Form按钮 
    <el-button type="text" @click="dialogFormVisible = true">打开Dialog</el-button>
    -->
    <!-- Form -->
    <el-dialog title="定义端口流向与规则" :visible.sync="dialogFormVisible" width="50%">
    <el-form :model="form">
        <el-form-item label="选择流入网口" :label-width="formLabelWidth" >
        <el-select v-model="form.selportIn" placeholder="请选择入端口" :disabled="form.isEdit">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="选择流出网口" :label-width="formLabelWidth" >
        <el-select v-model="form.selportOut" placeholder="请选择入端口" :disabled="form.isEdit">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item">
            </el-option>
          </el-select>
        </el-form-item>

        <!--
        <el-form-item label="协议" :label-width="formLabelWidth">
        <el-select v-model="form.proto" placeholder="请选择协议">
        <el-option label="all" value="all"></el-option>
        <el-option label="tcp" value="tcp"></el-option>
        <el-option label="udp" value="udp"></el-option>
        <el-option label="icmp" value="icmp"></el-option>
        </el-select>
        </el-form-item>
        -->
      

    </el-form>
    <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="saveRule">确 定</el-button>
    </div>
    </el-dialog>
    </div>

    </div>
</template>
<script>

export default {
    name: 'test5',
    //组件创建
    created() {
    },
    mounted(){
      this.refreshPort();
      this.refreshTable();
    },
    data() {
        return{
            dialogFormVisible: false,
            status: "",
            form: {
              selportIn: "", // 对话框返回的接口 
              selportOut: "",
              line : "",
              isNew: true,
            },
            formLabelWidth: '120px',
            options: [
            //   {
            //   value: 'eth0',
            //   label: 'eth0'
            // }, 
    
            ],
            
      

            tableDataBrRule: [
                // {       
                // line: 1,
                // alias: "br0",
                // sip: "192.168.0.1/24",
                // dip: "any",
                // sport: 0,
                // dport: 0,
                // proto: "tcp",
                // action: "丢弃",
                // direction:"流出"

                // },
          
            ],
        }
    },
    methods:{
        tableRowClassName({ row, rowIndex }) {
            row.id = rowIndex +1;
            if (rowIndex % 2 === 0) {
                return "warning-row";
            } else if (rowIndex % 2 === 1) {
                return "success-row";
                
            }
         
        },

        refreshPort(){
          this.$rpc.allportlist().then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        this.options = []
                        if (data.state == 'ok') {
                            for(var i = 0; i < data.ports.length; i++){                         
                                //data.ports[i].show = false
                                var item = {value: data.ports[i].name,
                                          label: data.ports[i].alias}
                                this.options.push(item);          
            
                            }

                            console.log(this.options);
                          
                           this.tableDataPort = data.ports
                           console.log(data.ports)
                          
                        } else {
                            this.$message("获取端口列表失败：" + data.des)
                        }
                    })
                } else {
                    console.log(res.status);
                    this.$message("获取端口列表失败：" + data.des)

                }
            })

        },

        refreshTable(){
          this.$rpc.listMapRules().then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        if (data.state == 'ok') {
                           
                          
                           this.tableDataBrRule = data.maps
                           console.log(data.rules)
                           this.checkStatus();
                          
                        } else {
                            this.$message("获取规则列表失败：" + data.des)
                        }
                    })
                } else {
                    console.log(res.status);
                    this.$message("获取规则列表失败：" + res.status)

                }
            })
            .catch(error => {
                    console.error(error);
                    this.$message("遇到失败：" + error)
           })
        },

        // 对话框点击确定按钮后
        saveRule(){
          var rule = {  
            name: this.form.selportIn.value,
            alias: this.form.selportIn.label,
            index: 0,
            dest: this.form.selportOut.value,
            destAlias :this.form.selportOut.label,
            destIndex: 0,
            protol: "",
            dport:""
          
          }

          // 检查网口
          if (this.form.selportIn == ""){

            this.$message("请选择一个流入端口");
            return
          }

          if (this.form.selportOut == ""){

            this.$message("请选择一个流出端口");
            return
            }

          if (this.form.selportIn.value == this.form.selportOut.value){
            this.$message("错误：流入和流出端口不能相同");
            return
          }   

          // 这里区别是新增还是编辑
          if (!this.form.isEdit){
            this.addRule(rule)
          }else{
            ///this.editRule(rule)
          }
          
          console.log(rule)

        },
////////////////////////////////////////////////
//     处理点击按钮动作
       
        handleNew(){
            this.form =  {
              selportIn: "", // 对话框返回的接口 
              selportOut: "",
              line : "",
              isEdit: false,
            };
            this.dialogFormVisible = true;

        },
        handleEdit(row) {
          this.form =  {
              selportIn: "", // 对话框返回的接口 
              selportOut: "",
              line : "",
              isEdit: true,
            };
            this.dialogFormVisible = true; 
        },
        handleDel(row) {
            var index = row.id;
            var rule = this.tableDataBrRule[index-1];
            console.log(rule);
            //this.dialogFormVisible = true;
            this.$rpc.delMapRule(rule).then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        if (data.state == 'ok') {
                            
                           this.$message("删除规则完成。");
                           this.refreshTable();
                           console.log(data.rules)
                          
                          
                        } else {
                            this.$message("删除规则遇到错误：" + data.des);
                            this.refreshTable();
                           console.log(data.rules);
                     
                        }
                    })
                } else {
                    console.log(res.status);
                    this.$message("删除规则遇到错误：" + res.status);

                }
            }).catch(error => {
                    console.error(error);
                    this.$message("遇到失败：" + error);
           })

        },
        handleRestart(){
          
          this.$rpc.restartMapRule().then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        if (data.state == 'ok') {
                            
                           this.$message("重启服务完成。" );
                           this.checkStatus();
                           console.log(data.rules);
                          
                          
                        } else {
                            this.$message("重启服务遇到错误：" + data.des);
              
                           console.log(data.rules);
                     
                        }
                    })
                } else {
                    console.log(res.status);
                    this.$message("重启服务遇到错误：" + data.des);

                }
            }).catch(error => {
                    console.error(error);
                    this.$message("重启服务遇到错误：" + error);
           })
        },
        /////////////////////////////////////
        // 添加或者更改

        addRule(rule){
          this.$rpc.addMapRule(rule).then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        if (data.state == 'ok') {
                            
                           this.$message("添加规则完成。")
                           this.refreshTable()
                           console.log(data.rules)
                           this.dialogFormVisible = false;
                          
                        } else {
                            this.$message("添加规则遇到错误：" + data.des)
                            this.refreshTable()
                           console.log(data.rules)
                          // this.dialogFormVisible = false;
                        }
                    })
                } else {
                    console.log(res.status);
                    this.$message("添加规则遇到错误：" + data.des)

                }
            }).catch(error => {
                    console.error(error);
                    this.$message("遇到失败：" + data.des)
           })
        },
        checkStatus(){
          

          this.$rpc.getMirrorStatus().then((res) => {
                if (res.ok) {//如果取数据成功
                    res.json().then((data) => {
                        //转化为json数据进行处理
                        console.log(data);
                        if (data.state == 'ok') {
                            
                          this.status = data.des;
                          
                        } else {
                           
                           console.log(data.rules)
                           this.status = "获取服务状态错误" + data.des
                        }
                    })
                } else {
                  this.status = "未知"
                    console.log(res.status);
                    this.$message("获取服务状态到错误：" + data.des)

                }
            }).catch(error => {
                     this.status = "未知"
                    console.error(error);
                    this.$message("遇到失败：" + data.des)
           })
        },

        

        
    }
}
</script>
<style lang="less" scoped>

.search_input{
    border: 1px solid #DBDBDB;
    border-radius: 2px 0 0 2px;
    height: 40px;
    width: 220px;
    line-height: 30px;
    padding: 0px 0px;
    box-sizing: border-box;
}

</style>