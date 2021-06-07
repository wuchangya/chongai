<template>
	<view class="px-2">
		<view class="uni-uploader" >
			<view class="uni-uploader-head">
				<view class="uni-uploader-title">点击可预览图片</view>
				<view class="uni-uploader-info">共{{images.length}}张</view>
			</view>
			<view class="uni-uploader-body">
				<view class="uni-uploader__files">
					<block v-for="(image,index) in images" :key="index">
						
						<view class="uni-uploader__file position-relative">
							<image class="uni-uploader__img rounded" :src="image.url" :data-src="image.url" @tap="TanPreviewImage(image.url)" mode="aspectFill"></image>
							
						</view>
						
					</block>
					<!-- <view class="uni-uploader__input-box rounded">
						<view class="uni-uploader__input" @tap="chooseImage"></view>
					</view> -->
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import $store from '@/store/index.js';
	export default {
		data() {
			return {
				images:[],
/* 				title: 'choose/previewImage',
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 8,
				count: [1, 2, 3, 4, 5, 6, 7, 8, 9] */
			}
		},
		onLoad() {
			this.images = $store.state.pet.images;
		},
		methods: {
			// 预览图片
			previewImage(e) {
				console.log(e);
				var current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.images
				})
			},
			chooseImage(){
				
			},
			
			TanPreviewImage(imageUrl){
			    console.log(imageUrl) // http://192.168.100.251:8970/6_1597822634094.png
			    var images = [];
			    images.push(imageUrl);
			    console.log(images)  // ["http://192.168.100.251:8970/6_1597822634094.png"]
			    uni.previewImage({ // 预览图片  图片路径必须是一个数组 => ["http://192.168.100.251:8970/6_1597822634094.png"]
			        current:0,
			        urls:images,
			        longPressActions: {  //长按保存图片到相册
			            itemList: ['保存图片'],
			            success: (data)=> {
			                console.log(data);
			                uni.saveImageToPhotosAlbum({ //保存图片到相册
			                    filePath: payUrl,
			                    success: function () {
			                        uni.showToast({icon:'success',title:'保存成功'})
			                    },
			                    fail: (err) => {
			                        uni.showToast({icon:'none',title:'保存失败，请重新尝试'})
			                    }
			                });
			            },
			            fail: (err)=> {
			                console.log(err.errMsg);
			            }
			    }
			    });
			},
			
		}
	}
</script>

<style>

</style>
