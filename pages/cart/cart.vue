<template>
	<view class="container" v-if="carList.length >0">
		<view class="top">
			<scroll-view class="scroll-view_H wrap"  scroll-x="true" scroll-left="0" v-for="(item,index) in carList" :key="item.id" >
				<view class="cartInfo" id="demo1">
					<!-- 购物车商品信息 -->
					<view class="cartInfochild">
						<!-- 选择框 -->
						<view class="cartInfo_switch common">
							<!-- 点击当前商品 -->
							<switch type="checkbox" :checked="item.checked" @click="choose(index)" />
						</view>
						<!-- 图片 -->
						<view class="cartInfo_image common"><image :src="item.img" mode=""></image></view>

						<!-- 信息 -->
						<view class="cartInfo_info">
							<label for="" style="font-size: 26rpx;">{{item.goodsname}}</label>
							<label for="" style="font-size: 24rpx; color: #ccc;">规格：黑色</label>
							<label for="" style="color: red;">￥{{item.price}}</label>
						</view>
						<!-- 加减数量 -->
						<view class="cartInfo_num">
							<view class="cartInfo_num_child">
								<label for="" @click="less(index)">-</label>
								<label for="">{{item.num}}</label>
								<label for="" @click="more(index)">+</label>
							</view>
						</view>
					</view>
				</view>
				<view class="cartDel" id="demo2" @click="del(index)"><label for="">删除</label></view>
			</scroll-view>
		</view>

		<!-- 底部 -->
		<view class="foot">
			<view class="footd1">
				<switch type="checkbox" :checked="isAllCheck" @click="chooseAll" />
				<label>全选</label>
			</view>
			<view class="footd2">
				<view class="footd2sp1">
					总计：
					<label style="color: red;">￥{{total}}</label>
				</view>
				<label class="footd2sp2">不含运费，已优惠￥0.00</label>
			</view>
			<!-- 跳到提交订单，结算页面 -->
			<view class="footd3" @click="toPay">
				<!-- 被选中的商品的个数 -->
				<label>去结算({{proNum}}件)</label>
			</view>
		</view>
	</view>
	<view class="tit" v-else>
	  快去添加宝贝吧~
	</view>
</template>

<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
    export default {
	  data(){
		return{
			carList:[],
			// baseUrl:'',
			
		}
	  },
	  onShow() {
		  this.baseUrl = baseUrl
		 var user =  uni.getStorageSync("list")
		 //获取购物车列表
		myRequest("/api/cartlist",{uid:user.uid},"GET",{authorization:user.token}).then(res=>{
			if(res[1].data.list){
				res[1].data.list.forEach(item=>{
					item.checked = item.checked == 1?true:false; //数据中加上checked
					item.img = baseUrl+item.img
				})
				this.carList = res[1].data.list
			}else{
				this.carList = []
			}
			
		})
	 },
	 computed:{
		 total(){//计算总价
			 var sum = 0
			 this.carList.forEach(item=>{
				 if(item.checked){
					 sum+= item.price*item.num
				 }
			 })
			 return sum;
		 },
		 isAllCheck:{//复选框 
			 get:function(){
				 return this.carList.every(item=>{
					 return item.checked == true
				 });
			 },
			 set:function(val){
				 this.carList.forEach(item=>{
					 item.checked=val
				 })
			 }
		},
		proNum(){//选中商品的数量
		    var arr = []
		    this.carList.map(item=>{
				if(item.checked){
					arr.push(item)
				}
			})
			return arr.length
		}
	 },
	 methods:{
		 change(index){//修改数据库中的数据
			 let {id,num,checked} = this.carList[index]
			 checked=checked?1:0;//
			 var data = {
			 		id,
			 		num,
			 		checked
			 }
			 let {token} = uni.getStorageSync('list')
			 myRequest("/api/cartedit",data,"GET",{authorization:token}).then(res=>{
			 				 // console.log(res)
			 })
		 },
		 less(index){
			 if( this.carList[index].num <= 1){
				 uni.showToast({
				 	icon:'none',
				 	title:"已经不能更少了！！"
				 })
				 return
			 }
			 this.carList[index].num--//页面中的num减少
			 this.change(index)
		 },
		 more(index){//点击添加
			 this.carList[index].num++//页面中的num增加
			 this.change(index)
		 },
		 choose(index){//点击复选框
			 this.carList[index].checked = !this.carList[index].checked//修改页面中的checked
			 //修改数据库中的checked
			 this.change(index)
		 },//=============================================================================
		 chooseAll(){//点击全选
			 this.isAllCheck = !this.isAllCheck
			 //点击了全选之后修改数据库中的checked
			 this.carList.forEach(item=>{
				 let {id,num,checked} = item
				 checked=item.checked?1:0;//
				 var data = {
				 		id,
				 		num,
				 		checked
				 }
				 let {token} = uni.getStorageSync('list')
				 myRequest("/api/cartedit",data,"GET",{authorization:token})
			 })
		 },//===================================================================
		 del(index){//点击删除
			 var id = this.carList[index].id
			 let {token} = uni.getStorageSync('list')
			 myRequest("/api/cartdelete",{id:id},"GET",{authorization:token}).then(res=>{
				  //获取购物车列表
				 var user =  uni.getStorageSync("list")
				 myRequest("/api/cartlist",{uid:user.uid},"GET",{authorization:user.token}).then(res=>{
				 	res[1].data.list.forEach(item=>{
				 		item.checked = item.checked == 1?true:false; //数据中加上checked
				 		item.img = baseUrl+item.img
				 	})
				 	this.carList = res[1].data.list
				 })
				 uni.showToast({
				 	icon:'none',
				 	title:res[1].data.msg
				 })
			 })
		 },
		 toPay(){//点击去结算
		     var user =  uni.getStorageSync("list")
		      //获取购物车列表
		     myRequest("/api/cartlist",{uid:user.uid},"GET",{authorization:user.token}).then(res=>{
		     	res[1].data.list.forEach(item=>{
		     		item.checked = item.checked == 1?true:false; //数据中加上checked
		     		item.img = baseUrl+item.img
		     	})
		     	var arr = []
		     	//将购物车选中的商品筛选到list
		     	res[1].data.list.map(item=>{
		     		if(item.checked){
		     			arr.push(item)
		     		}
		     	})
		     uni.setStorageSync("order",arr)//将数据存到缓存里
		     })
			 uni.navigateTo({
			 	url:"../confirm/confirm"
			 })
		 }
	 }
};
</script>

<style scoped>
@import url('../../common/css/cart.css');
.cartInfo_image_img {
	width: 120rpx;
	height: 120rpx;
}
.wrap{
	height: 220rpx;
	border: none;
}
.tit{
	height: 100rpx;
	line-height: 100rpx;
	text-align: center;
}
</style>
