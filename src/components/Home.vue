<template>
  <div class="home">
    <!-- 昵称头像 -->
    <el-form ref="parmas" label-position="top" :model="parmas" label-width="80px">
      <el-form-item label="活动名称">
        <el-row :gutter="24" class="h100">
          <el-col :span="16">
            <el-input v-model="parmas.name" placeholder="请输入昵称" />
          </el-col>
          <el-col :span="8" class="lf lf-j">
            <el-upload
              class="avatar-uploader"
              action="https://jsonplaceholder.typicode.com/posts/"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
              :before-upload="beforeAvatarUpload"
            >
              <img v-if="parmas.headImg" :src="parmas.headImg" class="avatar" />
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </el-col>
        </el-row>
      </el-form-item>
    </el-form>

    <!-- 发表类型 1、文字+图片 2、公众号文章+文字 3、第三方链接+文字 -->
    <!-- 发表内容 -->
    <!-- 位置 + 时间 -->
    <!-- 展示选择 -->
    <!-- 集赞数量 -->
    <!-- 评论数量 -->
    <!-- 截图时间 -->
    <!-- 生成 -->
  </div>
</template>

<script>
export default {
  name: "home",
  data() {
    return {
      parmas: {
        name: "",
        headImg: ""
      }
    };
  },
  methods: {
    handleAvatarSuccess(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
    },
    beforeAvatarUpload(file) {
      let _this = this;
      const isJPG = file.type === "image/jpeg";
      const isLt2M = file.size / 1024 / 1024 < 2;

      if (!isJPG) {
        this.$message.error("上传头像图片只能是 JPG 格式!");
      }
      if (!isLt2M) {
        this.$message.error("上传头像图片大小不能超过 2MB!");
      }
      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function() {
        _this.parmas.headImg = this.result;
      };
      return isJPG && isLt2M;
    }
  }
};
</script>
<style>
.home {
  height: 500px;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  box-sizing: border-box;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.home .avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 100px;
  height: 100px;
  display: block;
}
</style>
