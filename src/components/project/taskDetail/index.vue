<template>
    <div class='task_detail_yun'>
      <!-- 头部阶段 -->
      <div class="detail_top">
          <div class="project_name">EGGS产品开发</div>
          <div class="stage_list">
            <el-tabs v-model="selectStage" @tab-click="stageChange">
              <el-tab-pane 
                v-for='stage in stageList'
                :key='stage.stageId'
                :label="stage.title" 
                :name="stage.stageId"
                >
                  <span slot="label">
                    <span class="round">
                      <!-- <i v-if='stage.state === 1' class="iconfont icon-xuanzhong"></i> -->
                    </span>
                    {{stage.title}}
                  </span>
                </el-tab-pane>
            </el-tabs>
            <div class='stage_all' :class="fullPreviewShow ? 'select_stage_all' : ''">
                <span class='line'></span>
                <span @click='fullPreview'>整体预览</span> 
            </div>
          </div>
          <i class='iconfont icon-guanbijiantou'></i>
      </div>
      <!-- 左侧目录 -->
      <div class="detail_left">
          <div 
              class="task_group"
              v-for="(group, index) in this.tasksList"
              :key="group.pkid"
              >
              <div class="group_title">
                  <i 
                      class='iconfont icon-unfold'
                      :class="group.extend ? '' : 'icon_rotate'"
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
                          <p 
                            :class="list.pkid === selectTaskId ? 'select_task every' : 'every'"
                            @click="taskChange(list.pkid)"
                          >{{list.title}}</p>
                      </div>
                  </div>
              </transition-group>
          </div>
          
      </div>
      <div class="detail_main">
          <!-- 头部操作按钮 -->
          <div class="top_operate">
            <div class="t_o_left fl">
              <span :class="demandOrGain ? '' : 'demand_gain'" @click="demandOrGainChange(false)">相关需求</span>
              <span class="line"></span>
              <span :class="demandOrGain ? 'demand_gain' : ''" @click="demandOrGainChange(true)">成果文件</span>
            </div>
            <div class="t_o_right fr">
              <span class="market_state">
                <i class='iconfont icon-wancheng'></i>
                状态标记
              </span>
              <span class="line"></span>
              <i class='iconfont icon-haoyou1'></i>
              <i class='iconfont icon-rili1'></i>
              <span class="line"></span>
              <span class="self_file" @click='personalFilesPull'>
                <i class='iconfont icon-gerenwenjianjia'></i>
                个人文档
              </span>
            </div>
          </div>
          <!-- 文件的所有展示 -->
          <div class="detail_file">
              <!-- 任务文件 -->
              <div id="operateFile" class="operate_file">
                <div class="top_box">
                  <div class="left fl">
                    <i class='iconfont icon-shangchuan'></i>
                    <i 
                      class='iconfont icon-tianjiawenzi'
                      @click.stop="inputTextShowToggle('left')"
                      ></i>
                    <span class="line"></span>
                    <el-checkbox class="all_checked" v-model="fileCheckbox">全选</el-checkbox>
                  </div>
                  <div class="right fr">
                    <span class="">
                      <el-dropdown class="fixed file_more">
                        <span class="el-dropdown-link"><i class='iconfont icon-fenzu1'></i>分组管理</span>
                        <el-dropdown-menu slot="dropdown">
                          <el-dropdown-item @click.native="groupCommand('create')">新建分组</el-dropdown-item>
                          <el-dropdown-item @click.native="groupCommand('sort')">分组排序</el-dropdown-item>
                        </el-dropdown-menu>
                      </el-dropdown>
                    </span>
                      <span class="toggle" @click='viewToggles'>
                      <i class='iconfont icon-liebiao'></i>
                      视图切换
                    </span>
                  </div>

                  <!-- 输入文字 -->
                  <div 
                    v-if='inputTextShow1'
                    class="input_text"
                    @click.stop='() => {}'
                    >
                    <textarea 
                      id="inputText"
                      class="textarea" 
                      rows="4"
                      placeholder="请输入文字内容"
                      v-model='inputText'
                      @focus="inputTextFocus"
                      @blur="inputTextBlur"
                      ></textarea>
                    <button class="main_button_bg fr" @click="inputTextSure('left')">添加</button>
                    <button class="main_button fr" @click="inputTextCancel('left')">取消</button>
                  </div>
                </div>
                <div v-if='viewToggle' class="view">
                  <div id="leftBox" class="left_box">
                    <div v-if='(fileList && fileList.length) || dragItem.fromGroup' class="left_file_list">
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
                                <span class="file_pic">
                                  <img :src="'http://server.apexgame.cn'+ele.Url" alt="">
                                  <span class="none"></span>
                                </span>
                                <div class="file_info">
                                  <p class="title">{{ele.Title}}</p>
                                  <img src="http://server.apexgame.cn/upload/user/20180719110629240.jpeg" alt="" class="from_header">
                                  <span class="file_message fr">
                                    <i class='iconfont icon-pinglun'></i>
                                    12
                                  </span>
                                  <span class="fixed file_checkbox">
                                    <el-checkbox v-model="fileCheckbox"></el-checkbox>
                                  </span>
                                  <el-dropdown class="fixed file_more">
                                    <span class="el-dropdown-link"><i class='iconfont icon-gengduo'></i></span>
                                    <el-dropdown-menu slot="dropdown">
                                      <el-dropdown-item @click.native="fileCommand('download')">下载</el-dropdown-item>
                                      <el-dropdown-item @click.native="fileCommand('collect')">收藏</el-dropdown-item>
                                      <el-dropdown-item @click.native="fileCommand('transfer')">移交</el-dropdown-item>
                                      <el-dropdown-item @click.native="fileCommand('rename')">重命名</el-dropdown-item>
                                      <el-dropdown-item @click.native="fileCommand('delete')">删除</el-dropdown-item>
                                    </el-dropdown-menu>
                                  </el-dropdown>
                                </div>
                            </div>
                            <div class="null"></div>
                        </draggable>
                    </div>
                    <div v-else class="file_empty">
                      <div class="empty_img">
                        <img src="./style/file_empty.png" alt="">
                      </div>
                      <p class="title">文件拖到此区域即可上传，支持批量上传</p>
                    </div>
                  </div>
                  <div id="rightBox" class="right_box">
                      <div v-if='(parthsGroup && parthsGroup.length) || dragItem.fromGroup' class="parths">
                          <div 
                            class="parths_group"
                            v-for="(group, index) in parthsGroup"
                            :key="group.id"
                            >
                            <!-- 分组的头部操作 -->
                            <div class="group_top">
                                <span v-if='!group.edit' class="group_name">{{group.groupTitle}}</span>
                                  <input 
                                    v-else 
                                    :ref="group.createdGroup ? 'createdGroup' : ''" 
                                    class="group_name edit" 
                                    type="text" 
                                    v-model='group.groupTitle'
                                    @blur='groupTitleBlur(group)'
                                    />
                                <div class="group_operate">
                                  <span 
                                    v-if='group.packUp' 
                                    @click='groupExtendToggle(index)'
                                    class='mainColor_underline_text'
                                    >收起</span>{{group.packUp}}
                                    <el-dropdown class="fixed file_more">
                                      <span class="el-dropdown-link"><i class='iconfont icon-gengduo1'></i></span>
                                      <el-dropdown-menu slot="dropdown">
                                        <el-dropdown-item @click.native="fileGroupCommand('upload')">上传文件</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileGroupCommand('download')">下载</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileGroupCommand('collect')">收藏</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileGroupCommand('transfer')">整组移交</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileGroupCommand('rename')">重命名</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileGroupCommand('delete')">删除</el-dropdown-item>
                                      </el-dropdown-menu>
                                    </el-dropdown>
                                </div>
                            </div>

                            <!-- 折叠成一个的时候 -->
                            <div 
                              v-if='group.allList'
                              :key="group.id"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? 'drag_in' : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.id'
                                v-model="group.allList"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.id)'
                                @end='dragEnd'
                                >
                                <div 
                                  class="every_file fold_up"
                                    v-if='item.overLength'
                                  v-for="item in group.allList"
                                  :key="item.Pkid"
                                  :groupid='group.id'
                                  :id='item.Pkid'
                                  :filename='item.Title'
                                  @click='groupExtendToggle(index)'
                                  >
                                    <span class="file_pic">
                                      <span
                                        class="more" 
                                        v-for='(pic, index) in item.Url'
                                        :key='index'
                                        >
                                          <img :src="'http://server.apexgame.cn' + pic" alt="
                                        ">
                                      </span>
                                      <span class="more"><img class="more" src="./style/more.png" alt=""></span>
                                      <span class="none"></span>
                                    </span>
                                    <div class="file_info">
                                      <p class="file_length"><span class="mainColor">{{group.list.length}}</span>个文件</p>
                                    </div>

                                </div>
                                <div class="null"></div>
                              </draggable>
                            </div>

                            <!-- 折叠成一行的时候 -->
                            <div 
                              v-else-if='group.overList && !leftCenterFlag'
                              :key="group.id"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? 'drag_in' : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.id'
                                v-model="group.overList"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.id)'
                                @end='dragEnd'
                                >
                                  <div 
                                    class="every_file fold_up"
                                    :class="item.overLength ? '' : 'draged'"
                                    v-for="item in group.overList"
                                    :key="item.Pkid"
                                    :groupid='group.id'
                                    :id='item.Pkid'
                                    :filename='item.Title'
                                    @click='groupExtendToggle(index)'
                                    >
                                    <template v-if='item.overLength'>
                                      <span class="file_pic">
                                        <span
                                          class="more" 
                                          v-for='(pic, index) in item.Url'
                                          :key='index'
                                          >
                                            <img :src="'http://server.apexgame.cn' + pic" alt="
                                          ">
                                        </span>
                                        <span class="more"><img class="more" src="./style/more.png" alt=""></span>
                                        <span class="none"></span>
                                      </span>
                                      <div class="file_info">
                                        <p class="file_length underline_text">展开更多<span class="mainColor">{{item.overLength}}</span>个文件</p>
                                      </div>
                                    </template>
                                    <template v-else>
                                      <span class="file_pic">
                                        <img :src="'http://server.apexgame.cn'+item.Url" alt="">
                                        <span class="none"></span>
                                      </span>
                                      <div class="file_info">
                                        <p class="title">{{item.Title}}</p>
                                        <img src="http://server.apexgame.cn/upload/user/20180719110629240.jpeg" alt="" class="from_header">
                                        <span class="file_message fr">
                                          <i class='iconfont icon-pinglun'></i>
                                          12
                                        </span>
                                        <span class="fixed file_checkbox">
                                          <el-checkbox v-model="fileCheckbox"></el-checkbox>
                                        </span>
                                        <el-dropdown class="fixed file_more">
                                          <span class="el-dropdown-link"><i class='iconfont icon-gengduo'></i></span>
                                          <el-dropdown-menu slot="dropdown">
                                            <el-dropdown-item @click.native="fileCommand('download')">下载</el-dropdown-item>
                                            <el-dropdown-item @click.native="fileCommand('rename')">重命名</el-dropdown-item>
                                            <el-dropdown-item @click.native="fileCommand('delete')">删除</el-dropdown-item>
                                          </el-dropdown-menu>
                                        </el-dropdown>
                                      </div>
                                    </template>
                                    <div class="null"></div>
                                  </div>
                              </draggable>

                            </div>

                            <!-- 展开的时候 -->
                            <div 
                              v-else-if='group.list'
                              :key="group.id"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? (group.list.length ? 'group_border' : 'drag_in') : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.id'
                                v-model="group.list"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.id)'
                                @end='dragEnd'
                                >
                                <div 
                                  class="every_file fold_up draged"
                                  v-for="item in group.list"
                                  :key="item.Pkid"
                                  :groupid='group.id'
                                  :id='item.Pkid'
                                  :filename='item.Title'
                                  >
                                  <span class="file_pic">
                                    <img :src="'http://server.apexgame.cn'+item.Url" alt="">
                                    <span class="none"></span>
                                  </span>
                                  <div class="file_info">
                                    <p class="title">1--{{item.Title}}</p>
                                    <img src="http://server.apexgame.cn/upload/user/20180719110629240.jpeg" alt="" class="from_header">
                                    <span class="file_message fr">
                                      <i class='iconfont icon-pinglun'></i>
                                      12
                                    </span>
                                    <span class="fixed file_checkbox">
                                      <el-checkbox v-model="fileCheckbox"></el-checkbox>
                                    </span>
                                    <el-dropdown class="fixed file_more">
                                      <span class="el-dropdown-link"><i class='iconfont icon-gengduo'></i></span>
                                      <el-dropdown-menu slot="dropdown">
                                        <el-dropdown-item @click.native="fileCommand('download')">下载</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileCommand('rename')">重命名</el-dropdown-item>
                                        <el-dropdown-item @click.native="fileCommand('delete')">删除</el-dropdown-item>
                                      </el-dropdown-menu>
                                    </el-dropdown>
                                  </div>
                                </div>
                                <div class="null"></div>
                              </draggable>

                            </div>

                            <div class="group_line"></div>
                          </div>
                      </div>
                      
                      <div v-else class="file_empty">
                        <div class="empty_img">
                          <img src="./style/group_empty.png" alt="">
                        </div>
                        <p class="title">文件拖到此区域设置分组</p>
                      </div>

                      <div v-if='fileList.length || parthsGroup.length' class="left_right_toggle" @click='leftRightToggle'>
                        <i 
                          class="iconfont icon-tubiaozhizuomoban"
                          :class="leftCenterFlag ? 'icon_rotate' : ''"
                          ></i>
                      </div>
                  </div>
                </div>

                <!-- 切换视图 另一个视图 -->
                <other-view
                  v-if='!viewToggle'
                />
              </div>
              <!-- 个人文档 -->
              <div v-if="personalFilesShow" id="personalFiles" class="personal_files">
                <div class="top_box">
                  <div class="left fl">
                    <i class='iconfont icon-shangchuan'></i>
                    <i 
                      class='iconfont icon-tianjiawenzi'
                      @click.stop="inputTextShowToggle()"
                      ></i>
                    <i 
                      class='iconfont icon-xinjianfenzu'
                      @click='newFolder'
                      ></i>
                    <span class="line"></span>
                    <el-checkbox class="" v-model="fileCheckboxSelf"></el-checkbox>
                  </div>
                  <div class="right fr">
                    <i class='iconfont icon-sousuo'></i>
                    <i class='iconfont icon-filter'></i>
                  </div>

                  <!-- 输入文字 -->
                  <div 
                    v-if='inputTextShow2'
                    class="input_text"
                    @click.stop='() => {}'
                    >
                    <textarea 
                      id="inputText"
                      class="textarea" 
                      rows="4"
                      placeholder="请输入文字内容"
                      v-model='inputText'
                      @focus="inputTextFocus"
                      @blur="inputTextBlur"
                      ></textarea>
                    <button class="main_button_bg fr" @click="inputTextSure()">添加</button>
                    <button class="main_button fr" @click="inputTextCancel()">取消</button>
                  </div>
                </div>
                <div class="personal_files_main">
                  <div class="p_f_list">
                      <div class="nav_bar" v-if='navBar.length - 1'>
                        <span
                          v-for="(bar, index) in navBar"
                          :key='bar.id'
                          @click='goBack(index)'
                        >
                          <span v-if='index' class='span1 fl'>></span>
                          <span class='cur fl' :class="index === navBar.length - 1 ? 'mainColor' : ''">{{bar.name}}</span>
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
                            <!-- 文件夹 -->
                            <template v-if='file.children'>
                              <span class="file_pic">
                                <img src="./style/wenjianjia.png" alt="">
                                <span class="none"></span>
                              </span>
                              <div class="file_info">
                                <p v-if='!file.edit' class="title">{{file.Title}}</p>
                                <input 
                                  v-else 
                                  :ref="file.createdFolder ? 'createdFolder' : ''" 
                                  class="title edit" 
                                  type="text" 
                                  v-model='file.Title'
                                  @blur='folderBlur(file)'
                                  />
                                <p class="file_num">{{file.children.length}}个文件</p>
                                <!-- <span class="fixed file_checkbox">
                                  <el-checkbox v-model="fileCheckbox"></el-checkbox>
                                </span> -->
                              </div>
                            </template>
                            <!-- 文件 -->
                            <template v-else>
                                <span class="file_pic">
                                <img :src="'http://server.apexgame.cn'+file.Url" alt="">
                                <span class="none"></span>
                              </span>
                              <div class="file_info">
                                <p class="title">{{file.Title}}</p>
                                <p class="file_num">123KB</p>
                                <span class="fixed file_checkbox">
                                  <el-checkbox v-model="fileCheckbox"></el-checkbox>
                                </span>
                                <el-dropdown class="fixed file_more">
                                  <span class="el-dropdown-link"><i class='iconfont icon-gengduo'></i></span>
                                  <el-dropdown-menu slot="dropdown">
                                    <el-dropdown-item @click.native="fileCommand('download')">下载</el-dropdown-item>
                                    <el-dropdown-item @click.native="fileCommand('rename')">重命名</el-dropdown-item>
                                    <el-dropdown-item @click.native="fileCommand('delete')">删除</el-dropdown-item>
                                  </el-dropdown-menu>
                                </el-dropdown>
                              </div>
                            </template>
                        </div>
                        <div v-if='navBar.length - 1 && !personalFiles.length' class="folder_empty">
                          <p>该文件夹为空</p>
                          <p>您可以从任务中拖拽或从本地上传</p>
                        </div>
                      </draggable>
                  </div>
                </div>
              </div>
          </div>

          <!-- 整体预览 -->
          <full-preview
            v-if='fullPreviewShow'
            taskId='1'
          />
      </div>

    </div>
</template>
<script>
import draggable from "vuedraggable";
import FullPreview from './fullPreview';
import OtherView from './otherView';
export default {
  components: {
    draggable,
    FullPreview,
    OtherView
  },
  data() {
    return {
      stageList: [
        {
          stageId: "1",
          title: "产品规划",
          state: 1
        },
        {
          stageId: "2",
          title: "产品设计",
          state: 2
        },
        {
          stageId: "3",
          title: "开发阶段",
          state: 3
        },
        {
          stageId: "4",
          title: "测试",
          state: 4
        }
      ], // 阶段列表
      selectStage: "1", // 当前选择的阶段
      tasksList: [], // 左侧分组任务列表
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
      selectTaskId: null, // 现在选择的任务
      demandOrGain: false, // false--相关需求 true--成果文件
      fileCheckbox: false, // 文件全选
      viewToggle: true, // true--视图1  false--视图2
      fileCheckboxSelf: false, // 个人文档
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
        {
          Pkid: "6573",
          Title: "详情页面-2",
          Url:
            "/upload/file/20181226081958/5e77da48-39a0-491f-a5db-c23cd5a4023d/b4f00229-8439-4f78-b0a1-957644a58492_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
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
        {
          Pkid: "6566",
          Title: "详情页面-输入文字",
          Url:
            "/upload/file/20181226081938/3afc6cd3-72ce-45c0-b4a2-e9e47b73a6d2/82dfd666-49a2-4c15-928c-6dc9b5dfeea3_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6565",
          Title: "详情页面-上传文件to成果文件",
          Url:
            "/upload/file/20181226081931/55e92e54-1075-43ab-a8ba-74ced18d6121/9a56d16e-6246-4b48-9ea4-3276bc2cdce9_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6564",
          Title: "详情页面-上传文件to个人文档",
          Url:
            "/upload/file/20181226081931/5ff72ed8-1c2d-4323-bfe4-7e3d45ee83dc/7fd6baae-cb04-4207-9cbf-fa53d399a978_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6563",
          Title: "详情页面-删除文件夹与删除文件提示",
          Url:
            "/upload/file/20181226081931/819c9db1-70a8-453d-b739-a978be80585f/ce970ac7-d760-4be3-8969-316e22cf6efd_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
          Edition: "1",
        },
        {
          Pkid: "6562",
          Title: "详情页面-空页面",
          Url:
            "/upload/file/20181226081930/14707eb6-6f74-4819-844c-76afc08de9b9/8f37b596-48ae-4262-b241-beee53d646a3_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6561",
          Title: "详情页面-进入文件夹--文件夹为空",
          Url:
            "/upload/file/20181226081930/83ecf1e5-5f21-44c6-bcf7-48e391b99e19/fe95c51a-3ab0-4ab2-8826-c5e925447b4a_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6560",
          Title: "详情页面-进入文件夹--出现面包屑---文件hover的操作",
          Url:
            "/upload/file/20181226081930/5db23181-724c-42c0-b036-7d4471ec6247/f391c292-ac6b-4ab2-b787-94ea6cd07e26_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6559",
          Title: "详情页面-分组文件的展开与收起样式",
          Url:
            "/upload/file/20181226081929/4cf89dc7-18e1-456b-ad93-8b687cbf48e4/9713e604-fec5-4298-a28b-9e2eb11fe414_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6558",
          Title: "详情页面-成果文件分组更多操作",
          Url:
            "/upload/file/20181226081929/82b8f088-2c08-4728-a739-97760c0c3d61/dd3b3458-ddf1-415e-9b75-af241125224e_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        },
        {
          Pkid: "6557",
          Title: "详情页面-多选文件样式",
          Url:
            "/upload/file/20181226081929/729c03fc-fd7c-41c1-99e8-2fcd1e63dd3a/c96654d0-0582-4809-8359-ecea14d26148_s1.png",
          FileType: "png",
          Uname: "卢洪臣",
        }
      ], // 未分组文件列表
      parthsGroup: [
        {
          id: 0,
          groupTitle: "分组1",
          border: false,
          dragDisabled: false,
          packUp: null,
          list: [
            {
              Pkid: "404",
              Title: "附件1",
              Url:
                "/upload/file/20181227072023/3c36843c-24be-4d34-9f88-38aadcd9e617/ced7bc91-99d6-4345-ba44-292e4a206593.png",
              FileType: "png",
              Uname: "祝建云"
            },
            {
              Pkid: "403",
              Title: "赫本",
              Url:
                "/upload/file/20181227072001/e9e95bcc-11f0-4470-914e-87cb65be398b/4e822aa0-2b99-4bc0-b58d-48725929ef4b.png",
              FileType: "png",
              Uname: "祝建云"
            }
          ]
        }
      ], // 分组文件列表
      personalFilesShow: false, // 个人文档是否显示
      personalFiles: [
        {
          Pkid: "408",
          Title: "bg",
          Url:
            "/upload/file/20181213070700/d0b06e24-5111-497c-a512-8d4b139fd475/55924df2-63b6-4914-ade4-0ba667d577db_s1.png",
          FileType: "svg",
          Uname: "祝建云"
        },
        {
          Pkid: "406",
          Title: "video2",
          Url:
            "/upload/file/20180817071340/5e0dba23-b76a-43a6-9589-1aee0ae38fdb/f7088adf-dbc4-4b83-994a-5d69c4a66c0d_s1.png",
          FileType: "mp4",
          Uname: "祝建云"
        },
        {
          Pkid: 2,
          Title: "我的文件",
          Url: null,
          FileType: 0,
          Uname: null,
          default: true,
          children: [
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
          Pkid: 3,
          Title: "我的收藏",
          Url: null,
          FileType: 0,
          Uname: null,
          default: true,
          children: [
            {
              Pkid: "408",
              Title: "bg",
              Url:
                "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
              FileType: "svg",
              Uname: "祝建云"
            },
            {
              Pkid: 5,
              Title: "文件夹2--第二个文件夹中文件夹",
              Url: null,
              FileType: 0,
              Uname: null,
              children: []
            }
          ]
        },
        {
          Pkid: 4,
          Title: "文件夹3--工作文件夹之我的项目",
          Url: null,
          FileType: 0,
          Uname: null,
          children: []
        }
      ], // 个人文档列表
      dragFileId: "", // 当前拖拽文件的ID
      moveOrClone: "move", // 是移动还是copy
      dragDisabled: false, // 未分组是否禁止拖拽
      dragDisabled_personal: false, // 个人文档是否禁止拖拽
      dragItem: {
        item: {},
        fromGroup: null,
        toGroup: null
      }, // 当前拖拽文件的详情
      navBar: [{ id: 0, name: "个人文档" }], // 个人文档的文件层级
      fileBoxW: 182, // 188 // 文件元素盒子的宽度
      leftBoxW: 210, // 未分组盒子的宽度
      personalBoxW: 388, // 个人文档盒子的宽度
      leftCenterFlag: false, // true--未分组占大份 false--分组区域占大份
      inputTextShow1: false, // 输入文字弹窗——任务文件
      inputTextShow2: false, // 输入文字弹窗——个人文档
      inputText: '', // 添加文字的内容
      fullPreviewShow: false, // 整体预览是否显示
    };
  },
  watch: {},
  methods: {
    // 文件视图切换
    viewToggles() {
      this.viewToggle = !this.viewToggle;
    },
    // 输入文字的显示/隐藏
    inputTextShowToggle(flag) {
      if(flag === 'left') { // 任务文件
        this.inputTextShow1 = true;
        if(this.inputTextShow2) {
          this.inputTextShow2 = false;
          this.inputText = '';
        }
      }else { // 个人文档
        this.inputTextShow2 = true;
        if(this.inputTextShow1) {
          this.inputTextShow1 = false;
          this.inputText = '';
        }
      }
      let clickHide = e => {
        if(flag === 'left') { // 任务文件
          this.inputTextShow1 = false;
        }else { // 个人文档
          this.inputTextShow2 = false;
        }
        $(document).unbind("click", clickHide)

      };
      $(document).bind("click", clickHide)
    },

    // 输入文字--获取焦点
    inputTextFocus() {
      $('#inputText').addClass('textarea_border');
    },

    // 输入文字--失去焦点
    inputTextBlur() {
      $('#inputText').removeClass('textarea_border');
    },
    // 添加文字--确定
    inputTextSure(flag) {
      if(this.inputText == '') {
        this.$message.warning('请输入文字内容！');
        return;
      }
      if(flag === 'left') { // 任务文件
        this.inputTextShow1 = false;
      }else { // 个人文档
        this.inputTextShow2 = false;
      }
      this.inputText = '';
    },

    // 添加文字--取消
    inputTextCancel(flag) {
      if(flag === 'left') { // 任务文件
        this.inputTextShow1 = false;
      }else { // 个人文档
        this.inputTextShow2 = false;
      }
      this.inputText = '';
    },
    // 分区和任务的展开/折叠
    extendToggle(index) {
      this.tasksList[index].extend = !this.tasksList[index].extend;
      this.tasksList = this.tasksList.concat();
      $(".list_box")
        .eq(index)
        .slideToggle(400);
    },

    // 切换当前选择的阶段
    stageChange() {
      if(this.fullPreviewShow) {
        this.fullPreviewShow = false;
      }
    },

    // 阶段的整体预览
    fullPreview() {
      this.selectStage = null;
      this.fullPreviewShow = true;
    },

    // 关闭阶段的整体预览
    closeFullPreview() {
      this.fullPreviewShow = false;
    },

    // 点击左侧任务列表
    taskChange(id) {
      this.selectTaskId = id;
    },
    // 切换相关需求/成果文件
    demandOrGainChange(flag) {
      this.demandOrGain = flag;
    },

    // 左右文件视图大小转换
    leftRightToggle() {
      this.leftCenterFlag = !this.leftCenterFlag;
      if (this.leftCenterFlag) {
        // 未分组占大份
        $("#leftBox").addClass("left_box_toggle");
        $("#rightBox").addClass("right_box_toggle");
        this.countFileOne();
        for (let x of this.parthsGroup) {
          x.packUp = false;
          if (x.overList) {
            delete x.overList;
          }
        }
        this.parthsGroup = this.parthsGroup.concat();
      } else {
        // 分组区域占大份
        $("#leftBox").removeClass("left_box_toggle");
        $("#rightBox").removeClass("right_box_toggle");
        for (let x of this.parthsGroup) {
          if (x.allList) {
            delete x.allList;
          }
        }
        for (let i = 0; i < this.parthsGroup.length; i++) {
          let x = this.parthsGroup[i];
          if (!x.overList) {
            this.countFileMore(i);
          }
        }
        this.parthsGroup = this.parthsGroup.concat();
      }
    },

    // 文件组折叠/展开
    groupExtendToggle(index) {
      if (this.leftCenterFlag) {
        this.leftRightToggle();
        this.parthsGroup[index].packUp = null;
      } else {
        this.parthsGroup[index].packUp = !this.parthsGroup[index].packUp;
      }
      if (this.parthsGroup[index].packUp) {
        // 收起
        this.countFileMore(index, false);
      } else {
        // 展开
        this.countFileMore(index, true);
      }
      this.parthsGroup = this.parthsGroup.concat();
    },

    // 计算需要折叠的文件个数--分组折叠成一行
    countFileMore(i, flag) {
      // console.log(this.parthsGroup[i].list);
      if (flag === false) {
        // 展开
        if (this.parthsGroup[i].overList) {
          delete this.parthsGroup[i].overList;
        }
        this.parthsGroup = this.parthsGroup.concat();
        return;
      }
      this.$nextTick(() => {
        const w = $(".parths_group")
          .eq(i)
          .find(".group_file")
          .eq(0)
          .width();
        const x = Math.floor(w / this.fileBoxW);
        let list = [...this.parthsGroup[i].list];
        let length = list.length;
        if (length <= x) {
          this.parthsGroup[i].packUp = null;
          this.parthsGroup = this.parthsGroup.concat();
          return;
        }
        let over = length - x;
        let overList = [...list].splice(0, x - 1);
        let others = [...list].splice(x - 1);
        let urls = [];
        others.map(ele => urls.push(ele.Url));
        if (urls.length > 3) {
          urls.splice(0, 3);
        }
        overList.push({
          Pkid: `group${i}`,
          Title: null,
          Url: urls,
          FileType: null,
          Uname: null,
          overLength: over + 1
        });
        this.parthsGroup[i] = Object.assign({}, this.parthsGroup[i], {
          overList: overList
        });
        this.parthsGroup = this.parthsGroup.concat();
        // console.log('countFileMore--', 'w--> ' + w, 'x--> ' + x, 'length==>' + this.parthsGroup[i].list.length, this.parthsGroup[i]);
      });
    },
    // 计算需要折叠的文件个数--分组折叠成一个
    countFileOne() {
      let i = 0;
      for (let ele of this.parthsGroup) {
        let urls = [];
        ele.list.map(el => urls.push(el.Url));
        if (urls.length > 3) {
          urls.splice(0, 3);
        }
        let allList = [
          {
            Pkid: `group${i}`,
            Title: null,
            Url: urls,
            FileType: null,
            Uname: null,
            overLength: ele.list.length
          }
        ];
        i++;
        this.$set(ele, "allList", allList);
      }
      this.parthsGroup = this.parthsGroup.concat();
    },

    // 整体的分组管理
    groupCommand(type, drag) {
      // 文件拖拽时默认临时加的分组
      if(drag) {
        this.parthsGroup.push({
          id: this.parthsGroup.length,
          groupTitle: `分组${this.parthsGroup.length + 1}`,
          border: false,
          packUp: null,
          list: [],
          dragDisabled: false,
          temporary: true
        });
        return;
      }
      // 新建分组
      if(type === 'create') {
        let title = '新建分组';
        let repeat = this.parthsGroup.findIndex(ele => ele.groupTitle === title);
        // 分组名称判断
        if(repeat !== -1) { // 文件名重复
          let repeatNum = [];
          for(let x of this.parthsGroup) {
            let y = 0;
            if(x.groupTitle.indexOf('新建分组') !== -1 && x.groupTitle.length > 4) {
              y = x.groupTitle.slice(4);
              y = parseInt(y);
              repeatNum.push(y);
            }
          }
          if(repeatNum.length) {
            repeatNum.sort();
            title = title + (repeatNum[repeatNum.length - 1] + 1);
          } else {
            title = title + 1;
          }
        }
        // 添加一条数据
        this.parthsGroup.push({
          id: this.parthsGroup.length,
          groupTitle: title,
          border: false,
          packUp: null,
          list: [],
          dragDisabled: false,
          edit: true,
          createdGroup: true
        });
        // 分组名获取焦点并选
        this.$nextTick(() => {
          const ele = $(this.$refs.createdGroup[0]);
          ele.focus();
          ele.select();
        });
        return;
      }

       // 分组排序
      if(type === 'sort') {
        return;
      }
    },

    // 新建/编辑分组
    groupTitleBlur(group) {
      if(group.createdGroup) { // 新建
        // 发送请求---新建分组
        group.edit = false;
        delete group.createdGroup;
        console.log('新建分组成功');
      }else { // 编辑
        // 先判重，如果有重复的名字--提示，否则--发送请求
        let repeat = this.parthsGroup.findIndex(ele => ele.groupTitle === group.groupTitle);
        if(repeat !== -1) {
          this.$message.warning('已含有同名分组名！');
        }else {
          // 发送修改分组名的接口
          console.log('分组名修改成功');
        }
      }
    },

    // 文件的更多操作
    fileCommand(type) {
      if(type === 'download') { // 下载
        return;
      }
      if(type === 'collect') { // 收藏
        return;
      }
      if(type === 'transfer') { // 移交

        return;
      }
      if(type === 'rename') { // 重命名

        return;
      }
      if(type === 'delete') { // 删除

        return;
      }
    },

    // 组的更多操作
    fileGroupCommand() {
      if(type === 'upload') { // 上传文件
        return;
      }
      if(type === 'download') { // 下载
        return;
      }
      if(type === 'collect') { // 收藏
        return;
      }
      if(type === 'transfer') { // 整组移交

        return;
      }
      if(type === 'rename') { // 重命名

        return;
      }
      if(type === 'delete') { // 删除

        return;
      }
    },

    // 文件拖拽操作-------------
    // 文件移动时的回调函数
    fileMove(e, under) {
      // e.dragged 拖拽的元素
      // e.draggedContext 拖拽的元素的详情
      // e.draggedContext.element 拖拽的元素的内容
      // e.draggedContext.futureIndex / index 拖拽的元素的索引
      // e.to: 拖入区域
      // e.relatedContext: 拖入区域的上下文

      // console.log("fileMove", e);
      if (!this.dragItem.item.Title) {
        const item = e.draggedContext.element;
        this.dragItem.item = Object.assign({}, item);
      }
    },
    // 文件开始移动时的回调函数
    dragStart(e, groupid) {
      this.dragFileId = $(e.item).attr("id");
      const filename = $(e.item).attr("filename");
      this.dragItem.fromGroup = $(e.from).attr("groupid");
      this.$nextTick(() => {
        const ele = $(`#${this.dragFileId}`);
        if (ele.attr("draggable") === "false") {
          ele.addClass("dragging");
        }
      });

      // console.log("dragStart----", e);
      this.parthsGroup.push({
        id: this.parthsGroup.length,
        groupTitle: `分组${this.parthsGroup.length + 1}`,
        border: false,
        packUp: null,
        list: [],
        dragDisabled: false,
        temporary: true
      });
      // 判断是否重复
      this.judegRepeat(groupid, filename);
    },
    // 文件结束移动时的回调函数
    async dragEnd(e) {
      this.dragItem.toGroup = $(e.to).attr("groupid");
      const from = this.dragItem.fromGroup;
      const to = this.dragItem.toGroup;
      const item = this.dragItem.item;
      if (from !== "personal" && to === "personal") {
        // 添加到个人文档--copy
      } else if (from === "personal" && to !== "personal") {
        // 从个人文档添加到我的操作中--copy
      } else if (from !== to) {
        // 移动--move
        let indexs = null;
        if (from === "noGroup") {
          indexs = this.fileList.findIndex(x => x.Pkid === item.Pkid);
          indexs != -1 && this.fileList.splice(indexs, 1);
        } else if (parseInt(from) !== NaN) {
          const id = parseInt(from);
          let ids = this.parthsGroup.findIndex(x => parseInt(x.id) === id);
          if (ids !== -1) {
            indexs = this.parthsGroup[ids].list.findIndex(
              x => x.Pkid === item.Pkid
            );
            indexs != -1 && this.parthsGroup[ids].list.splice(indexs, 1);
          }
        }
      }
      await this.parthsGroupChange(e.newIndex);
      await this.dragEndInit();
    },
    // 开始拖拽时判断是否有重复区域放置
    judegRepeat(groupid, filename) {
      let have = null;
      if (groupid === "personal") {
        have = this.fileList.findIndex(x => x.Title === filename);
        have !== -1 && (this.dragDisabled = true);
      } else {
        have = this.personalFiles.findIndex(x => x.Title === filename);
        have !== -1 && (this.dragDisabled_personal = true);
      }

      for (let ele of this.parthsGroup) {
        let has = ele.list.findIndex(x => x.Title === filename);
        if (ele.id.toString() === this.dragItem.fromGroup) {
          ele.border = true;
          ele.dragDisabled = false;
        } else if (has != -1) {
          ele.border = false;
          ele.dragDisabled = true;
        } else {
          ele.border = true;
          ele.dragDisabled = false;
        }
      }
      this.parthsGroup = this.parthsGroup.concat();
    },

    // 拖拽完成后parthsGroup数组改变后的展开/折叠属性
    parthsGroupChange(insertIndex) {
      this.$nextTick(() => {
        return new Promise(resolve => {
          let list = [...this.parthsGroup];
          for (let i = 0; i < list.length; i++) {
            let x = list[i];
            if (x.id != this.dragItem.toGroup) {
              continue;
            } else if (this.leftCenterFlag) {
              if (x.allList) {
                x.list.push(this.dragItem.item);
              }
              this.countFileOne(i);
            } else if (x.packUp === false) {
              x.list.splice(insertIndex + 1, 0, this.dragItem.item);
              this.countFileMore(i);
            } else if (x.packUp === null) {
              const h = $(".parths_group")
                .eq(i)
                .find(".group_file")
                .eq(0)
                .height();
              if (h > 220) {
                x.packUp = true;
              } else {
                x.packUp = null;
              }
            }
          }
          this.parthsGroup = [...list];
          resolve(true);
        });
      });
    },

    // 拖拽结束后初始化数据
    dragEndInit() {
      return new Promise(resolve => {
        $(".dragging")
          .eq(0)
          .removeClass("dragging");
        this.dragFileId = null;
        this.dragItem = {
          item: {},
          fromGroup: null,
          toGroup: null
        };
        this.dragDisabled = false;
        this.dragDisabled_personal = false;
        for (let ele of this.parthsGroup) {
          ele.border = false;
          ele.dragDisabled = false;
        }
        if (!this.parthsGroup[this.parthsGroup.length - 1].list.length) {
          this.parthsGroup.pop();
        } else {
          delete this.parthsGroup[this.parthsGroup.length - 1].temporary;
        }
        resolve(true);
      });
    },

    // 窗口/元素大小变化对文件分组收起时的影响
    sizeChange() {
      for(let i = 0; i < this.parthsGroup.length; i++) {
        let x = this.parthsGroup[i];
        if(x.packUp === false) { // 折叠
          this.countFileMore(i);
        }
      }
    },

    // 个人文档的操作-------------
    // 是否显示个人文件
    personalFilesPull() {
      this.personalFilesShow = !this.personalFilesShow;
      if (this.personalFilesShow) {
        $("#personalFiles").show();
        $('#operateFile').addClass('self_file_show');
      } else {
        $("#personalFiles").hide();
        $('#operateFile').removeClass('self_file_show');
        if(this.inputTextShow2) {
          this.inputTextShow2 = false;
          this.inputText = '';
        }
      }
      this.sizeChange();
    },

    // 双击文件夹
    dbFolder(file) {
      if (!file.children) {
        return;
      }
      this.personalFiles = file.children;
      this.navBar.push({ id: this.navBar.length, name: file.Title });
    },
    // 返回上一级
    goBack(index) {
      if (index === this.navBar.length - 1) {
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
          Title: "文件夹2--这是我的文件夹中的第二个",
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
            },
            {
              Pkid: 5,
              Title: "文件夹2--第二个文件夹中文件夹",
              Url: null,
              FileType: 0,
              Uname: null,
              children: []
            }
          ]
        },
        {
          Pkid: 4,
          Title: "文件夹3--工作文件夹之我的项目",
          Url: null,
          FileType: 0,
          Uname: null,
          children: []
        }
      ];
      this.navBar = [{ id: 0, name: "个人文档" }];
    },

    // 点击新建文件夹
    newFolder() {
      let title = '新建文件夹';
      let repeat = this.personalFiles.findIndex(ele => ele.Title === title);
      if(repeat !== -1) { // 文件名重复
        let repeatNum = [];
        for(let x of this.personalFiles) {
          let y = 0;
          if(x.Title.indexOf('新建文件夹') !== -1 && x.Title.length > 6) {
            y = x.Title.slice(6);
            y = parseInt(y);
            repeatNum.push(y);
          }
        }
        if(repeatNum.length) {
          repeatNum.sort();
          title = title + ' ' + (repeatNum[repeatNum.length - 1] + 1);
        } else {
          title = title + ' ' + 1;
        }
      }
      this.personalFiles.push({
        Pkid: this.personalFiles.length,
        Title: title,
        Url: null,
        FileType: 0,
        Uname: null,
        children: [],
        edit: true,
        createdFolder: true
      });
      this.$nextTick(() => {
        const ele = $(this.$refs.createdFolder[0]);
        ele.focus();
        ele.select();
      });
    },

    // 添加/修改文件夹失去焦点--保存
    folderBlur(file) {
      if(file.createdFolder) { // 新建
        // 发送请求---新建文件夹
        file.edit = false;
        delete file.createdFolder;
        console.log('新建成功');
      }else { // 编辑
        // 先判重，如果有重复的名字--提示，否则--发送请求
        let repeat = this.personalFiles.findIndex(ele => ele.Title === file.Title);
        if(repeat !== -1) {
          this.$message.warning('该目录下已含有同名文件夹！');
        }else {
          // 发送修改文件名的接口
          console.log('文件名修改成功');
        }
      }
    },


    // 获取任务列表
    getTaskList() {
      let obj = {
        projectId: 1253
      };
      this.tasksList = this.res;

      for (let y of this.fileList) {
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
  },
  mounted() {
    // 监听窗口变化
    window.onresize = () => {
      this.sizeChange();
    }
  }
};
</script>
<style lang='less'>
@import "./style/index.less";
</style>


