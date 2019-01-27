<template>
    <div class="new-song" @click="newSet" :class="{active: istrue}">
        {{title}}
    </div>
</template>
<script>
export default {
    name: 'newSong',
    data(){
        return {
            title: '新建歌曲',
            istrue: true
        }
    },
    inject: ['eventBus'],
    created(){
        this.eventBus.$on('modify',()=>{
            this.istrue = false
        })
    },
    methods: {
        newSet(){
            this.istrue = true
            this.eventBus.$emit('newSet')
        }
    }
}
</script>
<style lang="scss" scoped>
    .new-song{
        text-align: center;
        line-height: 40px;
        &.active{
            background: #d4d4d4;
        }
    }
</style>
