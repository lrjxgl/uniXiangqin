<template>
	<view v-if="pageLoad">
		<form @submit="submit">
			<input type="hidden" name="id" style="display:none;" v-model="data.id">
			 
			<div class="input-flex">
				<div class="input-flex-label">姓名：</div>
				<input type="text" name="truename" id="truename" class="input-flex-text" :value="data.truename" />
			</div>
			<div class="input-flex">
				<div class="input-flex-label">联系电话：</div>
				<input type="text" name="telephone" id="telephone" class="input-flex-text" :value="data.telephone" />
			</div>
			<div class="input-flex" >
				<div class="input-flex-label">所在地</div>
				<div class="none">
					<input  type="text" name="province_id" :value="province_id"/>
					<input  type="text" name="city_id" :value="city_id"/>
					<input  type="text" name="town_id" :value="town_id"/>
				</div>
				
				
				<picker-region @callParent="setAddr" :defaultProvinceid="province_id" :defaultCityid="city_id" :defaultTownid="town_id"></picker-region>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">头像：</div>
				<div class="flex-1">
					<div class="upimg-box bg-fff">
						<div @click="upload()"  v-if="data && data.imgurl!=''" class="upimg-item">
							<image class="upimg-img" :src="data.trueimgurl+'.100x100.jpg'" mode="widthFix">
							</image>
						</div>
						
						<div @click="upload()" class="upimg-btn js-upimg-btn">
							<i class="upimg-btn-icon"></i>
						</div>
						
						<input type="text" name="imgurl" :value="data.imgurl" class="none" />
					</div>
				</div>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">出生年月：</div>
				 <picker class="input-flex-text" style="line-height: 36px;"   mode="date" @change="setBirthday">{{data.birthday}}</picker>
				<input type="text" name="birthday" id="birthday" class="none" :value="data.birthday" />
			</div>
			<div class="input-flex flex-ai-center">
				<div class="input-flex-label">性别：</div>
				<input type="text" name="gender" class="none" :value="data.gender" />
				<radio-group  class="flex flex-ai-center" @change="set_gender">
					<radio  :checked="data.gender==1"  value="1" /> 男
					<radio :checked="data.gender==2"  class="mgl-20"  value="2" /> 女
				</radio-group>
				
			</div>
			<div class="input-flex">
				<div class="input-flex-label">年收入：</div>
				<input type="text" name="income" id="income" class="input-flex-text" :value="data.income" /> 
				<span class="mgl-5">万元</span>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">职业：</div>
				<input type="text" name="zhiye" id="zhiye" class="input-flex-text" :value="data.zhiye" />
			</div>
			<div class="input-flex">
				<div class="input-flex-label">房子</div>
				<div class="none">
					<input  name="has_house" :value="data.has_house" />
				</div>
				<radio-group @change="set_has_house" class="flex flex-ai-center">
					 
						<radio  :checked="data.has_house==1" value="1" /> 有
						<radio   :checked="data.has_house==2" class="mgl-20"  value="2" /> 没有 
					 
					
				</radio-group>
			</div>
			
			<div class="input-flex">
				<div class="input-flex-label">车子</div>
				<div class="none">
					<input  name="has_car" :value="data.has_car" />
				</div>
				
				<radio-group @change="set_has_car" class="flex flex-ai-center">
					 
					<radio  :checked="data.has_car==1"  value="1" /> 有 
					<radio  :checked="data.has_car==2" class="mgl-20" value="2" /> 没有 
				</radio-group>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">对象要求：</div>
				<textarea name="you_desc" id="you_desc" class="textarea-flex-text" :value="data.you_desc"></textarea>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">自我介绍：</div>
				 
					<textarea name="self_desc" id="self_desc" class="textarea-flex-text" :value="data.self_desc" ></textarea>
			</div>
			 
			 
			<textarea name="content" class="none" :value="data.content"></textarea>
			<sky-editor contenteditable="true" class="sky-editor-content" @call-parent="setContent" :html="data.content"></sky-editor>
			
			 
			<button form-type="submit" class="btn-row-submit">保存</button>
		</form>
	</view>
</template>

<script>
	import pickerRegion from "../../components/pickerregion.vue";
	import skyEditor from "../../components/sky-editor.vue";
	export default{
		components:{
			pickerRegion,
			skyEditor
		},
		data:function(){
			return {
				pageLoad:false,
				data:{
					id:0,
					truename:"",
					telephone:"",
					province_id:0,
					city_id:0,
					town_id:0,
					imgurl:"",
					trueimgurl:"",
					birthday:"",
					gender:0,
					income:"",
					zhiye:"",
					has_house:0,
					has_car:0,
					you_desc:"",
					self_desc:"",
					content:""
					
				},
				province_id:0,
				city_id:0,
				town_id:0,
			}
		},
		created:function(){
			this.getPage();
		},
		methods:{
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_people&a=add",
					success:function(res){
						
						if(res.data.data){
							that.data=res.data.data;
							that.province_id=that.data.province_id;
							that.city_id=that.data.city_id;
							that.town_id=that.data.town_id;
							
						}
						that.pageLoad=true;
					}
				})
			},
			upload:function(){
				var that=this;
				uni.chooseImage({
					success: (chooseImageRes) => {
						const tempFilePaths = chooseImageRes.tempFilePaths;
						uni.uploadFile({
							url: that.app.apiHost+"/index.php?m=upload&a=img&ajax=1&loginToken="+that.app.getToken(),
							filePath: tempFilePaths[0],
							name: 'upimg',
							
							success: function(res){
								var e=JSON.parse(res.data);
								that.data.imgurl=e.data.imgurl;
								that.data.true_imgurl=e.data.trueimgurl;
								
							}
						});
					}
				});
			},
			setAddr:function(e){
				var that=this;
				that.city_id=e.cityid
				that.town_id=e.townid;
				that.province_id=e.provinceid
			},
			setBirthday:function(e){
				this.data.birthday=e.target.value;
			},
			set_has_car:function(e){
				this.data.has_car=e.target.value;
			},
			set_has_house:function(e){
				this.data.has_house=e.target.value;
			},
			set_gender:function(e){
				this.data.gender=e.target.value
			},
			setContent:function(e){
				this.data.content=e;
			},
			submit:function(e){
				var that=this;
				if(!this.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=xiangqin_people&a=save&ajax=1",
					data:e.detail.value,
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if(res.error){
							return false;
						}
						setTimeout(function(){
							uni.navigateBack()
						},1000)
						
					}
				})
				 
			}
		}
	}
</script>

<style>
</style>