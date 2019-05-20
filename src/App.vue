<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>
<style>
@import "../static/css/main.css";
@import "../static/css/color-dark.css"; /*深色主题*/
/* @import "../static/css/theme-green/color-green.css";   /*浅色主题*、/ */
.fontIcon {
  font-size: 14px;
  color: rgb(64, 158, 255);
  margin-left: 10px;
}
.container{
  height:750px;
}
.el-input.is-disabled .el-input__inner {
    background-color: #e5e6e6;
    border-color: #E4E7ED;
    color: #989a9e;
    cursor: not-allowed;
    border-radius:none;
}
.el-input {
  width:95%;
}
</style>