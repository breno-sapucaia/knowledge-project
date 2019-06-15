<template>
    <div class="article-admin">
        
        <b-form>
            <input type="hidden" v-model="article.id" id="article-id">
                <b-form-group label="Nome:" label-for="article-name">
                    <b-form-input  
                        id="article-name" 
                        type="text"
                        v-model="article.name"
                        placeholder="Informe o nome do artigo"
                        require :readonly="mode === 'remove'"/>
                </b-form-group>
                <b-form-group label="Descrição:" label-for="article-description">
                    <b-form-input  
                        id="article-description" 
                        type="text"
                        v-model="article.description"
                        placeholder="Insira a descrição do artigo"
                        require :readonly="mode === 'remove'"/>
                </b-form-group>
                <b-form-group v-if="mode === 'save'"
                label="Imagem (URL):" label-for="article-imageUrl">
                    <b-form-input  
                        id="article-imageUrl" 
                        type="text"
                        v-model="article.imageUrl"
                        placeholder="insira a URL da imagem"
                        require :readonly="mode === 'remove'"/>
                </b-form-group>
                <b-form-group v-if="mode === 'save'" label="Categoria" label-for="article-categoryId">
                    <b-form-select 
                        id="article-categoryId"
                        v-model="article.categoryId"
                        :options="categories"
                        require
                    />
                </b-form-group>
                <b-form-group v-if="mode === 'save'"
                 label="Autor" label-for="article-userId">
                    <b-form-select 
                        id="article-userId"
                        v-model="article.userId"
                        :options="users"
                        require
                    />
                </b-form-group>
                    <b-form-group v-if="mode === 'save'" label="Conteudo" label-for="article-content">
                        <VueEditor v-model="article.content" 
                        placeholder="Informe o Conteúdo do Artigo..." />
                    </b-form-group>
        
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
        <b-table hover striped :items="this.articles"  :fields="fields">
            <template slot="actions" slot-scope="data">
                <b-button variant="warning" @click="loadArticle(data.item)" class="mr-2">
                    <i class='fa fa-pencil'></i>
                </b-button>
                <b-button variant="danger" @click="loadArticle(data.item, 'remove')">
                    <i class="fa fa-trash"></i>
                </b-button>
            </template>
        </b-table>
        <b-pagination size="md" v-model="page" :total-rows="count" :per-page="limit" />
    </div>
</template>

<script>
import { VueEditor } from 'vue2-editor'
import { baseApiUrl, showError } from '@/global'
import axios from 'axios'
export default {    
    name: 'Aricle',
    components: { VueEditor },
    data: function(){
        return {
            mode: 'save',
            article: {},
            articles: [],
            categories: [],
            users:[],
            page:1,
            limit:0,
            count:0,
            fields: [
                { key: 'id', label: 'Código', sortable: true },
                { key: 'name', label: 'Nome', sortable: true},
                { key: 'description', label: 'Descrição', sortable:true },
                { key: 'actions', label: 'Ações'}
            ]
        }
    },
    methods: {
        loadArticles(){
            const url = `${baseApiUrl}/articles?page=${this.page}`
            axios.get(url).then(res =>  {
                this.articles = res.data.articles
                this.count = res.data.count
                this.limit = res.data.limit
                })    
        },
        save() {
            
            const method = this.article.id ? 'put' : 'post'
            const id = this.article.id ? `/${this.article.id}`: ''
            axios[method](`${baseApiUrl}/articles${id}`, this.article)
                .then(() => {
                    this.$toasted.global.defaultSuccess()
                    this.reset()
                })
                .catch(showError)
        },
        remove() {
            const id = this.article.id
            axios.delete(`${baseApiUrl}/articles/${id}`)
                .then(() => {
                    this.$toasted.global.defaultSuccess()
                    this.reset()
                })
                .catch(showError)
        },
        reset(){
            this.mode = 'save'
            this.article = {}
            this.loadArticles()
        },
        loadUsers(){
            const url = `${baseApiUrl}/users`
            axios.get(url).then(res => { this.users = res.data.map(user => {
                
                return user.admin === true ? { value:user.id, text: `${user.name}  -  ${user.email}`} : null
                })
            })
                
        },
        loadArticle(article, mode='save'){
            this.mode = mode;
            axios.get(`${baseApiUrl}/articles/${article.id}`)
                .then(res => this.article = res.data)
        },
        loadCategories(){
            const url = `${baseApiUrl}/categories`
            axios.get(url).then(res => {
                this.categories = res.data.map(category => {
                    return { value: category.id, text: category.path }
                })
            })
        }
    },
    watch: {
        page(){
            this.loadArticles()
        }
    },
    mounted(){
        this.loadUsers(),
        this.loadCategories(),
        this.loadArticles()
    }
}

</script>

<style>

</style>

