<template>
	<view class="grid">
		<button type="primary" @tap="cI">选择图片</button>
		<image v-if="image	!==	''" :src="image"></image>
		<button type="primary" v-if="image	!==	''" @tap="makeNFT">制作为nft</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				iconcheck: 0, //头像是否改变
				baseUrl: "http://159.138.143.51:8081/public/",
				host:"http://159.138.143.51:8081",
				image: "" //默认头像
			}
		},
		methods: {
			cI() {
				let _self = this;
				console.log(_self.image)
				uni.chooseImage({
					count: 1,
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success: function(res) {
						console.log(res)
						uni.uploadFile({
							url: this.host+'/file_upload', //	后端api接口
							filePath: res.tempFilePaths[0], //	uni.chooseImage函数调用后获取的本地文件路劲
							name: 'file',
							success: (res) => {
								console.log("123-----", res.data)
								let ret = JSON.parse(res.data)
								if (res.statusCode == 200) {
									_self.image = _self.baseUrl + ret.filename
									console.log(_self.image)
									uni.showToast({
										title: "上传成功...",
									})
								}
							},
							error(res1) {
								uni.showToast({
									title: "添加失败...",
								})
							}
						})
					},
					error: function(e) {
						console.log("e---", e)
					}
				});
			},
			makeNFT() {
				let _self = this;
				console.log("_self.image", _self.image)


				uni.request({
					url: this.host+"/makenft", //	后端api接口
					method: 'POST',
					dataType:"json",
					data: {
						imageurl: _self.image,
					},
					header:{"content-type":"application/x-www-form-urlencoded"},
					success: (res) => {
						console.log(res)
						if (res.statusCode == 200) {
							uni.showToast({
								icon: 'success',
								title: "修改成功...",
							})
						}
						setTimeout(() => {
							uni.navigateTo({
								url: ''
							})
						}, 2000);

					},
					error(res1) {
						uni.showToast({
							title: "添加失败...",
						})
					}
				});
			}
		}
	}
</script>

<style>


</style>
