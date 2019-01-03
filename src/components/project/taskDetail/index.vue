<template>
    <div class='task_detail_yun'>
      <!-- 头部阶段 -->
      <div class="detail_top">
          <div class="project_name">EGGS产品开发</div>
          <!-- state=1(一开始) state=2(一完成) state=3(一未开始) state=4(一进行中) state=5(一已超时) state=6(一已关闭)  -->
          <div class="stage_list">
            <el-tabs v-model="stageId" @tab-click="taskStageDetail(taskId, stageId)">
              <el-tab-pane 
                v-for='stage in stageList'
                :key='stage.stagePkid'
                :label="stage.stageTitle" 
                :name="stage.stagePkid"
                >
                  <span slot="label">
                    <span class="round">
                      <img v-if='stage.state === 1' src="./style/state_icon/state1.png" alt="">
                      <img v-else-if='stage.state === 2' src="./style/state_icon/state2.png" alt="">
                      <img v-else-if='stage.state === 3' src="./style/state_icon/state3.png" alt="">
                      <img v-else-if='stage.state === 4' src="./style/state_icon/state4.png" alt="">
                      <img v-else-if='stage.state === 5' src="./style/state_icon/state5.png" alt="">
                      <img v-else-if='stage.state === 6' src="./style/state_icon/state6.png" alt="">
                    </span>
                    {{stage.stageTitle}}
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
              :key="group.partitionId"
              >
              <div class="group_title">
                <i 
                    class='iconfont icon-unfold'
                    :class="group.extend ? '' : 'icon_rotate'"
                    @click='extendToggle(index)'
                    ></i>
                <span>{{group.partitionTitle}}</span>
              </div>
              <transition-group>
                <div class="list_box" :key="group.partitionId">
                    <div 
                        class="group_list"
                        v-for="list in group.taskList"
                        :key="list.taskId"
                        >
                        <p 
                          :class="list.taskId == taskId ? 'select_task every' : 'every'"
                          @click="taskStageDetail(list.taskId, stageId)"
                        >{{list.taskTitle}}</p>
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
              <!-- 成果文件 -->
              <div v-if='demandOrGain' id="operateFile" class="operate_file">
                <div class="top_box">
                  <div class="left fl">
                    <i class='iconfont icon-shangchuan'></i>
                    <i 
                      class='iconfont icon-tianjiawenzi'
                      @click.stop="inputTextShowToggle('left')"
                      ></i>
                    <span class="line"></span>
                    <el-checkbox 
                      v-if='checkedList.length' 
                      class="all_checked" 
                      v-model="fileCheckbox" 
                      @change='fileCheckboxAll'
                      >
                      已选<span class="mainColor">{{checkedList.length}}</span>项
                    </el-checkbox>
                    <el-checkbox 
                      v-else 
                      class="all_checked" 
                      v-model="fileCheckbox" 
                      @change='fileCheckboxAll'>全选</el-checkbox>
                      <template v-if='checkedList.length'>
                        <i class="iconfont icon-xiazai"></i>
                        <i class="iconfont icon-shoucang1"></i>
                        <i class="iconfont icon-jihuayijiao"></i>
                      </template>
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
                  <!-- 左侧未分组 -->
                  <div id="leftBox" class="left_box">
                    <div v-if='(notGroupedList && notGroupedList.length) || dragItem.fromGroup' class="left_file_list">
                        <draggable
                            class='draggable'
                            groupid='noGroup'
                            v-model="notGroupedList"
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
                                v-for="(ele) in notGroupedList"
                                :key="ele.FilePkid"
                                :id='ele.FilePkid'
                                :filename='ele.FileName'
                                @mouseenter="enterFile(ele)"
                                @mouseleave="leaveFile(ele)"
                                >
                                <span class="file_pic">
                                  <img :src="'http://server.apexgame.cn'+ele.Url" alt="">
                                  <span class="none"></span>
                                </span>
                                <div class="file_info">
                                  <p class="title">{{ele.FileName}}</p>
                                  <img :src="ele.userPic" alt="" class="from_header">
                                  <span class="file_message fr">
                                    <i class='iconfont icon-pinglun'></i>
                                      {{ele.Count}}
                                  </span>
                                  <span class="fixed file_checkbox" v-if='oneChecked || ele.hover'>
                                    <el-checkbox v-model="ele.checked" @change="everyFileCheckbox($event, ele)"></el-checkbox>
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
                  <!-- 右侧分组 -->
                  <div id="rightBox" class="right_box">
                      <div v-if='(parthsGroup && parthsGroup.length) || dragItem.fromGroup' class="parths">
                          <div 
                            class="parths_group"
                            v-for="(group, index) in parthsGroup"
                            :key="group.pkid"
                            >
                            <!-- 分组的头部操作 -->
                            <div class="group_top">
                                <span v-if='!group.edit' class="group_name">{{group.groupName}}</span>
                                  <input 
                                    v-else 
                                    :ref="group.createdGroup ? 'createdGroup' : ''" 
                                    class="group_name edit" 
                                    type="text" 
                                    v-model='group.groupName'
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
                              :key="group.pkid"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? 'drag_in' : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.pkid'
                                v-model="group.allList"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.pkid)'
                                @end='dragEnd'
                                >
                                <div 
                                  class="every_file fold_up"
                                    v-if='item.overLength'
                                  v-for="item in group.allList"
                                  :key="item.FilePkid"
                                  :groupid='group.pkid'
                                  :id='item.FilePkid'
                                  :filename='item.FileName'
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
                                      <p class="file_length"><span class="mainColor">{{group.fileList.length}}</span>个文件</p>
                                    </div>

                                </div>
                                <div class="null"></div>
                              </draggable>
                            </div>

                            <!-- 折叠成一行的时候 -->
                            <div 
                              v-else-if='group.overList && !leftCenterFlag'
                              :key="group.pkid"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? 'drag_in' : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.pkid'
                                v-model="group.overList"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.pkid)'
                                @end='dragEnd'
                                >
                                  <div 
                                    class="every_file fold_up"
                                    :class="item.overLength ? '' : 'draged'"
                                    v-for="item in group.overList"
                                    :key="item.FilePkid"
                                    :groupid='group.pkid'
                                    :id='item.FilePkid'
                                    :filename='item.FileName'
                                    @click='groupExtendToggle(index)'
                                    @mouseenter="enterFile(item)"
                                    @mouseleave="leaveFile(item)"
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
                                        <p class="title">{{item.FileName}}</p>
                                        <img :src="item.userPic" alt="" class="from_header">
                                        <span class="file_message fr">
                                          <i class='iconfont icon-pinglun'></i>
                                          {{item.Count}}
                                        </span>
                                        <span class="fixed file_checkbox" v-if='oneChecked || item.hover'>
                                          <el-checkbox v-model="item.checked" @change="everyFileCheckbox($event, item)"></el-checkbox>
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
                              v-else-if='group.fileList'
                              :key="group.pkid"
                              class="group_file"
                              :class="dragItem && dragItem.fromGroup ? (group.border ? (group.fileList.length ? 'group_border' : 'drag_in') : 'drag_dis') : ''"
                              >
                              <draggable
                                class="draggable"
                                :groupid='group.pkid'
                                v-model="group.fileList"
                                :options="{
                                  group:{name: 'file',pull:'clone'},
                                  ghostClass: 'ghost_file', 
                                  dragClass: 'drag_file',
                                  draggable: '.draged',
                                  disabled: group.dragDisabled
                                }"
                                :move='fileMove'
                                @start='dragStart($event, group.pkid)'
                                @end='dragEnd'
                                >
                                <div 
                                  class="every_file fold_up draged"
                                  v-for="item in group.fileList"
                                  :key="item.FilePkid"
                                  :groupid='group.pkid'
                                  :id='item.FilePkid'
                                  :filename='item.FileName'
                                  @mouseenter="enterFile(item)"
                                  @mouseleave="leaveFile(item)"
                                  >
                                  <span class="file_pic">
                                    <img :src="'http://server.apexgame.cn'+item.Url" alt="">
                                    <span class="none"></span>
                                  </span>
                                  <div class="file_info">
                                    <p class="title">1--{{item.FileName}}</p>
                                    <img :src="item.userPic" alt="" class="from_header">
                                    <span class="file_message fr">
                                      <i class='iconfont icon-pinglun'></i>
                                      {{item.Count}}
                                    </span>
                                    <span class="fixed file_checkbox" v-if='oneChecked || item.hover'>
                                      <el-checkbox v-model="item.checked" @change="everyFileCheckbox($event, item)"></el-checkbox>
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

                      <div v-if='notGroupedList.length || parthsGroup.length' class="left_right_toggle" @click='leftRightToggle'>
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
                  ref="otherView"
                />
              </div>
              <!-- 相关需求 -->
              <div v-else id="operateFile" class="operate_file">
                <demand-view/>
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
                    <el-checkbox 
                      v-if='checkedListSelf.length' 
                      v-model="fileCheckboxSelf" 
                      @change='fileCheckboxAllSelf'
                      >
                      已选<span class="mainColor">{{checkedListSelf.length}}</span>项
                    </el-checkbox>
                    <el-checkbox 
                      v-else 
                      v-model="fileCheckboxSelf" 
                      @change='fileCheckboxAllSelf'>全选</el-checkbox>
                      <template v-if='checkedListSelf.length'>
                        <i class="iconfont icon-xiazai"></i>
                        <i class="iconfont icon-delete"></i>
                      </template>
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
                            :key="file.FilePkid"
                            :id='file.FilePkid'
                            :filename='file.FileName'
                            @dblclick='dbFolder(file)'
                            @mouseenter="enterFileSelf(file)"
                            @mouseleave="leaveFileSelf(file)"
                            >
                            <!-- 文件夹 -->
                            <template v-if='file.children'>
                              <span class="file_pic">
                                <img src="./style/wenjianjia.png" alt="">
                                <span class="none"></span>
                              </span>
                              <div class="file_info">
                                <p v-if='!file.edit' class="title">{{file.FileName}}</p>
                                <input 
                                  v-else 
                                  :ref="file.createdFolder ? 'createdFolder' : ''" 
                                  class="title edit" 
                                  type="text" 
                                  v-model='file.FileName'
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
                                <p class="title">{{file.FileName}}</p>
                                <p class="file_num">123KB</p>
                                <span class="fixed file_checkbox">
                                  <el-checkbox v-model="file.checked" @change="everyFileCheckboxSelf($event, file)"></el-checkbox>
                                </span>

                                <span class="fixed file_checkbox" v-if='oneCheckedSelf || file.hover'>
                                  <el-checkbox v-model="file.checked" @change="everyFileCheckboxSelf($event, file)"></el-checkbox>
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
import DemandView from './demandView';
import { mapMutations } from 'vuex';
export default {
  components: {
    draggable,
    FullPreview,
    OtherView,
    DemandView
  },
  data() {
    return {
      // http://server.apexgame.cn/upload/user/20180719110629240.jpeg
      projectId: 0, // 项目ID
      stageId: '0', // 阶段ID
      taskId: 0, // 任务ID
      stageList: [], // 阶段列表
      tasksList: [], // 左侧分组任务列表
      fileList: [], // 分组+未分组列表
      demandOrGain: true, // false--相关需求 true--成果文件
      fileCheckbox: false, // 文件全选
      viewToggle: true, // true--视图1  false--视图2
      fileCheckboxSelf: false, // 个人文档
      notGroupedList: [
        {
          FilePkid: "4085",
          FileName: "egLogo1.png",
          Url:
            "/upload/file/20181227065330/63f7b3f6-f448-448f-b26a-6d6a12741f03/7f31dfb2-196a-48b6-89bc-aa2652013506.png",
          FileType: "png",
          userName: "祝建云",
        },
        {
          FilePkid: "6575",
          FileName: "详情页面1",
          Url:
            "/upload/file/20180524103014/41735179-c2d4-45b8-85ca-da4681d5b3af/01.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6573",
          FileName: "详情页面-2",
          Url:
            "/upload/file/20181226081958/5e77da48-39a0-491f-a5db-c23cd5a4023d/b4f00229-8439-4f78-b0a1-957644a58492_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6574",
          FileName: "详情页面-3",
          Url:
            "/upload/file/20180524103014/489053b4-6574-47b0-ace3-22101721e612/2.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6572",
          FileName: "详情页面-4",
          Url:
            "/upload/file/20181226081939/d8e7a662-6f0a-4b41-ba0d-f359aaa3fb5b/2113c63f-2b0b-487a-a3cc-d78b301001b8_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6570",
          FileName: "详情页面-5",
          Url:
            "/upload/file/20181226081938/90571b62-18cb-45aa-a8f0-1f3e30178176/ae3a890a-f693-4023-8b14-f4d960ca0c73_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6571",
          FileName: "详情页面-6",
          Url:
            "/upload/file/20181226081938/ea47a6ae-e9f8-43d6-a63f-08d3c2bb3373/bdca1df3-3343-4381-bed0-8eecf050cd98_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6569",
          FileName: "详情页面-无分组",
          Url:
            "/upload/file/20181226081938/e29e3cec-30ce-4670-8d12-acd4c6a372c3/0d4dfecd-439b-418d-b368-0e2ec3d6206d_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6568",
          FileName: "详情页面-文件分组最小化",
          Url:
            "/upload/file/20181226081938/95216f40-6b6f-43e7-b8ab-a62c285f8b00/a1ec02b1-a3bd-4cb1-afc2-8bd9787c4d03_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6567",
          FileName: "详情页面-文件移动n",
          Url:
            "/upload/file/20181226081938/8f8daa93-ffb1-4445-b4cb-7f79ed5fd2ce/5bc9a050-5e1a-44df-8ac2-0f52d5030175_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6566",
          FileName: "详情页面-输入文字",
          Url:
            "/upload/file/20181226081938/3afc6cd3-72ce-45c0-b4a2-e9e47b73a6d2/82dfd666-49a2-4c15-928c-6dc9b5dfeea3_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6565",
          FileName: "详情页面-上传文件to成果文件",
          Url:
            "/upload/file/20181226081931/55e92e54-1075-43ab-a8ba-74ced18d6121/9a56d16e-6246-4b48-9ea4-3276bc2cdce9_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6564",
          FileName: "详情页面-上传文件to个人文档",
          Url:
            "/upload/file/20181226081931/5ff72ed8-1c2d-4323-bfe4-7e3d45ee83dc/7fd6baae-cb04-4207-9cbf-fa53d399a978_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6563",
          FileName: "详情页面-删除文件夹与删除文件提示",
          Url:
            "/upload/file/20181226081931/819c9db1-70a8-453d-b739-a978be80585f/ce970ac7-d760-4be3-8969-316e22cf6efd_s1.png",
          FileType: "png",
          userName: "卢洪臣",
          Edition: "1",
        },
        {
          FilePkid: "6562",
          FileName: "详情页面-空页面",
          Url:
            "/upload/file/20181226081930/14707eb6-6f74-4819-844c-76afc08de9b9/8f37b596-48ae-4262-b241-beee53d646a3_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6561",
          FileName: "详情页面-进入文件夹--文件夹为空",
          Url:
            "/upload/file/20181226081930/83ecf1e5-5f21-44c6-bcf7-48e391b99e19/fe95c51a-3ab0-4ab2-8826-c5e925447b4a_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6560",
          FileName: "详情页面-进入文件夹--出现面包屑---文件hover的操作",
          Url:
            "/upload/file/20181226081930/5db23181-724c-42c0-b036-7d4471ec6247/f391c292-ac6b-4ab2-b787-94ea6cd07e26_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6559",
          FileName: "详情页面-分组文件的展开与收起样式",
          Url:
            "/upload/file/20181226081929/4cf89dc7-18e1-456b-ad93-8b687cbf48e4/9713e604-fec5-4298-a28b-9e2eb11fe414_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6558",
          FileName: "详情页面-成果文件分组更多操作",
          Url:
            "/upload/file/20181226081929/82b8f088-2c08-4728-a739-97760c0c3d61/dd3b3458-ddf1-415e-9b75-af241125224e_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        },
        {
          FilePkid: "6557",
          FileName: "详情页面-多选文件样式",
          Url:
            "/upload/file/20181226081929/729c03fc-fd7c-41c1-99e8-2fcd1e63dd3a/c96654d0-0582-4809-8359-ecea14d26148_s1.png",
          FileType: "png",
          userName: "卢洪臣",
        }
      ], // 未分组文件列表
      parthsGroup: [
        {
          pkid: 0,
          groupName: "分组1",
          border: false,
          dragDisabled: false,
          packUp: null,
          fileList: [
            {
              FilePkid: "404",
              FileName: "附件1",
              Url:
                "/upload/file/20181227072023/3c36843c-24be-4d34-9f88-38aadcd9e617/ced7bc91-99d6-4345-ba44-292e4a206593.png",
              FileType: "png",
              userName: "祝建云"
            },
            {
              FilePkid: "403",
              FileName: "赫本",
              Url:
                "/upload/file/20181227072001/e9e95bcc-11f0-4470-914e-87cb65be398b/4e822aa0-2b99-4bc0-b58d-48725929ef4b.png",
              FileType: "png",
              userName: "祝建云"
            }
          ]
        }
      ], // 分组文件列表
      personalFilesShow: false, // 个人文档是否显示
      personalFiles: [
        {
          FilePkid: "408",
          FileName: "bg",
          Url:
            "/upload/file/20181213070700/d0b06e24-5111-497c-a512-8d4b139fd475/55924df2-63b6-4914-ade4-0ba667d577db_s1.png",
          FileType: "svg",
          userName: "祝建云"
        },
        {
          FilePkid: "406",
          FileName: "video2",
          Url:
            "/upload/file/20180817071340/5e0dba23-b76a-43a6-9589-1aee0ae38fdb/f7088adf-dbc4-4b83-994a-5d69c4a66c0d_s1.png",
          FileType: "mp4",
          userName: "祝建云"
        },
        {
          FilePkid: 2,
          FileName: "我的文件",
          Url: null,
          FileType: 0,
          userName: null,
          default: true,
          children: [
            {
              FilePkid: "407",
              FileName: "WX20181220-170213",
              Url:
                "/upload/file/20180528070747/8b10d083-a85b-45a4-825a-9437fd47fa4a/logo-3.png",
              FileType: "png",
              userName: "祝建云"
            },
            {
              FilePkid: "405",
              FileName: "video",
              Url:
                "/upload/file/20180711020446/9ccd7618-5010-40f4-ac2e-008e64108a22/165310yogoihqr63svh6tm.jpg",
              FileType: "mp4",
              userName: "祝建云"
            }
          ]
        },
        {
          FilePkid: 3,
          FileName: "我的收藏",
          Url: null,
          FileType: 0,
          userName: null,
          default: true,
          children: [
            {
              FilePkid: "408",
              FileName: "bg",
              Url:
                "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
              FileType: "svg",
              userName: "祝建云"
            },
            {
              FilePkid: 5,
              FileName: "文件夹2--第二个文件夹中文件夹",
              Url: null,
              FileType: 0,
              userName: null,
              children: []
            }
          ]
        },
        {
          FilePkid: 4,
          FileName: "文件夹3--工作文件夹之我的项目",
          Url: null,
          FileType: 0,
          userName: null,
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
      oneChecked: false, // 是否有一个文件被选中
      oneCheckedSelf: false, // 个人文档--是否有一个文件被选中
      checkedList: [], // 已选中的文件列表
      checkedListSelf: [], // 个人文档--已选中的文件列表
    };
  },
  watch: {
    checkedList(val) {
      // this.CHECKEDLIST_CHANGE(val);
    }
  },
  computed: {
    
  },
  methods: {
    ...mapMutations([
      'CHECKEDLIST_CHANGE',
    ]),
    // 文件视图切换
    viewToggles() {
      this.viewToggle = !this.viewToggle;
      if(this.viewToggle) {
        const list = this.$refs.otherView.close();
        this.CHECKEDLIST_CHANGE(list);
        // 进行文件多选的回选
      }else {
        this.CHECKEDLIST_CHANGE(this.checkedList);
      }
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

    // 阶段的整体预览
    fullPreview() {
      this.stageId = null;
      this.fullPreviewShow = true;
    },

    // 关闭阶段的整体预览
    closeFullPreview() {
      this.fullPreviewShow = false;
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
          if (x.overList) {
            delete x.overList;
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
      // console.log(this.parthsGroup[i].fileList);
      const arg = [...arguments]; 
      if(this.leftCenterFlag) { // 左侧--未分组占大份
      }else { // 右侧--分组区域占大份
      }
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
        let list = [...this.parthsGroup[i].fileList];
        let length = list.length;
        if (length <= x) {
          this.parthsGroup[i].packUp = null;
          if(this.parthsGroup[i].overList) {
            delete this.parthsGroup[i].overList;
          }
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
          FilePkid: `group${i}`,
          FileName: null,
          Url: urls,
          FileType: null,
          userName: null,
          overLength: over + 1
        });
        this.parthsGroup[i] = Object.assign({}, this.parthsGroup[i], {
          overList: overList
        });
        this.parthsGroup = this.parthsGroup.concat();
        // console.log('countFileMore--', 'w--> ' + w, 'x--> ' + x, 'length==>' + this.parthsGroup[i].fileList.length, this.parthsGroup[i]);
      });
    },
    // 个人文件显示/隐藏对文件结构的影响
    countFileView(i, packUp, pull) {
      this.$nextTick(() => {
        const w = $(".parths_group")
          .eq(i)
          .find(".group_file")
          .eq(0)
          .width();
        const x = Math.floor(w / this.fileBoxW);
        let list = [...this.parthsGroup[i].fileList];
        let length = list.length;
        if((pull && packUp === false) || (!pull && packUp === false && length > x)) {
          let over = length - x;
          let overList = [...list].splice(0, x - 1);

          let others = [...list].splice(x - 1);
          let urls = [];
          others.map(ele => urls.push(ele.Url));

          if (urls.length > 3) {
            urls.splice(0, 3);
          }

          overList.push({
            FilePkid: `group${i}`,
            FileName: null,
            Url: urls,
            FileType: null,
            userName: null,
            overLength: over + 1
          });
          this.parthsGroup[i] = Object.assign({}, this.parthsGroup[i], {
            overList: overList
          });
          this.parthsGroup = this.parthsGroup.concat();
          return;
        }
        if(pull && packUp === null && length > x) {
          this.parthsGroup[i].packUp = true;
          this.parthsGroup = this.parthsGroup.concat();
          return;
        }
        if(!pull && (packUp !== null && (length <= x))) {
          this.parthsGroup[i].packUp = null;
          if(this.parthsGroup[i].overList) {
            delete this.parthsGroup[i].overList
          }
          this.parthsGroup = this.parthsGroup.concat();
          return;
        }
        return;
        if(pull) { // 当个人文档从不显示-->显示的时候，
          if(packUp === false) { // 重新计算要折叠剩余的个数
          
          }else if(packUp === null) { // 计算现在一行还能展示下吗 如果展示不下的话，让flag = true
            if(length > x) {
            }
          }
          // 如果flag === true 继续为true
        }else { // 当个人文档从显示-->不显示的时候，
          if(packUp !== null) {
            if(length <= x) {
              // this.parthsGroup[i].packUp = null;
            }
          }
          if(packUp === true) { // 重新计算个人文件收起时一行还能展示下吗：可以的话让flag = null；否则 flag还是true
            if(length <= x) {
              // this.parthsGroup[i].packUp = null;
            }
          }else if(packUp === false) { // 重新计算一行还能展示下吗：可以的话让flag = null；否则 flag还是false，但折叠的个数需改变
            if(length <= x) {
              // this.parthsGroup[i].packUp = null;
            }else { // 折叠的个数需改变 
            
            }
          }
          // 如果flag === null 不需要操作
        }

      });

    }, 
    // 计算需要折叠的文件个数--分组折叠成一个
    countFileOne() {
      let i = 0;
      for (let ele of this.parthsGroup) {
        let urls = [];
        for(let x of ele.fileList) {
          if(urls.length < 3) {
            urls.push(x.Url);
          } 
        }
        let allList = [
          {
            FilePkid: `group${i}`,
            FileName: null,
            Url: urls,
            FileType: null,
            userName: null,
            overLength: ele.fileList.length
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
          pkid: this.parthsGroup.length,
          groupName: `分组${this.parthsGroup.length + 1}`,
          border: false,
          packUp: null,
          fileList: [],
          dragDisabled: false,
          temporary: true
        });
        return;
      }
      // 新建分组
      if(type === 'create') {
        let title = '新建分组';
        let repeat = this.parthsGroup.findIndex(ele => ele.groupName === title);
        // 分组名称判断
        if(repeat !== -1) { // 文件名重复
          let repeatNum = [];
          for(let x of this.parthsGroup) {
            let y = 0;
            if(x.groupName.indexOf('新建分组') !== -1 && x.groupName.length > 4) {
              y = x.groupName.slice(4);
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
          pkid: this.parthsGroup.length,
          groupName: title,
          border: false,
          packUp: null,
          fileList: [],
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
        let repeat = this.parthsGroup.findIndex(ele => ele.groupName === group.groupName);
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
    fileGroupCommand(type) {
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

    // 鼠标移入文件
    enterFile(item) {
      item.hover = true;
      this.notGroupedList = this.notGroupedList.concat();
      this.parthsGroup = this.parthsGroup.concat();

    },
    // 鼠标移出文件
    leaveFile(item) {
      item.hover = false;
      this.notGroupedList = this.notGroupedList.concat();
      this.parthsGroup = this.parthsGroup.concat();

    },
    // 文件的选中状态发生改变
    everyFileCheckbox(val, item) {
      let x1,x2, indexs;
      if(val) {
        this.oneChecked = true;
        this.checkedList.push(item);

        x1 = this.notGroupedList.findIndex(ele => !ele.checked);
        for(let x of this.parthsGroup) {
          x2 = x.fileList.findIndex(ele => !ele.checked);
        }
        if(x1 === -1 && x2 === -1) {
          this.fileCheckbox = true;
        }

      }else {
        indexs = this.checkedList.findIndex(ele => ele.FilePkid === item.FilePkid);
        indexs !== -1 && (this.checkedList.splice(indexs, 1));
        x1 = this.notGroupedList.findIndex(ele => ele.checked);
        for(let x of this.parthsGroup) {
          x2 = x.fileList.findIndex(ele => ele.checked);
        }
        if(x1 !== -1 || x2 !== -1) {
          this.oneChecked = true;
        }else {
          this.oneChecked = false;
        }
        this.fileCheckbox && (this.fileCheckbox = false);
      }
      this.notGroupedList = this.notGroupedList.concat();
      this.parthsGroup = this.parthsGroup.concat();
    },

    // 文件全选
    fileCheckboxAll(val) {
      this.checkedList = [];
      if(val) {
        for(let y of this.notGroupedList) {
          y.checked = true;
          this.checkedList.push(y);
        }
        for(let x of this.parthsGroup) {
          for(let y of x.fileList) {
            y.checked = true;
            this.checkedList.push(y);

          }
        }
        this.oneChecked = true;
      }else {
        for(let y of this.notGroupedList) {
          y.checked = false;
        }
        for(let x of this.parthsGroup) {
          for(let y of x.fileList) {
            y.checked = false;
          }
        }
        this.oneChecked = false;
      }

      this.notGroupedList = this.notGroupedList.concat();
      this.parthsGroup = this.parthsGroup.concat();
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
      if (!this.dragItem.item.FileName) {
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
        pkid: this.parthsGroup.length,
        groupName: `分组${this.parthsGroup.length + 1}`,
        border: false,
        packUp: null,
        fileList: [],
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
          indexs = this.notGroupedList.findIndex(x => x.FilePkid === item.FilePkid);
          indexs != -1 && this.notGroupedList.splice(indexs, 1);
        } else if (parseInt(from) !== NaN) {
          const id = parseInt(from);
          let ids = this.parthsGroup.findIndex(x => parseInt(x.pkid) === id);

          if (ids !== -1) {
            indexs = this.parthsGroup[ids].fileList.findIndex(
              x => x.FilePkid === item.FilePkid
            );
            indexs != -1 && this.parthsGroup[ids].fileList.splice(indexs, 1);
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
        have = this.notGroupedList.findIndex(x => x.FileName === filename);
        have !== -1 && (this.dragDisabled = true);
      } else {
        have = this.personalFiles.findIndex(x => x.FileName === filename);
        have !== -1 && (this.dragDisabled_personal = true);
      }

      for (let ele of this.parthsGroup) {
        let has = ele.fileList.findIndex(x => x.FileName === filename);
        if (ele.pkid.toString() === this.dragItem.fromGroup) {
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
            // 当前组的ID 拖拽到目的地组的ID
            if (x.pkid != this.dragItem.toGroup) {
              if(x.packUp === false) {
                // console.log(111);
                this.countFileMore(i);
              } else {
              const h = $(".parths_group")
                .eq(i)
                .find(".group_file")
                .eq(0)
                .height();
              if (h > 220) { // 也有问题
                x.packUp = true;

              } else {
                x.packUp = null;
              }

            }
              continue;
            } else 
            if (this.leftCenterFlag) {
              if (x.allList) {
                x.fileList.push(this.dragItem.item);
              }
              this.countFileOne(i);
            } else if (x.packUp === false) {
              x.fileList.splice(insertIndex + 1, 0, this.dragItem.item);
              this.countFileMore(i);
            } else if (x.packUp === null || x.packUp === true) {
              const h = $(".parths_group")
                .eq(i)
                .find(".group_file")
                .eq(0)
                .height();
              if (h > 220) { // 也有问题
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
        if (!this.parthsGroup[this.parthsGroup.length - 1].fileList.length) {
          this.parthsGroup.pop();
        } else {
          delete this.parthsGroup[this.parthsGroup.length - 1].temporary;
        }
        resolve(true);
      });
    },

    // 窗口/元素大小变化对文件分组收起时的影响
    sizeChange(desc) {
      for(let i = 0; i < this.parthsGroup.length; i++) {
        let x = this.parthsGroup[i];
        if(desc && desc === 'personalFilesPull') {
          if(this.personalFilesShow) { // 个人文档显示
            this.countFileView(i, x.packUp, true);
          }else { // 个人文档不显示
            this.countFileView(i, x.packUp, false);
          }
        }else {
          if(x.packUp === false) { // 折叠
            this.countFileMore(i);
          }
        }
        
      }

      // 当个人文档从不显示-->显示的时候，
        // 如果flag === true 继续为true
        // 如果flag === false （半折叠状态）重新计算要折叠剩余的个数
        // 如果flag === null 计算现在一行还能展示下吗 如果展示不下的话，让flag = true
      // 当个人文档从显示-->不显示的时候，
        // 如果flag === true 重新计算个人文件收起时一行还能展示下吗：可以的话让flag = null；否则 flag还是true
        // 如果flag === false （半折叠状态）重新计算一行还能展示下吗：可以的话让flag = null；否则 flag还是false，但折叠的个数需改变
        // 如果flag === null 不需要操作
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
      this.sizeChange('personalFilesPull');
      
    },

    // 双击文件夹
    dbFolder(file) {
      if (!file.children) {
        return;
      }
      this.personalFiles = file.children;
      this.navBar.push({ id: this.navBar.length, name: file.FileName });
    },
    // 返回上一级
    goBack(index) {
      if (index === this.navBar.length - 1) {
        return;
      }
      this.personalFiles = [
        {
          FilePkid: "408",
          FileName: "bg",
          Url:
            "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
          FileType: "svg",
          userName: "祝建云"
        },
        {
          FilePkid: "406",
          FileName: "video2",
          Url:
            "/upload/file/20181224063835/472243c2-382a-423c-80ab-9ce41a6d790f/b3c9bc00-538f-4167-83b9-879676690519.mp4",
          FileType: "mp4",
          userName: "祝建云"
        },
        {
          FilePkid: 2,
          FileName: "文件夹1",
          Url: null,
          FileType: 0,
          userName: null,
          children: [
            {
              FilePkid: "407",
              FileName: "WX20181220-170213",
              Url:
                "/upload/file/20181224063844/2288995a-d1c0-4144-9c94-a8eb2a1c568c/3ab87050-c290-40df-9171-f2543be97c68.png",
              FileType: "png",
              userName: "祝建云"
            },

            {
              FilePkid: "405",
              FileName: "video",
              Url:
                "/upload/file/20181224063835/e85c7a46-b564-46d5-8743-18e63e4a6588/eeb294ad-6ce7-4b86-950a-2869fd7814e8.mp4",
              FileType: "mp4",
              userName: "祝建云"
            }
          ]
        },
        {
          FilePkid: 3,
          FileName: "文件夹2--这是我的文件夹中的第二个",
          Url: null,
          FileType: 0,
          userName: null,
          children: [
            {
              FilePkid: "408",
              FileName: "bg",
              Url:
                "/upload/file/20181224063855/7e9a5d52-d934-4f26-afba-9229d7524bfe/9ca290fc-5f78-421f-b237-9228b1a41263.svg",
              FileType: "svg",
              userName: "祝建云"
            },
            {
              FilePkid: 5,
              FileName: "文件夹2--第二个文件夹中文件夹",
              Url: null,
              FileType: 0,
              userName: null,
              children: []
            }
          ]
        },
        {
          FilePkid: 4,
          FileName: "文件夹3--工作文件夹之我的项目",
          Url: null,
          FileType: 0,
          userName: null,
          children: []
        }
      ];
      this.navBar = [{ id: 0, name: "个人文档" }];
    },

    // 点击新建文件夹
    newFolder() {
      let title = '新建文件夹';
      let repeat = this.personalFiles.findIndex(ele => ele.FileName === title);
      if(repeat !== -1) { // 文件名重复
        let repeatNum = [];
        for(let x of this.personalFiles) {
          let y = 0;
          if(x.FileName.indexOf('新建文件夹') !== -1 && x.FileName.length > 6) {
            y = x.FileName.slice(6);
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
        FilePkid: this.personalFiles.length,
        FileName: title,
        Url: null,
        FileType: 0,
        userName: null,
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
        let repeat = this.personalFiles.findIndex(ele => ele.FileName === file.FileName);
        if(repeat !== -1) {
          this.$message.warning('该目录下已含有同名文件夹！');
        }else {
          // 发送修改文件名的接口
          console.log('文件名修改成功');
        }
      }
    },

    // 个人文档--鼠标移入文件
    enterFileSelf(item) {
      item.hover = true;
      this.personalFiles = this.personalFiles.concat();
    },
    // 个人文档--鼠标移出文件
    leaveFileSelf(item) {
      item.hover = false;
      this.personalFiles = this.personalFiles.concat();

    },
    // 个人文档--文件的选中状态发生改变
    everyFileCheckboxSelf(val, item) {
      let x1, indexs;
      if(val) {
        this.oneCheckedSelf = true;
        this.checkedListSelf.push(item);

        x1 = this.personalFiles.findIndex(ele => !ele.hasOwnProperty('children') && !ele.checked);
        if(x1 === -1) {
          this.fileCheckboxSelf = true;
        }

      }else {
        indexs = this.checkedListSelf.findIndex(ele => !ele.hasOwnProperty('children') && ele.FilePkid === item.FilePkid);
        indexs !== -1 && (this.checkedListSelf.splice(indexs, 1));
        x1 = this.personalFiles.findIndex(ele => ele.checked);
        if(x1 !== -1) {
          this.oneCheckedSelf = true;
        }else {
          this.oneCheckedSelf = false;
        }
        this.fileCheckboxSelf && (this.fileCheckboxSelf = false);
      }
      this.personalFiles = this.personalFiles.concat();
    },

    // 个人文档--文件全选
    fileCheckboxAllSelf(val) {
      this.checkedListSelf = [];
      if(val) {
        for(let y of this.personalFiles) {
          if(!y.hasOwnProperty('children')) {
            y.checked = true
            this.checkedListSelf.push(y);
          }
        }
        this.oneCheckedSelf = true;
      }else {
        for(let y of this.personalFiles) {
          if(!y.hasOwnProperty('children')) {
            y.checked = false
          }
        }
        this.oneCheckedSelf = false;
      }

      this.personalFiles = this.personalFiles.concat();
    },

    // 递归遍历文件夹层级，给每个文件加checked
    fibChecked(list) {
      for(let y of list) {
        if(y.children) {
          return this.fibChecked(y.children);
        }else {
          y.Count = 0;
          y.Desc = '0'; // 文字描述
          y.SystemType = 0; // 0--上传的文件 1--文字描述
          let indexs = y.FileName.lastIndexOf('.');
          y.Type = y.FileName.slice(indexs + 1);
          y.userPic = 'http://server.apexgame.cn/upload/user/20180719110629240.jpeg';
          y.userPkid = 0;
          y.checked = false;
          y.hover = false;
        }
      }
    },

    // 获取个人文档列表
    getPersonalFile() {
      
      this.personalFiles = this.personalFiles.concat();
      for(let y of this.personalFiles) {
        if(!y.hasOwnProperty('children')) {
          y.Count = 0;
          y.Desc = '0'; // 文字描述
          y.SystemType = 0; // 0--上传的文件 1--文字描述
          let indexs = y.FileName.lastIndexOf('.');
          y.Type = y.FileName.slice(indexs + 1);
          y.userPic = 'http://server.apexgame.cn/upload/user/20180719110629240.jpeg';
          y.userPkid = 0;
          y.checked = false;
          y.hover = false;
        }
      }
      // this.fibChecked(this.personalFiles);
      console.log(this.personalFiles);
    },

    // 获取任务列表
    getTaskList(params) {
      let obj = {
        project: params
      };
      this.$HTTP("post", "/project_get_Catalog", obj)
        .then(res => {
          this.tasksList = res.result;
          for (let y of this.tasksList) {
            y.extend = true;
          }
          console.log("获取任务列表", this.tasksList);
          this.taskStageDetail(this.taskId ,this.stageId);
        })
        .catch(err => {
          console.log("获取任务列表失败", err);
          this.$message.error("获取任务列表失败，请检查网络");
        });
    },

    // 任务/阶段--详情
    taskStageDetail(taskId, stageId) {
      if(this.fullPreviewShow) {
        this.fullPreviewShow = false;
      }
      // this.parthsGroup = [];
      for(let y of this.notGroupedList) {
        y.Count = 0;
        y.Desc = '0'; // 文字描述
        y.SystemType = 0; // 0--上传的文件 1--文字描述
        let indexs = y.FileName.lastIndexOf('.');
        y.Type = y.FileName.slice(indexs + 1);
        y.userPic = 'http://server.apexgame.cn/upload/user/20180719110629240.jpeg';
        y.userPkid = 0;
        y.checked = false;
        y.hover = false;
      }
      for(let x of this.parthsGroup) {
        for(let y of x.fileList) {
          y.Count = 0;
          y.Desc = '0'; // 文字描述
          y.SystemType = 0; // 0--上传的文件 1--文字描述
          let indexs = y.FileName.lastIndexOf('.');
          y.Type = y.FileName.slice(indexs + 1);
          y.userPic = 'http://server.apexgame.cn/upload/user/20180719110629240.jpeg';
          y.userPkid = 0;
          y.checked = false;
          y.hover = false;
        }
      }
      
      this.taskId = taskId;
      this.stageId = stageId;
      let obj = {
        projectId: this.projectId,
        stageId: this.stageId,
        taskId: this.taskId,
      }

      this.$HTTP("post", "/stagetask_get", obj)
      .then(res => {
        this.stageList = [...res.result.stageList];
        this.stageList.map(ele => ele.stagePkid = ele.stagePkid.toString());
        return;
        this.fileList = [...res.result.fileList];
        for(let ele of this.fileList) {
          if(ele.pkid === 0) {
            this.notGroupedList = ele.fileList;
          }else {
            this.parthsGroup.push(ele);
          }
        }
        console.log("获取任务详情",  this.notGroupedList, this.parthsGroup);
      })
      .catch(err => {
        console.log("获取任务详情失败", err);
        this.$message.error("获取任务详情失败，请检查网络");
      });
    },
  },
  created() {
    let params = this.$route.params;
     params = {
          projectId: 1252,
          stageId: '38',
          taskId: 118,
        }
    this.projectId = params.projectId;
    this.stageId = params.stageId;
    this.taskId = params.taskId;
    this.getTaskList( this.projectId); // 获取任务列表
    this.getPersonalFile(); // 获取个人文档列表
    
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


