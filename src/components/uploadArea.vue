<template>
    <div class="upload-area">
        <div id="uploadContainer" ref="b" class="draggable">
            <div id="uploadButton" ref="a" class="clickable">
                <p>点击或拖曳文件</p>
                <p>文件大小不能超过40MB</p>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: 'uploadArea',
    data(){
        return {

        }
    },
    created(){
        
    },
    inject:['eventBus'],
    methods: {
        init(){
            var uploader = Qiniu.uploader({
                runtimes: 'html5',    //上传模式,依次退化
                browse_button: this.$refs.a,       //上传选择的点选按钮，**必需**
                uptoken_url: 'http://localhost:8888/uptoken',            //Ajax请求upToken的Url，**强烈建议设置**（服务端提供）
                domain: 'http://plxkbr3rx.bkt.clouddn.com/',   //bucket 域名，下载资源时用到，**必需**
                get_new_uptoken: false,  //设置上传文件的时候是否每次都重新获取新的token
                max_file_size: '40mb',           //最大文件体积限制
                dragdrop: true,                   //开启可拖曳上传
                drop_element: this.$refs.b,        //拖曳上传区域元素的ID，拖曳文件或文件夹后可触发上传
                auto_start: true,                 //选择文件后自动上传，若关闭需要自己绑定事件触发上传
                init: {
                    'FilesAdded': function(up, files) {
                        plupload.each(files, function(file) {
                            // 文件添加进队列后,处理相关的事情
                        });
                    },
                    'BeforeUpload': (up, file)=> {
                            // 每个文件上传前,处理相关的事情
                            
                    },
                    'UploadProgress': (up, file)=> {
                            // 每个文件上传时,处理相关的事情
                            
                    },
                    'FileUploaded': (up, file, info)=> {
                    
                            // 每个文件上传成功后,处理相关的事情
                            // 其中 info.response 是文件上传成功后，服务端返回的json，形式如
                            // {
                            //    "hash": "Fh8xVqod2MQ1mocfI4S4KpRL6D98",
                            //    "key": "gogopher.jpg"
                            //  }
                            // 参考http://developer.qiniu.com/docs/v6/api/overview/up/response/simple-response.html
                            let {key} = JSON.parse(info.response)
                            let domain = up.getOption('domain')
                            let newArr = key.split('-').map(list=>{
                                return list.trim()
                            })
                            console.log(this)
                            let singer = newArr[0]
                            let name =newArr[1].split('.')[0]
                            let url = domain + encodeURIComponent(key)
                            let data ={name,singer,url}
                            this.eventBus.$emit('upload',JSON.parse(JSON.stringify(data)))
                    },
                    'Error': function(up, err, errTip) {
                            //上传出错时,处理相关的事情
                    },
                    'UploadComplete': function() {
                            //队列文件处理完毕后,处理相关的事情
                    },
                }
            });
        }
    },
    mounted(){
        this.init()
    }
}
</script>
<style lang="scss" scoped>
.draggable {
    padding: 20px;
    border-radius: 4px;
    border: 2px dashed #ddd;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 200px;
    flex-direction: column;
    text-align: center;
}
.clickable {
    cursor: pointer;
}
</style>
