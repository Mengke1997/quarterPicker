<template>
	<div id="app">
		<HelloWorld 
			width='200' 
			dateDisabled='2018-4' 
			:rankDisabled='1'
			nowDate='2019-4'
			@on-change='dateChange'/>
	</div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
	name: "App",
	components: {
		HelloWorld
	},
	methods:{
		dateChange(item){
			console.log(item)
		}
	}
};
</script>

<style>
#app {
	font-family: "Avenir", Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
	padding: 0 400px;
}
</style>
