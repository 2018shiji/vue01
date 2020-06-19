<template>
    <b-container class="layout">
        <div class="headerNavbar">
            <b-navbar toggleable="lg" type="dark" variant="success">
                <b-container>
                    <b-navbar-brand >图像识别</b-navbar-brand>
                    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>
                    <b-collapse id="nav-collapse" is-nav>
                        <!-- Right aligned nav items -->
                        <b-navbar-nav class="ml-auto">
                            <b-nav-form>
                                <b-form-input
                                        size="sm"
                                        class="mr-sm-2"
                                        placeholder="Search for a meal"
                                        v-model="imageUri"
                                ></b-form-input>
                                <b-button
                                        size="sm"
                                        class="my-2 my-sm-0"
                                        type="submit"
                                        @click.prevent="searchImage"
                                >Search</b-button>
                            </b-nav-form>
                            <b-nav-item-dropdown right>
                                <!-- Using 'button-content' slot -->
                                <template slot="button-content"><em>User</em></template>
                                <b-dropdown-item href="#">Profile</b-dropdown-item>
                                <b-dropdown-item href="#">Sign Out</b-dropdown-item>
                            </b-nav-item-dropdown>
                        </b-navbar-nav>
                    </b-collapse>
                </b-container>
            </b-navbar>
        </div>

<div class="main">
    <div class="content">
        <div class="leftPanel">
            <div class="fileBox">
                <input type="file" id="myFile" class="leftFormFile" multiple @change="handleUpload($event)">
                <label for="myFile">Choose a file</label>
            </div>
            <div class="fileInfo">
                <ul >
                    <li v-for="file in files" v-bind:key="file.name">
                        <div class="fileName filePart">{{file.name}}</div>
                    </li>
                </ul>
            </div>
            <div class="leftFormButtons">
                <b-button size="sm" squared variant="outline-primary" v-on:click="getContents('/api/getFrontTails', 'frontTail')">头尾</b-button>
                <b-button size="sm" squared variant="outline-success" v-on:click="getContents('/api/getStatuses', 'status')">关闭</b-button>
                <b-button size="sm" squared variant="outline-primary" v-on:click="getContents('/api/getInfos', 'info')">集装箱号</b-button>
                <b-button size="sm" squared variant="outline-success" v-on:click="getContents('/api/getRoofInfos', 'roofInfo')">顶号</b-button>

                <b-button size="sm" squared variant="outline-primary" v-on:click="getContents('/api/getFrontTailsAsync', 'frontTail')">*头尾</b-button>
                <b-button size="sm" squared variant="outline-success" v-on:click="getContents('/api/getStatusesAsync', 'status')">*关闭</b-button>
                <b-button size="sm" squared variant="outline-primary" v-on:click="getContents('/api/getInfosAsync', 'info')">*集装箱号</b-button>
                <b-button size="sm" squared variant="outline-success" v-on:click="getContents('/api/getRoofInfosAsync', 'roofInfo')">*顶号</b-button>
                <b-button size="sm" squared variant="outline-success" v-on:click="refresh()">清空内容</b-button>
            </div>
        </div>

        <div class="rightTextArea">
            <b-form-textarea
                    id="textareaRight"
                    v-model="text"
                    placeholder="Response Content area"
                    readonly
                    plaintxt :value="text"
                    rows="9"
                    max-rows="9">
            </b-form-textarea>
        </div>
    </div>
    <div class="bottom">
        <div>
            <div>调用结果</div>
        </div>

        <div>
            <b-table sticky-header="300" striped hover :items="contents" :fields="fields"></b-table>
        </div>
    </div>
</div>
    </b-container>
</template>

<script>
    import axios from "axios";
    export default {
        name: "axiosuse",
        data(){
            return{
                text:"",
                files:[],
                imageUri:"C:Users\\Public\\Nwt\\cache\\recv\\毛骁\\识别图片base64Test",
                contents:[],
                fields:[],
                options:[],
            };
        },


        mounted() {

        },
        methods:{

            formatFrontTailOutput(){
                this.text = "";
                this.fields = [];
                for(let i = 0; i < this.contents.length; i++) {
                    this.text = this.text + this.contents[i].message + "\n"
                        + this.contents[i].status + "\n" + this.contents[i].success + "\n" + this.contents[i].file_name + "\n";
                }
                this.fields = [
                    {key:"file_name", label:'文件名', sortable:true},
                    {key:'message', label:'信息', sortable:false},
                    {key:'status', label:'状态', sortable:true},
                    {key:'success', label:'成功', sortable:true}
                ]
            },
            formatStatusOutput(){
                this.text = "";
                this.fields = [];
                for(let i = 0; i < this.contents.length; i++){
                    this.text = this.text + this.contents[i].message + "\n"
                        + this.contents[i].status + "\n" + this.contents[i].success + "\n" + this.contents[i].file_name + "\n";
                }
                this.fields = [
                    {key:"file_name", label:'文件名', sortable:true},
                    {key:'message', label:'信息', sortable:false},
                    {key:'status', label:'状态', sortable:true},
                    {key:'success', label:'成功', sortable:true}
                ]
            },
            formatInfoOutput(){
                this.text="";
                this.fields = [];
                for(let i = 0; i < this.contents.length; i++){
                    this.text = this.text + this.contents[i].container_num + "\n"
                        + this.contents[i].iso_num + "\n" + this.contents[i].message + "\n" + this.contents[i].success + "\n" + this.contents[i].file_name + "\n";
                }
                console.log(this.text);
                this.fields = [
                    {key:"file_name", label:'文件名', sortable:true},
                    {key:'container_num', label:'集装箱号', sortable:false},
                    {key:'iso_num', label:'ISO号', sortable:true},
                    {key:'message', label:'信息', sortable:false},
                    {key:'success', label:'成功', sortable:true}
                ]
            },
            formatRoofInfoOutPut(){
                this.text="";
                this.fields = [];
                for(let i = 0; i < this.contents.length; i++) {
                    this.text = this.text + this.contents[i].message + "\n"
                        + this.contents[i].roof_num + "\n" + this.contents[i].success + "\n" + this.contents[i].file_name + "\n";
                }
                console.log(this.text)
                this.fields = [
                    {key:"file_name", label:'文件名', sortable:true},
                    {key:'message', label:'信息', sortable:false},
                    {key:'roof_num', label:'顶号', sortable:true},
                    {key:'success', label:'成功', sortable:true}
                ]

            },
            getContents(url, type){
                // axios
                //     .get(url + "?imageUri=" + encodeURI(this.imageUri))
                //     .then(response => {
                //         this.contents = response.data;
                //         console.log("--------------" + this.contents);
                //         console.log("++++++++++++++" + this.contents.length + url);
                //         switch(type){
                //             case 'frontTail':
                //                 this.formatFrontTailOutput();
                //                 break;
                //             case 'status':
                //                 this.formatStatusOutput();
                //                 break;
                //             case 'info':
                //                 this.formatInfoOutput();
                //                 break;
                //             case 'roofInfo':
                //                 this.formatRoofInfoOutPut();
                //                 break;
                //             default:break;
                //         }
                //     })
                //     .catch(err => {
                //         console.log(err);
                //     });


                // let config = {headers:{"Content-type":"multipart/form-data"}};
                let param = new FormData()
                for(let i = 0; i < this.files.length; i++) {
                    param.append('files', this.files[i]);
                }
                axios
                    .post(url, param)
                    .then(response => {
                        this.contents = response.data;
                        console.log("++++++++++++++" + this.contents.length + url);
                        console.log("--------------" + this.contents);
                        switch (type) {
                            case 'frontTail':
                                this.formatFrontTailOutput();
                                break;
                            case 'status':
                                this.formatStatusOutput();
                                break;
                            case 'info':
                                this.formatInfoOutput();
                                break;
                            case 'roofInfo':
                                this.formatRoofInfoOutPut();
                                break;
                            default:
                                break;
                        }
                    })
                    .catch(err => {
                        console.log(err);
                    })

            },
            refresh(){
                window.location.reload();
            },
            handleUpload(e){
                let tempFiles = e.target.files;
                let len = tempFiles.length;
                for (let i = 0; i < len; i++) {
                    let item = tempFiles[i];
                    console.log(item);
                    this.files.push(item);
                }
            }
        }
    }

</script>

<style scoped>
    .layout{
        width:100%;
        height:100%;
        display:flex;
        flex-direction: column;
    }
    .headerNavbar{
        flex:0 0 70px;
    }

    .main{
        flex:1;
        display:flex;
        flex-direction: column;
        overflow:auto;
    }
    .content{
        flex:1;
        display:flex;
    }
    .leftPanel{
        flex:1;
        display:flex;
        flex-direction: column;
        overflow:auto;
    }
    .fileBox,
    .fileInfo{
        flex:1;
        border:1px solid #ccc;
        padding-left:16px;
        font-size:16px;
    }
    .leftFormFile{
        width: 0.1px;
        height:0.1px;
        flex:1;
        overflow:hidden;
        opacity:0;
    }
    .fileInfo{
        flex:0 0 0px;
    }
    .leftFormButtons{
        flex:0 0 0px;
    }

    .rightTextArea{
        flex:1;
        overflow:hidden;
    }

    .bottom{
        flex:0 0 100px
    }


</style>