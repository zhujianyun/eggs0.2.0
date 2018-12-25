<template>
    <div class='task_detail_yun'>
        <div class="detail_top">
            <div class="project_name">EGGS产品开发</div>
            <ul class="stage_list">
                <li class='stage_li stage_li_bottom'>产品规划</li>
                <li class='stage_li'>产品设计</li>
                <li class='stage_li'>开发阶段</li>
                <li class='stage_li'>测试</li>
                <li class='stage_li stage_all'>
                   <span class='line'></span>
                   <span>整体预览</span> 
                </li>
            </ul>
            <i class='iconfont icon-guanbijiantou'></i>
        </div>
        <div class="detail_left">
            <div 
                class="task_group"
                v-for="(group, index) in this.tasksList"
                :key="group.pkid"
                >
                <div class="group_title">
                    <i 
                        class='iconfont icon-unfold'
                        :class="group.extend ? '' : 'icon-unfold_rotate'"
                        @click='extendToggle(index)'
                        ></i>
                    <span>{{group.groupName}}</span>
                </div>
                <transition-group>
                    <div class="list_box" :key="group.pkid">
                        <div 
                            class="group_list"
                            v-for="list in group.taskList"
                            :key="list.pkid"
                            >
                            <p class='every'>{{list.title}}</p>
                        </div>
                    </div>
               
                </transition-group>
            </div>
            
        </div>
        <div class="detail_main">
            <div class="top_operate">
                top_operate~~
            </div>
            <div class="detail_file">
                <div class="left_box">
                    <div class="left_file_list">
                        <draggable
                            class='draggable'
                            groupid='noGroup'
                            v-model="fileList"
                            :options="{
                              group:{name: 'file',pull: 'clone'}, 
                              ghostClass: 'ghost_file', 
                              dragClass: 'drag_file',
                              chosenClass: 'chosen_file',
                              disabled: dragDisabled
                            }"
                            :move='fileMove'
                            @start='dragStart($event, "noGroup")'
                            @end='dragEnd'
                        >
                            <div 
                                class="every_file"
                                v-for="(ele) in fileList"
                                :key="ele.Pkid"
                                :id='ele.Pkid'
                                :filename='ele.Title'
                                >
                                {{ele.Title}}
                            </div>
                        </draggable>
                    </div>
                </div>
                 <div class="right_box">
                    <div class="parths">
                        <div 
                            class="parths_group"
                            v-for="group in parthsGroup"
                            :key="group.id"
                            >
                            <div class="group_top">
                                <span class="group_name">{{group.groupTitle}}</span>
                                <div 
                                    :class="group.border ? 'group_border group_file' : 'group_file'"
                                    >
                                    <draggable
                                        class="draggable"
                                        :groupid='group.id'
                                        v-model="group.list"
                                        :options="{
                                          group:{name: 'file',pull:'clone'},
                                          ghostClass: 'ghost_file', 
                                          dragClass: 'drag_file',
                                          disabled: group.dragDisabled
                                        }"
                                        :move='fileMove'
                                        @start='dragStart($event, group.id)'
                                        @end='dragEnd'
                                    >
                                        <div 
                                            class="every_file"
                                            v-for="item in group.list"
                                            :key="item.Pkid"
                                            :groupid='group.id'
                                            :id='item.Pkid'
                                            :filename='item.Title'
                                            >
                                            <div class='file_title' :groupid='group.id'>{{item.Title}}</div>
                                            <div class="from_who">{{item.Uname}}</div>
                                        </div>
                                     </draggable>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="personal_files" id='personalFiles'>
                <div class="left_pull">
                    <span @click='personalFilesPull'>个人文档</span>
                </div>
                <div class="personal_files_main">
                    <div class="p_f_top">
                         <span>个人文档</span>
                    </div>
                    <div class="p_f_filter">
                        this is file filter!
                    </div>
                    <div class="p_f_list">
                        <div class="nav_bar" v-if='navBar.length - 1'>
                          <span
                            v-for="(bar, index) in navBar"
                            :key='bar.id'
                            @click='goBack(index)'
                          >
                            <i  v-if='index' class='iconfont icon-tubiaozhizuomoban'></i>
                            <span>{{bar.name}}</span>
                          </span>
                        </div>
                        <draggable
                          class="draggable"
                          groupid='personal'
                          v-model="personalFiles"
                          :options="{
                            group:{name: 'file',pull:'clone'},
                            ghostClass: 'ghost_file', 
                            dragClass: 'drag_file',
                            draggable: '.draged',
                            disabled: dragDisabled_personal
                          }"
                          :move='fileMove'
                          @start='dragStart($event, "personal")'
                          @end='dragEnd'
                        >
                          <div 
                              :class="file.children ? 'every_file' : 'draged every_file'"
                              v-for="file in personalFiles"
                              :key="file.id"
                              :id='file.Pkid'
                              :filename='file.Title'
                              @dblclick='dbFolder(file)'
                              >
                              <div class='file_title'>{{file.Title}}</div>
                              <div class="from_who">{{file.Uname}}</div>
                          </div>
                        </draggable>
                    </div>
                    <div class="p_f_bottom">
                        this is file bottom operate!
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import draggable from "vuedraggable";
import { file } from "../projectTask/data";

export default {
  components: {
    draggable
  },
  data() {
    return {
      tasksList: [],
      res: [
        {
          pkid: 0,
          groupName: "未分组的任务",
          taskList: [
            {
              pkid: 98,
              title: "this is the first task",
              creator: "祝建云"
            }
          ],
          taskListCont: 1
        },
        {
          pkid: 31,
          groupName: "分区1",
          taskList: [
            {
              pkid: 99,
              title: "分区1--task1",
              creator: "祝建云"
            },
            {
              pkid: 100,
              title: "分区1--task22",
              creator: "祝建云"
            },
            {
              pkid: 101,
              title: "分区1--task333",
              creator: "祝建云"
            }
          ],
          taskListCont: 4
        },
        {
          pkid: 32,
          groupName: "分区2",
          taskList: [
            {
              pkid: 103,
              title: "分区2--task1",
              creator: "祝建云"
            },
            {
              pkid: 104,
              title: "分区2--task22",
              creator: "祝建云"
            },
            {
              pkid: 105,
              title: "分区2--task333",
              creator: "祝建云"
            },
            {
              pkid: 106,
              title: "分区2--task4444",
              creator: "祝建云"
            }
          ],
          taskListCont: 4
        }
      ],
      fileList: [...file],
      parthsGroup: [
        {
          id: 0,
          groupTitle: "分组1",
          border: false,
          dragDisabled: false,
          list: [
            {
              Pkid: "404",
              Title: "屏幕快照 2018-12-24 上午10.44.17 (2)",
              YimageUrl:
                "/upload/file/20181224050137/ca823fe4-260b-4eee-bfd9-3d7fcf8b32b2/dd9a8e20-9686-45fc-bbaf-fbb9d2b8e44e.png",
              Url:
                "/upload/file/20181224050137/ca823fe4-260b-4eee-bfd9-3d7fcf8b32b2/dd9a8e20-9686-45fc-bbaf-fbb9d2b8e44e_s1.png",
              Titles:
                "/upload/file/20181224050137/ca823fe4-260b-4eee-bfd9-3d7fcf8b32b2/dd9a8e20-9686-45fc-bbaf-fbb9d2b8e44e_s0.png",
              ImageHeight: 608,
              ImageWidth: 1080,
              FileType: "png",
              Size: 0,
              Uname: "祝建云",
              IsCollection: false,
              CreateTime: "2018年12月24日 17:01"
            },
            {
              Pkid: "403",
              Title: "屏幕快照 2018-12-24 上午10.44.17",
              YimageUrl:
                "/upload/file/20181224050130/397969a5-5bd8-452a-99f6-76ddbe8d0738/e6e47bb3-d648-4aee-b78a-4b312d2ea75d.png",
              Url:
                "/upload/file/20181224050130/397969a5-5bd8-452a-99f6-76ddbe8d0738/e6e47bb3-d648-4aee-b78a-4b312d2ea75d_s1.png",
              Titles:
                "/upload/file/20181224050130/397969a5-5bd8-452a-99f6-76ddbe8d0738/e6e47bb3-d648-4aee-b78a-4b312d2ea75d_s0.png",
              ImageHeight: 675,
              ImageWidth: 1080,
              FileType: "png",
              Size: 0,
              Uname: "祝建云",
              IsCollection: false,
              CreateTime: "2018年12月24日 17:01"
            }
          ]
        }
      ],
      personalFilesShow: false,
      personalFiles: [
        {
          Pkid: "408",
          Title: "bg",
          Url:
            "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
          FileType: "svg",
          Uname: "祝建云"
        },
        {
          Pkid: "406",
          Title: "video2",
          Url:
            "/upload/file/20181224063835/472243c2-382a-423c-80ab-9ce41a6d790f/b3c9bc00-538f-4167-83b9-879676690519.mp4",
          FileType: "mp4",
          Uname: "祝建云"
        },
        {
          Pkid: 2,
          Title: "文件夹1",
          Url: null,
          FileType: 0,
          Uname: null,
          children: [
            {
              Pkid: "407",
              Title: "WX20181220-170213",
              Url:
                "/upload/file/20181224063844/2288995a-d1c0-4144-9c94-a8eb2a1c568c/3ab87050-c290-40df-9171-f2543be97c68.png",
              FileType: "png",
              Uname: "祝建云"
            },

            {
              Pkid: "405",
              Title: "video",
              Url:
                "/upload/file/20181224063835/e85c7a46-b564-46d5-8743-18e63e4a6588/eeb294ad-6ce7-4b86-950a-2869fd7814e8.mp4",
              FileType: "mp4",
              Uname: "祝建云"
            }
          ]
        },
        {
          Pkid: 3,
          Title: "文件夹2",
          Url: null,
          FileType: 0,
          Uname: null,
          children: [
            {
              Pkid: "408",
              Title: "bg",
              Url:
                "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
              FileType: "svg",
              Uname: "祝建云"
            }
          ]
        }
      ],
      dragFileId: "",
      moveOrClone: "move",
      dragDisabled: false,
      dragDisabled_personal: false,
      dragItem: {
        item: {},
        fromGroup: null,
        toGroup: null
      },
      navBar: [{id: 0, name: '个人文档'}]
    };
  },
  methods: {
    // 分区和任务的展开/折叠
    extendToggle(index) {
      this.tasksList[index].extend = !this.tasksList[index].extend;
      this.tasksList = this.tasksList.concat();
      $(".list_box")
        .eq(index)
        .slideToggle(400);
    },
    // 双击文件夹
    dbFolder(file) {
      if(!file.children) { return; }
      this.personalFiles = file.children;
      this.navBar.push({id: 1, name: file.Title});
    },
    // 返回上一级
    goBack(index) {
      if(index === this.navBar.length - 1) {
        return;
      }
      this.personalFiles = [
        {
          Pkid: "408",
          Title: "bg",
          Url:
            "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
          FileType: "svg",
          Uname: "祝建云"
        },
        {
          Pkid: "406",
          Title: "video2",
          Url:
            "/upload/file/20181224063835/472243c2-382a-423c-80ab-9ce41a6d790f/b3c9bc00-538f-4167-83b9-879676690519.mp4",
          FileType: "mp4",
          Uname: "祝建云"
        },
        {
          Pkid: 2,
          Title: "文件夹1",
          Url: null,
          FileType: 0,
          Uname: null,
          children: [
            {
              Pkid: "407",
              Title: "WX20181220-170213",
              Url:
                "/upload/file/20181224063844/2288995a-d1c0-4144-9c94-a8eb2a1c568c/3ab87050-c290-40df-9171-f2543be97c68.png",
              FileType: "png",
              Uname: "祝建云"
            },

            {
              Pkid: "405",
              Title: "video",
              Url:
                "/upload/file/20181224063835/e85c7a46-b564-46d5-8743-18e63e4a6588/eeb294ad-6ce7-4b86-950a-2869fd7814e8.mp4",
              FileType: "mp4",
              Uname: "祝建云"
            }
          ]
        },
        {
          Pkid: 3,
          Title: "文件夹2",
          Url: null,
          FileType: 0,
          Uname: null,
          children: [
            {
              Pkid: "408",
              Title: "bg",
              Url:
                "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
              FileType: "svg",
              Uname: "祝建云"
            }
          ]
        }
      ];
      this.navBar = [{id: 0, name: '个人文档'}];
    },
    // 文件移动时的回调函数
    fileMove(e, under) {
      // e.dragged 拖拽的元素
      // e.draggedContext 拖拽的元素的详情
      // e.draggedContext.element 拖拽的元素的内容
      // e.draggedContext.futureIndex / index 拖拽的元素的索引
      // e.to: 拖入区域
      // e.relatedContext: 拖入区域的上下文

      // console.log("fileMove", e);
      if(!this.dragItem.item.Title) {
        const item =  e.draggedContext.element;
        this.dragItem.item = Object.assign({}, item);
      }

    },
    // 文件开始移动时的回调函数
    dragStart(e, groupid) {
      this.dragFileId = $(e.item).attr("id");
      const filename = $(e.item).attr("filename");
      this.dragItem.fromGroup = $(e.from).attr('groupid');
      this.$nextTick(() => {
        const ele = $(`#${this.dragFileId}`);
        if(ele.attr('draggable') === 'false') {
          ele.addClass('dragging');
        }
      });

      // console.log("dragStart----", e);
      this.parthsGroup.push({
        id: this.parthsGroup.length,
        groupTitle: `分组${this.parthsGroup.length + 1}`,
        border: false,
        list: [],
        dragDisabled: false,
        temporary: true
      });
      // 判断是否重复
      this.judegRepeat(groupid, filename);
    },
    // 文件结束移动时的回调函数
    dragEnd(e) {
      this.dragItem.toGroup = $(e.to).attr('groupid');
      // console.log(e, this.dragItem);
      const from = this.dragItem.fromGroup;
      const to = this.dragItem.toGroup;
      const item = this.dragItem.item;
      if(from !== 'personal' && to === 'personal') { // 添加到个人文档--copy
      }else if(from === 'personal' && to !== 'personal') { // 从个人文档添加到我的操作中--copy
      }else if(from !== to) { // 移动--move
        let indexs = null;
        if(from === 'noGroup') {
          indexs = this.fileList.findIndex(x => x.Pkid === item.Pkid);
          indexs != -1 && this.fileList.splice(indexs, 1);
        }else if(parseInt(from) !== NaN) {
          const id = parseInt(from);
          let ids = this.parthsGroup.findIndex(x =>  parseInt(x.id) === id);
          if(ids !== -1) {
            indexs = this.parthsGroup[ids].list.findIndex(x => x.Pkid === item.Pkid);
            indexs != -1 && this.parthsGroup[ids].list.splice(indexs, 1);
          }
        }
      }   
      this.dragEndInit();
    },
    // 开始拖拽时判断是否有重复区域放置
    judegRepeat(groupid, filename) {
      let have = null;
      if(groupid === 'personal') {
        have = this.fileList.findIndex( x => x.Title.indexOf(filename) != -1);
        have !== -1 && (this.dragDisabled = true);
      }else {
        have = this.personalFiles.findIndex( x => x.Title.indexOf(filename) != -1);
        have !== -1 && (this.dragDisabled_personal = true);
      }
        
      for(let ele of this.parthsGroup) {
        let has = ele.list.findIndex( x => x.Title.indexOf(filename) != -1);
        if(ele.id.toString() === this.dragItem.fromGroup) {
          ele.border = true; 
          ele.dragDisabled = false;
        }else if(has != -1) {
          ele.border = false; 
          ele.dragDisabled = true;
        }else {
          ele.border = true; 
          ele.dragDisabled = false;
        }
      }
      this.parthsGroup = this.parthsGroup.concat();
    },
    // 拖拽结束后初始化数据
    dragEndInit() {
      this.dragFileId = null;
      this.dragItem = {
        item: {},
        fromGroup: null,
        toGroup: null
      }
      this.dragDisabled = false;
      this.dragDisabled_personal = false;
      for(let ele of this.parthsGroup) {
        ele.border = false; 
        ele.dragDisabled = false;
      }
      if (!this.parthsGroup[this.parthsGroup.length - 1].list.length) {
        this.parthsGroup.pop();
      } else {
        delete this.parthsGroup[this.parthsGroup.length - 1].temporary;
      }
    },

    // 是否显示个人文件
    personalFilesPull() {
      this.personalFilesShow = !this.personalFilesShow;
      if (this.personalFilesShow) {
        $("#personalFiles").css("right", 0);
      } else {
        $("#personalFiles").css("right", "-375px");
      }
    },
    // 获取任务列表
    getTaskList() {
      let obj = {
        projectId: 1253
      };
      this.tasksList = this.res;

      for(let y of this.fileList) {
        delete y.CreateTime;
        delete y.Edition;
        delete y.GroupId;
        delete y.Groupedition;
        delete y.ImageHeight;
        delete y.ImageWidth;
        delete y.Size;
        delete y.Titles;
        delete y.YimageUrl;
        delete y.collect;
      }
      for (let y of this.tasksList) {
        y.extend = true;
      }
      this.tasksList = this.tasksList.concat();
      return;
      this.$HTTP("post", "/task_getList", obj, $("#app")[0])
        .then(result => {
          this.tasksList = this.res;
          for (let y of this.tasksList) {
            y.extend = true;
          }
          this.tasksList = this.tasksList.concat();

          console.log("获取任务列表", this.tasksList);
        })
        .catch(err => {
          console.log("获取任务列表失败", err);
          this.$message.error("获取任务列表失败，请检查网络");
        });
    }
  },
  created() {
    this.getTaskList(); // 获取任务列表
  }
};
</script>
<style lang='less'>
@import "../../../../assets/css/base.less";
.task_detail_yun {
  position: fixed;
  top: 50px;
  left: 0;
  right: 0;
  bottom: 0;
  background: #ffffff;
  z-index: 11;
  width: 100%;
  height: calc(100vh - 50px);
  overflow: hidden;
  .iconfont:hover {
    color: @mainColor !important;
  }
  .detail_top {
    width: 100%;
    height: 50px;
    line-height: 50px;
    border-bottom: 1px solid #eee;
    padding: 0 24px;
    .box_sizing;
    overflow: hidden;
    .project_name {
      float: left;
    }
    .stage_list {
      height: 49px;
      display: inline-block;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      .stage_li {
        float: left;
        height: 47px;
        margin: 0 40px;
        .cur;
      }
      .stage_li_bottom {
        border-bottom: 2px solid #3684ff;
      }
      .stage_all {
        position: relative;
        .line {
          position: absolute;
          left: -40px;
          top: 17px;
          display: inline-block;
          width: 1px;
          height: 16px;
          background: #999;
        }
      }
    }
    .icon-guanbijiantou {
      float: right;
      color: #666;
      font-weight: 700;
    }
  }

  .detail_left {
    width: 200px;
    height: calc(100% - 51px);
    border-right: 1px solid #eee;
    float: left;
    padding-left: 24px;
    padding-top: 18px;
    .box_sizing;
    .task_group {
      .group_title {
        height: 32px;
        line-height: 32px;
        .iconfont {
          color: @grayNight;
          font-weight: bold;
        }
        span {
          font-weight: bold;
          margin-top: 10px;
        }
        .icon-unfold {
          .dis-in-bl;
          -webkit-transition: transform 0.25s linear;
          -moz-transition: transform 0.25s linear;
          -o-transition: transform 0.25s linear;
          transition: transform 0.25s linear;
        }
        .icon-unfold_rotate {
          transform: rotate(-180deg);
          -webkit-transition: transform 0.25s linear;
          -moz-transition: transform 0.25s linear;
          -o-transition: transform 0.25s linear;
          transition: transform 0.25s linear;
        }
      }
      .group_list {
        height: 32px;
        line-height: 32px;
        p {
          margin-left: 20px;
          width: 140px;
          .word_over;
          .cur;
        }
      }
    }
  }

  .detail_main {
    float: right;
    width: calc(100% - 201px);
    height: calc(100% - 51px);
    position: relative;
    .top_operate {
      width: 100%;
      height: 50px;
      line-height: 50px;
      border-bottom: 1px solid #eee;
    }

    .every_file {
      width: 100px;
      height: 50px;
      border: 1px solid #33ccff;
      .border_radius(@br: 2px;);
      text-align: center;
      padding: 30px 10px;
      .file_title {
        width: 100%;
        height: 30px;
        .word_over;
      }
    }
    .dragging {
      border-color: @line !important;
    }
    .detail_file {
      width: 100%;
      height: calc(100% - 51px);
      background: #f0fcff;
      overflow: hidden;

      .left_box {
        float: left;
        width: 220px;
        height: 100%;
        .left_file_list {
          width: 100%;
          height: 100%;
          padding: 20px;
          .box_sizing;
          overflow: auto;
          .every_file {
            margin: 0 auto;
            margin-top: 20px;
          }
        }
      }
      .right_box {
        float: right;
        width: calc(100% - 221px);
        height: 100%;
        border-left: 1px solid #eee;
        .parths {
          width: 100%;
          height: 100%;
          overflow: auto;
          .parths_group {
            width: 100%;
            padding: 20px;
            .box_sizing;

            .group_top {
              width: 100%;

              .group_name {
                font-weight: bold;
              }
              .group_file {
                width: 100%;
                min-height: 170px;
                margin-left: -20px;
                padding-bottom: 20px;
                border: 1px solid transparent;
                overflow: hidden;
                .box_sizing;
                .every_file {
                  float: left;
                  margin-left: 20px;
                  margin-top: 20px;
                }
              }
              .draggable {
                min-height: 170px;
              }
              .group_border {
                border: 1px dashed @mainColor;
                background: #fff;
                opacity: 0.5;
              }
            }
          }
        }
      }
    }

    .personal_files {
      position: absolute;
      top: 0;
      right: -375px;
      width: 400px;
      height: 100%;
      overflow: hidden;
      .left_pull {
        width: 24px;
        height: 100%;
        text-align: center;
        position: relative;
        float: left;
        span {
          position: absolute;
          top: 50%;
          transform: translateY(-50%);
          border: 1px solid @line;
          border-right: 0 solid transparent;
          padding: 6px;
          .cur;
          .border_radius(@br: 4px;);

          word-break: normal;
          width: auto;
          display: block;
          white-space: pre-wrap;
          word-wrap: break-word;
          overflow: hidden;
        }
      }
      .personal_files_main {
        width: 376px;
        height: 100%;
        float: left;
        background: #e0f0e9;
        .border_radius(@br: 4px;);
        .p_f_top {
          width: 100%;
          height: 32px;
          line-height: 32px;
        }
        .p_f_filter {
          width: 100%;
          height: 32px;
          line-height: 32px;
          border-bottom: 1px solid @line;
        }
        .p_f_list {
          width: 100%;
          height: calc(100% - 33px * 3);
          overflow: auto;
          padding: 24px;
          .box_sizing;
          .draggable {
            width: 100%;
            height: 100%;
          }
          .every_file {
            float: left;
            margin-left: 20px;
            margin-top: 20px;
          }
        }
        .p_f_bottom {
          width: 100%;
          height: 32px;
          line-height: 32px;
          border-top: 1px solid @line;
        }
      }
    }
  }
  .ghost_file {
    background: #33ccff !important;
    // height: 2px !important;
    // padding: 0 !important;
    // .file_title,
    // .from_who {
    //   display: none;
    // }
    // overflow: hidden;
  }
  .drag_file {
    // background-color: green !important;
  }
  .chosen_file {
    // background-color: red;
  }
}
</style>


