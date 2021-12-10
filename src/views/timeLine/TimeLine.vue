<template>
  <div class="container">
    <div class="header">
      <el-form ref="form" :model="form" label-width="80px" inline>
        <el-form-item label="模式">
          <el-select v-model="form.model" placeholder="请选择">
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
          <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="效果">
          <el-select v-model="form.effect" placeholder="请选择">
            <el-option label="效果1" value="1"> </el-option>
            <el-option label="效果2" value="2"> </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="宽度">
          <el-input v-model="form.width"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="exportImage()"
            >导出效果图</el-button
          >
        </el-form-item>
      </el-form>
    </div>
    <div class="body">
      <div class="left">
        <div v-if="form.model == 1">
          <!-- 小白模式 -->
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
          <div v-if="form.effect == 2"></div>
        </div>
      </div>
    </div>
    <div class="footer"></div>
  </div>
</template>

<script>
import html2canvas from "html2canvas";
// import Cookies from "js-cookie";
export default {
  data() {
    return {
      form: {
        title: "12月17日云平台主管团建",
        model: "2",
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
  methods: {
    localStorageChang(key, value) {
      localStorage.setItem(key, value);
    },

    init() {
      // 首先尝试从本地cookie获取
      var ok = localStorage.getItem("tools-time-line");
      if (ok) {
        this.inputText = ok;
        this.activities = JSON.parse(ok);
        return;
      }
      this.inputText = JSON.stringify(this.activities, null, 4);
      // 保存到本地cookie
      this.localStorageChang("tools-time-line", this.inputText);
    },

    inputTextChange() {
      this.activities = JSON.parse(this.inputText);
      this.localStorageChang("tools-time-line", this.inputText);
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
}

.body .right {
  height: 100%;
  flex: 5;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04);
  padding: 20px;
}
</style>