<template>
  <div class="test">
    <el-container>
      <el-header>前后端分离之接口测试</el-header>
      <el-main>
        <el-card>
          <div slot="header">
            <span>API测试（自身服务端）</span>
          </div>
          <el-button type="warning" @click="apiGet()">Get</el-button>
          <span v-text="apiGetResult"></span>
          <el-form :inline="true">
            <el-form-item>
              <el-input type="text" v-model="postData" placeholder="请输入信息"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="danger" @click="apiPost()">Post</el-button>
              <span v-text="apiPostResult"></span>
            </el-form-item>
          </el-form>
        </el-card>
        <br>
        <el-card>
          <div slot="header">
            <span>图灵聊天机器人（第三方服务端）</span>
          </div>
          <el-form :model="chatForm" :rules="rules" ref="chatForm">
            <el-form-item label="聊天信息" prop="content">
              <el-tooltip class="item" effect="dark" content="请输入聊天信息" placement="bottom-start">
                <el-input type="textarea" v-model="chatForm.content" placeholder="请输入聊天信息"></el-input>
              </el-tooltip>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="chat('chatForm')">发送</el-button>
              <el-button type="info" @click="reset('chatForm')">重置</el-button>
            </el-form-item>
          </el-form>

          <el-row>
            <el-card>
              <div>{{reply}}</div>
            </el-card>
          </el-row>
        </el-card>
      </el-main>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "Test",
  data() {
    return {
      chatForm: {
        content: ""
      },
      rules: {
        content: [
          { required: true, message: "请输入聊天信息", trigger: "blur" },
          {
            min: 2,
            max: 300,
            message: "长度在 1 到 300 个字符",
            trigger: "blur"
          }
        ]
      },
      reply: "",
      apiGetResult: "",
      apiPostResult: "",
      postData: ""
    };
  },
  // dist
  created() {},
  methods: {
    chat(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          console.log("valid");
          this.$http
            .post("/3rd", {
              key: "your-api-key",
              info: this.chatForm.content,
              userid: "test"
            })
            .then(res => {
              this.reply = res.data.text;
            })
            .catch(function(err) {
              console.log(err);
            });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    reset(formName) {
      this.$refs[formName].resetFields();
    },

    apiGet() {
      this.$http
        .get("/api/v1/greeting")
        .then(res => {
          this.apiGetResult = res.data;
        })
        .catch(function(err) {
          console.log(err);
        });
    },

    apiPost() {
      if (this.postData.replace(/\s/g, "").length == 0) {
        this.$message.error("Required!");
        return;
      }
      this.$http
        .post("/api/v1/greeting", { name: this.postData })
        .then(res => {
          this.apiPostResult = res.data;
        })
        .catch(function(err) {
          console.log(err);
        });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
