<template>
<div class="app">
    <h1>Страница с постами</h1>
    <my-input v-model:value="searchQuery" placeholder="Поиск..."></my-input>
    <div class="app__btns">
        <my-button @click="showDialog">Создать пост</my-button>
        <my-select 
            v-model="selectedSort" 
            :options="sortOptions"
        ></my-select>
    </div>
    <my-button @click="fetchPosts">Получить посты</my-button>
    <my-button @click="showDialog">Создать пост</my-button>
    <my-dialog v-model:show="dialogVisible">

        <PostForm @create="createPost" />

    </my-dialog>
    <PostList 
        :posts="sortedAndSearchedPosts" 
        @remove="removePost" 
        v-if="!isPostsLoading" 
    />
    <div v-else>Идет загрузка...</div>
    <div class="page__wrapper">
        <div 
            v-for="totalPage in totalPages" 
            :key="totalPage" 
            class="page" 
            :class="{'current-page': page === totalPage}"
            @click="changePage(totalPage)"
        >{{totalPage}}</div>
    </div>
</div>
</template>

<script>
    import PostForm from "@/components/PostForm";
    import PostList from "@/components/PostList";
    import axios from 'axios';
    export default {
        components: {
            PostForm, PostList
        },
        data() {
            return {
               likes: 0,
               dislikes: 0,
               posts: [],
               dialogVisible: false,
               isPostsLoading: false,
               selectedSort: '',
               searchQuery: '',
               page: 1,
               limit: 10,
               totalPages: 0,
               sortOptions: [
                {
                    value: 'title',
                    name: 'По названию'
                },
                {
                    value: 'body',
                    name: 'По содержимому'
                },
               ]
            }
        },
        methods: {
            createPost(post) {
                this.posts.push(post)
            },
            removePost(post) {
                this.posts = this.posts.filter(elem => elem.id !== post.id)
            },
            showDialog() {
                this.dialogVisible = true;
            },
            changePage(totalPage) {
                this.page = totalPage;
                // this.fetchPosts();
            },
            async fetchPosts() {
                try {
                    this.isPostsLoading = true;
                    await fetch(`https://jsonplaceholder.typicode.com/posts?_page=${this.page}_limit=${this.limit}`)
                    .then(res => {
                        this.totalPages = Math.ceil(res.headers.get('x-total-count') / this.limit); 
                        return res.json()
                    })
                    .then(data => this.posts = data)
                    // const response = await axios.get('https://jsonplaceholder.typicode.com/posts?_limit=10');
                    // this.posts = response.data;

                } catch(e) {
                    alert('Ошибка!')
                } finally {
                    this.isPostsLoading = false;
                }
            }
        },
        mounted() {
            this.fetchPosts();
        },
        computed: {
            sortedPosts() {
                return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
            },
            sortedAndSearchedPosts() {
                return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
            }
        },
        watch: {
            // selectedSort(newValue) {
            //     this.posts.sort((post1, post2) => {
            //         return post1[newValue]?.localeCompare(post2[newValue])
            //     });
            // }
            page() {
                this.fetchPosts();
            }
        }
    }
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
.app {
    padding: 20px;
}
.app__btns {
    display: flex;
    justify-content: space-between;
    padding: 20px 0px;
}
.page__wrapper {
    display: flex;
    margin-top: 15px;
}
.page {
    border: 1px solid black;
    padding: 10px;
}
.current-page {
    border: 2px solid teal;
}
</style>