<template>
    <b-container fluid>
        <div>
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

        <div>
            <b-button squared variant="outline-primary" press=true v-on:click="getContents('/api/getFrontTails', 'frontTail', 0)">头尾</b-button>
            <b-button squared variant="outline-success" press='selecteds[1]' v-on:click="getContents('/api/getStatuses', 'status', 1)">关闭</b-button>
            <b-button squared variant="outline-primary" press='selecteds[2]' v-on:click="getContents('/api/getInfos', 'info', 2)">集装箱号</b-button>
            <b-button squared variant="outline-success" press='selecteds[3]' v-on:click="getContents('/api/getRoofInfos', 'roofInfo', 3)">顶号</b-button>

            <b-button squared variant="outline-primary" press='selecteds[4]' v-on:click="getContents('/api/getFrontTailsAsync', 'frontTail', 4)">*头尾</b-button>
            <b-button squared variant="outline-success" press='selecteds[5]' v-on:click="getContents('/api/getStatusesAsync', 'status', 5)">*关闭</b-button>
            <b-button squared variant="outline-primary" press='selecteds[6]' v-on:click="getContents('/api/getInfosAsync', 'info', 6)">*集装箱号</b-button>
            <b-button squared variant="outline-success" press='selecteds[7]' v-on:click="getContents('/api/getRoofInfosAsync', 'roofInfo', 7)">*顶号</b-button>
        </div>


        <div>
            <b-form-file
                    v-model="file"
                    :state="Boolean(file)"
                    class="leftFormFile"
                    placeholder="Choose a file or drop it here..."
                    drop-placeholder="Drop file here...">
            </b-form-file>
        </div>

        <div>

            <b-form-textarea
                    id="textareaRight"
                    class="rightTextArea"
                    v-model="text"
                    placeholder="Response Content area"
                    readonly
                    plaintxt :value="text"
                    rows="9"
                    max-rows="9">
            </b-form-textarea>

        </div>

        <div>
            <div class="leftFormFileLabel">Selected file:{{file ? file.name: ''}}</div>
            <div class="rightTextAreaLabel">结果展示</div>
        </div>

        <div>
            <b-table sticky-header="500" striped hover :items="contents" :fields="fields"></b-table>
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
                imageUri:"C:Users.Public.Nwt.cache.recv.毛骁.识别图片",
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
            getContents(url, type, num){
                axios
                    .get(url + "?imageUri=" + this.imageUri)
                    .then(response => {
                        this.contents = response.data;
                        console.log("++++++++++++++" + this.contents.length + url);
                        switch(type){
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
                            default:break;
                        }
                    })
                    .catch(err => {
                        console.log(err);
                    });
                for(let i = 0; i < this.selecteds.length; i++){
                    this.selecteds[i] = false;
                }
                this.selecteds[num] = true;
            },

            searchImage(){

            }
        }
    }

</script>

<style scoped>

    .leftFormFile{
        float:left;
        width:42%;
        position:absolute;
    }

    .rightTextArea{
        float:right;
        width:47%;
    }

    /*.leftFormFileLabel{*/
    /*    float:left;*/
    /*    width:42%;*/
    /*    position:absolute;*/
    /*}*/

    /*.rightTextAreaLabel{*/
    /*    float:right;*/
    /*    width:47%;*/
    /*}*/


</style>