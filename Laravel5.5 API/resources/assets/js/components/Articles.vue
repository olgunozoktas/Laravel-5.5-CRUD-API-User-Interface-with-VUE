<template>
	<div>
		<h2>Articles</h2>
		<!-- prevent to submit a file -->
		<form @submit.prevent="addArticle" action="" class="mb-3">
			<div class="form-group">
			<input type="text" class="form-control" placeholder="Title" v-model="article.title">
			</div>
			<div class="form-group">
				<textarea class="form-control" placeholder="Body" v-model="article.body"></textarea>
			</div>
			<button type="submit" class="btn btn-success">Save</button>
		</form>
		<nav aria-label="Page navigation example">
			<ul class="pagination">
				<!-- if there is not previous_page_url will be disabled -->
				<li v-bind:class="[{disabled: !pagination.prev_page_url}]" class="page-item">
					<a class="page-link" href="#" @click="fetchArticles(pagination.prev_page_url)">Previous</a></li>

				<li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{pagination.current_page}} of {{pagination.last_page}}</a></li>

				<li v-bind:class="[{disabled: !pagination.next_page_url}]" class="page-item">
					<a class="page-link" href="#" @click="fetchArticles(pagination.next_page_url)">Next</a></li>
			</ul>
		</nav>
		<div class="card card-body mb-2" v-for="article in articles" v-bind:key="article.id">
			<h3>{{article.title}}</h3>
			<p>{{article.body}}</p>
			<hr>
			<div>
			<button @click="editArticle(article)" class="btn btn-warning">Edit</button>
			<button @click="deleteArticle(article.id)" class="btn btn-danger">Delete</button>
			</div>
		</div>
	</div>
</template>

<script>
    export default {
        data() {
            return {
                articles: [], // will be used to list
                article: { //will be used to post or edit
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
            console.log(this.article_id)
            console.log(this.edit)
        },

        methods: {
            fetchArticles(page_url) {
                let vm = this;
                page_url = page_url || '/api/articles';
                console.log("Page Url  is: " + page_url);
                fetch(page_url)
                    .then(res => res.json())
                    .then(res => {
                        this.articles = res.data;
                        //res.meta, res.links in the response
                        vm.makePagination(res.meta, res.links);
                    })
					.catch(err => console.log(err));
            },
			makePagination(meta,links){
                let pagination = {
                    current_page: meta.current_page,
					last_page: meta.last_page,
					next_page_url: links.next,
					prev_page_url: links.prev
				};

				this.pagination = pagination;
			},
			deleteArticle(id) {
                if(confirm('Are You Sure?')) {
                    fetch(`api/article/${id}`, {
                        method: 'delete'
					})
						.then(res => res.json())
						.then(data => {
						    alert('Article Removed');
						    this.fetchArticles();
						})
						.catch(err => console.log(err));
				}
			},
			addArticle() {
                //if edit false -> call store post
				//else -> call store put
                if(this.edit === false){
                    //Add
					fetch('api/article', {
					    method: 'post',
						body: JSON.stringify(this.article),
						headers: {
					        'content-type': 'application/json'
						}
					})
						.then(res => res.json())
						.then(data => {
                            this.article.title = '';
                            this.article.body = '';
                            alert('Article Added');
                            this.fetchArticles();
						})
						.catch(err => console.log(err))
				}else{
                    //Update
                    fetch('api/article', {
                        method: 'put',
                        body: JSON.stringify(this.article),
                        headers: {
                            'content-type': 'application/json'
                        }
                    })
                        .then(res => res.json())
                        .then(data => {
                            this.article.title = '';
                            this.article.body = '';
                            alert('Article Updated');
                            console.log(this.edit)
                            this.fetchArticles();
                            console.log(this.edit)
                        })
                        .catch(err => console.log(err))
				}
			},
			editArticle(article){
                this.edit = true;
                this.article.id = article.id;
                this.article.article_id = article.id;
                this.article.title = article.title;
                this.article.body = article.body;
			}
        }
    }
</script>