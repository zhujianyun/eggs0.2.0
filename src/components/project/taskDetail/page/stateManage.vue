<template>
    <div class='state_manage_yun'>
        <!-- 1.任务成员（是创建者） -->
        <span 
            v-if='stageInfo.isMyParticipate && stageInfo.isCreatorId' 
            class="market_state case1"
            >
            <el-dropdown 
                @visible-change="visibleChange"
                >
                <span v-if='stageInfo.stageTaskUserIsState' class="el-dropdown-link">
                    <i class='iconfont icon-zhuangtaiguanli-1'></i>
                    <span class='span_text finish_text'>已完成</span>
                </span>
                <span v-else class="el-dropdown-link">
                    <i class='iconfont icon-zhuangtaiguanli-'></i>
                    <span class='span_text'>状态标记</span>
                </span>
                <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item @click.native="handleState1('finish')">{{stageInfo.stageTaskUserIsState ? '取消标记' : '标记完成'}}</el-dropdown-item>
                    <el-dropdown-item 
                        class="el_dropdown_state"
                        @mouseenter.native="state1Enter"
                        >
                        <span class="parth_el_dropdown_span">状态管理</span>
                        <i class="iconfont icon-enter"></i>
                        <el-collapse-transition>
                            <state-manage-list v-if="stateManageFlag"
                                ref="stateManageList"
                                id="stateManage"
                                :data="stateManageList" 
                                @handleSure="handleState1"
                                />
                        </el-collapse-transition>
                    </el-dropdown-item>
                </el-dropdown-menu>
            </el-dropdown>
        </span>
        <!-- 2.任务成员（不是创建者） -->
        <span 
            v-else-if='stageInfo.isMyParticipate && !stageInfo.isCreatorId' 
            class="market_state case2"
            @click="handleState2"
            >
            <template v-if='stageInfo.stageTaskUserIsState'>
                <i class='iconfont icon-zhuangtaiguanli-1'></i>
                <span class='finish_text'></span>
            </template>
            <template v-else>
                <i class='iconfont icon-zhuangtaiguanli-'></i>
                <span>标记完成</span>
            </template>
        </span>
        <!-- 3.不是任务成员（但是创建者） -->
        <span 
            v-else-if='!stageInfo.isMyParticipate && stageInfo.isCreatorId' 
            class="market_state"
            @mouseenter="stateEnter"
            @mouseleave="stateLeave"
            >
            <i class='iconfont icon-zhuangtaiguanli-'></i>
            <span class='finish_text'>状态管理</span>
            <el-collapse-transition>
                <state-manage-list v-if="stateManageFlag"
                    ref="stateManageList"
                    id="stateManage"
                    :data="stateManageList" 
                    @handleSure="handleState3"
                    />
            </el-collapse-transition>
        </span>
        <!-- 温馨提示2_标记状态完成的提示 -->
        <transition name="fade1">
            <Reminder2 v-if="remarkStateFlag"
                :type="1"
                :text="reminderText"
                :selectList="selectList"
                :multiple="true"
                sureText="选择文件"
                @handleCancle="remarkStateCancel"
                @handleSure="remarkStateSure" />
        </transition>
        
      <!-- 阶段交接的文件选择和需求描述 -->
       <transition name="fade1">
            <selected-file 
                v-if='selectedFileShow && selectedIds.length'
                :list='stageInfo.fileList'
                :ids="selectedIds"
                @handleCancle='selectedFileCancel'
                @handleSure='selectedFileSure'
            />
        </transition>
    </div>
</template>
<script>
import Reminder2 from "../../../common/reminder2";
import SelectedFile from "./selectedFile";
import StateManageList from "./stateManageList";

export default {
    components: {
        Reminder2,
        SelectedFile,
        StateManageList
    },
    props: ['info'],
    data() {
        return {
            userId: JSON.parse(localStorage.getItem("staffInfo")).userPkid, // 当前登录者的ID
            stageInfo: {}, // 阶段的所有信息
            remarkStateFlag: false, // 标记完成时温馨提示
            reminderText: '',
            selectList: [],
            selectedIds: [], // 选择的ID组
            selectedFileShow: false, // 阶段交接的文件选择和需求描述显示/隐藏
            stateManageFlag: false, // 状态管理列表
            stateManageList: [], // 当前阶段的列表
        }
    },
    methods: {
        

        // 1.任务成员（是创建者）: 标记完成 / 取消完成 整体的状态管理
        handleState1(flag) {
            let ids = [this.stageInfo.stageTaskUserId];
            if(flag !== 'finish' && flag !== null) { // 标记完成 // state状态管理
                ids = flag;
            }
            this.sendHttp(ids).then(res => {
                // console.log("1--修改完成状态", res);
                if(flag === 'finish') { // 标记完成
                    this.stageInfo.stageTaskUserIsState = !this.stageInfo.stageTaskUserIsState;
                }else { // state状态管理
                    this.stateManageFlag = false;
                }
            }).catch(err => {
                console.log("1--修改完成状态失败", err);
            });
        },
        state1Enter() {
            this.stateManageFlag = true;
        },
        visibleChange() {
            this.stateManageFlag = false;
        },

        //  2.任务成员（不是创建者）：标记完成 / 取消完成
        handleState2(flag) {
            let ids = [this.stageInfo.stageTaskUserId];
            this.sendHttp(ids).then(res => {
                // console.log("2--修改完成状态", res);
                this.stageInfo.stageTaskUserIsState = !this.stageInfo.stageTaskUserIsState;
                if(this.stageInfo.stageTaskUserIsState) {
                    this.reminderText = '您已将这部分工作标记为完成，请确认是否将工作成果移交给下一阶段使用';
                    this.remarkStateFlag = true;
                }
                
            }).catch(err => {
                console.log("2--修改完成状态失败", err);
            });
        },

        // 状态3的鼠标移入
        stateEnter() {
            this.stateManageFlag = true;
        },
        // 状态3的鼠标移出
        stateLeave() {
            this.stateManageFlag = false;
        },
        // 3.不是任务成员（但是创建者）: 整体的状态管理
        handleState3(ids) {
            // console.log('return--', ids);
            if(ids !== null) { // 数据有变化，发送请求
                this.sendHttp(ids).then(res => {
                    // console.log("3--修改完成状态", res);
                }).catch(err => {
                    console.log("3--修改完成状态失败", err);
                });
            }
            this.stateManageFlag = false;

        },
        
        // 状态改变发送请求
        sendHttp(ids) {
            return new Promise((resolve, reject) => {
                let obj = {
                    vale: ids.join(',')
                }
                this.$HTTP("post", "/stageTaskUser_update", obj)
                .then(res => {
                    resolve(true);
                })
                .catch(err => {
                    reject(err);
                });
            });
        },

        // 标记状态完成的提示--取消
        remarkStateCancel() {
            this.remarkStateFlag = false;
        },
        // 标记状态完成的提示--确定
        remarkStateSure(val) {
            this.selectedFileShow = true;
            this.selectedIds = val;

        },
        // 文件选择--取消
        selectedFileCancel() {
            this.selectedFileShow = false;
        },
        // 文件选择--确定
        selectedFileSure() {
            this.selectedFileShow = false;
            this.remarkStateFlag = false;
        },

        // 数据获取/设置
        setData() {
            this.stageInfo = Object.assign({}, this.info);
            this.selectList = [];
            for (let x of this.stageInfo.stageList) {
                if(x.stageId != this.stageInfo.stageId) {
                    this.selectList.push({
                        value: x.stageId,
                        label: x.stageTitle,
                        disabled: false
                    });
                }else {
                    this.stateManageList = x.userList;
                }
                
            }
            console.log('状态管理---', this.stageInfo);

        },
    },
    created() {
        this.setData();
        
    }

}
</script>
<style lang='less'>
    .state_manage_yun {
        .market_state {
            position: relative;
            display: inline-block;
            height: 24px;
            line-height: 24px;
            .state_manage_y {
                top: 23px;
                left: 8px;
            }
        }
        
    }
   
</style>


