<template>
	<!-- 限时抢购 -->
	<view class="seckill-goods pa20 mx20 mb10" v-if="showActivity">
		<view class="title-box x-bc">
			<text class="title">{{ detail.name }}</text>
			<view class="group-people x-f" @tap="$Router.push('/pages/activity/seckill/list')">
				<view class="time-box x-f" v-if="time.s">
					<view class="x-f" v-if="parseInt(time.d)">
						<view class="count-text-box">{{ time.d }}</view>
						天
					</view>
					<view class="x-f">
						<view class="count-text-box">{{ time.h }}</view>
						时
					</view>

					<view class="count-text-box">{{ time.m }}</view>
					分
					<view class="count-text-box">{{ time.s }}</view>
					秒
				</view>
				<text class="tip">更多</text>
				<text class="cuIcon-right"></text>
			</view>
		</view>
		<view class="goods-box swiper-box x-f">
			<swiper class="carousel" circular @change="swiperChange" :autoplay="true" duration="2000">
				<swiper-item v-for="(goods, index) in goodsList" :key="index" class="carousel-item">
					<view class="goods-list-box x-f">
						<block v-for="mgoods in goods" :key="mgoods.id">
							<sh-activity-goods :detail="mgoods" class="goods-item"><!-- <block slot="titleText">立减￥8.5</block> --></sh-activity-goods>
						</block>
					</view>
				</swiper-item>
			</swiper>
			<view class="swiper-dots" v-if="goodsList.length > 1">
				<text :class="swiperCurrent === index ? 'dot-active' : 'dot'" v-for="(dot, index) in goodsList.length" :key="index"></text>
			</view>
		</view>
	</view>
</template>

<script>
import shActivityGoods from './sh-activity-goods.vue';
export default {
	components: {
		shActivityGoods
	},
	data() {
		return {
			time: {}, //倒计时
			goodsList: [],
			swiperCurrent: 0,
			showActivity: true //是否显示活动。
		};
	},
	props: {
		detail: {}
	},
	watch: {},
	computed: {},
	created() {
		this.getSeckillGoodsList();
	},
	methods: {
		swiperChange(e) {
			this.swiperCurrent = e.detail.current;
		},
		// 路由跳转
		jump(path, parmas) {
			this.$Router.push({
				path: path,
				query: parmas
			});
		},
		// 数据分层
		sortData(oArr, length) {
			let arr = [];
			let minArr = [];
			oArr.forEach(c => {
				if (minArr.length === length) {
					minArr = [];
				}
				if (minArr.length === 0) {
					arr.push(minArr);
				}
				minArr.push(c);
			});

			return arr;
		},
		countDown(t) {
			let _self = this;
			let timer = setInterval(() => {
				if (t > 0) {
					_self.time = _self.$tools.format(t);
					t--;
				} else {
					clearInterval(timer);
					_self.time = '秒杀结束';
				}
			}, 1000);
		},
		// 获取秒杀商品
		getSeckillGoodsList() {
			let that = this;
			that.$api('goods.activity', {
				activity_id: that.detail.id
			}).then(res => {
				if (res.code === 1) {
					let arr = that.sortData(res.data.goods.data, 4);
					that.goodsList = arr;
					let nowTime = new Date().getTime();
					let endTime = res.data.endtime * 1000;
					let t = endTime - nowTime;
					that.countDown(t / 1000);
				} else {
					that.showActivity = false;
				}
			});
		}
	}
};
</script>

<style lang="scss">
// 轮播

.swiper-box,
.carousel {
	width: 700rpx;
	height: 240upx;
	position: relative;
	border-radius: 20rpx;

	.carousel-item {
		width: 100%;
		height: 100%;
		// padding: 0 28upx;
		overflow: hidden;
	}

	.swiper-image {
		width: 100%;
		height: 100%;
		// border-radius: 10upx;
		background: #ccc;
	}
}

.swiper-dots {
	display: flex;
	position: absolute;
	left: 50%;
	transform: translateX(-50%);
	bottom: 0rpx;
	z-index: 66;

	.dot {
		width: 45rpx;
		height: 3rpx;
		background: #eee;
		border-radius: 50%;
		margin-right: 10rpx;
	}

	.dot-active {
		width: 45rpx;
		height: 3rpx;
		background: #a8700d;
		border-radius: 50%;
		margin-right: 10rpx;
	}
}
// 今日必拼+限时抢购
.seckill-goods {
	background: #fff;
	border-radius: 20rpx;
	overflow: hidden;

	.title-box {
		padding-bottom: 20rpx;

		.title {
			font-size: 32rpx;
			font-weight: bold;
		}

		.group-people {
			.time-box {
				font-size: 26rpx;
				color: #edbf62;
				.count-text-box {
					width: 30rpx;
					height: 34rpx;
					background: #edbf62;
					text-align: center;
					line-height: 34rpx;
					font-size: 24rpx;
					border-radius: 6rpx;
					color: rgba(#fff, 0.9);
					margin: 0 8rpx;
				}
			}

			.head-box {
				.head-img {
					width: 40rpx;
					height: 40rpx;
					border-radius: 50%;
					background: #ccc;
				}
			}

			.tip {
				font-size: 28rpx;
				padding-left: 30rpx;
				color: #666;
			}

			.cuIcon-right {
				font-size: 30rpx;
				line-height: 28rpx;
				color: #666;
			}
		}
	}

	.goods-box {
		.goods-item {
			margin-right: 22rpx;
			&:nth-child(4n) {
				margin-right: 0;
			}
		}
	}
}
</style>
