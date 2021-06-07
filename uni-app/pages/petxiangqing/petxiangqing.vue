<template>
	<view class="a">
		
		<view class="flex justify-between pl-2 pr-2 font-md">
			<view class="font text-muted">照片</view>
			<view class="font text-muted" @click="petpics">全部{{this.petList.images.length}}<text class="iconfont icon-jinru"></text>
			</view>
		</view>
		<!-- 轮播图 -->
		<swiper class="" :indicator-dots="true" 
		:interval="3000" :duration="1000" style="height: 400rpx;">
			<swiper-item v-for="(item,index) in this.lunbolist"
			:key="index">
				<image :src="item.url"
				style="width:100% ;mode:widthFix ; border-radius: 50rpx;" 
				class="w-100 rounded"></image>
			</swiper-item>
		</swiper>
		
		
		<view class="p-4 mb-4 b" hover-class="bg-light">
			
			<view class="flex align-center">
			
				<view class="">
					<image :src="petAvatar"
					style="width: 100rpx;height: 100rpx;"
					class="rounded-circle"></image>
					
				</view>
				
				<view class="flex flex-column flex-1 px-2">
					<text class="font-lg font-weight-bold text-dark">{{this.petList.pet_name}}
					<text v-if="this.petList.pet_sex===2" class="iconfont icon-nv container"></text>
					<text v-else-if="this.petList.pet_sex===1" class="iconfont icon-nan container"></text>
					</text>
				</view>
				
				<view @click="xiugai()" v-if="$store.state.user.id===$store.state.theid">
					<text class="font-md text-muted">修改</text>
					<text class="iconfont icon-jinru"></text>
				</view>
			
				
			</view>
			
			<view class="pl-1 pr-1">
				<!--宠物性别、宠物生日、是否绝育、宠物品种、宠物体重、宠物爱好 -->
				<text class="font text-muted d-block">宠物性别：{{sexText(this.petList.pet_sex)}}</text>
				<text class="font text-muted d-block">宠物生日：{{this.petList.pet_birthday}}</text>
				<text class="font text-muted d-block">是否绝育：{{jueyuText(this.petList.jueyu)}}</text>
				<text class="font text-muted d-block">宠物品种：{{this.petList.species}}</text>
				<text class="font text-muted d-block">宠物体重：{{this.petList.weight}}KG</text>
				<text class="font text-muted d-block">宠物特征：{{this.petList.feature}}</text>
			</view>
		</view>
		
		
		<view v-if="$store.state.user.id===$store.state.theid">
			<button class="bg-main text-white" style="border-radius: 50rpx;border: 0;" type="primary">项圈定位</button>
		</view>
		
	</view>
</template>

<script>
	const sexArray = ['保密', 'GG', 'MM'];
	const jueyuArray = ['保密','是','否'];
	import divider from '@/components/common/divider.vue';
	import $store from '@/store/index.js';
	export default {
		components:{
			divider
		},
		data() {
			return {
				id:0,
				petId:0,
				petList:[],
				swiperList:[],
				xingbie:"",
				shengri:"",
				jueyu:"否",
				pinzhong:"",
				tizhong:"",
				aihao:"",
				// 轮播图的list，只有宠物图片的四项
				lunbolist:[]
			}
		},
		computed:{
			// 宠物头像
			petAvatar(){
				// this.petList[this.id].petpic ? this.petList[this.id].petpic : 
				return this.petList.petpic ? this.petList.petpic : '/static/pet-default.png'
			},
			jueyuText(){
				return item=>{
					return jueyuArray[item]
				}
			},
			sexText() {
				return item=>{
					return sexArray[item]
				}
			},
		},
		onLoad(id) {
			console.log(this.$store.state);
			this.id = id.id;
			console.log(this.id);
		},
		onShow() {
			//使用vuex的话暂时就不需要数据请求了
			if($store.state.user.id===$store.state.theid){
				this.petList = $store.state.petList
				console.log(this.petList);
			}
			else{
				this.petList = $store.state.ThePet
				console.log($store.state.user.id);
				console.log($store.state.theid);
				console.log(this.petList);
			}
			// console.log(this.petList);
			this.petId = this.petList[this.id].id;
			console.log(this.petId);
			console.log($store.state.token);
				this.getPetInfo()
		},
		methods: {
			// 修改详情
			xiugai(){
				console.log(this.id);
				uni.navigateTo({
					url: '../xiugaipet/xiugaipet?id='+this.id
				});
			},
			
			//加载宠物信息
			getPetInfo(){
				console.log('https://ceshi.aichong.online/api/v1/pet/'+this.petId);
				// 请求宠物相关信息
				uni.request({
					url:'https://ceshi.aichong.online/api/v1/pet/'+this.petId,
					method:'GET',
					data:{},
					header:{
						"content-type":"application/json;charset=utf-8",
						"token":$store.state.token},
					success: (res) => {
						console.log(res);
						this.petList = res.data.data.detail;
						this.lunbolist = res.data.data.detail.images.slice(0,3)
						console.log(this.lunbolist);
						console.log(this.petList);
						// 将要修改的当前宠物旧的信息提交到到vuex
						this.$store.commit('editPetXiugai',{
							value:res.data.data.detail
						})
					},
					fail: (res) => {
						console.log(res);
					}
				})
				
			},
			petpics(){
				uni.navigateTo({
					url: '../thepetpics/thepetpics'
				});
			}
		}
	}
</script>

<style>
	.a{
		height: 100%;
		background-color: #F2F2F2;
		padding: 30rpx;
	}
	.b{
		border-radius: 0 0 50rpx 50rpx;
		background-color:#FFF;
	}
	.c{
		margin: 10rpx;
		padding: 0 10rpx;
		background-color: #6CB969;
	}
</style>
