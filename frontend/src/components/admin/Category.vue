<template>
    <div class="category-admin">
        
        <b-form>
            <input type="hidden" v-model="category.id" id="category-id">
            <b-row>
                <b-col xs="12">
                    <b-form-group label="Nome:" label-for="category-name">
                        <b-form-input id="category-name" type="text"
                        v-model="category.name"
                        placeholder="Informe a categoria"
                        require :readonly="mode === 'remove'"/>
                    </b-form-group>
                </b-col>
            </b-row>
            <b-row >
                <b-col xs=12>
                    <b-form-group label="Categoria Pai:" label-for="category-parentId">
                        <b-form-select 
                            v-if="mode === 'save'"
                            id="category-parentId"
                            v-model="category.parentId"
                            :options="categories"
                            require
                        />
                        <b-form-input 
                            v-else
                            id="category-parentId" 
                            type="text"
                            v-model="category.path"
                            require :readonly="mode === 'remove'"/>
                    </b-form-group>
                </b-col>
            </b-row>
        </b-form>
        <b-row class="mb-3">
            <b-col xs="12">
                <b-button variant="primary" v-if="mode === 'save'"
                    @click="save" class="mr-2"> Salvar
                </b-button>
                <b-button variant="danger"
                    @click="remove" class="mr-2" v-if="mode === 'remove'">
                    Excluir
                </b-button>
                <b-button variant="secondary" @click="reset"> 
                    Cancelar
                </b-button>
            </b-col>
        </b-row>
        <b-table hover striped :items="this.categories"  :fields="fields">
            <template slot="actions" slot-scope="data">
                <b-button variant="warning" @click="loadCategory(data.item)" class="mr-2">
                    <i class='fa fa-pencil'></i>
                </b-button>
                <b-button variant="danger" @click="loadCategory(data.item, 'remove')">
                    <i class="fa fa-trash"></i>
                </b-button>
            </template>
        </b-table>
    </div>
</template>

<script>
import { baseApiUrl, showError } from '@/global'
import axios from 'axios'
export default {    
    name: 'Category',
    data: function(){
        return {
            mode: 'save',
            category: {},
            categories: [],
            fields: [
                { key: 'id', label: 'Código', sortable: true },
                { key: 'name', label: 'Nome', sortable: true},
                { key: 'path', label: 'Caminho', sortable:true },
                { key: 'actions', label: 'Ações'}
            ]
        }
    },
    methods: {
        loadCategories(){
            const url = `${baseApiUrl}/categories`
            axios.get(url)
                .then(res => {
                    this.categories = res.data.map(category => {
                        return {...category, value: category.id, text: category.path}
                    })
                    
                    
                })
                .catch(showError)
            
        },
        save() {
            
            const method = this.category.id ? 'put' : 'post'
            const id = this.category.id ? `/${this.category.id}`: ''
            axios[method](`${baseApiUrl}/categories${id}`, this.category)
                .then(() => {
                    this.$toasted.global.defaultSuccess()
                    this.reset()
                })
                .catch(showError)
        },
        remove() {
            const id = this.category.id
            axios.delete(`${baseApiUrl}/categories/${id}`)
                .then(() => {
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
        loadCategory(category, mode='save'){
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
