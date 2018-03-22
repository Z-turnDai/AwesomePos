<template>
    <div id="pos">
        <el-row>
            <el-col :span="7" class="pos-order" id="order-list">
               <el-tabs>
                   <el-tab-pane label="点餐">
                       <el-table  :data="tableData" style="width: 100%">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" label="数量" width="50"></el-table-column>
                            <el-table-column prop="price" label="金额" width="70"></el-table-column>
                            <el-table-column  label="操作"  width="100" fixed="right">
                                <template slot-scope="scope">
                                     <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>   
                                     <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>   
                                </template>
                            </el-table-column>
                       </el-table>
                       <div class="total-goods">
                           <small>数量： </small>{{totalCount}} &nbsp;<small>金额：</small>{{totalMoney}} 元
                       </div>
                        <div class="div-btn">
                              <el-button type="warning">挂单</el-button>   
                              <el-button type="danger" @click="delAllMoney">删除</el-button>   
                              <el-button  type="success" @click="checkout">结账</el-button>   
                        </div>
                    </el-tab-pane>  
                   <el-tab-pane label="挂单">
                       挂单
                    </el-tab-pane>  
                   <el-tab-pane label="外卖">
                       外卖
                    </el-tab-pane>  
               </el-tabs> 
            </el-col>
            <el-col :span="17">
                <div class="often-goods">
                    <div class="title">常用商品</div>
                    <div class="goods-list"> 
                        <ul class='clearFloat'>
                            <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                                <span>{{goods.goodsName}}</span>
                                <span class="goods-price">￥{{goods.price}}</span>
                             </li>
                        </ul>
                    </div>
                </div>

                <div class="goods-type">
                    <el-tabs>
                        <el-tab-pane label="汉堡">
                           <ul class="cookList clearFloat">
                               <li v-for="goods in goodsType0" @click="addOrderList(goods)">
                                   <span class="foodImg">
                                       <img :src="goods.goodsImg" width="100%">
                                   </span>
                                   <span class="foodName">{{goods.goodsName}}</span>
                                   <span class="foodPrice">￥{{goods.price}}</span>
                               </li>
                           </ul>
                        </el-tab-pane>
                        <el-tab-pane label="小吃">
                            <ul class="cookList clearFloat">
                               <li v-for="goods in goodsType1" @click="addOrderList(goods)">
                                   <span class="foodImg">
                                       <img :src="goods.goodsImg" width="100%">
                                   </span>
                                   <span class="foodName">{{goods.goodsName}}</span>
                                   <span class="foodPrice">￥{{goods.price}}</span>
                               </li>
                            </ul>   
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <ul class="cookList clearFloat">
                               <li v-for="goods in goodsType2" @click="addOrderList(goods)">
                                   <span class="foodImg">
                                       <img :src="goods.goodsImg" width="100%">
                                   </span>
                                   <span class="foodName">{{goods.goodsName}}</span>
                                   <span class="foodPrice">￥{{goods.price}}</span>
                               </li>
                            </ul>   
                        </el-tab-pane> 
                        <el-tab-pane label="套餐">
                             <ul class="cookList clearFloat">
                               <li v-for="goods in goodsType3" @click="addOrderList(goods)">
                                   <span class="foodImg">
                                       <img :src="goods.goodsImg" width="100%">
                                   </span>
                                   <span class="foodName">{{goods.goodsName}}</span>
                                   <span class="foodPrice">￥{{goods.price}}</span>
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
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      goodsType0: [],
      goodsType1: [],
      goodsType2: [],
      goodsType3: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  created: function() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(res => {
        this.oftenGoods = res.data;
      })
      .catch(error => {
        console.log("错误");
      });
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(res => {
        this.goodsType0 = res.data[0];
        this.goodsType1 = res.data[1];
        this.goodsType2 = res.data[2];
        this.goodsType3 = res.data[3];
      })
      .catch(error => {
        console.log("错误");
      });
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  methods: {
    addOrderList(goods) {
      this.totalMoney = 0;
      this.totalCount = 0;
      //商品是否存在订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          //如果相等，说明存在
          isHave = true;
        }
      }
      //如果存在
      if (isHave) {
        //改变列表中商品的数量
        let arr = this.tableData.filter((o) => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        //没有就创建一个对象用来接收商品id等
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
    //    this.getAllMoney();
      //计算汇总价格和数量
      this.tableData.forEach((item) => {
        this.totalCount += item.count;
        this.totalMoney += item.price * item.count;
      });
    },
    //删除单个商品
    delSingleGoods(goods) {
    //   this.tableData = this.tableData.filter(item => item.goodsId != goods.goodsId);
        goods.count--;
      this.getAllMoney();
    },
    delAllMoney(){
        this.tableData = [];
        this.totalMoney =0;
        this.totalCount =0;
    },
    //汇总数量和金额
    getAllMoney() {
      this.totalMoney = 0;
      this.totalCount = 0;
      if (this.tableData) {
        this.tableData.forEach((item) => {
          this.totalCount += item.count;
          this.totalMoney += item.price * item.count;
        });
      }
    },
    //模拟结账
    checkout(){
        if(this.totalCount!=0){
             this.tableData = [];
             this.totalMoney = 0;
             this.totalCount = 0;
             this.$message({
                 message:'结账成功',
                 type:'success'
             })
        }else{
            this.$message.error('不能空结')
        }
    }
  }
};
</script>
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid blue;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 39px;
  line-height: 39px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding-left: 10px;
  text-align: left;
}
.clearFloat {
  overflow: hidden;
}
.goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e5e5;
  padding: 10px;
  margin: 10px;
}
.goods-price {
  color: #58b7ff;
}
.goods-type {
  padding-left: 10px;
}
.cookList li {
  list-style: none;
  float: left;
  width: 23%;
  height: auto;
  border: 1px solid #e5e9f2;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.total-goods {
  background-color: #ffffff;
  padding: 10px;
  border-bottom: 1px solid #e5e9f2;
}
</style>