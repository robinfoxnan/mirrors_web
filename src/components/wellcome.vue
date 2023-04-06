<template>
    <div id="wellcome">
        欢迎使用本流量镜像设备。<br/>
        <br/>
        下面简要介绍几种典型应用模式
        <br/>
        <!--
        <img width="400px" height="400px" src="../assets/page1.png" style="border-radius: 5px">
        -->
        <el-carousel trigger="click" :interval="60000" height="250px" arrow="always">

            <el-carousel-item v-for="item in carouselList" :key="item">
                
                <el-image         
                :src="item" 
                style="position:absolute;top:0;bottom:0;left:0;right:0;width:100%;margin:auto;" 
                fit="contain"></el-image>
 
            </el-carousel-item>

        </el-carousel>
        
        <br/><br/>
        设置步骤<br/><br/>
       1. 在规则页面添加流入和流出端口即可实现复制；
       <br/><br/>
       2. 可以设置多组规则实现某端口流量复制到多个端口；
       <br/><br/>
       3. 可以设置多组规则实现多端口流量汇聚到某个端口；
       <br/><br/>
       4. 需要注意：端口不能同时设置数为据流入和流出端口。
       


        <span class="descript"> </span><br/><br/>

        <span class="descript"> 
        </span>
    </div>
</template>
<script>
export default {
    //name :"欢迎",
    
    data() {
        return {
            carouselList:[
                require("../assets/page1.png"),
                require("../assets/page2.png"),
                require("../assets/page3.png"),
                ] ,
        };
    },
    
   

    //组件创建
    created() {
    },
    mounted(){
    },
    methods:{
    }
}
</script>
<style lang="less" scoped>

.descript {
    font-size: 15px;
    color: #242323;
  }

  /deep/.el-carousel__item img{
     height: 250px;
	 max-width: 700px;
  }
  /deep/.el-carousel__item{
	  text-align: center;
  }
  /deep/.el-carousel__container {
      height: 250px;
  }

</style>