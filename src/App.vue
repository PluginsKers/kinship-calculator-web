<script setup lang="ts">
// @ts-ignore
import relationship from "relationship.js"

import { reactive, watch, onMounted } from 'vue'

type data = {
  timer: Object | any,
}

let data = reactive({
	search: '',
	append: '',
	result: '------',
	sex: 1,
	isActive: '',
	timer: 0,
});

/**
 * 加载中
 */

let loaded = reactive({
	value: false
});

const fn = () => {
	if (document.readyState == "complete") {
		window.removeEventListener("load", fn);
		complele();
	}
};

window.addEventListener("load", fn, false);

const complele = () => {
	setTimeout(() => {
		loaded.value = true;
	}, 1895);
};

watch(data, (n, o) => {
	if ('爸爸,老公,儿子,哥哥,弟弟'.indexOf(n.append) > -1) {
		data.sex = 1;
	} else if ('妈妈,老婆,女儿,姐姐,妹妹'.indexOf(n.append) > -1) {
		data.sex = 0;
	}
});

/**
 * 切换查询对象性别
 * 
 */
const _selector = () => {
	data.sex = (data.sex == 1) ? 0 : 1;
}

const _clear = () => {

	_active.call(_append, "clear");

	data.search = '';
	data.append = '';
	data.result = '------';
}

const _delete = () => {

	_active.call(_append, "delete");

	let index = data.search.lastIndexOf('的'),
		search = data.search;
	index = Math.max(0, index);
	if (search) {
		search = search.slice(0, index);
		data.search = search;
		let _i = search.lastIndexOf('的');
		_i = Math.max(0, _i);
		data.append = search.slice(_i + 1);
		data.result = search;
	} else {
		data.search = '';
		data.append = '';
		data.result = '------';
	}
}

const _append = (v: string) => {

	_active.call(_append, v);

	let arr = data.search.split('的'),
		search = data.search

	if (arr.length > 10) {
		data.search = '';
		data.append = '';
		data.result = '------';
	} else {
		let a = (search ? search + '的' + v : v);
		data.append = v;
		data.search = data.result = a;
	}
}

const _calculate = () => {

	_active.call(_append, 'calculate');

	if (!data.search) {
		data.append = '';
		data.result = '------';
	} else {
		let _r = relationship({ text: data.search, sex: data.sex });
		if (_r.length > 0) {
			let result = '';
			for (let i = 0; i < _r.length; i++) {
				if (i != 0 && i != _r.length) result += ' / ';
				result += _r[i];
			}
			data.result = result;
		} else {
			data.result = '未知';
		}
	}
}

const _active = (n: string) => {
	if (data.timer) clearTimeout(data.timer);
	data.isActive = n;
	data.timer = setTimeout(() => {
		data.isActive = '';
	}, 120);
}
</script>

<template>


	<div v-show="!loaded.value" id="loading">
		<h2>加载中</h2>
	</div>

	<a href="https://github.com/PluginsKers/kinship_calculator" target="_blank" class="github"
		aria-label="View source on Github"><svg viewBox="0 0 250 250" aria-hidden="true">
			<path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
			<path
				d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2"
				fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path>
			<path
				d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z"
				fill="currentColor" class="octo-body"></path>
		</svg>
	</a>

	<div class="calculator">
		<div class="result" style="grid-area: result">
			{{ data.result }}
		</div>

		<button :class="{ active: data.isActive === 'clear' }" style="grid-area: ac" @click="_clear">AC</button>
		<button :class="{ active: data.isActive === 'calculate', equal: true }" style="grid-area: equal"
			@click="_calculate">=</button>

		<button :class="{ active: data.isActive === '儿子' }" style="grid-area: son" @click="_append('儿子')">子</button>
		<button :class="{ active: data.isActive === '女儿' }" style="grid-area: daughter"
			@click="_append('女儿')">女</button>

		<button :class="{ active: data.isActive === '哥哥' }" style="grid-area: brother" @click="_append('哥哥')">兄</button>
		<button :class="{ active: data.isActive === '弟弟' }" style="grid-area: younger_brother"
			@click="_append('弟弟')">弟</button>

		<button :class="{ active: data.isActive === '老公', disabled: data.sex === 1 }" style="grid-area: husband"
			@click="_append('老公')">夫</button>
		<button :class="{ active: data.isActive === '老婆', disabled: data.sex === 0 }" style="grid-area: wife"
			@click="_append('老婆')">妻</button>

		<button :class="{ active: data.isActive === '姐姐' }" style="grid-area: sister" @click="_append('姐姐')">姐</button>
		<button :class="{ active: data.isActive === '妹妹' }" style="grid-area: younger_sister"
			@click="_append('妹妹');">妹</button>

		<button :class="{ active: data.isActive === '爸爸' }" style="grid-area: dad" @click="_append('爸爸')">父</button>
		<button :class="{ active: data.isActive === '妈妈' }" style="grid-area: mom" @click="_append('妈妈')">母</button>

		<button :class="{ active: data.isActive === 'delete' }" style="grid-area: back" @click="_delete">
			<svg style="width:24px;height:24px;margin-top: 6px;" viewBox="0 0 24 24">
				<path fill="currentColor"
					d="M22,3H7C6.31,3 5.77,3.35 5.41,3.88L0,12L5.41,20.11C5.77,20.64 6.31,21 7,21H22A2,2 0 0,0 24,19V5A2,2 0 0,0 22,3M19,15.59L17.59,17L14,13.41L10.41,17L9,15.59L12.59,12L9,8.41L10.41,7L14,10.59L17.59,7L19,8.41L15.41,12" />
			</svg>
		</button>
		<button class="selector" style="grid-area: selector" @click="_selector">
			<div>{{ (data.sex == 0) ? '女' : '男' }}</div>
		</button>
	</div>
</template>

<style scoped>
#loading>h2 {
	margin-top: -10vh;
}

#loading {
	position: fixed;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	width: 100%;
	height: 100%;
	background: #eee;
	z-index: 999;
	display: flex;
	justify-content: center;
	align-items: center;
}

.github {
	width: 65px;
	color: #eeeeee;
	border-bottom: 0;
	position: fixed;
	right: 0;
	text-decoration: none;
	top: 0;
}

.github svg {
	fill: #999999;
}
</style>
