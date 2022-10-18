<template>
	<view>
		<div class="row-box mgb-5">
			<div class="flex flex-ai-center bd-mp-10">
				<image mode="widthFix" class="wh-40 bd-radius-50 mgr-5" :src="party.user_head+'.100x100.jpg'"></image>
				<div class="cl2">{{party.nickname}}</div>
				<div class="flex-1"></div>
				<div @click="gourl('/pages/pm/detail?userid='+party.userid)" class="cl-primary f12">发私信</div>
			</div>
			<div class="d-title">{{party.title}}</div>
			
			<div class="flex mgb-10">
				<div class="cl2 mgr-5">限制人数</div>
				<div class="cl-num">{{party.max_num}}</div>
				<div class="flex-1"></div>
				<div class="cl2 mgr-5">参与人数</div>
				<div class="cl-num">{{party.join_num}}</div>
			</div>
			<div class="flex mgb-10">
				<div class="cl2 mgr-5">活动时间</div>
				<div class="cl2">{{party.stime}}</div>
			</div>
			 
			<div class="cl2">地址：{{party.address}}</div>
		</div>
		<div class="row-box">
			<div>
				<div class="tabs-border mgb-10">
					<div @click="setTab('detail')" :class="tab=='detail'?'tabs-border-active':''" class="tabs-border-item">活动详情</div>
					<div @click="setTab('blog')"  :class="tab=='blog'?'tabs-border-active':''"   class="tabs-border-item">活动动态</div>
					<div @click="setTab('user')"  :class="tab=='user'?'tabs-border-active':''"  class="tabs-border-item">参与用户</div>
				</div>
				<div v-if="tab=='detail'">
					<div class="d-content" v-html="party.content"></div>
				</div>
				<div v-if="tab=='blog'">
					<div class="flex">
						<div class="fw-600">活动动态</div>
						<div class="flex-1"></div>
						<div @click="gourl('../party_blog/add?partyid='+party.id)"  class="btn-mini">发布动态</div>
					</div>
					<div class="emptyData" v-if="blogList.length==0">
						暂无动态
					</div>
					<template v-else>
						<div v-for="(item,index) in blogList" :key="index" @click="goBlog(item.id)" class="sglist-item">
							
							<div class="sglist-title">{{item.content}}</div>
							<div class="sglist-imglist">
								 
								<img v-for="(img,imgIndex) in item.imgslist" :key="imgIndex" :src="img+'.100x100.jpg'" class="sglist-imglist-img" />
								
							</div>
							<div class="sglist-ft">
								<div class="sglist-ft-love">{{item.love_num}}</div>
								<div class="sglist-ft-cm">{{item.comment_num}}</div>
								<div class="sglist-ft-view">{{item.view_num}}</div>
							</div> 
						</div>
					</template>
				</div>	
				<div v-if="tab=='user'">
					<div >
						<div v-if="Object.keys(joinList).length==0" class="emptyData">暂无参与用户</div>
						<div v-else class="flex flex-wrap">
							<div class="jItem" v-for="(item,index) in joinList" :key="index">
								<img class="wh-40 bd-radius-50 mgb-5" :src="item.user_head" />
								<div class="f12 cl2">{{item.nickname}}</div>
							</div>
						</div>
					</div>
				</div>
			</div>
			
			
		</div> 
		<div class="footer-row"></div>
		<div class="footerFix pd-10 bg-white">
			<div class="flex flex-ai-center">
				<div class="cl2 mgr-5">活动费用</div>
				<div class="cl-money">￥{{party.money}}</div>
				<div class="flex-1"></div>
				 
				<template v-if="join">
					 
					<div class="flex flex-ai-center" v-if="join.status==1" >
						<div class="cl-status mgr-10">已报名</div>
						<div @click="tel(party.telephone)"   class="pd-5 iconfont icon-telephone cl-primary f16"></div>
					</div>
			 
					<div v-else-if="join.status==2" class="cl3">未通过</div>
					 
					<div v-else class="cl2">待审核</div>
				 
				
				</template>
				<div v-else @click="joinModal=true" class="btn btn-outline-primary">我要参与</div>
				 
			</div>
		</div>
		<div v-if="joinModal">
			<div @click="joinModal=false" class="modal-mask"></div>
			<div class="modal">
				<div class="modal-header">
					<div class="modal-title">活动参与</div>
					<div  @click="joinModal=false" class="modal-close icon-close"></div>
				</div>
				<div class="modal-body">
					<form id="actForm" @submit="joinSubmit">
					<input type="text" class="none" name="pid" :value="party.id" />
					<div class="input-flex">
						<div class="input-flex-label">联系人：</div>
						<input type="text" class="input-flex-text"  name="nickname" id="nickname" value="">
					</div>
					<div class="input-flex">
						<div class="input-flex-label">电话：</div>
						<input type="text" class="input-flex-text"  name="telephone" id="telephone" value="">
					</div>
					<div class="input-flex">
						<div class="input-flex-label">自我介绍：</div>
						<input type="text" class="input-flex-text"  name="description" id="description" value="">
					</div>
					<div class="flex flex-center mgb-5">
						<div class="cl2 mgr-5">活动费用</div>
						<div class="cl-money">￥{{party.money}}</div>
					</div>
					<button form-type="submit" id="actSubmit" class="btn-row-submit">确认参与</button>
					</form>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	import dtPay from "../../common/dtpay.js";
	export default{
		data:function(){
			return {
				pageLoad:false,
			 
				party:{},
				joinList:[],
				join:{},
				blogList:[],
				id:0,
				tab:"detail",
				joinModal:false
			}
		},
		onLoad:function(ops){
			this.id=ops.id;
			this.getPage();
			this.getJoin();
			this.getBlog();
		},
		 
		onShareAppMessage:function(){
			
		},
		methods: {
			setTab:function(t){
				this.tab=t;
			},
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			tel:function(phone){
				uni.makePhoneCall({
					phoneNumber:phone
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party&a=show&ajax=1",
					data:{
						id:this.id
					},
					success:function(res){
						
						that.party=res.data.data;
						that.join=res.data.join;
						that.pageLoad=true;
					}
				})
			},
			getJoin:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party_join&ajax=1",
					dataType:"json",
					data:{
						pid:this.id
					},
					success:function(res){
						if(res.error){
							return false;
						}
						that.joinList=res.data.list;
					}
				})
			},
			 
			getBlog:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party_blog&a=list&type=new&ajax=1",
					dataType:"json",
					data:{
						partyid:this.id
					},
					success:function(res){
						if(res.error){
							return false;
						}
						that.blogList=res.data.list;
					}
				})
			},
			goBlog:function(id){
				uni.navigateTo({
					url:"../party_blog/show?id="+id
				})
				 
			},
			joinSubmit:function(e){
				var that=this;
				if(!that.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=party_join&a=save",
					data:e.detail.value,
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if(res.error){
							return false;
						}
						if(res.data.action=="pay"){
							dtPay.paytype=that.paytype;
							dtPay.urlFail="../party_order/fail";
							dtPay.urlSuccess="../party_order/success";
							dtPay.pay({
								payurl:res.data.payurl,
								orderno:res.data.orderno
							});		
						}
					}
				})
			}
		},
	}
</script>

<style>
</style>