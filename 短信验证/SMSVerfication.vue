<template>
    <el-dialog v-model="dialogFormVisible" title="短信验证" @close="handler.data?.onCancel()">
        <el-form class="elform" label-position="right" label-width="80px" :model="smsFrom">
            <el-form-item label="手机号">
                    <el-input v-model="smsFrom.phone" disabled>
                        <template #prepend>+86</template>
                    </el-input>
                </el-form-item>
            <el-form-item label="验证码">
                <el-input v-model="smsFrom.smscode">
                    <template #append><el-button type="primary" @click="onClickSendSms">发送验证码{{ smsFrom.reamin == 0 ? '' : `(${smsFrom.reamin})` }}</el-button></template>
                </el-input>
            </el-form-item>
        </el-form>
        <template #footer>
            <span class="dialog-footer">
                <el-button @click="handler.closeDialog()">取消</el-button>
                <el-button type="primary" @click="handler.data?.onConfirm(smsFrom.smscode)">
                    确定
                </el-button>
            </span>
        </template>
    </el-dialog>
</template>


<script lang="ts" setup>
import { ElMessage } from 'element-plus';
import { reactive, ref } from 'vue'
const dialogFormVisible = ref(false);
const formLabelWidth = '140px';

let smsFrom = reactive({
    phone:'',
    smscode: '',
    reamin:0
});

let onClickSendSms = () => {
    if(smsFrom.reamin != 0){
        ElMessage({
            type:'warning',
            message:`请等待${smsFrom.reamin}秒后再重试`
        });
        return;
    }
    handler.data?.onSendSMSClick()
}

type RequestData = {
    phone: string;
    onConfirm: (smscode:string) => void;
    onCancel:() => void;
    onSendSMSClick:() => void;
}
class Handler{

    public data?:RequestData;

    constructor(){
        
        setInterval(() => {
            smsFrom.reamin--;
            if(smsFrom.reamin < 0){
                smsFrom.reamin = 0
            }
        },1000);

    }

    openDialog(){
        dialogFormVisible.value = true;

        smsFrom.phone = this.data?.phone || '';
    }

    setSendSMSReamin(sec:number){
        smsFrom.reamin = sec;
    }

    closeDialog(){
        dialogFormVisible.value = false;
        this.cleanDialog();
    }

    cleanDialog(){
        this.data = undefined;
    }

    request(data:RequestData){
        this.data = data;
        this.openDialog();
        smsFrom.phone = data.phone
    }


}

let handler = new Handler();


defineExpose({
    handler
})

</script>

<style scoped></style>