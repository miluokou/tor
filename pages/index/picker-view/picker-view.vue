<template>
	<view class="conatiner">
		<view class="search-box">
			<view class="s-input">
				<image src="/static/images/index/search.png" class="s-img"></image>
				<input placeholder-class="phcolor" class="input" name="input" placeholder="请输入关键字" confirm-type="search" @confirm="search"
				 @input="bindViewInput" :value="searchKey" />
				<image src="/static/images/index/cancle.png" class="img30" v-show="!iconHidden" @tap="resetInput"></image>
			</view>
			<view class="s-input s-select" @tap="showPicker">
				<text class="text  wid27">{{text[0]}}</text>
				<image src="/static/images/index/icon_right.png" class="img30 pdr20"></image>
				<text class="text  wid27">{{text[1]}}</text>
				<image src="/static/images/index/icon_right.png" class="img30 pdr20"></image>
				<text class="text wid46">{{text[2]}}</text>
				<image src="/static/images/index/icon_down.png" class="img32 flr"></image>
			</view>
		</view>
		<view class="btn-select">
			<tui-button shape="circle" @click="showPicker">请选择</tui-button>
		</view>

		<!--picker-view start-->
		<view class="tui-mask-screen" :class="[showPickerStatus?'tui-mask-show':'']" @tap="hidePicker"></view>
		<view class="tui-picker-box" :class="[showPickerStatus?'tui-pickerbox-show':'']">
			<view class="picker-header tui-list-item">
				<view class="btn-cancle" hover-class="tui-opcity" :hover-stay-time="150" @tap.stop="hidePicker">取消</view>
				<view class="btn-sure" hover-class="tui-opcity" :hover-stay-time="150" @tap.stop="picker">确定</view>
			</view>
			<picker-view indicator-style="height: 50px;" class="picker-view" :value="value" @change="columnPicker">
				<picker-view-column>
					<view v-for="(item,index) in proviceArr" :key="index" class="item">{{item}}</view>
				</picker-view-column>
				<picker-view-column>
					<view v-for="(item,index) in cityArr" :key="index" class="item">{{item}}</view>
				</picker-view-column>
				<picker-view-column>
					<view v-for="(item,index) in districtArr" :key="index" class="item">{{item}}</view>
				</picker-view-column>
			</picker-view>
		</view>
		<!--picker-view end-->
	</view>
</template>

<script>
	import cityData from '@/utils/picker.city.js'
	export default {
		data() {
			return {
				proviceArr: [],
				cityArr: [],
				districtArr: [],
				value: [0, 0, 0],
				iconHidden: true,
				showPickerStatus: false,
				text: ["请选择", "请选择", "请选择"],
				searchKey: ""
			}
		},
		onLoad: function() {
			//初始化数据
			this.proviceArr = this.toArr(cityData);
			this.cityArr = this.toArr(cityData[0].children);
			this.districtArr = this.toArr(cityData[0].children[0].children)
		},
		methods: {
			toArr(object) {
				let arr = [];
				for (let i in object) {
					arr.push(object[i].text);
				}
				return arr;
			},
			//picker change切换事件
			columnPicker: function(e) {
				let value = e.detail.value;
				//如果两者下标不一致，表示滚动过
				if (this.value[0] !== value[0]) {
					this.proviceArr = this.proviceArr;
					this.cityArr = this.toArr(cityData[value[0]].children);
					this.districtArr = this.toArr(cityData[value[0]].children[0].children);
					this.value = [value[0], 0, 0]
				} else if (this.value[1] !== value[1]) {
					this.proviceArr = this.proviceArr;
					this.cityArr = this.cityArr;
					this.districtArr = this.toArr(cityData[value[0]].children[value[1]].children);
					this.value = [value[0], value[1], 0]
				} else {
					this.value = value
				}
			},
			//确定按钮
			picker: function(e) {
				let value = this.value;
				if (cityData.length > 0) {
					let provice = cityData[value[0]].text;
					let city = cityData[value[0]].children[value[1]].text;
					let district = cityData[value[0]].children[value[1]].children[value[2]].text;
					this.text = [provice, city, district];
					this.showPickerStatus = false
				}
			},
			// 显示picker-view
			showPicker: function() {
				this.showPickerStatus = true
			},
			// 隐藏picker-view
			hidePicker: function() {
				this.showPickerStatus = false
			},
			//input事件
			bindViewInput: function(e) {
				//e.detail.value
				let hidden = true;
				if (e.detail.value != "") {
					hidden = false
				}
				this.iconHidden = hidden;
				this.searchKey = e.detail.value
			},
			resetInput: function(e) {
				this.searchKey = "";
				this.iconHidden = true
			},
			search(){}
		}
	}
</script>

<style>
	/* picker start*/

	.tui-mask-screen {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: rgba(0, 0, 0, 0.6);
		z-index: 99996;
		transition: all 0.3s ease-in-out;
		opacity: 0;
		visibility: hidden;
	}

	.tui-mask-show {
		opacity: 1;
		visibility: visible;
	}

	.tui-picker-box {
		width: 100%;
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 99999;
		visibility: hidden;
		transform: translate3d(0, 100%, 0);
		transform-origin: center;
		transition: all 0.3s ease-in-out;
		min-height: 20rpx;
		background: #fff;
	}

	.tui-pickerbox-show {
		transform: translate3d(0, 0, 0);
		visibility: visible;
	}

	.picker-header {
		width: 100%;
		height: 90rpx;
		padding: 0 46rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		box-sizing: border-box;
		font-size: 32rpx;
		background: #fff;
	}

	.tui-list-item::after {
		left: 0;
	}

	.btn-cancle {
		padding: 20rpx;
		color: #888;
	}

	.btn-sure {
		padding: 20rpx;
		color: #5677fc;
	}

	.picker-view {
		width: 100%;
		height: 260px;
	}

	.item {
		line-height: 50px;
		text-align: center;
	}

	/* picker end*/

	.search-box {
		width: 672rpx;
		border-radius: 10rpx;
		box-sizing: border-box;
		margin: 80rpx auto 40rpx auto;
		position: relative;
		/* #ifdef MP-ALIPAY */
		background: #fff;
		/* #endif */
	}

	.search-box::after {
		content: '';
		position: absolute;
		height: 200%;
		width: 200%;
		border: 1rpx solid #dbe1ef;
		transform-origin: 0 0;
		-webkit-transform-origin: 0 0;
		-webkit-transform: scale(0.5);
		transform: scale(0.5);
		left: 0;
		top: 0;
		border-radius: 20rpx;
		pointer-events: none;
	}

	.s-input {
		height: 86rpx;
		padding: 0 21rpx;
		display: flex;
		align-items: center;
	}

	.s-img {
		width: 34rpx;
		height: 34rpx;
		margin-right: 17rpx;
		flex-shrink: 0;
	}

	.input {
		font-size: 32rpx;
		color: #353535;
		width: 565rpx;
		padding-right: 5rpx;
		box-sizing: border-box;
		height: 100%;
	}

	.img30 {
		height: 30rpx;
		width: 30rpx;
	}

	.s-select {
		position: relative;
		z-index: 9;
	}

	.s-select::before {
		content: '';
		position: absolute;
		border-top: 1rpx solid #dbe1ef;
		-webkit-transform: scaleY(0.5);
		transform: scaleY(0.5);
		top: 0;
		right: 0;
		left: 0;
	}

	.text {
		color: #353535;
		font-size: 32rpx;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.wid27 {
		width: 27%;
	}

	.wid46 {
		width: 46%;
	}

	.img32 {
		height: 32rpx;
		width: 32rpx;
	}

	.pdr20 {
		padding-right: 20rpx;
	}

	.flr {
		margin-left: auto;
	}

	.btn-select {
		padding: 20rpx 40rpx;
	}
</style>
