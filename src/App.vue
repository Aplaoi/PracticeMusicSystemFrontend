<script setup>
	import {ref,onMounted} from "vue";
	import axios from "axios";
	import {ElMessage} from "element-plus";

	const formData=ref(null);
	const isOpen=ref(false);

	const name=ref('');
	const singer=ref('');

	//删除music
	function deleteData(id){
		axios.delete(`http://localhost:8080/musics?id=${id}`).then(()=>{
			console.log("已删除");
			ElMessage({
				message:"已删除",
				type:'success'
			})
			getData()
		}
		).catch(()=>console.log("删除失败"))
	}

	//获取歌曲列表
	function getData(){
		axios.get("http://localhost:8080/musics").then(
			response=>formData.value=response.data.data
		).catch(()=>console.log("失败"))
	}

	//添加新歌曲
	function NewData(postForm){
		postForm.name=name.value;
		postForm.singer=singer.value;
		axios.post(`http://localhost:8080/musics`,postForm).then(()=>{
				console.log("成功添加");
				getData();
				isOpen.value=false;
				ElMessage({
					message:"添加成功",
					type:'success'
				})
				name.value='';
				singer.value='';
			}
		).catch((error)=>console.log("添加失败"+error))
	}

	//修改现有歌曲
	function modifyData(){
		axios.put("http://localhost:8080/musics")
	}

	onMounted(()=>getData());

</script>


<template>
	<h1>音乐列表</h1>
			<el-menu default-active="2" class="el-menu-vertical-demo">
				<el-sub-menu index="1">
					<template #title>
						<span>Navigator One</span>
					</template>
					<el-menu-item-group title="Group One">
						<el-menu-item index="1-1">item one</el-menu-item>
						<el-menu-item index="1-2">item two</el-menu-item>
					</el-menu-item-group>
					<el-menu-item-group title="Group Two">
						<el-menu-item index="1-3">item three</el-menu-item>
					</el-menu-item-group>
					<el-sub-menu index="1-4">
						<template #title>item four</template>
						<el-menu-item index="1-4-1">item one</el-menu-item>
					</el-sub-menu>
				</el-sub-menu>
				<el-menu-item index="2">
					<span>Navigator Two</span>
				</el-menu-item>
				<el-menu-item index="4">

					<span>Navigator Four</span>
				</el-menu-item>
			</el-menu>

	<div class="button"><el-button type="primary" @click="isOpen=true">新增音乐</el-button></div>
	<br>
	<div class="table">
		<el-table :data="formData" border stripe style="width: 800px">
			<el-table-column align="center" label="ID" width="100">
				<template #default="scope1">
					<span>{{ scope1.$index+1}}</span>
				</template>
			</el-table-column>
			<el-table-column align="center" prop="name" label="Name" width="250"/>
			<el-table-column align="center" prop="singer" label="Singer" width="250"/>
			<el-table-column align="center" label="Operations" width="200">
				<template #default="scope">
					<el-button type="primary" size="small" @click="isOpen=true">编辑</el-button>
					<el-button type="danger" size="small" @click="deleteData(scope.row.id)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
	</div>

	<!--修改弹窗-->
	<el-dialog v-model="isOpen" title="添加" width="500">
		<el-form>
			<el-form-item label="Name">
				<el-input v-model="name"></el-input>
			</el-form-item>
		</el-form>
		<el-form>
			<el-form-item label="Singer">
				<el-input v-model="singer"></el-input>
			</el-form-item>
		</el-form>
		<template #footer>
			<div class="dialog-footer">
				<el-button @click="isOpen = false">取消</el-button>
				<el-button type="primary" @click="NewData">
					保存
				</el-button>
			</div>
		</template>
	</el-dialog>
<!--	<el-pagination background layout="prev, pager, next" :total="100" />-->
	<el-backtop :right="100" :bottom="100" />
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
.button{
	display: flex;
	justify-content: center;
}

.el-menu-vertical-demo{
	position: absolute;
	height: 100%;

}
</style>
