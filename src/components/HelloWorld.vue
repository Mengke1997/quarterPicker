<template>
	<div class="date-picker" @click.stop :style='`width:${width}px`'>
		<input class="input" v-model="dateValue" @click="openPanel" readonly="readonly">
		<!-- 动画特效 -->
		<transition name="fadeDownBig">
			<div class="date-panel" v-show="panelState">
				<div class="topbar">
					<span @click="leftBig">&lt;&lt;</span>
					<span class="year" @click="panelType = 'year'">{{tmpYear}}年</span>
					<span @click="rightBig">&gt;&gt;</span>
				</div>
				<!-- 年面板 -->
				<div class="type-year" v-show="panelType === 'year'">
					<ul class="year-list">
						<li
							v-for="(item, index) in yearList"
							:key="index"
							@click="!yearDisabledClass(item)&&selectYear(item)"
						>
							<span
								:class="[(item === tmpYear)&&(tmpYear==chooseYear)?'selected':'',yearDisabledClass(item)?'disabled':'']"
							>{{item}}</span>
						</li>
					</ul>
				</div>
				<!-- 季度面板 -->
				<div class="type-year" v-show="panelType === 'quarter'">
					<ul class="quarter-list year-list">
						<li
							v-for="(item, index) in quarterList"
							:key="index"
							@click="!quarterDisabledClass(index)&&selectquarter(item)"
						>
							<span
								:class="[(index+1 == tmpQuarter)&&(tmpYear==dateValue.slice(0,4))?'selected':'',quarterDisabledClass(index)?'disabled':'']"
							>{{item.label}}</span>
						</li>
					</ul>
				</div>
			</div>
		</transition>
	</div>
</template>

<script>
export default {
	data() {
		return {
			date: new Date().getDate(), // 当前日期
			dateValue:
				new Date().getFullYear() +
				"-" +
				Math.ceil((new Date().getMonth() + 1) / 3), // 输入框显示日期
			tmpYear: new Date().getFullYear(), // 年度面板上年份显示
			chooseYear: new Date().getFullYear(), //当前选中的年份
			panelType: "quarter", // 面板状态
			panelState: false, // 初始值，默认panel关闭
			quarterList: [
				{ label: "第一季度", value: 1 },
				{ label: "第二季度", value: 2 },
				{ label: "第三季度", value: 3 },
				{ label: "第四季度", value: 4 }
			],
			tmpQuarter: Math.ceil((new Date().getMonth() + 1) / 3) //选择的季度
		};
	},
	props: {
		width:{
			//输入框的宽度
			type:[Number,String],
			default:200
		},
		dateDisabled: {
			//禁掉的截止日期(不包括该日期) '2019-2'//2019年第二个季度
			type: String,
			default:
				new Date().getFullYear() +
				"-" +
				Math.ceil((new Date().getMonth() + 1) / 3)
		},
		rankDisabled: {
			//禁掉截止日期前1/后2/不禁3
			type: Number,
			default: 3
		},
		nowDate: {
			//默认输入框选择日期
			type: String,
			default:''
		},
		onChange:{
			//选中日期传值
			type:Function,
		}
	},
	computed: {
		yearList() {
			return Array.from(
				{ length: 12 },
				(value, index) => this.tmpYear + index
			);
		}
	},
	mounted() {
		if (this.nowDate) {
			this.dateValue = this.nowDate; //初始化输入框数据
			this.tmpQuarter = this.nowDate.substr(5, 1); //初始化季度选择
		}
		window.addEventListener("click", this.eventListener);
	},
	methods: {
		openPanel() {
			this.panelState = !this.panelState;
			this.panelType = "quarter";
		},
		leftBig() {
			if (this.panelType === "year") this.tmpYear -= 12;
			else this.tmpYear--;
		},
		rightBig() {
			if (this.panelType === "year") this.tmpYear += 12;
			else this.tmpYear++;
		},
		eventListener() {
			this.panelState = false;
		},
		selectYear(item) {
			this.tmpYear = item; //年度面板上年份显示
			this.chooseYear = item; //当前选择的年份
			this.panelType = "quarter"; //面板更换为季度选择
		},
		selectquarter(item) {
			this.nowValue = item.value;
			this.dateValue = this.tmpYear + "-" + item.value; //输入框显示值
			this.tmpQuarter = item.value; //季度选中值
			this.panelState = false; //面板隐藏
			this.$emit('on-change',this.dateValue)
		},
		quarterDisabledClass(index) {
			const disabled_year = this.dateDisabled.slice(0, 4);
			const disabled_quarter = this.dateDisabled.substr(5, 1);
			if (
				(this.rankDisabled == 1 &&
					((this.tmpYear == disabled_year &&
						index + 1 < disabled_quarter) ||
						this.tmpYear < disabled_year)) ||
				(this.rankDisabled == 2 &&
					((this.tmpYear == disabled_year &&
						index + 1 > disabled_quarter) ||
						this.tmpYear > disabled_year))
			) {
				return true;
			} else {
				return false;
			}
		},
		yearDisabledClass(item) {
			if (
				(this.rankDisabled == 1 &&
					item < this.dateDisabled.slice(0, 4)) ||
				(this.rankDisabled == 2 && item > this.dateDisabled.slice(0, 4))
			) {
				return true;
			} else {
				return false;
			}
		}
	},
	destroyed() {
		window.removeEventListener("click", this.eventListener);
	}
};
</script>
<style scoped>
.date-picker {
	/* width: 210px; */
	text-align: center;
	font-family: "Avenir", Helvetica, Arial, sans-serif;
}
input {
	display: inline-block;
	box-sizing: border-box;
	width: 100%;
	height: 32px;
	line-height: 1.5;
	padding: 4px 7px;
	font-size: 12px;
	border: 1px solid #dcdee2;
	border-radius: 4px;
	color: #515a6e;
	background-color: #fff;
	background-image: none;
	position: relative;
	cursor: text;
	transition: border 0.2s ease-in-out, background 0.2s ease-in-out,
		box-shadow 0.2s ease-in-out;
	margin-bottom: 6px;
}
input:focus {
	border-color: #57a3f3;
	outline: 0;
	-webkit-box-shadow: 0 0 0 2px rgba(45, 140, 240, 0.2);
	box-shadow: 0 0 0 2px rgba(45, 140, 240, 0.2);
}
.date-panel {
	box-shadow: 0 1px 6px rgba(0, 0, 0, 0.2);
	font-size: 12px;
	width: 210px;
	box-shadow: 0 0 8px #ccc;
	background: #fff;
}
.topbar {
	padding-top: 8px;
	display: flex;
	flex-direction: row;
	justify-content: space-around;
}
.topbar span {
	display: inline-block;
	width: 20px;
	height: 30px;
	line-height: 30px;
	color: #c5c8ce;
	cursor: pointer;
	border-radius: 3px;
	user-select: none;
}
.topbar span:hover {
	color: #2d8cf0;
}
.topbar .year {
	width: 60px;
	color: #515a6e;
}
.year-list {
	height: 200px;
	width: 210px;
}
.year-list li {
	display: inline-block;
	width: 70px;
	height: 50px;
	line-height: 50px;
	border-radius: 10px;
	cursor: pointer;
}
.year-list span {
	display: inline-block;
	line-height: 16px;
	padding: 8px;
	user-select: none;
}
.year-list span:hover {
	background: #e1f0fe;
}
.year-list span.selected {
	background: #2d8cf0;
	border-radius: 4px;
	color: #fff;
}
.year-list span.disabled {
	cursor: not-allowed;
	color: #c5c8ce;
	background: #f7f7f7;
}
.quarter-list li {
	width: 50%;
	height: 100px;
	line-height: 100px;
}
ul {
	list-style: none;
	padding: 0;
	margin: 0;
}
.fadeDownBig-enter-active,
.fadeDownBig-leave-active,
.fadeInDownBig {
	-webkit-animation-duration: 0.5s;
	animation-duration: 0.5s;
	-webkit-animation-fill-mode: both;
	animation-fill-mode: both;
}
.fadeDownBig-enter-active {
	-webkit-animation-name: fadeInDownBig;
	animation-name: fadeInDownBig;
}
.fadeDownBig-leave-active {
	-webkit-animation-name: fadeOutDownBig;
	animation-name: fadeOutDownBig;
}
@-webkit-keyframes fadeInDownBig {
	from {
		opacity: 0.8;
		-webkit-transform: translate3d(0, -4px, 0);
		transform: translate3d(0, -4px, 0);
	}
	to {
		opacity: 1;
		-webkit-transform: none;
		transform: none;
	}
}
@keyframes fadeInDownBig {
	from {
		opacity: 0.8;
		-webkit-transform: translate3d(0, -4px, 0);
		transform: translate3d(0, -4px, 0);
	}
	to {
		opacity: 1;
		-webkit-transform: none;
		transform: none;
	}
}
@-webkit-keyframes fadeOutDownBig {
	from {
		opacity: 1;
	}
	to {
		opacity: 0.8;
		-webkit-transform: translate3d(0, -4px, 0);
		transform: translate3d(0, -4px, 0);
	}
}
@keyframes fadeOutDownBig {
	from {
		opacity: 1;
	}
	to {
		opacity: 0;
	}
}
</style>
