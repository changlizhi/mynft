<template>
	<view class="grid">
		<view class="card mycard">
			<view class="showRed">
				说明:蓝色字体可点击复制内容，铸造完成后将自动跳转至ETH浏览器，请保持您的网络畅通（需要翻墙）
			</view>
			铸造完nft后可以在eth的rinkeby网查看钱包
			<view @tap="fuZhiQianBao()" class="showBlue qianBao">0xaf6d667582953Eee0b059F656e8b125Aae636F53</view>
			对应的账户中Erc721代币数量是否增长，地址为
			<view class="showBlue ETHAddr" @tap="fuZhiEth()">
				https://rinkeby.etherscan.io/address/0xaf6d667582953eee0b059f656e8b125aae636f53#tokentxnsErc721</view>
			也可以使用metamask钱包导入助记词
			<view @tap="fuZhiZhuJiCi()" class="showBlue zhuJiCi">cabbage fragile suspect pig alley story capital strike
				mandate crop feel tunnel</view>
			，然后使用metamask授权给opensea后进行查看或者销售铸造成功的nft地址为
			<view class="showBlue opensea" @tap="fuZhiOpensea()">https://testnets.opensea.io/account</view>
		</view>
		<button type="primary" @tap="cI">选择图片</button>
		<button type="warn" v-if="image	!==	''" @tap="makeNFT">铸造为nft</button>
		<image v-if="image	!==	''" :src="image"></image>
	</view>
</template>

<script>
	import Clipboard from 'clipboard'
	export default {
		data() {
			return {
				iconcheck: 0, //头像是否改变
				baseUrl: "http://159.138.143.51:8081/public/",
				host: "http://159.138.143.51:8081",
				image: "" //默认头像
			}
		},
		methods: {
			fuZhi(neirong) {
				uni.setClipboardData({
					data: neirong,
					success: function(succ) {
						console.log("succclip---:", succ)
						uni.showToast({
							title: "复制成功"
						})
					},
					fail: function(error) {
						uni.showToast({
							title: "复制失败"
						})
					}
				})
			},
			fuZhiQianBao() {
				let clip = new Clipboard('.qianBao', {
					text: function() {
						return "0xaf6d667582953Eee0b059F656e8b125Aae636F53"
					}
				})
				clip.on('success', function(e) {
					uni.showToast({
						title: "复制钱包地址成功"
					})
				})
			},
			fuZhiEth() {
				let clip = new Clipboard('.ETHAddr', {
					text: function() {
						return "https://rinkeby.etherscan.io/address/0xaf6d667582953eee0b059f656e8b125aae636f53#tokentxnsErc721"
					}
				})
				clip.on('success', function(e) {
					uni.showToast({
						title: "复制ETH地址成功"
					})
				})

			},
			fuZhiZhuJiCi() {
				let clip = new Clipboard('.zhuJiCi', {
					text: function() {
						return "cabbage fragile suspect pig alley story capital strike mandate crop feel tunnel"
					}
				})
				clip.on('success', function(e) {
					uni.showToast({
						title: "复制助记词成功"
					})
				})
			},
			fuZhiOpensea() {
				let clip = new Clipboard('.opensea', {
					text: function() {
						return "https://testnets.opensea.io/account"
					}
				})
				clip.on('success', function(e) {
					uni.showToast({
						title: "复制opensea地址成功"
					})
				})
			},

			toOpensea() {
				console.log(12334)
				location.href = "https://testnets.opensea.io/account"
			},
			toEth() {
				uni.showToast({
					title: "跳转至ETH...",
					success: function() {
						location.href =
							"https://rinkeby.etherscan.io/address/0xaf6d667582953eee0b059f656e8b125aae636f53#tokentxnsErc721"
					}
				})
			},
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
							url: _self.host + '/file_upload', //	后端api接口
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
					url: _self.host + "/makenft", //	后端api接口
					method: 'POST',
					dataType: "json",
					data: {
						imageurl: _self.image,
					},
					header: {
						"content-type": "application/x-www-form-urlencoded"
					},
					success: (res) => {
						console.log(res)
						if (res.statusCode == 200) {
							uni.showToast({
								icon: 'success',
								title: "铸造成功...",
								success: function() {
									_self.toEth()
								}
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
							title: "铸造失败...",
						})
					}
				});
			}
		}
	}
</script>

<style>
	.grid {
		font-size: 30rpx;
	}

	.mycard {
		height: 56%;
		padding: 1%;
		margin: 1%;
		word-wrap: break-word;
		word-break: break-all;
	}

	.showBlue {
		font-size: 22rpx;
		color: #007AFF;
		word-wrap: normal;
		margin: 2px;
	}

	.showRed {
		font-size: 28rpx;
		color: #E96900;
		word-wrap: normal;
		margin-bottom: 4px;
	}
</style>
