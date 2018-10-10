<template>
    <div>
        <nv-head page-type="主题"
            :show-menu="false"
            :fix-head="true"></nv-head>
        <div class="add-container">
            <div class="line">选择版块：
                <select class="add-tab" v-model="topic.tab">
                    <option value="electric">电力资源</option>
                    <option value="pool">矿池</option>
                </select>
               
                <a class="add-btn" @click="addTopic">发布</a>
            </div>
            <div class="line">
                发布性质：
                <select class="add-tab" v-model="topic.business">
                    <option value="出售">出售</option>
                    <option value="求购">求购</option>
                </select>  
            </div>
            <div class="line">
                &nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp城市：
                <select class="add-tab" v-model="topic.city">
                    <option value="北京">北京</option>
                    <option value="深圳">深圳</option>
                    <option value="成都">成都</option>
                </select>  
            </div>
            <div class="line">
                电力性质：
                <select class="add-tab" v-model="topic.electricity">
                    <option value="国电">国电</option>
                    <option value="地方电">地方电</option>
                    <option value="直供电">直供电</option>
                </select>  
            </div>
            <div class="line">
                能源性质：
                <select class="add-tab" v-model="topic.energy">
                    <option value="火电">火电</option>
                    <option value="水电">水电</option>
                    <option value="新能源">新能源</option>
                </select>  
            </div>
            <div class="line">
                <input class="add-title" v-model="topic.title"
                        type="text" :class="{'err':err=='title'}"
                        placeholder="标题，字数10字以上" max-length="100"/>
            </div>
 <!--            <textarea v-model="topic.content" rows="35" class="add-content"
                :class="{'err':err=='content'}"
                placeholder='回复支持Markdown语法,请注意标记代码'>
            </textarea> -->
            <quill-editor   v-model="topic.content" 
                            rows="35" 
                            class="add-content" 
                            :class="{'err':err=='content'}"
                            :options="editorOption">
		    </quill-editor>
        </div>
    </div>
</template>

<script>
    import $ from 'webpack-zepto';
    import utils from '../libs/utils.js';
    import nvHead from '../components/header.vue';
    import {
        mapGetters
    } from 'vuex';

    export default {
        data() {
            return {
                topic: {
                    tab: 'electric',
                    business: '',
                    city: '',
                    electricity: '',
                    energy: '',
                    title: '',
                    content: ''
                },
                err: '',
                authorTxt: '<br/>',
                editorOption: {
                    theme: 'snow',
                    placeholder: '输入任何内容，支持html'
                }
            };
        },
        computed: {
            ...mapGetters({
                userInfo: 'getUserInfo'
            })
        },
        methods: {
            addTopic() {
                let title = $.trim(this.topic.title);
                let contents = $.trim(this.topic.content);

                if (!title || title.length < 10) {
                    this.err = 'title';
                    return false;
                }
                if (!contents) {
                    this.err = 'content';
                    return false;
                }

                let postData = {
                    ...this.topic,
                    content: this.topic.content + this.authorTxt,
                    accesstoken: this.userInfo.token
                };
                $.ajax({
                    type: 'POST',
                    url: utils.BE_URL + '/topics',
                    data: postData,
                    dataType: 'json',
                    success: (res) => {
                        if (res.success) {
                            this.$router.push({
                                name: 'list'
                            });
                        }
                    },
                    error: (res) => {
                        let error = JSON.parse(res.responseText);
                        this.$alert(error.error_msg);
                        return false;
                    }
                });
            }
        },
        components: {
            nvHead
        }
    };
</script>

<style lang="scss">

    .add-container {
        margin-top: 50px;
        background-color: #fff;
        .line {
            padding: 10px 15px;
            border-bottom: solid 1px #d4d4d4;
            .add-btn {
                color: #fff;
                background-color: #80bd01;
                padding: 5px 15px;
                border-radius: 5px;
            }
            .add-tab {
                display: inline-block;
                width: calc(100% - 140px);
                min-width: 50%;
                font-size: 16px;
                background: transparent;
                 :after {
                    content: 'xe60e';
                }
                ;
            }
            .add-title {
                font-size: 16px;
                border: none;
                width: 100%;
                background: transparent;
                height: 25px;
            }
            .err {
                border: solid 1px red;
            }
        }
        .add-content {
            margin: 15px 2%;
            width: 96%;
            height: 10rem;
            border-color: #d4d4d4;
            color: #000;
        }
        .err {
            border: solid 1px red;
        }
    }
</style>