<template>
  <div>
    <el-collapse v-model="activeNames" @change="handleChange">
      <el-collapse-item title="模式" name="1">
        <div>
          小白模式：纯表单操作，不需要懂任何相关知识，备注：部分功能需要查阅<a href="https://element.faas.ele.me/#/zh-CN/component/icon" target="_blank">element-ui</a>文档，如图标等，找到你喜欢的图标，复制过来即可
        </div>
        <div>
          自定义模式：对于懂JSON的人来说，这个模式无疑是最简单的操作模式！作者更推荐这种模式！！！
        </div>
      </el-collapse-item>
      <el-collapse-item title="标题" name="2">
        <div>
          能够有效描述时间线的一个Title，可以为空
        </div>
      </el-collapse-item>
      <el-collapse-item title="效果" name="3">
        <div>目前只支持这两种模式哦，后续会添加更多好看的样式，也欢迎有好想法的用户简单画一个原型图告诉作者，作者为你支持，email：tqb820965236@163.com</div>
        <div>
          作者考虑一种更通用的模式，就是支持用户自己写CSS样式，当然，这有个个矛盾的地方，就是懂CSS的用户会在这个平台使用吗，=_=
        </div>
      </el-collapse-item>
      <el-collapse-item title="宽度+导出" name="4">
        <div>
            这里宽度控制的是右边区域的宽度，默认为5，即左右各自55分，支持小数，用户可以自己尝试操作，达到自己满意的样式，然后进行效果图导出，如果你不想使用，可以自行截图哦....
        </div>
      </el-collapse-item>
      <el-collapse-item title="联系方式" name="5">
        <div>
            Email：tqb820965236@163.com
        </div>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activeNames: ["1"],
    };
  },
  methods: {
    handleChange(val) {
      console.log(val);
    },
  },
};
</script>

<style scoped>
</style>