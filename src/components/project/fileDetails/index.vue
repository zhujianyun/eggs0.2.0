<template>
    <div id="FileDetails">
        <div class="fileTop">
            <div class="fl leftNav">
                <el-breadcrumb separator="/">
                    <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
                    <el-breadcrumb-item>
                        <a href="/">活动管理</a>
                    </el-breadcrumb-item>
                    <el-breadcrumb-item>活动列表</el-breadcrumb-item>
                    <el-breadcrumb-item>活动详情
                        <i class="iconfont icon-bianji"></i>
                    </el-breadcrumb-item>
                </el-breadcrumb>
            </div>
            <div class="right fr">
                <span class="cur">
                    <i class="iconfont icon-tihuan"> </i> 文件替换</span>
                <span class="cur">
                    <i class="iconfont icon-xiazai"></i> 下载</span>
                <span class="cur">
                    <i class="iconfont icon-fenxiang"></i> 分享</span>
                <span class="cur">
                    <i class="iconfont icon-delete"></i> 删除</span>
                <span class="cur"
                      @click="closeDetails">
                    <i class="iconfont icon-guanbijiantou"></i>
                </span>
            </div>
        </div>
        <div class="fileContent">
            <!-- 左侧列表 -->
            <div class="leftFileOne">
                <!-- 文件预览 -->
                <div class="file">

                    <img src=""
                         alt="">



                         
                </div>
                <!-- 全部评论 -->
                <div class="commentBox">
                    <div class="topInputBox">
                        <textarea name=""
                                  id=""
                                  cols="30"
                                  rows="10"
                                  placeholder="输入评论内容，可@通知对方，Enter快速发布"
                                  class="commentiInput">

                        </textarea>
                    </div>
                    <div class="allComment">
                        <p>全部评论</p>
                        <ul>
                            <li class="clearfix com">
                                <span class="fl">
                                    <img src=""
                                         alt="">
                                </span>
                                <span class="fl">
                                    <p>mingzi</p>
                                    <p>neirong</p>
                                </span>
                                <span class="fr">
                                    <p>时间</p>
                                </span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
            <!-- 右侧列表 -->
            <div class="rightFileLists">
                <template>
                    <el-select v-model="value"
                               placeholder="请选择">
                        <el-option v-for="item in options"
                                   :key="item.value"
                                   :label="item.label"
                                   :value="item.value">
                        </el-option>
                    </el-select>
                </template>
                <ul class="allFileLists">
                    <li v-for="(list,index) of fileLists"
                        :key="index"
                        @click="checkFile(list,index)"
                        class="allList"
                        :class="{'allListCheck':checkIndex==index}">
                        <span class="check"
                              v-show="checkIndex==index"></span>
                        <div class="fileBox">
                            <img :src="list.UrlMin"
                                 alt="">
                            <p>{{list.FileTitle}}</p>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            options: [{
                value: '选项1',
                label: '黄金糕'
            }],
            value: '',
            fileLists: '', //文件列表 总
            checkIndex: 0, //当前选择index
        }
    },
    props: ['fileLists'],
    methods: {
        // 关闭文件详情
        closeDetails() {
            this.$emit('closeDetails');
        },
        // 切换文件
        checkFile(list, index) {
            this.checkIndex = index;
            console.log(list)
        }
    },
    created() {

        this.fileLists = this.fileLists;
    }
}
</script>
<style lang='less'>
@import "../../../assets/css/base.less";
.el-button {
  border: none !important;
  background: none;
  padding: 0 !important;
}

#FileDetails {
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 90;
  background: #535353;
  font-size: 14px;
  i {
    font-size: 12px;
  }
  .fileTop {
    height: 50px;
    padding: 0 25px;
    .box_sizing;
    background: #ffffff;
    .leftNav {
      .el-breadcrumb {
        line-height: 50px;
      }
    }
    .right {
      line-height: 50px;
      color: #666666;
      span {
        margin-left: 20px;
      }
    }
  }
  .fileContent {
    width: 1111px;
    height: 100%;
    margin: 24px auto;
    display: flex;
    flex-direction: row;
    .leftFileOne {
      width: 880px;
      background: red;
      .file {
        width: 100%;
        background: rgba(255, 255, 255, 1);
        box-shadow: 0px 1px 6px 0px rgba(0, 0, 0, 0.2);
        border-radius: 4px;
      }
      .commentBox {
        padding: 30px 20px;
        .box_sizing;
        margin-top: 15px;
        width: 100%;
        background: rgba(255, 255, 255, 1);
        box-shadow: 0px 1px 6px 0px rgba(0, 0, 0, 0.2);
        border-radius: 4px;
        .commentiInput {
          width: 832px;
          height: 44px;
          background: rgba(250, 250, 250, 1);
          border: 1px solid rgba(242, 242, 242, 1);
          border-radius: 4px;
          padding: 10px;
          .box_sizing;
        }
      }
    }
    .rightFileLists {
      margin-left: 24px;
      width: 207px;
      height: calc(100% - 75px);
      overflow: scroll;
      background: #ffffff;
      border-radius: 4px;
      .allFileLists {
        .allList {
          width: 207px;
          height: 152px;
          border-radius: 4px;
          background: #ffffff;
          position: relative;
          padding: 7px 0 0 0;
          .box_sizing;
          .check {
            width: 0;
            height: 0;
            overflow: hidden;
            font-size: 0; /*是因为, 虽然宽高度为0, 但在IE6下会具有默认的 */
            line-height: 0; /* 字体大小和行高, 导致盒子呈现被撑开的长矩形 */
            border-width: 7px;
            border-style: solid dashed dashed dashed; /*IE6下, 设置余下三条边的border-style为dashed,即可达到透明的效果*/
            border-color: transparent transparent transparent #3684ff;
            position: absolute;
            top: 50%;
            margin-top: -4px;
            left: 10px;
          }
          .fileBox {
            margin: 0 auto;
            width: 156px;
            height: 117px;
            // line-height: 117px;
            text-align: center;
            background: #ffffff;
            border: 1px solid rgba(238, 238, 238, 1);
            border-radius: 4px;
            overflow: hidden;
            img {
              //   vertical-align: middle;
              max-height: 100%;
            }
            p {
              position: absolute;
              bottom: 5px;
              left: 50%;
              margin-left: -51px;
              width: 119px;
              height: 14px;
              font-size: 12px;
              font-family: "PingFang-SC-Regular";
              overflow: hidden;
              font-weight: 400;
              color: rgba(51, 51, 51, 1);
            }
          }
        }
        .allListCheck {
          background: rgba(242, 242, 242, 1);
        }
      }
    }
  }
}
</style>


