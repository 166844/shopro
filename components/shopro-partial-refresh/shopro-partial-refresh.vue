<template>
	<view class="shopro-load-card strip-loading x-c" :style="'height:' + vh + 'rpx'">
		<view v-if="loadStatus === 'loading'" class="load-line load-success" v-for="v in 6" :key="v" :style="{ '--line-index': v }"></view>
		<view v-if="loadStatus === 'fail'" class="load-fail x-c" @tap="onRetry">
			<text class="iconfont icon-shibai2"></text>
			<view class="retry-btn">加载失败，点击重试~</view>
		</view>
	</view>
</template>

<script>
export default {
	components: {},
	data() {
		return {};
	},
	props: {
		value: {
			type:String,
			default:'loading'
		},
		vh: {
			type: Number,
			default: 300
		}
	},
	computed: {
		loadStatus: {
			get() {
				return this.value;
			},
			set(val) {
				this.$emit('input', val);
			}
		}
	},
	methods: {
		onRetry() {
			this.$emit('retry');
		}
	}
};
</script>

<style lang="scss" scoped>
.shopro-load-card {
	width: 100%;
	background-color: #fff;
}
.load-fail {
	width: 100%;
	height: 100%;
	color: #e0e0e0;
	.icon-shibai2 {
		font-size: 40rpx;
		margin-right: 20rpx;
	}
}
.strip-loading {
	.load-line {
		--time: calc((var(--line-index) - 1) * 200ms);
		border-radius: 3rpx;
		width: 6rpx;
		height: 30rpx;
		background-color: #e0e0e0;
		animation: beat 1.5s ease-in-out var(--time) infinite;
		& + .load-line {
			margin-left: 5rpx;
		}
	}
}
@keyframes beat {
	0%,
	100% {
		transform: scaleY(1);
	}
	50% {
		transform: scaleY(0.5);
	}
}
</style>
