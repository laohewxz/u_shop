<template>
	<view class="container">
		<!-- s收货地址 -->
		<view class="address">
			<view class="address_info">
				<label for="">收件人:悟空</label>	
				<label for="">18301628393</label>
			</view>
			<view class="address_res">
				收货地址：<label for="">花果山水帘洞27号</label>
			</view>
		</view>
		
		<!-- 商品信息 -->
		<view class="carts">
			
			
			<view class="cartsInfo" v-for="(item,index) in list" :key="item.id">
				<!-- 70px -->
				<view class="carts_detail">
					<view class="carts_image">
						<image :src="item.img" mode=""></image>
					</view>
					<view class="carts_name">
						<view for="" style="font-size: 30rpx;">{{item.goodsname}}</view>
						<view for="" style="font-size: 26rpx; color: #C0C0C0;">规格:300</view>
					</view>
					<view class="carts_price">
						<label for="">￥ {{item.price}}</label>
					</view>
				</view>
				<!-- 50px -->
				<view class="cart_num">
					<view class="">
						购买数量：
					</view>
					<view class="">
						<label for="" class="jian" @click="less(index)">-</label>
						<label for="" class="num">{{item.num}}</label>
						<label for="" class="jia" @click="more(index)">+</label>
					</view>
				</view>
				<!-- 横线 -->
				<view class="xian"></view>
			</view>		
				
				
				
			<!-- 50px -->
			<view class="kuaidi">
				<label for="">配送方式</label>
				<label for="">XX快递</label>
			</view>
		</view>
		
		<!-- 优惠券 -->
		<view class="yhquan">
			<label for="">优惠券</label>
			<label for="" style="margin-right: 40rpx;">无可用</label>
		</view>
		<!-- jifen积分 -->
		<view class="jifen">
			<view class="use" >
				<label for="">使用积分</label>
			</view>
			<view class="input" style="margin-right: 40rpx;">
				<input type="text" value="" placeholder="输入积分"/>
				<label for="" class="input_use">使用</label>
				<label for="" class="input_keyi">可使用50积分</label>
			</view>
		</view>
		
		<!-- 具体详细 -->
		<view class="resDetail">
			<view class="">
				<label for="">商品金额</label>
				<label for="" style="margin-right:40rpx;color:red">￥{{allPrice}}</label>
			</view>
			<view class="">
				<label for="">运费</label>
				<label for="" style="margin-right:40rpx;color:red">+￥0.00</label>
			</view>
			<view class="">
				<label for="">优惠券</label>
				<label for="" style="margin-right:40rpx;color:red">-￥0.00</label>
			</view>
			<view class="">
				<label for="">会员优惠</label>
				<label for="" style="margin-right:40rpx;color:red">-￥0.00</label>
			</view>
			<view class="">
				<label for="">积分抵扣</label>
				<label for="" style="margin-right:40rpx;color:red">-￥0.00</label>
			</view>
		</view>
		<!-- 实付金额 -->
		<view class="sfje">
			实付金额：<label for="" style="color:red">￥{{ allPrice }}</label>
		</view>
		
		<!-- 确认订单按钮 -->
		<view class="btn">
			<!-- 点击提交订单按钮，发起支付请求 -->
			<button type="primary" @click="pay">提交订单</button>
		</view>
	</view>
</template>

<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
	export default{
		   data(){
			   return{
				   list:[]
			   }
		   },
		   onLoad() {
		   	  this.list = uni.getStorageSync("order")
		   },
		   computed:{
			   allPrice(){
				   var sum = 0
				   this.list.forEach(item=>{
					   sum += item.price*item.num
				   })
				   return sum
			   }
		   },
		   methods:{
			   more(index){//点击增加
				   this.list[index].num++;
				   uni.setStorageSync("order",this.list)
			   },
			   less(index){//点击减少
				   if(this.list[index].num <= 1){
					   this.list[index].num =1;
					   uni.showToast({
					   	icon:'none',
					   	title:"已经不能更少了！！"
					   })
					   return;
				   }
				   this.list[index].num--;
				   uni.setStorageSync("order",this.list)
			   },
			   pay(){//点击提交订单
			        console.log('99')
				    let {token,uid,phone} = uni.getStorageSync('list')
					let userphone = phone
					var username = "悟空" //收件人名字
					var address = "花果山水帘洞27号" //地址
					var total=0
					this.list.map(item=>{
						total += item.num
					})
					let addtime = new Date().getTime()
				    let countmoney = total//商品数量
				    let countnumber = this.allPrice//总价
				    let params = {
				 	   uid,
					   username,
					   userphone,
					   address,
					   countnumber,
					   countmoney,
					   addtime
				   }
				   //将对象转化为字符串
				   params = JSON.stringify(params)
				   //获取购物车id，转化为字符串
				   let idArr = this.list.map(item=>{
					   return item.id
				   });
				   let idstr = idArr.join(",")
				   //发送请求
				   myRequest("/api/orderadd",{params:params,idstr:idstr},"GET",{authorization:token}).then(res=>{
					   console.log(res)
				   })
				   uni.navigateTo({
				   	url:"../order/order"
				   })
			   }
		   }
		
	}
</script>
<style>
	@import url("../../common/css/confirm.css");
</style>
