<template>
    <div class="admin-pages">
        <PageTitle icon="fa fa-cogs" main="Administração do Sistema"
            sub="Cadastros & Cia" />
        <b-card no-body>
            <b-tabs card>
                <b-tab title="Artigos" >
                    <Article/>
                </b-tab>
                <b-tab title="Categorias" active >
                    <Category/>
                </b-tab>
                <b-tab title="Usuários" >
                    <User/>
                </b-tab>

            </b-tabs>
        </b-card>
        <div class="adimn=pages-tabs"/>
    </div>
</template>

<script>
import Article from './Article'
import Category from './Category'
import User from './User'
import PageTitle from '../template/PageTitle'
export default {
    name: 'adminPages',
    components: { PageTitle, Article,User,Category }
}
</script>

<style>

</style>
