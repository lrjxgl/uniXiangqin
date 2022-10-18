<template>
	<view>
		<div class="flexlist-item">
			<image :src="data.imgurl+'.small.jpg'" mode="widthFix" class="w100 mgr-5" />
			<div class="flex-1">
				<div class="flex mgb-5">
					<div class="mgr-10">{{data.truename}}</div>
					
					<div class="flex-1"></div>
					
				</div>
				<div class="flex mgb-5">
					<div class="cl3 f12 mgr-10">{{data.gender_title}}</div>
					<div class="cl3 f12 mgr-10">{{data.age}}岁</div>
					
					<div class="cl-primary f12">年收入：{{data.income}}万</div>
				</div>
				<div class="mgb-5 cl3">{{data.address}}</div>
				<div class="mgb-5 f12 cl3">职业：{{data.zhiye}}</div>
				 
				<div class="flexlist-desc">{{data.self_desc}}</div>
				 
			</div>
			
		</div>
		<div>
			 
			<div>
				<div class="row-box mgb-5">
					<div class="fw-600 mgb-5">自我介绍</div>
					<div class="cl2 mgb-10">{{data.self_desc}}</div>
				</div>	
				<div class="row-box mgb-5">	
					<div class="fw-600 mgb-5">Ta的意中人</div>
					<div class="cl2 mgb-10">{{data.you_desc}}</div>
				</div>
				<div class="row-box mgb-5">
					<div class="mgb-10">他向{{stat.zh_num_send}}人打招呼，{{stat.zh_num_send_pass}}个人回应</div>
					<div class="mgb-10">有{{stat.zh_num_receive}}人跟她打招呼，回应{{stat.zh_num_receive_pass}}个人</div>
					<div class="mgb-10">他向{{stat.bb_num_send}}人表白，{{stat.bb_num_send_pass}}个人回应</div>
					<div class="mgb-10">{{stat.bb_num_receive}}人向他表白，回应{{stat.bb_num_receive_pass}}个人</div>
					<div class="flex">
						<div class="flex-1"></div>
						 
						<div v-if="!isFriend" @click="showModal" class="btn-small mgr-10">打声招呼</div>
						 
						<template v-else>
						<div @click="bbModal=true" class="btn-small mgr-10">向Ta表白</div> 
						
						<div @click="gourl('/pages/pm/detail?userid='+data.userid)" class="btn-small mgr-10">聊聊</div>
						</template>
						 
					</div>
					
				</div>
				<div class="row-box mgb-5">
					<div class="fw-600 mgb-5">个人详情</div>
					<div class="d-content" v-html="data.content"></div>
				</div>
			</div>
			
		</div>
			 
		<div v-if="zhModal">
			<div @click="zhModal=false" class="modal-mask"></div>
			<div class="modal">
				<div class="modal-header">
					<div class="modal-title">打招呼</div>
					<div @click="zhModal=false" class="modal-close icon-close"></div>
				</div>
				<div class="modal-body">
					<div class="input-flex">
						<input v-model="zhContent" placeholder="输入您要说的话" class="input-flex-text"/>
					</div>
					<div @click="zhaohuSave" class="btn-row-submit">发送</div>
				</div>
			</div>
		</div>
		<div v-if="bbModal">
			<div @click="bbModal=false" class="modal-mask"></div>
			<div class="modal">
				<div class="modal-header">
					<div class="modal-title">向Ta表白</div>
					<div @click="bbModal=false" class="modal-close icon-close"></div>
				</div>
				<div class="modal-body">
					<div class="input-flex">
						<input v-model="bbContent" placeholder="输入您要说的话" class="input-flex-text"/>
					</div>
					<div @click="bbSave" class="btn-row-submit">发送</div>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default{
	 
		data:function(){
			return {
				data:{},
				zhModal:false,
				zhContent:"",
				id:0,
				userid:0,
				bbModal:false,
				bbContent:"",
				isFriend:false,
				stat:{},
				 
			}
		},
		onLoad:function(ops){
			if(ops.id!=undefined){
				this.id=ops.id;
			}
			if(ops.userid!=undefined){
				this.userid=ops.userid;
			}
			
			this.getPage();
		},
		methods:{
			getPage:function(){
				var that=this;
				that.app.get({					
					url:that.app.apiHost+"/module.php?m=xiangqin_people&a=show&id="+this.id,
					data:{
						userid:this.userid
					},
					success:function(res){
						that.userid=res.data.data.userid;
						that.data=res.data.data;
						that.stat=res.data.stat;
					}
				})
			},
			showModal:function(){
				this.zhModal=true;
			},
			zhaohuSave:function(){
				var that=this;
				if(!this.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=xiangqin_zhaohu&a=save&ajax=1",
					data:{
						content:that.zhContent,
						touserid:that.userid
					},
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if(res.error){
							return false;
						}
						that.zhModal=false;
					}
				})
			},
			bbSave:function(){
				var that=this;
				if(!this.app.canPost()){
					return false;
				}
				that.app.post({
					url:"/module.php?m=xiangqin_biaobai&a=save&ajax=1",
					data:{
						content:that.bbContent,
						touserid:that.userid
					},
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						 
						if(res.error){
							return false;
						}
						that.bbModal=false;
					}
				})
			}
		}
	}
</script>

<style>
</style>