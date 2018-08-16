# Laravel 5.5 CRUD API + User Interface with VUE 

This is a tutorial to create UI with VUE - based on previous project which is in PHP/Laravel Framework and followed on youtube. [Video][1]

[1]: https://www.youtube.com/watch?v=4pc6cgisbKE 

## How to install node modules?

npm install

## How to watch the console for node?

npm run watch

## Where to find installed node modules?

resources\assets

## How to add CSRF TOken 

<head>

<meta name="csrf-token" content="{{ csrf_token() }}">
<script>window.Laravel = { csrfToken: '{{ csrf_token() }}' }</script>

</head>

## How to create vue component

1. Go To resources\assets\js\components -> create any file, if you put component name it will be useful

For example: Articles.vue

## How to use this component?

1. Go To resources\assets\js\app.js -> and add the name of the component like

~~~~

//Those are the components created in resources/assets/js/components
Vue.component('navbar', require('./components/Navbar.vue'));
Vue.component('articles', require('./components/Articles.vue'));

@Params:/

1. component name that will be used in html file
2. component's path

//this is keyword that will required to use components
const app = new Vue({
    el: '#app'
});

~~~~~~

2. Go To resources\views\welcome.blade.php -> you can choose any php file it doesn't matter

~~~~
        
        //Here id="app" is required to use component, because in app.js it defind as #app
        <div id="app">
            <navbar></navbar> component name which has been defined in app.js
            <div class="container">
                <articles> component name which has been defined in app.js
                    
                </articles>
            </div>
        </div>
~~~~

## How to design component?

1. Go To the component Articles.js file in components folder or any other component that u created

2. Design the components like as you wish

~~~~

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


//Script automatically will run on this compomemt
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
        },

        methods: {
            fetchArticles(page_url) {
                let vm = this;
                page_url = page_url || '/api/articles'; //if there is an page_url otherwise use default one '/api/articles
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
                            this.fetchArticles();
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

~~~~~


