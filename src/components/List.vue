<template>
    <div>
            <div class="title">歌曲列表</div>
            <div class="item" v-for="(item, index) in musicList.music">
                {{musicList.music.name[index]}}-{{musicList.music.singer[index]}}
            </div>
    </div>
</template>
<script>
    export default{
        data(){
            return{
                apiUrl: './static/data/data.json',
                musicList: ''
            }
        },
        created(){
            this.$http.get(this.apiUrl).then(response => {
                // get body data
                response = response.body
                this.musicList = response
            })
        }
    }
</script>
