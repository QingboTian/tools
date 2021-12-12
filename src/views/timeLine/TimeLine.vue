<template>
  <div class="container">
    <div class="header">
      <el-form ref="form" :model="form" label-width="80px" inline>
        <el-form-item label="模式">
          <el-select
            v-model="form.model"
            placeholder="请选择"
            @change="inputText = JSON.stringify(activities, null, 4)"
            size="small"
          >
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标题">
          <el-input v-model="form.title" size="small"></el-input>
        </el-form-item>
        <el-form-item label="效果">
          <el-select v-model="form.effect" placeholder="请选择" size="small">
            <el-option label="效果1" value="1"> </el-option>
            <el-option label="效果2" value="2"> </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="宽度">
          <el-input v-model="form.width" size="small"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="exportImage()"
            >导出效果图</el-button
          >
        </el-form-item>
        <el-form-item>
          <el-tooltip
            class="item"
            effect="dark"
            content="click me, 查看帮助文档"
            placement="right"
          >
            <i class="el-icon-question" @click="tipsHandler()"></i>
          </el-tooltip>
        </el-form-item>
      </el-form>
    </div>
    <div class="body">
      <div class="left">
        <div v-if="form.model == 1">
          <!-- 小白模式 -->
          <el-form
            ref="simpleForm"
            :model="simpleForm"
            label-width="80px"
            :rules="rules"
          >
            <el-form-item label="节点标题" prop="title">
              <el-input
                v-model.trim="simpleForm.title"
                placeholder="请输入节点标题"
                size="small"
              ></el-input>
            </el-form-item>
            <el-form-item label="节点时间" prop="timestamp">
              <el-input
                v-model.trim="simpleForm.timestamp"
                placeholder="请输入时间"
                size="small"
              ></el-input>
            </el-form-item>
            <el-form-item label="节点正文" prop="contents">
              <div style="display: flex">
                <div style="margin-right: 20px">
                  <el-input
                    v-model.trim="content.key"
                    placeholder="请输入节点正文条目KEY"
                    size="small"
                  ></el-input>
                </div>
                <div style="margin-right: 20px">
                  <el-input
                    size="small"
                    v-model.trim="content.value"
                    placeholder="请输入节点正文条目VALUE"
                  ></el-input>
                </div>
                <div>
                  <el-button
                    @click="addContentHandler()"
                    type="primary"
                    size="small"
                    >添加</el-button
                  >
                </div>
              </div>
              <div>
                <div
                  v-for="(item, key) in simpleForm.contents"
                  :key="key"
                  style="display: flex"
                >
                  <div style="display: flex; flex: 1">
                    <div>{{ item.key }}</div>
                    <div>{{ item.value }}</div>
                  </div>

                  <div style="margin-left: 20px">
                    <el-button
                      @click="deleteContentArray(key)"
                      size="small"
                      type="danger"
                      >删除</el-button
                    >
                  </div>
                </div>
              </div>
            </el-form-item>
            <el-form-item label="大小" prop="size">
              <el-select
                v-model="simpleForm.size"
                placeholder="请选择"
                size="small"
              >
                <el-option label="正常" value="normal"> </el-option>
                <el-option label="大的" value="large"> </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="类型" prop="type">
              <el-select
                v-model="simpleForm.type"
                placeholder="请选择"
                size="small"
              >
                <el-option label="无" :value="null"> </el-option>
                <el-option label="primary" value="primary"> </el-option>
                <el-option label="success" value="success"> </el-option>
                <el-option label="warning" value="warning"> </el-option>
                <el-option label="danger" value="danger"> </el-option>
                <el-option label="info" value="info"> </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="图标" prop="icon">
              <el-input
                v-model.trim="simpleForm.icon"
                placeholder="详见 element-ui 图标文档"
                size="small"
              ></el-input>
              <i class="el-icon-question" @click="tipsHandler()"></i>
            </el-form-item>
            <el-form-item label="颜色" prop="color">
              <el-input
                size="small"
                v-model.trim="simpleForm.color"
                @blur="color = simpleForm.color"
                placeholder="支持16进制颜色"
              ></el-input>
              <colorPicker
                v-on:change="headleChangeColor()"
                defaultColor="#ff0000"
                v-model="color"
              />
            </el-form-item>
            <el-form-item>
              <el-button
                type="primary"
                @click="addNode()"
                v-if="updateFlag"
                size="small"
                >更新</el-button
              ><el-button
                type="primary"
                @click="addNode()"
                v-if="!updateFlag"
                size="small"
                >创建</el-button
              >

              <el-button type="danger" @click="clearNode()" size="small"
                >清空</el-button
              >
            </el-form-item>
            <el-form-item>
              <div>
                <div style="display: flex; margin-bottom: 20px">
                  <el-input
                    v-model="deleteNode"
                    clearable
                    placeholder="如右侧效果图从上往下数第N个节点，请填入数字，节点从1开始"
                    size="small"
                  ></el-input>
                  <el-button
                    type="danger"
                    @click="deleteNodeHandler()"
                    style="margin-left: 50px"
                    size="small"
                    >删除节点</el-button
                  >
                </div>
                <div style="display: flex">
                  <el-input
                    clearable
                    v-model="updateNode"
                    placeholder="如右侧效果图从上往下数第N个节点，请填入数字，节点从1开始"
                    size="small"
                  ></el-input>
                  <el-button
                    type="success"
                    @click="updateNodeHandler()"
                    style="margin-left: 50px"
                    size="small"
                    >更新节点</el-button
                  >
                </div>
              </div>
            </el-form-item>
          </el-form>
        </div>
        <div v-if="form.model == 2">
          <!-- 自定义模式 -->
          <el-input
            type="textarea"
            :rows="40"
            placeholder="请输入JSON格式内容"
            v-model="inputText"
            @input="inputTextChange()"
          >
          </el-input>
        </div>
      </div>
      <div class="right" :style="'flex:' + form.width">
        <div style="font-size: x-large; margin-bottom: 20px">
          <center>效果图</center>
        </div>
        <div ref="screen">
          <div v-if="form.effect == 1">
            <div>
              <div class="title">{{ form.title }}</div>
              <el-timeline>
                <el-timeline-item
                  v-for="(activity, index) in activities"
                  :key="index"
                  :icon="activity.icon"
                  :type="activity.type"
                  :color="activity.color"
                  :size="activity.size"
                  :timestamp="activity.timestamp"
                  placement="top"
                >
                  <div class="a">
                    <div class="content-title">
                      <b>{{ activity.title }}</b>
                    </div>
                    <div
                      class="content-value-item"
                      v-for="(item, key) in activity.contents"
                      :key="key"
                    >
                      <div class="item content-value-item-key">
                        {{ item.key }}
                      </div>
                      <div class="item content-value-item-value">
                        {{ item.value }}
                      </div>
                    </div>
                  </div>
                </el-timeline-item>
              </el-timeline>
            </div>
          </div>
          <div v-if="form.effect == 2">
            <div>
              <div class="title">{{ form.title }}</div>
              <el-timeline>
                <el-timeline-item
                  v-for="(activity, index) in activities"
                  :key="index"
                  :icon="activity.icon"
                  :type="activity.type"
                  :color="activity.color"
                  :size="activity.size"
                  :timestamp="activity.timestamp"
                  placement="top"
                >
                  <div class="a">
                    <div class="content-title">
                      <b>{{ activity.title }}</b>
                    </div>
                    <el-card>
                      <div
                        class="content-value-item"
                        v-for="(item, key) in activity.contents"
                        :key="key"
                      >
                        <div class="item content-value-item-key">
                          {{ item.key }}
                        </div>
                        <div class="item content-value-item-value">
                          {{ item.value }}
                        </div>
                      </div>
                    </el-card>
                  </div>
                </el-timeline-item>
              </el-timeline>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="footer"></div>
    <span class="hidden">
      <el-dialog title="帮助文档" :visible.sync="dialogVisible" width="30%">
        <Tips />
      </el-dialog>
    </span>
  </div>
</template>

<script>
import html2canvas from "html2canvas";
// import Cookies from "js-cookie";
import Tips from "./Tips.vue";
const cookieKey = "tools-time-line";
export default {
  data() {
    return {
      updateFlag: false,
      rules: {
        title: [{ required: true, message: "请输入正文标题", trigger: "blur" }],
        contents: [
          {
            type: "array",
            required: true,
            message: "请至少添加一个正文条目",
            trigger: "change",
          },
        ],
      },
      content: {
        key: "",
        value: "",
      },
      color: "#ff0000",
      updateNode: "",
      deleteNode: "",
      simpleForm: {
        title: "",
        size: "normal",
        type: null,
        color: null,
        timestamp: "",
        icon: "",
        contents: [],
      },
      dialogVisible: false,
      form: {
        title: "12月17日云平台主管团建",
        model: "1",
        effect: "1",
        width: "5",
      },
      activities: [
        {
          title: "轰趴馆集合",
          contents: [
            {
              key: "坐标：",
              value: "WE·ME年会团建轰趴连锁(中关村店)",
            },
            {
              key: "地址：",
              value: "安宁庄东路安宁华庭一区14号楼101底商3层(水韵茶坊楼上)",
            },
            {
              key: "停车：",
              value: "楼下底商有停车场，收费1元/小时",
            },
          ],
          timestamp: "11:00",
          size: "large",
          type: "primary",
          icon: "el-icon-location",
        },
        {
          title: "午餐+轰趴",
          contents: [
            {
              key: "活动内容：",
              value: "韩式烤肉自助，茶歇不限量供应，各种设施体验娱乐",
            },
          ],
          timestamp: "11:30",
          color: "#0bbd87",
          size: "large",
          icon: "el-icon-football",
        },
        {
          title: "出发去晚餐",
          contents: [
            {
              key: "坐标：",
              value: "唐宫海鲜坊",
            },
            {
              key: "地址：",
              value: "北四环东路118号西藏大厦首层",
            },
            {
              key: "停车：",
              value: "地上停车场A、B座，收费6元/小时",
            },
          ],
          timestamp: "16:30",
          size: "large",
          color: "#0bbd87",
          icon: "el-icon-location",
        },
        {
          title: "聚餐时间",
          contents: [
            {
              key: "包房号：",
              value: "998",
            },
            {
              key: "友情提示：",
              value: "开酒不喝车，代驾/低碳出行是个好东西",
            },
          ],
          timestamp: "17:30",
          color: "#0bbd87",
          size: "large",
          icon: "el-icon-food",
        },
      ],
      options: [
        {
          value: "1",
          label: "小白模式",
        },
        {
          value: "2",
          label: "自定义模式",
        },
      ],
      inputText: "",
    };
  },
  watch: {
    activities(old, newVal) {
      this.localStorageChang(cookieKey, JSON.stringify(newVal, null, 4));
    },
  },
  components: {
    Tips,
  },
  methods: {
    updateNodeHandler() {
      if (!this.updateNode || this.updateNode === "") {
        this.$message.info("请输入节点标识");
        return;
      }
      if (!this.isNum(this.updateNode)) {
        this.$message.info("非数字，请检查输入内容");
        return;
      }
      if (this.updateNode > this.activities.length || this.updateNode <= 0) {
        this.$message.info("请输入正确的节点");
        return;
      }
      for (var i = 0; i < this.activities.length; i++) {
        if (i == this.updateNode - 1) {
          this.updateFlag = true;
          this.simpleForm = JSON.parse(JSON.stringify(this.activities[i]));
          this.color = this.simpleForm.color;
          return;
        }
      }
    },
    // 删除节点正文已经添加的内容
    deleteContentArray(val) {
      this.simpleForm.contents.splice(val, 1);
    },
    addContentHandler() {
      if (this.content.key === "" || this.content.value === "") {
        this.$message.info("内容为空，请填写");
        return;
      }
      this.simpleForm.contents.push(JSON.parse(JSON.stringify(this.content)));
    },
    headleChangeColor() {
      this.simpleForm.color = this.color;
    },
    deleteNodeHandler() {
      if (!this.deleteNode || this.deleteNode === "") {
        this.$message.info("请输入节点标识");
        return;
      }
      if (!this.isNum(this.deleteNode)) {
        this.$message.info("非数字，请检查输入内容");
        return;
      }
      if (this.deleteNode > this.activities.length || this.deleteNode <= 0) {
        this.$message.info("请输入正确的节点");
        return;
      }
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.activities.splice(this.deleteNode - 1, 1);
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {});
    },
    addNode() {
      this.$refs["simpleForm"].validate((valid) => {
        if (valid) {
          var tempData = JSON.parse(JSON.stringify(this.simpleForm));
          if (this.updateFlag) {
            for (var i = 0; i < this.activities.length; i++) {
              if (this.updateNode == i + 1) {
                this.activities[i] = tempData;
              }
            }
            this.updateFlag = false;
          } else {
            this.activities.push(tempData);
          }
        } else {
          return false;
        }
      });
    },
    clearNode() {
      this.$refs["simpleForm"].resetFields();
      this.content.key = "";
      this.content.value = "";
    },
    tipsHandler() {
      this.dialogVisible = true;
    },

    localStorageChang(key, value) {
      localStorage.setItem(key, value);
    },

    init() {
      // 首先尝试从本地cookie获取
      var ok = localStorage.getItem(cookieKey);
      if (ok) {
        this.activities = JSON.parse(ok);
        this.inputText = JSON.stringify(this.activities, null, 4);
        console.log("从cookie中获取", this.activities);
        return;
      }
      this.inputText = JSON.stringify(this.activities, null, 4);
      // 保存到本地cookie
      this.localStorageChang(cookieKey, this.inputText);
    },

    inputTextChange() {
      this.activities = JSON.parse(this.inputText);
      // this.localStorageChang(cookieKey, this.inputText);
    },

    exportImage() {
      html2canvas(this.$refs.screen, {
        backgroundColor: "#FFFFFF",
        useCORS: true,
      }).then((canvas) => {
        if (navigator.msSaveBlob) {
          // IE10+
          let blob = canvas.msToBlob();
          return navigator.msSaveBlob(blob, name);
        } else {
          let imageurl = canvas.toDataURL("image/png");
          //这里需要自己选择命名规则
          let imagename = new Date().getTime() + "";
          this.fileDownload(imageurl, imagename);
        }
      });
    },

    //下载截屏图片
    fileDownload(downloadUrl, downloadName) {
      let aLink = document.createElement("a");
      aLink.style.display = "none";
      aLink.href = downloadUrl;
      aLink.download = `${downloadName}.jpg`;
      // 触发点击-然后移除
      document.body.appendChild(aLink);
      aLink.click();
      document.body.removeChild(aLink);
    },

    // 是否是数字
    isNum(value) {
      var isnum = /^\d+$/.test(value);
      return isnum;
    },
  },

  created() {
    this.init();
  },
};
</script>

<style scoped>
.container {
  margin: 20px;
}

.title {
  margin-bottom: 30px;
  margin-left: 30px;
  font-size: x-large;
}

.content-value-item {
  display: flex;
}

.content-value-item-key {
  /* flex: 1; */
}
.content-value-item-value {
  flex: 1;
}

.content-title {
  margin-bottom: 5px;
}

.a {
  margin-left: 10px;
  margin-bottom: 10px;
}

.body {
  display: flex;
}

.body .left {
  flex: 5;
  margin-right: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
  padding: 20px;
  border-radius: 15px;
}

.body .right {
  height: 100%;
  flex: 5;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
  padding: 20px;
  border-radius: 15px;
}
</style>