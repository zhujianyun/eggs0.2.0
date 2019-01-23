<template>
    <div class="full_preview" @click.stop='() => {}'>
        <div v-if='false' class="top_operate">
            <div class="t_o_left fl">
              <span>{{fullPreview.taskTitle}}</span>
            </div>
            <div class="t_o_right fr">
              <i class='iconfont icon-haoyou1'></i>
              <i class='iconfont icon-rili1'></i>
            </div>
        </div>
        <div class="full_preview_list clearfix">
            <div    
                class="every_stage"
                v-for='stage in fullPreview.stageFileList'
                :key='stage.stageTaskId'
                >
                <div class="stage_top">{{stage.title}}</div>
                <div class="stage_file">
                    <template v-if='stage.enabled'>
                        <div 
                            class="file_group"
                            v-for='group in stage.fileList'
                            :key='group.pkid'
                            >
                            <p class="group_title">{{group.groupName ? group.groupName : '未分组文件'}}</p>
                            <div class="group_list clearfix">
                                <div class="file_box"
                                    v-for='file in group.fileList'
                                    :key='file.FilePkid'
                                    @mouseover.stop="fileMouseover($event, file)"
                                    @mouseout="fileMousout($event, file)"

                                >
                                    <span class="file_img">
                                        <img :src="file.UrlMins" alt="">
                                        <span class="none"></span>
                                    </span>
                                    <p class="file_title word_over">{{file.FileName}}</p>
                                </div>
                            </div>
                        </div>
                    </template>
                    <div v-if='!stage.enabled' class="file_group_empty">
                        <span class="empty_img">
                            <img src="../style/file_empty.png" alt="">
                        </span>
                        <p>该阶段已关闭</p>
                    </div>
                    <div v-if='!stage.fileList.length && stage.enabled' class="file_group_empty">
                        <span class="empty_img">
                            <img src="../style/file_empty.png" alt="">
                        </span>
                        <p>暂无文件</p>
                    </div>
                </div>
            </div>
        </div>
        <div id='hoverFile' class="hover_file" v-if='fileShow'>
            <div 
                class="every_file"
                @mouseenter="enterFile(file)"
                @mouseleave="leaveFile(file)"
                >
                <span class="file_pic">
                    <template v-if='file.FileType === 11 && file.Desc'>
                    <span class="text_desc"><span>{{file.Desc}}</span></span>
                    </template>
                    <template v-else>
                    <img :src="file.UrlMin" alt="">
                    </template>
                    <span class="none"></span>
                </span>
                <div class="file_info">
                    <p v-if='!file.edit' class="title" @dblclick="fileNameDblclick($event, file)">{{file.FileName}}</p>
                    <input 
                    v-else 
                    class="title edit" 
                    v-model='file.FileTitle' 
                    id="fileNameEdit"
                    @blur="fileNameEditBlur($event, file)"
                    />
                    <el-tooltip effect="dark" :content="file.nickName ? file.nickName : file.userName" placement="top" :open-delay="300">
                    <img :src="file.UserPic" alt="" class="from_header">
                    </el-tooltip>
                    <span class="file_message fr">
                    <el-tooltip effect="dark" content="评论" placement="top" :open-delay="300">
                        <i class='iconfont icon-pinglun'></i>
                    </el-tooltip>
                    {{file.Count}}
                    </span>
                    
                </div>
                <div class="null"></div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props: ['taskId', 'fullList'],
    data() {
        return {
            fileTypeImgM: [
                {
                    src: require("../../../../assets/img/file_m/0.png")
                }, {
                    src: require("../../../../assets/img/file_m/1.png")
                }, {
                    src: require("../../../../assets/img/file_m/2.png")
                }, {
                    src: require("../../../../assets/img/file_m/3.png")
                }, {
                    src: require("../../../../assets/img/file_m/4.png")
                }, {
                    src: require("../../../../assets/img/file_m/5.png")
                }, {
                    src: require("../../../../assets/img/file_m/6.png")
                }, {
                    src: require("../../../../assets/img/file_m/7.png")
                }, {
                    src: require("../../../../assets/img/file_m/8.png")
                }, {
                    src: require("../../../../assets/img/file_m/9.png")
                }, {
                    src: require("../../../../assets/img/file_m/10.png")
                }, {
                    src: require("../../../../assets/img/file_m/11.png")
                },
            ], // 文件类型图片
             fileTypeImgB: [
                {
                    src: require("../../../../assets/img/file_b/0.png")
                }, {
                    src: require("../../../../assets/img/file_b/1.png")
                }, {
                    src: require("../../../../assets/img/file_b/2.png")
                }, {
                    src: require("../../../../assets/img/file_b/3.png")
                }, {
                    src: require("../../../../assets/img/file_b/4.png")
                }, {
                    src: require("../../../../assets/img/file_b/5.png")
                }, {
                    src: require("../../../../assets/img/file_b/6.png")
                }, {
                    src: require("../../../../assets/img/file_b/7.png")
                }, {
                    src: require("../../../../assets/img/file_b/8.png")
                }, {
                    src: require("../../../../assets/img/file_b/9.png")
                }, {
                    src: require("../../../../assets/img/file_b/10.png")
                }, {
                    src: require("../../../../assets/img/file_b/11.png")
                },
            ], // 文件类型图片
            fullPreview: {},
            file: {},
            fileShow: false,
            _time: null,
            _time1: null,
        }
    },
    methods: {
        fileMouseover(e, item) {
            if(this._time) {
                clearTimeout(this._time);
            }
            if(this._time1) {
                clearTimeout(this._time1);
            }
            this._time = setTimeout(() => {
                this.fileShow = true;
                this.file = Object.assign({}, item);
                
                let returns = this.findparent($(e.target));
                let left = returns[0];
                let top = returns[1] + 70;
                this.$nextTick(() => {
                    let left1 = $('#hoverFile').offset().left;
                    let top1 = $('#hoverFile').offset().top;
                    let w = $('#hoverFile').width();
                    let h = $('#hoverFile').height();
                    let appW = $('#app').width();
                    let appH = $('#app').height();
                    if(left + w > appW) {
                        left = appW - w;;
                    }
                    if(top + h > appH) {
                        top = appH - h - 100;
                    }
                    $('#hoverFile').css({left: left + 'px', top: top + 'px'});

                });
            }, 200);
            
        },
        fileMousout(e, item) {
            if(this._time) {
                clearTimeout(this._time);
            }
            if(this._time1) {
                clearTimeout(this._time1);
            }
            this._time1 = setTimeout(() => {
                this.fileShow = false;
            }, 200);

        },
        enterFile() {
            if(this._time) {
                clearTimeout(this._time);
            }
            if(this._time1) {
                clearTimeout(this._time1);
            }
            this.fileShow = true;
        },
         leaveFile() {
            this.fileShow = false;
        },
        findparent(e) {
            let attrs = e.attr('class');
            if(attrs && attrs === 'file_box') {
                return [e.offset().left, e.offset().top]
            }else {
               return this.findparent(e.parent()); 
            }
        },

        fileNameDblclick(e, item) {
            item.edit = true;
            this.file = JSON.parse(JSON.stringify(this.file));
        },

        // 修改文件名失焦--保存
        fileNameEditBlur(e, item) {
            item.edit = false;
            this.file = JSON.parse(JSON.stringify(this.file));

            return;
            const { index, groupId, groupIndex } = this.operateFile;
            const newTitle = item.FileTitle + '.' + item.Type;
            if(item.FileTitle == '') {
                this.$message({
                type: 'warning',
                message: '文件名不能为空！',
                center: true
                });
                this.$nextTick(() => {
                const ele = $('#fileNameEdit');
                ele.focus();
                });
                return;
            }
            // 先判重，如果有重复的名字--提示，否则--发送请求
            let repeat = -1;
            if(groupId) {
                repeat = this.parthsGroup[groupIndex].fileList.findIndex(ele => (ele.FileName === newTitle && ele.FilePkid !== item.FilePkid));
            }else {
                repeat = this.notGroupedList.findIndex(ele => (ele.FileName === newTitle && ele.FilePkid !== item.FilePkid));
            }
            if(repeat !== -1) {
                this.$message({
                type: 'error',
                message: '该分组内含有同名文件！·',
                center: true
                });
                this.$nextTick(() => {
                const ele = $('#fileNameEdit');
                ele.focus();
                });
            }else {
                // 发送修改分组名的接口
                if(this.fileNameCopy !== item.FileTitle) {
                let obj = {
                    FilePkid: item.FilePkid,
                    newTitle: item.FileTitle + '.' + item.Type
                };
                this.$HTTP('post', '/stageTaskFile_update', obj).then(res => {
                    console.log('修改文件名成功', res);
                    this.$message({
                    type: 'success',
                    message: '修改文件名成功',
                    center: true
                    });
                }).catch(err => {
                    console.log('修改文件名失败', err);
                });
                item.FileName = newTitle;
                }
                item.edit = false;
                if(groupId) {
                this.parthsGroup = this.parthsGroup.concat();
                }else {
                this.notGroupedList = this.notGroupedList.concat();
                }
            }
        },
        getData() {
            this.fullPreview = Object.assign({}, this.fullList);
            for(let x of this.fullPreview.stageFileList) {
                for(let y of x.fileList) {
                    for(let z of y.fileList) {
                        this.$set(z, 'FileType', this.getFlieTyle(z.Type));
                        if(z.FileType !== 1) {
                            this.$set(z, 'UrlMin', this.fileTypeImgB[z.FileType].src);
                        }
                        this.$set(z, 'UrlMins', this.fileTypeImgM[z.FileType].src);

                    }
                }
            }

        }
    },
    created() {
        this.getData();
    }
};
</script>
<style lang='less'>
.full_preview {
  position: absolute;
  top: 51px;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 10;
  background: #fff;

  .full_preview_list {
      width: 100%;
      height: 100%;
      background: #fff;
      overflow-y: hidden;
      overflow-x: auto;
      white-space: nowrap;
      // 用这行的话就不能固定宽度了
      // display: flex;  
      .every_stage {
          width: 290px;
          height: 100%;
          border-right: 1px solid #EEEEEE;
            display: -webkit-inline-flex;
            display: -ms-inline-flexbox;
            display: inline-flex;
            -webkit-flex-direction: column;
            -ms-flex-direction: column;
            flex-direction: column;
            -webkit-align-items: stretch;
            -ms-flex-align: stretch;
            align-items: stretch;
          .stage_top {
              width: 100%;
              height: 40px;
              line-height: 40px;
              border-bottom: 1px solid #EEEEEE;
              text-align: center;
          }
          .stage_file {
              position: relative;
              width: 100%;
              height: calc(100% - 41px);
              padding: 6px 0 18px 24px;
              box-sizing: border-box;
              overflow-y: auto;
              .file_group {
                  .group_title {
                      font-weight: bold;
                      margin-bottom: 10px;
                      margin-top: 12px;
                  }
                  .group_list {
                      .file_box {
                          float: left;
                          width: 74px;
                          height: 70px;
                          border: 1px solid #EEEEEE;
                          border-radius: 2px;
                          box-sizing: border-box;
                          margin: 0 10px 6px 0;
                          cursor: pointer;
                          .file_img {
                              display: inline-block;
                              width: 100%;
                              height: 48px;
                              text-align: center;
                              img {
                                 width: 24px;
                                 height: 30px;
                                 margin-top: 10px;
                              }
                              .none {
                                height: 100%;
                                display: inline-block;
                                vertical-align: middle;
                              } 
                          }
                          .file_title {
                              width: 100%;
                              padding: 0 2px;
                              box-sizing: border-box;
                              font-size: 12px;
                              text-align: center;
                              
                          }
                      }
                  }
              }
              .file_group_empty {
                  position: absolute;
                  width: 74px;
                  top: 35%;
                  left: 50%;
                  margin-left: -37px;
                  .empty_img {
                      display: inline-block;
                      width: 74px;
                      height: 70px;
                      background: #FBFBFF;
                      border-radius: 2px;
                      position: relative;
                      img {
                          width: 36px;
                          height: 40px;
                          position: absolute;
                          top: 50%;
                          left: 50%;
                          margin-top: -20px;
                          margin-left: -18px;
                      }
                  }
                  p {
                      font-size: 12px;
                      margin-top: 12px;
                      color: #666;
                  text-align: center;

                  }
              }
          }
      }
  }
  .hover_file {
      position: fixed;
      .every_file {
          background: #ffffff;
      }
  }
}
</style>

