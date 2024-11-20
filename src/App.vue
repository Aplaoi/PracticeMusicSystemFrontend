<script setup>
	import {ref,onMounted} from "vue";
	import axios from "axios";
	import {ElMessage} from "element-plus";

	const formData=ref(null);
	const isNewOpen=ref(false);
	const isModifyOpen=ref(false);

	const name=ref('');
	const singer=ref('');

	const modifiedName=ref('');
	const modifiedSinger=ref('');
	const modifiedID=ref(0);

	//删除music
	function deleteData(id){
		axios.delete(`http://localhost:8080/musics?id=${id}`).then(()=>{
			console.log("已删除");
			ElMessage({
				message:"已删除",
				type:'success'
			})
			getAllData()
		}
		).catch(()=>console.log("删除失败"))
	}

	//获取歌曲列表
	function getAllData(){
		axios.get("http://localhost:8080/musics").then(
			response=>formData.value=response.data.data
		).catch(()=>console.log("失败"))
	}

	//添加新歌曲
	function newData(postForm){
		postForm.name=name.value;
		postForm.singer=singer.value;
		axios.post(`http://localhost:8080/musics`,postForm).then(()=>{
				console.log("成功添加");
				getAllData();
				isNewOpen.value=false;
				ElMessage({
					message:"添加成功",
					type:'success'
				})
				name.value='';
				singer.value='';
			}
		).catch((error)=>console.log("添加失败"+error))
	}

	//获取对应id的信息
	function getDataById(id){
		axios.get(`http://localhost:8080/musics/${id}`).then((response)=>{
			console.log(response);
			modifiedName.value=response.data.data.name;
			modifiedSinger.value=response.data.data.singer;
		}).catch((err)=>console.log("失败"+err))
	}

	//修改现有歌曲
	function modifyData(id){
		let putForm={
			id:id,
			name:modifiedName.value,
			singer:modifiedSinger.value
		}
		axios.put("http://localhost:8080/musics",putForm).then(()=>{
			ElMessage({
				message:"修改成功",
				type:"success"
			});
			getAllData();
			isModifyOpen.value=false;
		}).catch(()=>ElMessage({message:"失败",type:"error"}))
	}

	onMounted(()=>getAllData());

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

	<div class="button"><el-button type="primary" @click="isNewOpen=true">新增音乐</el-button></div>
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
					<el-button type="primary" size="small"
							   @click="isModifyOpen=true;
							   modifiedID=scope.row.id;
								getDataById(modifiedID)">编辑</el-button>
					<el-button type="danger" size="small" @click="deleteData(scope.row.id)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
	</div>

	<!--添加弹窗-->
	<el-dialog v-model="isNewOpen" title="添加" width="500">
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
				<el-button @click="isNewOpen = false">取消</el-button>
				<el-button type="primary" @click="newData">
					保存
				</el-button>
			</div>
		</template>
	</el-dialog>

	<!--	修改弹窗-->
	<el-dialog v-model="isModifyOpen" title="修改" width="500">
		<el-form>
			<el-form-item label="Name">
				<el-input v-model="modifiedName"></el-input>
			</el-form-item>
		</el-form>
		<el-form>
			<el-form-item label="Singer">
				<el-input v-model="modifiedSinger"></el-input>
			</el-form-item>
		</el-form>
		<template #footer>
			<div class="dialog-footer">
				<el-button @click="isModifyOpen = false">取消</el-button>
				<el-button type="primary" @click="modifyData(modifiedID)">
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
