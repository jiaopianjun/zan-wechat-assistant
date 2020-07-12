<template>
  <div class="home">
    <!-- 昵称头像 -->
    <el-form ref="parmas" label-position="top" :model="parmas" label-width="80px" v-if="true">
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
              <el-button icon="el-icon-upload" v-else type="primary">微信头像</el-button>
            </el-upload>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 发表类型1、自己相册可见头像 2、可看赞数量 3、文字赞无头绪-->
      <el-form-item label="选择集赞类型">
        <el-row :gutter="24" class="h100">
          <el-col>
            <el-radio-group v-model="parmas.zanType" type="button" @change="selectZanType">
              <el-radio-button :label="1">头像赞</el-radio-button>
              <el-radio-button :label="2">文字赞</el-radio-button>
              <el-radio-button :label="3">数量赞</el-radio-button>
            </el-radio-group>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 发表类型 1、文字+图片 2、公众号文章+文字 3、第三方链接+文字 -->
      <el-form-item label="选择内容类型">
        <el-row :gutter="24" class="h100">
          <el-col>
            <el-radio-group v-model="parmas.contentType" type="button">
              <el-radio-button :label="1">文字+图片</el-radio-button>
              <el-radio-button :label="2" v-if="parmas.zanType !== 3">公众号文章+文字</el-radio-button>
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
                <el-button icon="el-icon-upload" type="primary">上传图片</el-button>
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
              <el-button v-else type="primary">网页icon</el-button>
            </el-upload>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 位置 + 时间 -->
      <el-row :gutter="24">
        <el-col :span="12">
          <el-form-item label="位置">
            <el-input type="text" placeholder="请输入位置" v-model="parmas.location"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="发布时间">
            <el-date-picker v-model="parmas.time" type="datetime" placeholder="选择日期时间"></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <!-- 集赞数量 -->
      <el-row :gutter="24">
        <el-col :span="12">
          <el-form-item label="赞数量">
            <el-input-number
              v-model="parmas.zanNum"
              @change="handleChange"
              :min="1"
              :max="1000"
              label="请输入集赞数量"
            ></el-input-number>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="评论数量" v-if="parmas.zanType === 3">
            <el-input-number
              v-model="parmas.commitNum"
              @change="handleChange"
              :min="1"
              :max="1000"
              label="请输入评论数量"
            ></el-input-number>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 评论模块 -->
      <!-- 是否评论 -->
      <el-form-item label="是否评论" v-if="parmas.zanType !== 3">
        <el-switch v-model="parmas.isCommit" active-text="是" inactive-text="否"></el-switch>
      </el-form-item>
      <el-form-item label="评论设置" v-if="parmas.isCommit">
        <div v-for="(list, index) in parmas.commit" :key="index">
          <el-row class="lf lf-j-a mb-10">
            <el-col :span="18" class="mr-20">
              <el-input v-model="list.name" placeholder="请输入昵称" />
            </el-col>
            <el-col :span="6">
              <el-date-picker v-model="list.time" type="datetime" placeholder="评论时间" class="w120"></el-date-picker>
              <el-button
                type="danger"
                icon="el-icon-delete"
                circle
                class="ml-10"
                @click="delCommit(index)"
              ></el-button>
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
                v-if="list.avator !== undefined"
                :before-upload="upCommitAvator(index)"
              >
                <img v-if="list.avator" :src="list.avator" class="avatar" />
                <el-button v-else type="primary" icon="el-icon-upload">微信头像</el-button>
              </el-upload>
            </el-col>
          </el-row>
        </div>
        <el-button
          type="primary"
          round
          style="width: 100%"
          class="mt-15"
          @click="addCommit"
          icon="el-icon-circle-plus"
        >添加评论</el-button>
      </el-form-item>
      <!-- 是否通知栏 -->
      <el-form-item label="是否有通知栏">
        <el-switch v-model="parmas.isNavbar" active-text="是" inactive-text="否"></el-switch>
      </el-form-item>
      <el-form-item label="通知栏类型" v-if="parmas.isNavbar">
        <el-row :gutter="24" class="h100">
          <el-col>
            <el-radio-group v-model="parmas.navbarType" type="button">
              <el-radio-button :label="0">安卓</el-radio-button>
              <el-radio-button :label="1">苹果</el-radio-button>
            </el-radio-group>
          </el-col>
        </el-row>
      </el-form-item>
      <!-- 生成 -->
      <el-button round style="width: 100%" type="primary" @click="screenHot">生成</el-button>
    </el-form>

    <!-- 文字点赞 -->
    <div class="cellBox" ref="imageWrapper" id="imageWrapper">
      <!-- 通知栏 -->
      <div class="phoneBar"></div>
      <!-- 朋友圈背景 -->
      <div class="momentsBg">
        <div class="bgBox">
          <img src="../assets/image/default/bg.jpeg" alt="">
        </div>
        <!-- 顶部返回 -->
        <div class="bgTop">
          <span class="back"></span>
          <span class="camera"></span>
        </div>
        <!-- 用户名字头像 -->
        <div class="userInfo">
          <span class="name">Lie</span>
          <span class="headPic">
            <img src="../assets/image/default/avator.jpg" alt="">
          </span>
        </div>
      </div>
      <!-- 朋友的新动态 -->
      <div class="newDynamic">
        <span>朋友的新动态</span>
        <img src="../assets/image/default/avator.jpg" class="newDynamicHeadPid" alt="">
      </div>
      <!-- 假的新发布的内容 -->
      <!-- 要赞的内容 -->
      <div class="dynamicList">
        <div class="content">
          <div class="userHeadPic">
            <img src="../assets/image/default/avator.jpg" alt="">
          </div>
          <div class="userContent">
            <p class="userName">Lie</p>
            <!-- 内容-图文 -->
            <div class="subBox picText">
              <div class="userText">这是文字今天下雨哈。明天下雨哈，什么时候不下雨哈。😭这是文字今天下雨哈。明天下雨哈，什么时候不下雨哈。😭这是文字今天下雨哈。明天下雨哈，什么时候不下雨哈。😭</div>
              <div class="userPic onePic">
                <img src="../assets/image/default/bg.jpeg" alt="">
              </div>
            </div>
            <!-- 内容-链接 -->
            <div class="subBox linkText" v-if="false">
              <div class="userText"></div>
              <div class="linkBox">
                <span class="linkBoxIcon"></span>
                <span class="linkBoxText">这是链接</span>
              </div>
            </div>
            <!-- 位置 -->
            <div class="site">苏州</div>
            <!-- 时间 -->
            <div class="time">
              <span>7分钟前</span>
              <span class="timeIcon"></span>
            </div>
            <!-- 赞 -->
            <div class="zanBox">
              <span class="zanIcon"></span>
              <span>Lie</span>,
            </div>
            <!-- 评论 -->
            <div class="commitBox" v-if="false">
              <div class="commitList">
                <span class="commitName">Lie:</span>
                <span>这是评论</span>
              </div>
            </div>
          </div>
        </div>

      </div>
      <!-- 假的新发布的内容 -->
    </div>
    <!-- 生成弹窗 -->
    <el-dialog title="下载" :visible.sync="dialogVisible" width="80%">
      <div class="dialog">
        <img :src="imgUrl" alt="">
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import html2canvas from "html2canvas";
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
        commitNum: 1,
        isCommit: false,
        commit: [],
        navbarType: 0, // 0 安卓 1 苹果
        isNavbar: false,
        navBar: {
          time: "",
          battery: "",
          signal: "",
          isp: "",
          bluetooth: ""
        }
      },
      imgUrl: '',
      dialogVisible: false
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
    // 选择赞类型
    selectZanType(e) {
      if (e == 3) {
        this.parmas.contentType = 1;
      }
      this.parmas.commit = [];
      this.parmas.isCommit = false;
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
      let parmas = {};
      if (this.parmas.zanType === 1) {
        parmas = {
          avator: "",
          name: "",
          text: "",
          time: ""
        };
      } else if (this.parmas.zanType === 2) {
        parmas = {
          name: "",
          text: "",
          time: ""
        };
      }
      this.parmas.commit.push(parmas);
    },
    // 删除评论
    delCommit(index) {
      this.parmas.commit.splice(index, 1);
      if (this.parmas.commit.length === 0) {
        this.parmas.isCommit = false;
      }
    },
    // 评论头像
    upCommitAvator(index) {
      console.log(index);
    },
    // 生成截图
    screenHot() {


      window.pageYOffset = 0;
      document.documentElement.scrollTop = 0
      document.body.scrollTop = 0

      var canvas2 = document.createElement("imageWrapper");
      let _canvas = document.querySelector('#imageWrapper');
      var w = parseInt(window.getComputedStyle(_canvas).width);
      var h = parseInt(window.getComputedStyle(_canvas).height);

      canvas2.style.width = w + "px";
      canvas2.style.height = h + "px";


      let _this = this;
      html2canvas(this.$refs.imageWrapper,{
        backgroundColor: null //避免图片有白色边框
    },{canvas:canvas2},{useCORS:true,logging:true}).then(canvas => {
        let dataURL = canvas.toDataURL("image/png");
        _this.imgUrl = dataURL;
        if (_this.imgUrl !== "") {
          _this.dialogVisible = true;
        }
      });
    }
  }
};
</script>

<style lang="sass" scoped>
  @import ../assets/css/zan.scss
</style>
<style>
body {
  background: #d9d9d9;
}
.home {
  min-height: 500px;
  width: 750px;
  margin: 0 auto;
  box-sizing: border-box;
  padding: 15px;
  background: #fff;
  border-radius: 4px;
}
.avatar-uploader {
  /* height: 100px;
  width: 100px; */
  /* display: flex;
  justify-content: center;
  align-items: center; */
  box-sizing: border-box;
  border-radius: 6px;
  /* border: 1px dashed #d9d9d9; */
}
.avatar-uploader .el-upload {
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
