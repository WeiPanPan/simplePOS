<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class="posSys" id="posList">
        <el-tabs type="border-card">
          <el-tab-pane label="点餐">
              <el-table :data="tabData" style="width: 100%" border="true" >
                <el-table-column
                  prop="goodsName"
                  label="商品名称"
                 >
                </el-table-column>
                <el-table-column
                  prop="count"
                  label="数量"
                >
                </el-table-column>
                <el-table-column
                  prop="price"
                  label="金额">
                </el-table-column>
                <el-table-column
                  label="操作"
                  width="100"
                  fixed="right">
                  <template slot-scope="scope">
                    <el-button @click="delGoods(scope.$index,scope.row)" type="text" size="small">删除</el-button>
                    <el-button @click="addGoods(scope.row)" type="text" size="small">添加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="total">
                <span>合计</span>
                <span>数量：{{totalNum}}</span>
                <span>金额：{{totalMon}}元</span>
              </div>
              <div>
                <el-button @click="handleClick(scope.row)" type="warning">挂单</el-button>
                <el-button @click="clearGoods" type="danger" >删除</el-button>
                <el-button @click="checkout" type="success">结账</el-button>
              </div>
          </el-tab-pane>
          <el-tab-pane label="挂单"></el-tab-pane>
          <el-tab-pane label="外卖"></el-tab-pane>
        </el-tabs>
      </el-col>
      <el-col :span='17' class="sale">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" @click="addGoods(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs >
            <el-tab-pane label="汉堡">
              <ul class='cookList'>
                <li v-for="goods in type0Goods" @click="addGoods(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg"  width="100%"></span>
                  <span class="foodName">{{goods.goodsName }}</span>
                  <span class="foodPrice">￥{{goods.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class='cookList'>
                <li v-for="goods in type1Goods" @click="addGoods(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg"  width="100%"></span>
                  <span class="foodName">{{goods.goodsName }}</span>
                  <span class="foodPrice">￥{{goods.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class='cookList'>
                <li v-for="goods in type2Goods" @click="addGoods(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg"  width="100%"></span>
                  <span class="foodName">{{goods.goodsName }}</span>
                  <span class="foodPrice">￥{{goods.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class='cookList'>
                <li v-for="typeGoods in type3Goods" @click="addGoods(goods)">
                  <span class="foodImg"><img :src="typeGoods.goodsImg"  width="100%"></span>
                  <span class="foodName">{{typeGoods.goodsName }}</span>
                  <span class="foodPrice">￥{{typeGoods.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>

          </el-tabs>
        </div>
      </el-col>
    </el-row>

  </div>
</template>

<script>
import ElRow from "element-ui/packages/row/src/row";
import ElCol from "element-ui/packages/col/src/col";
import axios from "axios";
export default {
  components: {
    ElCol,
    ElRow},
  name: 'pos',
  created(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(response=>{
         this.oftenGoods=response.data;
    })
      .catch(error=>{alert('链接失败...')});
    axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response=>{
        this.type0Goods=response.data[0];
        this.type1Goods=response.data[1];
        this.type2Goods=response.data[2];
        this.type3Goods=response.data[3];
      })
      .catch(error=>{alert('链接失败...')})
  },
  mounted(){
    document.getElementById('posList').style.height=`${document.body.clientHeight}px`;
    window.onresize = function temp() {
      document.getElementById('posList').style.height = `${document.body.clientHeight}px`;
    };
  },
  data() {
    return{
      totalNum:0,
      totalMon:0,
      tabData:[],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
    }
  },
  methods:{
    addGoods(goods){
      this.totalNum=0;
      this.totalMon=0;
      //商品是否存在列表中
        var isHave=false;
        for(let i=0;i<this.tabData.length;i++){
          console.log(this.tabData[i].goodsId);
          if(this.tabData[i].goodsId==goods.goodsId){
            isHave=true;//存在
          }
        }
      //根据编写值判断业务逻辑
        if(isHave){
          var arr=this.tabData.filter(o=>o.goodsId==goods.goodsId);
          arr[0].count++;
          console.log(arr);
          console.log(this.tabData);
        }else{
          //不存在就推入数组
          let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
          this.tabData.push(newGoods);

        }
        //计算汇总价格和数量
        this.tabData.forEach(item=>{this.totalNum+=item.count;this.totalMon+=item.price*item.count});
    },
    delGoods(index,obj){
      obj.count--;
      if(obj.count>=1){
        count--;
      }
      else{
        this.tabData.splice(index,1);
      }
      this.totalNum-=1;
      this.totalMon-=obj.price;
    },
    clearGoods(){
      this.totalNum=0,
        this.totalMon=0,
        this.tabData=[]
    },
    checkout() {
      if (this.totalNum!=0) {
        this.tabData = [];
        this.totalNum = 0;
        this.totalMon = 0;
        this.$message({
          message: '结账成功，感谢你又为店里出了一份力!',
          type: 'success'
        });
      }else{
        this.$message.error('不能空结。老板了解你急切的心情！');
      }

    }
  }

}
</script>

<style>
.posSys{background-color: #f9fcfa; border-right:1px solid #000000;}
.posSys .total span{display: inline-block;padding: 5px 10px 5px 10px;border-right: 1px solid grey;margin: 15px auto;}
.posSys .total :first-child{padding: 5px 40px 5px 5px}
.posSys .total :last-child{border-right: 1px}
.sale{background-color: bisque}
.title{
  height: 20px;
  border-bottom:1px solid #D3DCE6;
  background-color: #F9FAFC;
  padding:10px;
  text-align: left;
}
.often-goods-list ul li{
  list-style: none;
  float:left;
  border:1px solid #E5E9F2;
  padding:10px;
  margin:5px;
  background-color:#fff;
  cursor: pointer;
}
.o-price{
  color:#58B7FF;
}
.goods-type{clear: both; padding-left: 20px;}
.cookList li{
  list-style: none;
  width:23%;
  border:1px solid #E5E9F2;
  height: auot;
  overflow: hidden;
  background-color:#fff;
  padding: 2px;
  float:left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span{
  display: block;
  float:left;
}

.foodImg{
  width: 40%;
}
.foodName{
  font-size: 15px;
  padding-left: 10px;
  color:brown;
  width: 80px;
  word-wrap: break-word;
  font-family: 微软雅黑;

}
.foodPrice{
  font-size: 16px;
  padding-left: 10px;
  padding-top:10px;
}
</style>
