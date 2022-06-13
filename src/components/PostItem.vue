<template>
    <div class="post">
        <div>Id: {{ post.id }}</div>
        <div>Название: {{ post.title }}</div>
        <div>Описание: {{ post.body }}</div>
        <div class="post__buttons">
            <my-button @click="$emit('remove')">Удалить</my-button>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        post: {
            type: Object,
            required: true
        }
    },
}
</script>

<style scoped>
.post {
    padding: 15px;
    border: 2px solid teal;
    margin-top: 10px;
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    flex-direction: column;
}
</style>