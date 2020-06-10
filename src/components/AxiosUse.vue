<template>
    <b-container fluid>

        <div>
            <b-button squared variant="outline-primary" v-on:click="getContents('/api/getFrontTails', 'frontTail')">头尾</b-button>
            <b-button squared variant="outline-success" v-on:click="getContents('/api/getStatuses', 'status')">关闭</b-button>
            <b-button squared variant="outline-primary" v-on:click="getContents('/api/getInfos', 'info')">集装箱号</b-button>
            <b-button squared variant="outline-success" v-on:click="getContents('/api/getRoofInfos', 'roofInfo')">顶号</b-button>
        </div>

        <div>
            <b-form-textarea
            id="textarea"
            v-model="text"
            placeholder="Response Content area"
            readonly
            plaintxt :value="text"
            rows="9"
            max-rows="9">
            </b-form-textarea>
            <pre class="mt-3 mb-0">调用结果展示</pre>
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
                contents:[],
                fields:[],
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
                        + this.contents[i].status + "\n" + this.contents[i].success + "\n";
                }
                this.fields = [
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
                        + this.contents[i].status + "\n" + this.contents[i].success + "\n";
                }
                this.fields = [
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
                        + this.contents[i].iso_num + "\n" + this.contents[i].message + "\n" + this.contents[i].success + "\n";
                }
                this.fields = [
                    {key:'container_num', label:'集装箱号', sortable:false},
                    {key:'iso_num', label:'序列号', sortable:true},
                    {key:'message', label:'信息', sortable:false},
                    {key:'success', label:'成功', sortable:true}
                ]
            },
            formatRoofInfoOutPut(){
                this.text="";
                this.fields = [];
                for(let i = 0; i < this.contents.length; i++) {
                    this.text = this.text + this.contents[i].message + "\n"
                        + this.contents[i].roof_num + "\n" + this.contents[i].success + "\n";
                }
                this.fields = [
                    {key:'message', label:'信息', sortable:false},
                    {key:'roofNum', label:'顶号', sortable:true},
                    {key:'success', label:'成功', sortable:true}
                ]

            },
            getContents(url, type){
                axios
                    .get(url)
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
            }
        }
    }

</script>

<style scoped>

</style>