<template>
	<view>
		<div class="row-box mgb-5">
			<div class="flex flex-ai-center">
				<image mode="widthFix" :src="user.user_head+'.100x100.jpg'" class="wh-60 mgr-10" ></image>
				<div class="flex-1">
					<div class="cl1 mgb-5">{{user.nickname}}</div>
					<div class="flex flex-ai-center">
						<div class="mgr-5 cl2">发起</div>
						<div class="cl-num mgr-10"> {{party_createnum}}</div>
						<div class="mgr-5  cl2">参与</div>
						<div class="cl-num mgr-10"> {{party_joinnum}}</div>
					</div>
				</div>
				<div @click="gourl('/pages/user/set')"  class="iconfont icon-settings"></div>
			</div>
		</div>
		
		<div class="row-box">
			<div  @click="gourl('/pages/notice/my')"   class="row-item">
				<div class="row-item-icon icon-notice"></div>
				<div class="row-item-title">我的消息</div>
			</div>
			<div  @click="gourl('/party/party/my')"   class="row-item">
				<div class="row-item-icon icon-activity"></div>
				<div class="row-item-title">我创建的活动</div>
			</div>
			<div @click="gourl('/party/party_join/my')"  class="row-item">
				<div class="row-item-icon icon-activity"></div>
				<div class="row-item-title">我参与的活动</div>
			</div>
			<div  @click="gourl('/party/party_blog/my')"  class="row-item">
				<div class="row-item-icon icon-news"></div>
				<div class="row-item-title">我的动态</div>
			</div>
			
			<div   @click="gourl('/party/party_blog_comment/my')"  class="row-item">
				<div class="row-item-icon icon-comment_light"></div>
				<div class="row-item-title">我的评论</div>
			</div>
			
		</div>
		<party-footer tab="party_user"></party-footer>
	</view>
</template>

<script>
	import partyFooter from "@/party/party-footer.vue"
	export default{
		components:{
			partyFooter
		},
		data:function(){
			return {
				user:{},
				party_createnum:0,
				party_joinnum:0
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
					url:that.app.apiHost+"/module.php?m=party_user",
					success:function(res){
						that.user=res.data.user;
						that.party_createnum=res.data.party_createnum;
						that.party_joinnum=res.data.party_joinnum;
					}
				})
			}
		}
	}
</script>

<style>
</style>