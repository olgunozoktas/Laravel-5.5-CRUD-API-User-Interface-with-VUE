<template>
    <nav class="navbar navbar-expand-sm navbar-dark bg-info mb-2">
        <div class="container">
            <a href="" class="navbar-brand">Larticles</a>
        </div>
    </nav>
</template>

<!--
<script>
    export default {
        data() {
            return {
                articles: [], // all articles
                article: { //single article
                    id: '',
                    title: '',
                    body: ''
                },
                article_id: '',
                pagination: {},
                edit: false
            }
        },

        //will run automatically
        created() {
            this.fetchArticles();
        },

        methods: {
            fetchArticles() {
                fetch('api/articles')
                    .then(res => res.json())
                    .then(data => {
                        console.log(res.data);
                    })
            }
        }
    }
</script>
-->

<style scoped>

</style>