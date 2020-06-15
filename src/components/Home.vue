<template>
  <div class="home">
    <!-- 昵称头像 -->
    <el-form ref="parmas" label-position="top" :model="parmas" label-width="80px">
      <el-form-item label="微信昵称和头像">
        <el-row :gutter="24" class="h100">
          <el-col :span="16">
            <el-input v-model="parmas.name" placeholder="请输入昵称" />
          </el-col>
          <el-col :span="8" class="lf lf-j">
            <el-upload
              class="avatar-uploader"
              :show-file-list="false"
              action
              :before-upload="beforeAvatarUpload"
            >
              <img v-if="parmas.headImg" :src="parmas.headImg" class="avatar" />
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 发表类型1、自己相册可见头像 2、可看赞数量 3、文字赞无头绪-->
      <el-form-item label="选择类型">
        <el-row :gutter="24" class="h100">
          <el-col>
            <el-radio-group v-model="parmas.zanType" type="button">
              <el-radio-button :label="1">头像赞</el-radio-button>
              <el-radio-button :label="2">文字赞</el-radio-button>
              <el-radio-button :label="3">数量赞</el-radio-button>
            </el-radio-group>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 发表类型 1、文字+图片 2、公众号文章+文字 3、第三方链接+文字 -->
      <el-form-item label="选择类型">
        <el-row :gutter="24" class="h100">
          <el-col>
            <el-radio-group v-model="parmas.contentType" type="button">
              <el-radio-button :label="1">文字+图片</el-radio-button>
              <el-radio-button :label="2">公众号文章+文字</el-radio-button>
            </el-radio-group>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 发表内容 图文 -->
      <el-form-item label="图文" v-if="parmas.contentType === 1">
        <el-row :gutter="24">
          <el-col>
            <el-input
              type="textarea"
              :autosize="{ minRows: 4, maxRows: 20}"
              placeholder="请输入内容"
              v-model="parmas.content"
            ></el-input>
          </el-col>
        </el-row>
        <el-row :gutter="24">
          <el-col :span="16">
            <div class="pic">
              <div class="imgPic" v-for="(list, index) in parmas.imgList" :key="index">
                <el-image shape="square" :size="100" fit="cover" :src="list"></el-image>
                <svg
                  t="1591252056691"
                  class="delImg"
                  @click="delImg(index)"
                  viewBox="0 0 1024 1024"
                  version="1.1"
                  xmlns="http://www.w3.org/2000/svg"
                  p-id="3181"
                  width="800"
                  height="800"
                >
                  <path
                    d="M512 32C246.4 32 32 246.4 32 512s214.4 480 480 480 480-214.4 480-480S777.6 32 512 32m201.6 681.6c-12.8 12.8-35.2 12.8-48 0L512 560l-153.6 153.6c-12.8 12.8-35.2 12.8-48 0-12.8-12.8-12.8-35.2 0-48l153.6-153.6-153.6-153.6c-12.8-12.8-12.8-35.2 0-48 12.8-12.8 35.2-12.8 48 0l153.6 153.6 153.6-153.6c12.8-12.8 35.2-12.8 48 0 12.8 12.8 12.8 35.2 0 48L560 512l153.6 153.6c16 12.8 16 35.2 0 48m0 0z"
                    fill="#F56C6C"
                    p-id="3182"
                  />
                </svg>
              </div>
              <el-upload
                class="avatar-uploader"
                v-if="parmas.imgList.length < 9"
                :show-file-list="false"
                action
                :before-upload="uploadImgList"
              >
                <i class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </div>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 转发文章 -->
      <el-form-item label="转发文章" v-if="parmas.contentType === 2">
        <el-row :gutter="24" class="mb-20">
          <el-col>
            <el-input
              type="textarea"
              :autosize="{ minRows: 4, maxRows: 20}"
              placeholder="请输入内容(可为空)"
              v-model="parmas.link.text"
            ></el-input>
          </el-col>
        </el-row>
        <el-row class="mb-20">
          <el-radio-group v-model="parmas.linkType" type="button">
            <el-radio-button :label="1">微信链接</el-radio-button>
            <el-radio-button :label="2">自定义链接</el-radio-button>
          </el-radio-group>
        </el-row>
        <el-row class="mb-20" v-if="parmas.linkType === 1">
          <el-col>
            <el-input type="text" placeholder="请输入微信文章链接" v-model="parmas.link.url"></el-input>
          </el-col>
        </el-row>
        <el-row :gutter="24" class="mb-20" v-if="parmas.linkType === 2">
          <el-col :span="16">
            <el-input v-model="parmas.link.linkText" placeholder="请输入自定义链接文字" />
          </el-col>
          <el-col :span="8" class="lf lf-j">
            <el-upload
              class="avatar-uploader"
              :show-file-list="false"
              action
              :before-upload="linkIcon"
            >
              <img v-if="parmas.link.linkImg" :src="parmas.link.linkImg" class="avatar" />
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 位置 + 时间 -->
      <el-form-item label="位置+时间">
        <el-col :span="8" class="mr-20">
          <el-input type="text" placeholder="请输入位置" v-model="parmas.location"></el-input>
        </el-col>
        <el-col :span="8">
          <el-date-picker v-model="parmas.time" type="datetime" placeholder="选择日期时间"></el-date-picker>
        </el-col>
      </el-form-item>
      <!-- 集赞数量 -->
      <el-form-item label="集赞数量">
        <el-input-number
          v-model="parmas.zanNum"
          @change="handleChange"
          :min="1"
          :max="1000"
          label="请输入集赞数量"
        ></el-input-number>
      </el-form-item>
      <!-- 评论模块 -->
      <!-- 是否评论 -->
      <el-form-item label="是否评论">
        <el-switch v-model="parmas.isCommit" active-text="是" inactive-text="否"></el-switch>
      </el-form-item>
      <el-form-item label="评论设置" v-if="parmas.isCommit">
        <div v-for="(list, index) in parmas.commit" :key="index">
          <el-row class="lf lf-j-a mb-10">
            <el-col :span="18" class="mr-20">
              <el-input v-model="list.name" placeholder="请输入昵称" />
            </el-col>
            <el-col :span="6">
              <el-date-picker v-model="list.time" type="datetime" placeholder="请输入评论时间"></el-date-picker>
              <el-button type="danger" icon="el-icon-delete" circle class="ml-20" @click="delCommit(index)"></el-button>
            </el-col>
          </el-row>
          <el-row class="lf lf-j-a mb-10">
            <el-col :span="18" class="mr-20">
              <el-input
                v-model="list.text"
                type="textarea"
                :autosize="{ minRows: 4, maxRows: 20}"
                class="mb-10"
                placeholder="请输入评论内容"
              />
            </el-col>
            <el-col :span="6">
              <el-upload
                class="avatar-uploader"
                :show-file-list="false"
                action
                :before-upload="upCommitAvator(index)"
              >
                <img v-if="list.avator" :src="parmas.link.linkImg" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-col>
          </el-row>
        </div>
        <el-button type="primary" round style="width: 100%" class="mt-15" @click="addCommit">添加评论</el-button>
      </el-form-item>
      <!-- 截图时间 -->
      <!-- 生成 -->
    </el-form>
  </div>
</template>

<script>
export default {
  name: "home",
  data() {
    return {
      parmas: {
        name: "",
        headImg: "",
        zanType: 1,
        contentType: 1,
        content: "",
        imgList: [],
        linkType: 1,
        link: {
          url: "",
          text: "",
          linkImg: "",
          linkText: ""
        },
        location: "",
        time: "",
        zanNum: 1,
        isCommit: false,
        commit: [
          {
            avator: "",
            name: "",
            text: "",
            time: ""
          }
        ]
      }
    };
  },
  methods: {
    beforeAvatarUpload(file) {
      let _this = this;
      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function() {
        _this.parmas.headImg = this.result;
      };
    },
    uploadImgList(file) {
      let _this = this;
      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function() {
        _this.parmas.imgList.push(this.result);
      };
    },
    delImg(index) {
      this.parmas.imgList.splice(index, 1);
    },
    linkIcon(file) {
      let _this = this;
      let reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = function() {
        _this.parmas.link.linkImg = this.result;
      };
    },
    handleChange(value) {
      this.parmas.zanNum = value;
    },
    // 添加评论
    addCommit() {
      let parmas = {
        avator: "",
        name: "",
        text: "",
        time: ""
      };
      this.parmas.commit.push(parmas)
    },
    // 删除评论
    delCommit(index) {
      this.parmas.commit.splice(index, 1)
      if (this.parmas.commit.length === 0) {
        this.parmas.isCommit = false
      }
    },
    // 评论头像
    upCommitAvator(index) {
      console.log(index);
    }
  }
};
</script>
<style>
.home {
  min-height: 500px;
}
.avatar-uploader {
  height: 100px;
  width: 100px;
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
.pic {
  display: flex;
  flex-wrap: wrap;
  padding: 20px 0;
}
.imgPic {
  position: relative;
  width: 100px;
  height: 100px;
  margin: 0 15px 15px 0;
  box-sizing: border-box;
  border: 1px solid #e8eaec;
}
.imgPic img,
.imgPic .el-image {
  width: 100%;
  height: 100%;
}
.imgPic .delImg {
  position: absolute;
  cursor: pointer;
  right: -10px;
  top: -10px;
  width: 20px;
  height: 20px;
}
</style>
