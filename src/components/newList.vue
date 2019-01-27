<template>
    <ul class="new-list">
        <li v-for="(list,index) in lists" :key="list.id" 
        @click="getCurrent(list,index)"
        :class="{active:index === currentIndex}"
        >
            {{list.name}}
            <span @click.stop="deleteData(list)">x</span>
        </li>
    </ul>
</template>
<script>
import AV from 'leancloud-storage'
export default {
    name: 'newList',
    data(){
        return {
            lists: [],
            currentIndex: -1,
            isTrue: false
        }
    },
    components: {AV},
    inject: ['eventBus'],
    created(){
        this.fetch().then((data)=>{
            this.lists = data.map(song=>{
                return {...song.attributes,id:song.id}
            })
        })
        this.eventBus.$on('new',(song)=>{
            this.lists.map((songlist,index)=>{
                if(songlist.id === song.id){
                    this.lists.splice(index,1,song)
                    this.isTrue = true
                }
            })
            if(!this.isTrue){
                this.lists.push(song)
            }
        })
        this.eventBus.$on('newSet',()=>{
            this.currentIndex = -1
        })
    },
    methods: {
        fetch(){
            var query = new AV.Query('Song')
            return query.find()
        },
        getCurrent(list,index){
            this.currentIndex = index
            this.eventBus.$emit('modify',JSON.parse(JSON.stringify(list)))
        },
        deleteData(list){
            var song = AV.Object.createWithoutData('Song',list.id)
            song.destroy().then(()=>{
                this.lists.map((songlist,index)=>{
                    if(songlist.id === list.id){
                        this.lists.splice(index,1)
                    }
                })
            })
        }
    }
}
</script>
<style lang="scss">
    .new-list{
        flex: 1;
        li{
            border-bottom: 1px solid #ddd;
            padding: 10px;
            &.active{
                background: #d4d4d4;
            }
            span{
                float: right;
                color: #aeabab;
                padding-left: 14px;
                cursor: pointer;
            }
        }
    }
</style>
