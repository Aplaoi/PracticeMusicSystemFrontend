<script setup>
	import {ref,onMounted} from "vue";
	import axios from "axios";

	const formData=ref(null);

	function deleteData(id){
		axios.delete(`http://localhost:8080/musics?id=${id}`).then(
			()=>{console.log("已删除");getData()}
		).catch(()=>console.log("删除失败"))
	}

	function getData(){
		axios.get("http://localhost:8080/musics").then(
			response=>formData.value=response.data.data
		).catch(()=>console.log("失败"))
	}

	onMounted(()=>getData());

</script>


<template>
	<h1>音乐列表</h1>
	<div class="table">
		<el-table :data="formData" border stripe style="width: 750px">
			<el-table-column align="center" label="ID" width="100">
				<template #default="scope1">
					<span>{{ scope1.$index+1}}</span>
				</template>
			</el-table-column>
			<el-table-column align="center" prop="name" label="Name" width="200"/>
			<el-table-column align="center" prop="singer" label="Singer" width="250"/>
			<el-table-column align="center" label="Operations" width="200">
				<template #default="scope">
					<el-button link type="danger" size="small" @click="deleteData(scope.row.id)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
	</div>

</template>


<style scoped>
h1{
	display: flex;
	justify-content: center;
	font-size: xx-large;
}
.table{
	display: flex;
	justify-content: center;
	align-content: center;
	align-items: center;
}

</style>
