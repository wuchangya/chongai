<template>
	<view class="a">
		
		<view v-for="(item,index) in petList" :key="index">
			
			<view class="flex align-center p-2 mb-2 b" hover-class="bg-light" @click="petxiangqing(index)">
				<image :src="petAvatar(item)"
				style="width: 100rpx;height: 100rpx;"
				class="rounded-circle"></image>
				<view class="flex flex-column flex-1 px-2">
					<text class="font-lg font-weight-bold text-dark">{{item.pet_name}}
					
					<text v-if="item.pet_sex===2" class="iconfont icon-nv container"></text>
					<text v-else-if="item.pet_sex===1" class="iconfont icon-nan container"></text>
					</text>
					
					<text class="font text-muted">
						{{item.species}} | {{age(item.pet_birthday)}}岁 | {{jueyuText(item)}}</text>
						<view class="flex">
							<view class="font text-white rounded c">绑定设备</view>
						</view>
				</view>
				详情
				<text class="iconfont icon-jinru"></text>
			</view>
			
		</view>

		<view >
			<view class="text-center" @click="addpet">
				<text class="iconfont icon-zengjia1" style="font-size: 100rpx; color: #cfcfcf;"></text>
			</view>
		</view>
		
		
		
	</view>
</template>

<script>
	const jueyuArray = ['绝育情况保密','已绝育','未绝育'];
	import $store from '@/store/index.js';
	import time from '@/common/time.js';
	import { mapState } from 'vuex';
	export default {
		data() {
			return {
				petList:[]
			}
		},
		methods: {
			//加载宠物信息
			getPetInfo(){
				
				// 请求宠物相关信息
				uni.request({
					url:'https://ceshi.aichong.online/api/v1/user/mypets',
					method:'GET',
					data:{},
					header:{
						"content-type":"application/json;charset=utf-8",
						"token":$store.state.token},
					success: (res) => {
						this.petList = res.data.data.list;
						console.log(this.petList);
					},
					fail: (res) => {
						console.log(res);
					}
				})
				
			},
			
			
			addpet(){
				console.log('添加宠物');
				uni.navigateTo({
					url: '../addpet/addpet'
				});
			},
			petxiangqing(id){
				// console.log('宠物详情');
				
				uni.navigateTo({
					// 通过url传参
					url: '../mypetxiangqing/mypetxiangqing?id='+id
				});
			}
		},
		computed: {
			...mapState({
				loginStatus:state=>state.loginStatus,
				user:state=>state.user
			}),
			// 宠物头像
			petAvatar(){
				return  (item) =>{
					return item.petpic ? item.petpic : '/static/pet-default.png'
				}
			},
			jueyuText(){
				return item=>{
					return jueyuArray[item.jueyu]
				}
			},
			age(){
				return b=>{
					return time.getAgeByBirthday(b)
				}
			}
			
		},
		onShow() {
			/* console.log($store.state.petList);
			if(this.loginStatus){
				this.getPetInfo()
			} *///使用vuex的话暂时就不需要数据请求了
			this.petList = $store.state.petList
			console.log(this.petList);
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
		border-radius: 50rpx;
		background-color:#FFF;
	}
	.c{
		margin: 10rpx;
		padding: 0 10rpx;
		background-color: #6CB969;
	}
	.d{
		color: #9C9C9C;
		margin: 10rpx;
		padding: 0 10rpx;
		background-color: #F5F5F5;
	}
</style>
