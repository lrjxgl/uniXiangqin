<template>
	<view>
		<div class="tabs-border">
			
			<div @click="setTab('pm')" :class="tab=='pm'?'tabs-border-active':''" class="tabs-border-item">我的私信</div>
			<div @click="setTab('zhaohu')" :class="tab=='zhaohu'?'tabs-border-active':''" class="tabs-border-item">打招呼</div>
			<div @click="setTab('notice')" :class="tab=='notice'?'tabs-border-active':''" class="tabs-border-item">我的消息</div>
			
			 
		</div>
		<div v-if="tab=='pm'">
			<page-pm></page-pm>
		</div>
		<div v-if="tab=='notice'">
			<page-notice></page-notice>
		</div>
		
		<div v-if="tab=='sysmsg'">
			 
			<page-sysmsg></page-sysmsg>
		</div>
		<div v-if="tab=='zhaohu'">
			<page-zhaohu></page-zhaohu>
		</div> 
		<xiangqin-footer tab="xiangqin_notice"></xiangqin-footer>
	</view>
</template>

<script>
	import pagePm from "./pm.vue";
	import pageNotice from "./notice.vue";
	import pageSysmsg from "./sysmsg.vue";
	import pageZhaohu from "./zhaohu.vue";
	import xiangqinFooter from "../xiangqin-footer.vue";
	export default{
		components:{
			pagePm,
			pageNotice,
			pageSysmsg,
			pageZhaohu,
			xiangqinFooter
		}, 
		data:function(){
			return {
				pageLoad:false,
				 
				tab:"pm"
				
			}
		},
		created:function(){
		 
			var s=localStorage.getItem("xiangqin-notice");
			
			if(s!=null){
				this.tab=s;
			} 
		},
		methods:{
			setTab:function(t){
				this.tab=t;
				localStorage.setItem("xiangqin-notice",t); 
			}
			 
		}
	}
	
</script>

<style>
	
</style>