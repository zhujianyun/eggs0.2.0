<template>
    <div class="full_preview" @click.stop='() => {}'>
        <div class="top_operate">
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
                v-for='stage in fullPreview.stageList'
                :key='stage.stageId'
                >
                <div class="stage_top">{{stage.stageName}}</div>
                <div class="stage_file">
                    <div 
                        class="file_group"
                        v-for='group in stage.fileGroupList'
                        :key='group.groupId'
                        >
                        <p class="group_title">{{group.groupName ? group.groupName : '未分组文件'}}</p>
                        <div class="group_list clearfix">
                            <div class="file_box"
                                v-for='file in group.fileList'
                                :key='file.Pkid'
                            >
                                <span class="file_img">
                                    <img :src="'http://server.apexgame.cn'+file.Url" alt="">
                                    <span class="none"></span>
                                </span>
                                <p class="file_title word_over">{{file.Title}}</p>
                            </div>
                        </div>
                    </div>
                    <div v-if='!stage.fileGroupList.length' class="file_group_empty">
                        <span class="empty_img">
                            <img src="./style/file_empty.png" alt="">
                        </span>
                        <p>暂无文件</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props: ['taskId'],
    data() {
        return {
            fullPreview: {},
        }
    },
    methods: {
        // 获取整体预览的信息
        getFullPreview() {
            // 发送请求
            this.fullPreview = {
                taskTitle: '邮箱注册',
                stageList: [
                    {
                        stageId: 0,
                        stageName: '产品规划',
                        fileGroupList: [
                            {
                                groupId: 0,
                                groupName: '',
                                fileList: [
                                    {
                                        Pkid: "4085",
                                        Title: "egLogo1.png",
                                        Url:
                                            "/upload/file/20181227065330/63f7b3f6-f448-448f-b26a-6d6a12741f03/7f31dfb2-196a-48b6-89bc-aa2652013506.png",
                                        FileType: "png",
                                        Uname: "祝建云",
                                    },
                                    {
                                        Pkid: "6575",
                                        Title: "详情页面1",
                                        Url:
                                            "/upload/file/20180524103014/41735179-c2d4-45b8-85ca-da4681d5b3af/01.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                ]
                            },
                            {
                                groupId: 1,
                                groupName: '分组1',
                                fileList: [
                                    {
                                        Pkid: "6573",
                                        Title: "详情页面-2",
                                        Url:
                                            "/upload/file/20181226081958/5e77da48-39a0-491f-a5db-c23cd5a4023d/b4f00229-8439-4f78-b0a1-957644a58492_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                ]
                            },
                            {
                                groupId: 2,
                                groupName: '这是第二个分组',
                                fileList: [
                                    {
                                        Pkid: "6574",
                                        Title: "详情页面-3",
                                        Url:
                                            "/upload/file/20180524103014/489053b4-6574-47b0-ace3-22101721e612/2.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                    {
                                        Pkid: "6572",
                                        Title: "详情页面-4",
                                        Url:
                                            "/upload/file/20181226081939/d8e7a662-6f0a-4b41-ba0d-f359aaa3fb5b/2113c63f-2b0b-487a-a3cc-d78b301001b8_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                    {
                                        Pkid: "6570",
                                        Title: "详情页面-5",
                                        Url:
                                            "/upload/file/20181226081938/90571b62-18cb-45aa-a8f0-1f3e30178176/ae3a890a-f693-4023-8b14-f4d960ca0c73_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                ]
                            },
                        ]
                    },
                    {
                        stageId: 1,
                        stageName: '产品设计',
                        fileGroupList: [
                            {
                                groupId: 3,
                                groupName: '',
                                fileList: [
                                    {
                                        Pkid: "6571",
                                        Title: "详情页面-6",
                                        Url:
                                            "/upload/file/20181226081938/ea47a6ae-e9f8-43d6-a63f-08d3c2bb3373/bdca1df3-3343-4381-bed0-8eecf050cd98_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                    {
                                        Pkid: "6569",
                                        Title: "详情页面-无分组",
                                        Url:
                                            "/upload/file/20181226081938/e29e3cec-30ce-4670-8d12-acd4c6a372c3/0d4dfecd-439b-418d-b368-0e2ec3d6206d_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                    {
                                        Pkid: "6568",
                                        Title: "详情页面-文件分组最小化",
                                        Url:
                                            "/upload/file/20181226081938/95216f40-6b6f-43e7-b8ab-a62c285f8b00/a1ec02b1-a3bd-4cb1-afc2-8bd9787c4d03_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                    {
                                        Pkid: "6567",
                                        Title: "详情页面-文件移动n",
                                        Url:
                                            "/upload/file/20181226081938/8f8daa93-ffb1-4445-b4cb-7f79ed5fd2ce/5bc9a050-5e1a-44df-8ac2-0f52d5030175_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    },
                                ]
                            },
                        ]
                    },
                    {
                        stageId: 2,
                        stageName: '开发阶段',
                        fileGroupList: []
                    },
                    {
                        stageId: 3,
                        stageName: '测试',
                        fileGroupList: [
                            {
                                groupId: 10,
                                groupName: '',
                                fileList: [
                                    {
                                        Pkid: "407",
                                        Title: "WX20181220-170213",
                                        Url:
                                            "/upload/file/20180528070747/8b10d083-a85b-45a4-825a-9437fd47fa4a/logo-3.png",
                                        FileType: "png",
                                        Uname: "祝建云"
                                    },
                                    {
                                        Pkid: "405",
                                        Title: "video",
                                        Url:
                                            "/upload/file/20180711020446/9ccd7618-5010-40f4-ac2e-008e64108a22/165310yogoihqr63svh6tm.jpg",
                                        FileType: "mp4",
                                        Uname: "祝建云"
                                    }
                                ]
                            },
                            {
                                groupId: 12,
                                groupName: '我的分组啊',
                                fileList: [
                                    {
                                        Pkid: "6557",
                                        Title: "详情页面-多选文件样式",
                                        Url:
                                            "/upload/file/20181226081929/729c03fc-fd7c-41c1-99e8-2fcd1e63dd3a/c96654d0-0582-4809-8359-ecea14d26148_s1.png",
                                        FileType: "png",
                                        Uname: "卢洪臣",
                                    }
                                ]
                            },
                        ]
                    },
                ]
            }
        },
    },
    created() {
        this.getFullPreview(this.taskId);
    }
};
</script>
<style lang='less'>
.full_preview {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 10;
  background: #fff;

  .full_preview_list {
      width: 100%;
      height: calc(100% - 51px);
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
}
</style>

