<template>
    <div class="admin">
        <div class="left">
            <new-song></new-song>
            <new-list></new-list>
            <upload-area></upload-area>
        </div>
        <div class="right">
            <song-form></song-form>
        </div>
    </div>
</template>
<script>
import newList from './newList.vue'
import newSong from './newSong.vue'
import songForm from './songForm.vue'
import uploadArea from './uploadArea.vue'
import AV from 'leancloud-storage'
import Vue from 'vue'
export default {
    name: 'admin',
    data(){
        return {
            eventBus: new Vue()
        }
    },
    provide(){
        return {
            eventBus: this.eventBus
        }
    },
    components: {
        newList,
        newSong,
        songForm,
        uploadArea,
        AV
    },
    created(){
        this.init()
        this.createData()
    },
    methods: {
        init(){
            var APP_ID = 'YphvNGQsx0eOnnhaW85cbBHd-gzGzoHsz';
            var APP_KEY = '9MI9s8r8rlm4uAGFVFf15OOP';
            AV.init({
              appId: APP_ID,
              appKey: APP_KEY
            });
        },
        createData(){
            
        }
    }
}
</script>
<style scoped lang="scss">
.admin{
    height: 100vh;
    display: flex;
    align-items: flex-start;
    .left{
        width: 200px;
        display: flex;
        flex-direction: column;
    }
    .right{
        flex: 1;
    }
}
</style>

