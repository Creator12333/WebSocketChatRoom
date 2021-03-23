<template>
    <div id="app">
        <el-form ref="form" :model="form" label-width="80px" style="margin-top:30px">
        <el-form-item label="昵称">
            <el-input v-model="form.nickName" placeholder="请输入您的昵称" :disabled="nickNameInput" style="width:200px;"></el-input>
            <el-button type="primary" style="margin-left:20px" @click="selectOk">确定</el-button>
            <el-button type="primary" @click="selectUpdate">修改</el-button>
        </el-form-item>
        <el-form-item label="消息">
            <el-input v-model="form.message" placeholder="请输入发送的消息" style="width:200px"></el-input>
            <el-button type="primary" style="margin-left:20px" @click="sendMessage">发送</el-button>
        </el-form-item>
        </el-form>

        <!-- 昵称：消息 时间 -->
        <div class="content">
            <div class="contentMessage" v-for="(item,index) in messageData" :key="index" style="width:100%">
                <span style="width:24%">{{item.nickName}}  ：</span>
                <p style="margin:0 10px;margin-right:20px;width:35%">{{item.message}}</p>
                <span style="width:41%">{{item.time}}</span>
            </div>
        </div>
    </div>
</template>

<script>
import util from '../util.js';
const ws = new WebSocket('ws://localhost:8000');
    export default {
        data () {
            return {
                form:{
                    nickName:'',
                    message:''
                },
                nickNameInput:false,
                messageData:[]
            }
        },
        mounted () {
            ws.addEventListener('open',this.handleWsOpen.bind(this),false);
            ws.addEventListener('close',this.handleWsClose.bind(this),false);
            ws.addEventListener('error',this.handleWsError.bind(this),false);
            ws.addEventListener('message',this.handleWsMessage.bind(this),false);            
            console.log(localStorage.getItem('nickName'));
            this.form.nickName = localStorage.getItem('nickName');
            if(this.form.nickName !== null) {
                this.nickNameInput = true;
            }
        },
        methods: {
            selectOk(){
                this.nickNameInput = true;
                localStorage.setItem('nickName',this.form.nickName);
            },
            selectUpdate(){
                this.nickNameInput = false;
            },
            sendMessage(){
                var arr = {};
                if(this.nickNameInput == false) {
                    this.$message({
                        type:'error',
                        message:'请先确认您的昵称'
                    })
                }else {
                    arr.nickName = this.form.nickName;
                    arr.message = this.form.message;
                    arr.time = util.formatTime(new Date());
                    // this.messageData.push(arr);
                    ws.send(JSON.stringify(arr));
                    this.form.message = '';
                }
                
            },
            handleWsOpen(e) {

            },
            handleWsClose(e) {

            },
            handleWsError(e) {

            },
            handleWsMessage(e) {
                const msg = JSON.parse(e.data);
                console.log(msg);
                this.messageData.push(msg);
            }
        }
    }
</script>

<style scoped>
.nickName,.message {
    display: flex;
    flex-direction: row;
    margin-left: 50px;
}
.nickName {
    margin-top: 30px;
}
.content {
    margin-left: 50px;
    margin-top: 30px;
    width: 400px;
    height: 900px;
    border:1px solid black
}
.content {
    display: flex;
    flex-direction: column;
    padding:20px 10px 5px 10px;
}
.contentMessage {
    display: flex;
    flex-direction: row;
    align-items: center;
}

</style>