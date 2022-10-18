<template>
	<view>
		<div class="tabs-border">
			<div @click="setType('new')" :class="type=='new'?'tabs-border-active':''" class="tabs-border-item">待审核</div>
			<div @click="setType('pass')" :class="type=='pass'?'tabs-border-active':''"  class="tabs-border-item">已通过</div>
			<div @click="setType('forbid')" :class="type=='forbid'?'tabs-border-active':''"  class="tabs-border-item">未通过</div>
			<div @click="setType('unpay')" :class="type=='unpay'?'tabs-border-active':''"  class="tabs-border-item">待支付</div>
		</div>
		<div class="flexlist">
			<div v-if="!list || list.length==0" class="emptyData">暂无数据</div>
			<div v-for="(item,index) in list" :key="index" class="flexlist-item">
				<img :src="item.user_head+'.100x100.jpg'" class="flexlist-img" />
				<div class="flex-1">
					<div class="flex">
						<div>{{item.nickname}}</div>
						<div class="flex-1"></div>
						<a :href="'tel:'+ item.telephone" class="iconfont cl-primary icon-mobile"></a>
					</div>
					<div class="flexlist-desc">{{item.description}}</div>
					<div class="flex">
						<div class="cl-status">{{item.status_name}}</div>
						<div class="flex-1"></div>
						<div @click="pass(item.id)" v-if="item.status==0 && item.ispay  " class="btn-mini btn-outline-primary mgr-10">通过</div>
						<div @click="forbid(item.id)" v-if="item.status==0  "  class="btn-mini btn-outline-primary">不通过</div>
					</div>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default{
	 
		data:function(){
			return {
				list:[],
				type:"new",
				pid:0
			}
		},
		onLoad:function(ops){
			this.pid=ops.pid;
			this.getPage();
		},
		methods:{
			setType:function(type){
				this.type=type;
				this.getPage();
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party_join&a=admin&ajax=1",
					dataType:"json",
					data:{
						pid:this.pid,
						type:this.type
					},
					success:function(res){
						that.list=res.data.list;
					}
				})
			},
			forbid:function(id){
				var that=this;
				uni.showModal({
					content:"确认禁止吗",
					success:function(e){
						if(!e.confirm) return false;
						that.app.get({
							url:that.app.apiHost+"/module.php?m=party_join&a=forbid&ajax=1",
							dataType:"json",
							data:{
								id:id
							},
							success:function(res){
								that.getPage();
							}
						})
					}
				})
			},
			pass:function(id){
				var that=this;
				uni.showModal({
					content:"确认审核通过吗",
					success:function(e){
						if(!e.confirm) return false;
						that.app.get({
							url:that.app.apiHost+"/module.php?m=party_join&a=pass&ajax=1",
							dataType:"json",
							data:{
								id:id
							},
							success:function(res){
								that.getPage();
							}
						})
					}
				})
			}
		}
	}
</script>

<style>
</style>