<template>
    <div class='state_manage_yun'>
        <span 
            v-if='stageInfo.isMyParticipate && stageInfo.isCreatorId' 
            class="market_state case1"
            >1
            <el-dropdown class="">
                <span class="el-dropdown-link">
                <i class='iconfont icon-zhuangtaiguanli-'></i>
                <span class='span_text'>{{stageInfo.stageTaskUserIsState ? '已完成' : '状态标记'}}</span>
                </span>
                <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="marketState('create')">{{stageInfo.stageTaskUserIsState ? '取消标记' : '标记完成'}}</el-dropdown-item>
                <el-dropdown-item @click.native="marketState('sort')">状态管理</el-dropdown-item>
                </el-dropdown-menu>
            </el-dropdown>
        </span>
        <!-- 2.任务成员（不是创建者） -->
        <span 
            v-else-if='stageInfo.isMyParticipate && !stageInfo.isCreatorId' 
            class="market_state case2"
            @click="handleState(2)"
            >2
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
        <span v-else-if='!stageInfo.isMyParticipate && stageInfo.isCreatorId' class="market_state">3
            <i class='iconfont icon-zhuangtaiguanli-'></i>
            <span class='finish_text'>状态管理</span>
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

export default {
    components: {
        Reminder2,
        SelectedFile,
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
        }
    },
    methods: {
        //  2.任务成员（不是创建者）：标记完成 / 取消完成
        handleState(flag) {
            let ids = [this.stageInfo.stageTaskUserId];
            // console.log(ids);
            if(flag === 1) {
            }else if(flag === 2) {
            }else if(flag === 3) {
            }
            let obj = {
                vale: ids.join(',')
            }
            this.$HTTP("post", "/stageTaskUser_update", obj)
            .then(res => {
                console.log("修改完成状态", res);
                this.reminderText = '您已将这部分工作标记为完成，请确认是否将工作成果移交给下一阶段使用';
                this.remarkStateFlag = true;
            })
            .catch(err => {
                console.log("修改完成状态失败", err);
            });
        },

        // 状态管理
        marketState(flag) {
            if(flag === 'finish') { // 标记完成

            }else { // state状态管理

            }
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

