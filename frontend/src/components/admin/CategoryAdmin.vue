<template>
	<div class="category-admin">
		<b-form class="m-3">
			<input type="hidden" id="category-id" v-model="category.id">
			<b-form-group label="Nome" label-for="category-name">
				<b-form-input id="category.name" type="text" v-model="category.name" required placeholder="Informe o nome da categoria">
				</b-form-input>
			</b-form-group>

			<b-form-group label="Categoria Pai"  label-for="category-parent">
				<b-form-select id="category-parent" :disabled="mode==='remove'" :options="categories" v-model="category.parentId">
				</b-form-select>
			</b-form-group>

			<b-button v-if="mode==='save'" class="mr-2" variant="primary" @click="save">Salvar</b-button>
			<b-button class="mr-2" variant="danger" v-if="mode === 'remove'"@click="remove">Excluir</b-button>
			<b-button @click="reset">Cancelar</b-button>
		</b-form>
		<hr>
		<b-table hover striped :items="categories" :fields="fields">
			<template slot="actions" slot-scope="data">
				<b-button variant="warning" @click="loadCategory(data.item)" class="btn-sm">
					<i class="fa fa-pencil"></i>
				</b-button>
				<b-button variant="danger" @click="loadCategory(data.item,'remove')" class="ml-2 btn-sm">
					<i class="fa fa-trash"></i>
				</b-button>
			</template>	
		</b-table>
	</div>
</template>

<script>

import axios from 'axios'
import {baseApiUrl, showError} from '@/global'

export default {
	name:"CategoryAdmin",
	props:{},
	data(){
		return {
			category:{},
			mode:'save',
			categories:[],
			fields:[
			{key:'id', label:'Código', sortable:true},
			{key:'name', label:'Nome', sortable:true},
			{key:'path', label:'Categoria Pai', sortable:true},
			{key:'actions',label:'Açoes'}
			]

		}
	},
	methods:{
		loadCategories(){
			const url = `${baseApiUrl}/categories`
			axios.get(url).then(res =>{
				this.categories = res.data.map(category => {
					return {...category,value:category.id,text:category.path}
				})
			}) 

		},
		save(){
			const method = this.category.id ? 'put' :'post'
			const id = this.category.id? `/${this.category.id}`:''
			axios[method](`${baseApiUrl}/categories${id}`,this.category)
			.then(()=>{
				this.$toasted.global.defaultSuccess()
				this.reset()
			})
			.catch(showError)
		},
		remove(){
			const id = this.category.id
			axios.delete(`${baseApiUrl}/categories/${id}`)
			.then(()=>{
				this.$toasted.global.defaultSuccess()
				this.reset()
			})
			.catch(showError)
		},
		reset(){
			this.mode = 'save'
			this.category = {}
			this.loadCategories()
		},
		loadCategory(category,mode="save"){
			this.mode = mode
			this.category = {...category}
		}
	},
	mounted(){
		this.loadCategories()
	}

	
}
</script>

<style>

</style>