<template>
    <div class="song-form">
        <h1>{{title}}</h1>
        <form class="form" @submit.prevent="update">
            <div class="row">
                <label>歌名</label>
                <input type="text" v-model="lists.name">
            </div>
            <div class="row">
                <label>歌手</label>
                <input type="text" v-model="lists.singer">
            </div>
            <div class="row">
                <label>封面</label>
                <input type="text" v-model="lists.cover">
            </div>
            <div class="row">
                <label>外链</label>
                <input type="text" v-model="lists.url">
            </div>
            <div class="row">
                <label>歌词</label>
                <textarea cols="60" rows="5" v-model="lists.lyric"></textarea>
            </div>
            <div class="row">
                <button type="submit">保存</button>
            </div>
        </form>
    </div>
</template>
<script>
import AV from 'leancloud-storage'
export default {
    name: 'songForm',
    data(){
        return {
            title: '新建歌曲',
            song: null,
            lists: {
                singer: '',
                cover: '',
                lyric: '',
                name: '',
                url: '',
                id: ''
            },
        }
    },
    inject: ['eventBus'],
    created(){
        console.log(1)
        this.eventBus.$on('upload',(data)=>{
            Object.assign(this.lists,data)
        })
        this.eventBus.$on('modify',(song)=>{
            this.title = '编辑歌曲'
            Object.assign(this.lists,song)
        })
        this.eventBus.$on('newSet',()=>{
            console.log(this.eventBus)
            this.title = '新建歌曲'
            this.resver()
        })
    },
    mounted(){
        console.log(2)
    },
    updated(){
        console.log(3)
    },
    methods: {
        update(){
            if(this.lists.id){
                this.modifyData()   
            }else{
                this.newCreate()  
            }
            this.saveData()
        },
        saveData(){
            for(let key in this.lists){
                this.song.set(key,this.lists[key])
            }
            this.song.save().then(data=>{
                let newSong = {...data.attributes,id:data.id}
                this.eventBus.$emit('new',JSON.parse(JSON.stringify(newSong)))
                this.resver()
            })     
        },
        newCreate(){
            var Song = AV.Object.extend('Song')
            this.song = new Song 
        },
        modifyData(){
            this.song = AV.Object.createWithoutData('Song', this.lists.id)
        },
        resver(){
            this.lists = {singer: '',cover: '',lyric: '',name: '',url: '',id: ''}
        }
    },
    
}
</script>
<style lang="scss">
    $bgColor: #d4d4d4;
    .song-form{
        text-align: left;
        background: $bgColor;
        padding-left: 40px;
        height: 100vh;
        h1{
            font-size: 24px;
            padding: 10px 0;
        }
        .row{
            padding: 10px 0;
            display: flex;
            align-items: center;
            label{
                width: 2em;
                margin-right: 5px;
            }
            input{
                line-height: 24px;
                padding: 4px;
            }
            button{
                margin-left: calc(2em + 5px);
                background: #fff;
                border: none;
                padding: 4px 10px;
                border-radius: 5px;
            }
        }
    }
</style>
