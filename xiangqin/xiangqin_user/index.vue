<template>
	<view>
		<div class="uhead" v-if="pageLoad">
		
			<image mode="widthFix"  class="uhead-img" :src="user.user_head+'.100x100.jpg'"></image>
		
			<div class="uhead-box">
				<div class="uhead-nick">{{user.nickname}}</div>
				 
				<div class="uhead-rnum flex  flex-ai-center">
					金币
					<text class="cl-money mgl-5 mgr-5">{{user.gold}}</text>
		
					积分
					<text class="cl-money mgl-5">{{user.grade}}</text>
		
				</div>
			</div>
			<div gourl="/index.php?m=user&a=set" class="flex-center btn-small btn-link iconfont icon-settings"></div>
		</div>
		<div class="row-box mgb-5">
			
			<div  @click="gourl('/xiangqin/xiangqin_people/add')"   class="row-item">
				<div class="row-item-icon icon-people  cl-u"></div>
				<div class="row-item-title">相亲信息</div>
			</div>
			 
			<template v-if="XIANGQIN_PARTY_ON">
				<div  @click="gourl('/party/party/my')"    class="row-item">
					<div class="row-item-icon icon-activity  cl-u"></div>
					<div class="row-item-title">我创建的活动</div>
				</div>
				<div @click="gourl('/party/party_join/my')"   class="row-item">
					<div class="row-item-icon icon-group_light  cl-u"></div>
					<div class="row-item-title">我参与的活动</div>
				</div>
			</template>
			 
			<div  @click="gourl('/xiangqin/xiangqin_friend/index')"   class="row-item">
				<div class="row-item-icon icon-friend  cl-u"></div>
				<div class="row-item-title">互动好友</div>
			</div>
			 
			<div @click="gourl('/xiangqin/xiangqin_biaobai/my')"  class="row-item">
				<div class="row-item-icon icon-like  cl-u"></div>
				<div class="row-item-title">我的表白</div>
			</div>
			
			 
		</div>		
		<div class="row-box mgb-5">	 
			<div @click="gourl('/pages/user_auth/index')" class="row-item">
				<div class="row-item-icon icon-vipcard  cl-u"></div>
				<div class="row-item-title">实名认证</div>
			</div>
			<div @click="gourl('/pages/kefu/index')"  class="row-item">
				<div class="row-item-icon icon-service  cl-u"></div>
				<div class="row-item-title">联系客服</div>
			</div>
			<div @click="gourl('/pages/html/aboutus')" class="row-item">
				<div class="row-item-icon icon-info  cl-u"></div>
				<div class="row-item-title">关于我们</div>
			</div>
		</div>
		<xiangqin-footer tab="xiangqin_user"></xiangqin-footer>
	</view>
</template>

<script>
	import xiangqinFooter from "../xiangqin-footer.vue"
	export default{
		components:{
			xiangqinFooter
		},
		data:function(){
			return {
				user:{},
				pageLoad:false,
				XIANGQIN_PARTY_ON:true
			}
		},
		onLoad:function(){
			this.getPage();
		},
		methods:{
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_user",
					success:function(res){
						that.user=res.data.user;
						that.pageLoad=true;
					}
				})
			}
		}
	}
</script>

<style>
	.uhead {
		display: flex;
		flex-direction: row;
		padding: 10px;
		background-color: #fff;
		margin-bottom: 5px;
	}
	
	.uhead-img {
		width: 80px;
		height: 80px;
		margin-right: 10px;
		display: block;
		border-radius: 50%;
	}
	
	.uhead-box {
		flex: 1;
	}
	
	.uhead-nick {
		margin-bottom: 10px;
		font-size: 16px;
	}
	
	.uhead-rnum {
		color: #999;
		margin-bottom: 10px;
		line-height: 14px;
		display: flex;
		font-size: 14px;
	}
	
	.cl-u,
	.cl-u:before {
		color: #ed6d53;
		font-size: 22px;
	}
	
	.row-item-icon:before {
		font-size: 22px;
	}
	
</style>