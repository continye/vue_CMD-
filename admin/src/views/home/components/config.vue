<template>
  <div class="mr-10 ml-10">
    <!-- 基本设置 -->
    <el-tab-pane label="应用配置" class="mt-20" name="config">
      <el-form ref="set" :model="set" label-width="120px">
        <el-form-item prop="template" label="模板">
          <el-select v-model="set.template" placeholder="请选择模板">
            <el-option
              v-for="(item,index) in folderList"
              :key="index"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>

        <el-form-item label="上传方式">
          <el-radio-group v-model="set.uploadWay" class="ml-4">
            <el-radio value="1">普通</el-radio>
            <el-radio value="2">七牛云</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="文件缓存">
          <el-radio-group v-model="set.maxAge" class="ml-4">
            <el-radio value="1">开启</el-radio>
            <el-radio value="2">关闭</el-radio>
          </el-radio-group>

          <el-popover
            placement="top-start"
            title="静态资源缓存"
            :width="200"
            trigger="hover"
            content="css,js,img等模板静态资源缓存。建议：线上环境开启"
          >
            <template #reference>
              <el-icon class="ml-20 pointer c-165dff"
                ><QuestionFilled
              /></el-icon>
            </template>
          </el-popover>
        </el-form-item>

        <el-form-item label="数据缓存">
          <el-radio-group v-model="set.dataCache" class="ml-4">
            <el-radio value="1">开启</el-radio>
            <el-radio value="2">关闭</el-radio>
          </el-radio-group>

          <el-popover
            placement="top-start"
            title="全局模板数据缓存"
            :width="200"
            trigger="hover"
            content="站点，分类，配置，友情链接，碎片，标签等数据。建议：线上环境开启"
          >
            <template #reference>
              <el-icon class="ml-20 pointer c-165dff"
                ><QuestionFilled
              /></el-icon>
            </template>
          </el-popover>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="submit('set')">保存</el-button>
        </el-form-item>
      </el-form>
    </el-tab-pane>
  </div>
</template>

<script>
import { find, update} from "@/api/sys_config.js";
import {folder} from "@/api/sys_app.js";

export default {
  name: "ConfigSet",
  data: () => {
    return {
      loading: true,
      folderList: [],
      set: {
        template: "default",
        maxAge: "1",
        dataCache: "1",
        appid: "",
        secret: "",
        accessKey: "", //ak
        secretKey: "", //sk
        domain: "", //域名
        bucket: "", //空间名称
        uploadWay: "1", //1->普通 2->七牛云
      },
    };
  },
  computed: {},
  created() {
    this.query();
    this.getFolder();
  },
  methods: {

    async getFolder(){
      try {
        let res = await folder();
        console.log('res--->',res);
        if(res.code === 200){
          res.data.forEach((item)=>{
            this.folderList.push({
              label: item,
              value: item
            })
          })
        }
      } catch (error) {
        console.log(error);
      }
    },
    //查询
    async query() {
      try {
        let res = await find();
        if (res.code === 200) {
          this.loading = false;
          this.set = res.data;
        } else {
          this.$message({
            message: res.msg,
            type: "success",
          });
        }
      } catch (error) {
        console.log(error);
      }
    },

    //更新SEO信息
    async update() {
      try {
        let res = await update(this.set);
        if (res.code === 200) {
          this.$message({
            message: "更新成功^_^",
            type: "success",
          });
          this.query();
        } else {
          this.$message({
            message: res.msg,
            type: "success",
          });
        }
      } catch (error) {
        console.log(error);
      }
    },

    submit(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.update();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
  },
};
</script>
<style></style>
