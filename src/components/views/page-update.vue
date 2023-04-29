<template>
    <div class="page">

        <el-card shadow="never" class="vel_card_override">

            <el-upload
                    action=""
                    accept=".jpg, .png"
                    :limit="1"
                    :auto-upload="false"
                    :on-change="getFile"
                    :on-exceed="handleExceed"
                    :before-remove="handleRemove"
            >
                <el-button type="primary" style="margin-left: 10px;">选择图片</el-button>
            </el-upload>
            <div
                    v-if="imgUrl"
            >
                <el-image
                        :src="imgUrl"
                        fit="fill"
                        :preview-src-list="srcList"
                        style="width: 600px; height: 400px; margin-left: 10px;"
                ></el-image>
                <el-button type="success" @click="submitUpload" style="margin-left: 200px;margin-top: 10px">
                    开始检索
                </el-button>
            </div>

        </el-card>
    </div>
</template>

<script>
import {ElMessage} from 'element-plus'
import {reqSendImg} from "/src/api"

export default {
    data() {
        return {
            base64Image: "",
            imgUrl: '',
            srcList: [],
        };
    },

    methods: {
        // 再次上传图片
        handleExceed(file) {
            ElMessage({
                message: '只能上传一张图片~',
                type: 'warning',
            })
        },
        handleRemove(file) {
            this.base64Image = '';
            this.imgUrl = '';
            this.srcList = [];
            ElMessage({
                message: '删除成功~',
                type: 'success',
            })
        },
        // 将图片转换为base64格式
        getBase64(file) {
            return new Promise(function (resolve, reject) {
                const reader = new FileReader();
                let imgResult = "";
                reader.readAsDataURL(file);

                reader.onload = function () {
                    imgResult = reader.result;

                };
                reader.onerror = function (error) {
                    reject(error);
                };
                reader.onloadend = function () {
                    resolve(imgResult);
                };
            });
        },
        // 获取已上传图片的信息
        getFile(file) {
            this.getBase64(file.raw).then((res) => {
                const params = res.split(",");

                if (params.length > 0) {
                    this.base64Image = params[1];
                    // this.imgUrl = ''
                    this.imgUrl = 'data:image/jpeg;base64,' + params[1];
                    this.srcList.push('data:image/jpeg;base64,' + params[1]);
                    // 发送给后端
                }
            });
        },
        // 上传图片
        submitUpload() {

            reqSendImg(this.base64Image).then((response) => {
                let data = response.data;
                console.log(response);
                console.log(data)
            })
        },
    },
};
</script>
<style scoped>
.page {
    padding: 20px;
}




.vel_card_override {
    height: calc(100vh - 90px - 20px - 20px - 2px);
}

</style>