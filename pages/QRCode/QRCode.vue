<template>
	<view class="container">
		<!-- 上方显示时间 显示wifi的通栏 -->
		<view class="status_bar">
			<view class="top_view"></view>
		</view>
		<view class="nav">
			<view class="nav-left">
				<image src="../../static/img/dt_store_back.png" @click="index1"></image>
			</view>
			<view class="nav-title">
				<image src="../../static/img/dun.png"></image>请假详情
			</view>
			<view class="nav-right">反馈</view>
		</view>
		<view class='tab-box'>
			<view class="check-left" @click="viewLeaves">请假信息</view>
			<view class="check-right"><view class="check-on">核验二维码</view></view> 
		</view>
		<view class="main">
			<view class="main-top-tips" v-if="leave.state == '正在休假中'">
				<image src="../../static/img/wenhao.png" class="main-top-tips-image1"></image>
				如何销假？
<!-- 				<image src="../../static/img/right.png" class="main-top-tips-image2"></image> -->
			</view>
			<view class="main-top-state" :style="getTopStateBackground()">
				<view class="main-top-state-pass">
					<image src="../../static/img/true.png"></image>
					<text>审批已通过</text>
				</view>
				<view class="main-top-state-text">
					{{leave.state}}
				</view>
				<view class="main-top-state-fangwei">
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
					<view class="main-top-state-fangwei-animation"></view>
				</view>
				<view class="main-top-state-date">
					<view>当前时间:{{currDate}}</view>
				</view>
			</view>
			<view class="QRcode">
				<image src="../../static/img/code.jpeg">
			</view>
			<view class="QR-text">
				请使用辅导猫扫码核验请假单真伪
			</view>
			<view class="QR-date">
				<view class="QR-date-text">当前时间:{{currDate}}</view>
			</view>
				
				<!-- <view class="main-content-clear" @click="clear" v-if="leave.state == '正在休假中'">去销假</view> -->
				<!-- <view class="main-content-applyLeave" v-if="leave.state == '正在休假中'">申请续假</view> -->
			</view>

		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				leave: null,
				currDate: '',
				index: 0,
				leaves: null,
				oneHeight:0,
				twoHeight:0,
				threeHeight:0,
			}
		},
		
		
		onLoad() {
			var that = this;
			this.refreshCurrDate();
			this.leave = uni.getStorageSync("currLeaves");
			this.index = uni.getStorageSync("currIndex");
			setInterval(function() {
				that.refreshCurrDate();
			}, 1000)
		},
		
		onBackPress(options) {
				if (options.from === 'navigateBack') {
					return false;
				}
				this.onreturn();
				return true;
			},
		
		methods: {
			onreturn(){
				uni.redirectTo({
					url:'../index/index'
				});
			},
			
			viewLeaves: function() {
				uni.navigateTo({
					url: '../viewLeaves/viewLeaves',
					animationType:'none'
				});
			},
			
			index1: function() {
				uni.navigateTo({
					url: '../index/index',
					animationType:'none'
				});
			},
			
			//刷新当前时间
			refreshCurrDate: function() {
				var date = new Date();
				this.currDate = date.getFullYear() + "-" +
					(Number(date.getMonth() + 1).toString().length <= 1 ? "0" + Number(date.getMonth() + 1) : Number(date.getMonth() + 1)) +
					"-" +
					(date.getDate().toString().length <= 1 ? "0" + date.getDate() : date.getDate()) + " " +
					(date.getHours().toString().length <= 1 ? "0" + date.getHours() : date.getHours()) + ":" +
					(date.getMinutes().toString().length <= 1 ? "0" + date.getMinutes() : date.getMinutes()) + ":" +
					(date.getSeconds().toString().length <= 1 ? "0" + date.getSeconds() : date.getSeconds());
			},
			//返回上一页
			backPage: function() {
				uni.navigateBack({
					delta: 1
				})
			},
			//去销假
			clear:function(){
				var that = this;
				uni.showModal({
					title:'提示',
					content:'确定要销假吗？',
					success:function(e){
						if(e.confirm){
							var date = new Date();
							that.leaves = uni.getStorageSync('todaySchoolLeaves').reverse();
							that.leave.state = '已完成';
							that.leave['clearDate'] = (Number(date.getMonth() + 1).toString().length <= 1 ? "0" + Number(date.getMonth() + 1) : Number(date.getMonth() + 1)) + "-" + 
								(date.getDate().toString().length <= 1 ? "0" + date.getDate() : date.getDate()) + " " + 
								(date.getHours().toString().length <= 1 ? "0" + date.getHours() : date.getHours()) + ":" + 
								(date.getMinutes().toString().length <= 1 ? "0" + date.getMinutes() : date.getMinutes());
							that.leaves[that.index] = that.leave;
							uni.setStorageSync('todaySchoolLeaves',that.leaves.reverse());
							uni.navigateBack({
								delta:1
							})
						}
					}
				})
			},
			
			//根据请假状态设置审批状态线高度
			getCheckStateBorderHeight:function(){
				const query = uni.createSelectorQuery().in(this);
				query.select('.main-content-checkState-state-checkOne').boundingClientRect(data => {
					this.oneHeight = data == null ? 0 : data.height;
				}).exec();
				query.select('.main-content-checkState-state-checkTwo').boundingClientRect(data => {
					this.twoHeight = data == null ? 0 : data.height;
				}).exec();
				query.select('.main-content-checkState-state-checkThree').boundingClientRect(data => {
					this.threeHeight = data == null ? 0 : data.height;
				}).exec();
				
				//判断一级到三级，有几个是多行
				var count = 0;
				if(this.oneHeight > 75){
					count++;
				}
				if(this.twoHeight > 75){
					count++;
				}
				if(this.threeHeight > 75){
					count++;
				}
				
				if(this.leave.state == '已完成'){
					if(this.leave.threeCheck != ''){
						if(count == 3){
							return 'height:330px';
						} else if(count == 2){
							return 'height:310px';
						} else if(count == 1){
							return 'height:290px';
						} else {
							return 'height:270px';
						}
					}
					if(this.leave.twoCheck != ''){
						if(count == 2){
							return 'height:220px';
						} else if(count == 1){
							return 'height:210px';
						} else {
							return 'height:175px';
						}
					}
					if(count == 1){
						return 'height:140px';
					} else {
						return 'height:100px';
					}
				} else if(this.leave.state == '正在休假中'){
					if(this.leave.threeCheck != ''){
						if(count == 3){
							return 'height:240px';
						} 
						if(count == 2 && this.threeHeight < 75){
							return 'height:230px';
						} else if(count == 2 && this.threeHeight > 75){
							return 'height:220px';
						}
						if(count == 1 && this.threeHeight < 75){
							return 'height:210px';
						}
						return 'height:190px';
					}
					if(this.leave.twoCheck != ''){
						if(count == 2){
							return 'height:125px';
						}
						if(count == 1 && this.twoHeight < 75){
							return 'height:125px';
						} else if(count == 1 && this.twoHeight > 75){
							return 'height:105px';
						}
						return 'height:105px';
					}
					return 'height:40px';
				}
			},
			//根据请假状态设置背景渐变色
			getTopStateBackground:function(){
				if(this.leave.state == '已完成'){
					return 'background-image: linear-gradient(#657181, #9EA8B4);';
				} else if(this.leave.state == '正在休假中'){
					return 'background-image: linear-gradient(#08A45A, #00DE72);';
				}
			},
			
			//设置实际休假时间颜色，双数为灰色，单数为黄
			leaveDateStyle:function(){
				if(this.index % 2 != 0){
					return "color:#F59A12;"
				}
			},
			
			
			//单击附件图片事件
			imgFileClick:function(){
				uni.previewImage({
					urls:[this.leave.imgFile]
				})
			},
		}
	}
</script>

<style>
	html{
		
	}
	page {
		background-color: #F6F7F9;
		overflow-x: hidden;
	}

	.blackFont {
		color: #66717F;
	}

	.container {
		padding: 20px;
		padding-left: 0px;
		padding-right: 0px;
		font-size: 22rpx;
		line-height: 24px;
	}

	//上方手机状态栏 通栏
	.status_bar {
		height: 0px;
		width: 100%;
		background-color: #545454;
	}

	.top_view {
		height: var(--status-bar-height);
		width: 100%;
		position: fixed;
		background-color: white;
		top: 0;
		z-index: 999;
	}

	//导航
	.nav {
		position: fixed;
		width: 100%;
		height: 70px;
		background: white;
		border-bottom: 1px solid #efefef;
		z-index: 9999;
	}
	
	.nav-left {
		padding-top: 34px;
		margin-left: 28rpx;
	}
	
	.nav-left image {
		width: 20px;
		height: 20px;
	}
	
	.nav-title {
		font-weight: 450;
		font-size: 33rpx;
		letter-spacing: 1.5px;
		position: absolute;
		left: calc(50% - 100rpx);
		top: 52rpx;
	}
	
	.nav-title image {
		width: 48rpx;
		height: 50rpx;
		position: relative;
		top: 14rpx;
		left: -4rpx;
	}

	.nav-right {
		position: absolute;
		right: 36rpx;
		top: 35px;
		font-size: 33rpx;
		color: #41E0EB;
	}
	
	.tab-box {
		position: fixed;
		width: 100%;
		height: 40px;
		top: 90px;
		background-color: white;
		border-bottom: 1px solid #efefef;
		z-index: 999;
	}
	
	.check-left {
		position: absolute;
		width: 70px;
		height: 40px;
		text-align: center;
		font-size: 30rpx;
		color:#b0b1b5;
		top: 10px;
		left: 70px;
	}
	
	.check-right {
		position: absolute;
		width: 50%;
		height: 40px;
		text-align: center;
		font-size: 30rpx;
		color:#b0b1b5;
		right: 10px;
		top: 10px;
	}
	
	.check-on {
		position: absolute;
		width: 100px;
		height: 28px;
		font-weight: 500;
		color:#3399FE;
		border-bottom: 3px solid #3399FE;
		left: 45px;
	}

	.main {
		position: relative;
		top: 110px;
	}

	.main-top-tips {
		width: 100%;
		height: 45rpx;
		background-color: #FE9900;
		text-align: center;
		color: white;
		font-size: 24rpx;
		line-height: 27px;
		position: relative;
	}

	.main-top-tips-image1 {
		width: 15px;
		height: 15px;
		position: absolute;
		top: 6px;
		left: calc(50% - 100rpx);
	}

	.main-top-tips-image2 {
		width: 22px;
		height: 20px;
		position: absolute;
		top: 6px;
	}

	.main-top-state {
		overflow-x: hidden;
		width: 100%;
		height: 110px;
		/* background-image: linear-gradient(#08A45A, #00DE72); */
		position: relative;
	}

	.main-top-state-pass {
		position: relative;
		text-align: center;
		top: 10rpx;
	}

	.main-top-state-pass text {
		color: white;
		font-size: 34rpx;
		letter-spacing: 2px;
		position: relative;
		left: 5px;
	}

	.main-top-state-pass image {
		width: 36rpx;
		height: 36rpx;
		position: relative;
		top: 3px;
	}

	.main-top-state-text {
		color: white;
		font-size: 66rpx;
		text-align: center;
		margin-top: 30rpx;
	}

	.main-top-state-fangwei {
		height: 18px;
		position: absolute;
		bottom: 0px;
		width: 1000px;
		/* background-color: red; */
		overflow: hidden;
		margin-left: -280px;
	}

	.main-top-state-fangwei-animation {
		display: inline-table;
		margin-right: 30px;
		width: 33px;
		height: 18px;
		transform: skew(-40deg);
		background-color: white;
		animation: fangweiAnimation 4.2s infinite linear;
	}

	@keyframes fangweiAnimation {
		from {
			transform: translateX(0px) skew(-40deg);
		}

		to {
			transform: translateX(313px) skew(-40deg);
		}
	}

	.main-top-state-date {
		text-align: center;
		position: absolute;
		bottom: 0px;
		width: 100%;
	}

	.main-top-state-date view {
		position: relative;
		background-color: rgba(0, 0, 0, 0.5);
		color: white;
		border-radius: 8px;
		width: 350rpx;
		height: 19px;
		line-height: 19px;
		margin: 0 auto;
		font-size: 25rpx;
	}

	.main-content {
		width: 100%;
	}

	.main-content-type {
		background-color: white;
		padding: 10px;
		padding-left: 25px;
		padding-right: 25px;
		color: #A2A9B2;
		font-size: 26rpx;
		position: relative;
	}

	.main-content-type-leaveType {
		display: inline-block;
	}

	.main-content-type-leaveSchool {
		display: inline-block;
		margin-left: 92px;
	}

	.main-content-type-clearRule {
		margin-top: 6px;
	}

	.main-content-type-leaveDate {
		margin-top: 6px;
	}

	.main-content-info {
		border-bottom: 1px solid #efefef;
		border-top: 1px solid #efefef;
		background-color: white;
		padding: 10px;
		padding-left: 22px;
		padding-right: 22px;
		padding-bottom: 15px;
		color: #949494;
		font-size: 28rpx;
		margin-top: 15px;
	}

	.main-content-info-left {
		line-height: 44rpx;
		color: #9EA8B4;
		font-size: 25rpx;
	}

	.main-content-info-left view {
		display: inline-block;
		width:155rpx;
		/* background-color: green; */
	}

	.main-content-info-left text {
		/* background-color: red; */
		color: #657181;
	}
	.main-content-info-left-file{
		margin-top: 15rpx;
		width: 250rpx;
		position: relative;
	}
	.main-content-info-left-file image{
		width: 128rpx;
		height: 128rpx;
		display: inline-block;
		margin-left: 160rpx;
	}
	.main-content-info-left-file text{
		position: absolute;
		color: #949494;
	}
	.main-content-checkState {
		position: relative;
		border-bottom: 1px solid #efefef;
		border-top: 1px solid #efefef;
		margin-top: 15px;
		/* margin-bottom: 30px; */
		background-color: white;
		padding: 10px;
		padding-left: 22px;
		padding-right: 22px;
		padding-bottom: 24px;
		color: #949494;
		font-size: 28rpx;
	}
	.main-content-checkState-border{
		position: absolute;
		left: 32px;
		top: 65px;
		height: 60px;
		border-left: 1px solid #DADFE5;
	}

	.main-content-checkState-cirBlue {
		width: 40rpx;
		height: 40rpx;
		background-color: white;
		text-align: center;
		padding-top: 5rpx;
	}
	.main-content-checkState-cirBlue view{
		width: 30rpx;
		height: 30rpx;
		border-radius: 999px;
		border: 1px solid #1D85F4;
		background-color: white;
	}

	.main-content-checkState-cirGreen {
		width: 40rpx;
		height: 40rpx;
		background-color: white;
		text-align: center;
		padding-top: 5rpx;
	}
	.main-content-checkState-cirGreen view{
		width: 30rpx;
		height: 30rpx;
		border-radius: 999px;
		border: 1px solid #04C876;
		background-color: white;
	}

	.main-content-checkState-state {
		/* background-color: red; */
		font-size: 25rpx;
		color: #667282;
	}

	.main-content-checkState-checkInfo {
		width: calc(100% - 40px);
		background-color: #F6F7F9;
		border: 1px solid #D7DDE4;
		border-radius: 5px;
		padding: 8rpx;
		margin-left: 25px;
		margin-top: 6px;
		color: #A1ABB5;
	}

	.main-content-checkState-state-apply {
		position: relative;
	}

	.main-content-checkState-state-apply view {
		display: inline-block;
	}

	.main-content-checkState-state-checkOne {
		margin-top: 15px;
		position: relative;
		/* background-color: red; */
	}

	.main-content-checkState-state-checkOne view {
		display: inline-block;
	}
	.main-content-checkState-state-checkTwo {
		margin-top: 10px;
		position: relative;
		/* background-color: red; */
	}
	
	.main-content-checkState-state-checkTwo view {
		display: inline-block;
	}
	.main-content-checkState-state-checkThree {
		margin-top: 10px;
		position: relative;
		/* background-color: red; */
	}
	
	.main-content-checkState-state-checkThree view {
		display: inline-block;
	}
/* 	.main-content-applyLeave{
		width: calc(100% - 55px);
		background-color: #FFFFFF;
		border: 1px solid #DDDEE2;
		margin-top: 10px;
		margin-left: 30px;
		height: 50px;
		line-height: 50px;
		text-align: center;
		border-radius: 5px;
		font-size: 32rpx;
	} */
/* 	.main-content-share{
		width: calc(100% - 55px);
		background-color: #FFFFFF;
		border: 1px solid #DDDEE2;
		margin-top: 10px;
		margin-left: 30px;
		margin-bottom: 100px;
		height: 50px;
		line-height: 50px;
		text-align: center;
		border-radius: 5px;
		font-size: 32rpx;
	} */
	.main-content-clearInfo{
		background-color: white;
		margin-bottom: 110rpx;
		margin-top: 15px;
		border-top: 1px solid #efefef;
	}
	.main-content-clearInfo-info{
		line-height: 55px;
		font-size: 28rpx;
		padding-left: 20px;
		border-bottom: 1px solid #efefef;
		color: #464C5A;
		font-size: 30rpx;
	}
	.main-content-clearInfo-info view{
		color: #A1AAB4;
		margin-top: -24px;
		font-size: 24rpx;
	}
	/* 已经销假状态下的转发按钮 */
	.main-content-share-clear{
		background-color: #3399FE;
		position: fixed;
		bottom: 0px;
		width: 100%;
		height: 85rpx;
		color: white;
		text-align: center;
		line-height: 85rpx;
		font-size: 32rpx;
	}
	/* 正在休假中 */
	.main-content-bottom-btn{
		/* background-color: green; */
		position: fixed;
		bottom: 0px;
		width: 100%;
		height: 85rpx;
		color: white;
		text-align: center;
		line-height: 85rpx;
		font-size: 32rpx;
	}
	.main-content-bottom-btn view{
		background-color: white;
		width: 33%;
		display: inline-block;
		border-right: 1px solid #EBEBEB;
		border-top: 1px solid #EBEBEB;
		color: #66717F;
	}
	.QRcode {
		margin:50px auto;
		text-align: center;
		width: 350rpx;
		height: 350rpx;
		border-radius: 8px;
		background-color: white;
		-webkit-box-shadow:1px 5px 15px #8e8c8f;
	}
	
	.QRcode image{
		margin:10rpx auto;
		width: 320rpx;
		height: 320rpx;
		
	}
	
	.QR-text{
		margin:-42px auto;
		text-align: center;
		width: 100%;
		color: #66717F;
		font-size: 12px;
	}
	
	.QR-date {
		width: 70%;
		height: 30px;
		border-radius:30px;
		background-color: #3399FE;
		margin:60px auto;
		color: white;
		font-size: 16px;
		font-weight: 600;
	}
	
	.QR-date-text{
		padding-top: 3px;
		width: 100%;
		margin:5px auto;
		text-align: center;
	}
</style>
