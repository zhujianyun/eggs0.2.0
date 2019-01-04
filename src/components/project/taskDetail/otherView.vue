<template>
	<div class="other_view" @click.stop='() => {}'>
		<div class="top_header">
			<div class="file_name header_item" @click='sortHandle(0)'>
				<span>文件</span>
			</div>
			<div class="file_from header_item" @click='sortHandle(1)'>
				<span>上传者</span>
				<i 
					v-if='sortType === 1' 
					class="iconfont icon-jiantou_xiangxia"
					:class="sortOrder ? '' : 'sort_rotate'"
					></i>

			</div>
			<div class="file_time header_item" @click='sortHandle(2)'>
				<span>上传日期</span>
				<i 
					v-if='sortType === 2' 
					class="iconfont icon-jiantou_xiangxia"
					:class="sortOrder ? '' : 'sort_rotate'"
					></i>
			</div>
			<div class="file_message header_item">
				<i class="iconfont icon-pinglun"></i>
			</div>
			<div class="file_more header_item">
				<i class="iconfont icon-gengduo"></i>
			</div>
		</div>
    <!-- 有分组 -->
		<div v-if='sortType === 0' class="group_box">
			<div 
				class="parths_group"
				v-for="group in fileGroup"
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
				<div class="group_file">
					<div 
						class="every_files"
						v-for="file in group.fileList"
						:key='file.FilePkid'
						>
						<div class="file_name every_common">
							<el-checkbox v-model='file.checked' @change='checkboxChange($event, file, 0)'></el-checkbox>
							<img :src="'http://server.apexgame.cn'+file.Url" alt="">
							<span class="file_title">{{file.FileName}}</span>
						</div>
						<div class="file_from every_common">
							<span>{{file.userName}}</span>
						</div>
						<div class="file_time every_common">
							<span>2018/12/29 19:20</span>
						</div>
						<div class="file_message every_common">
							<i class="iconfont icon-pinglun"></i>
							<span>12</span>
						</div>
						<div class="file_more every_common">
							<i class="iconfont icon-gengduo"></i>
						</div>
					</div>
				</div>
      </div>
		</div>
    <!-- 无分组 -->
    <div v-else class="group_box">
      <div class="no_group parths_group">
        <div class="group_file">
          <div 
            class="every_files"
            v-for="file in fileList"
            :key='file.FilePkid'
            >
            <div class="file_name every_common">
              <el-checkbox v-model='file.checked' @change='checkboxChange($event, file, 1)'></el-checkbox>
              <img :src="'http://server.apexgame.cn'+file.Url" alt="">
              <span class="file_title">{{file.FileName}}</span>
            </div>
            <div class="file_from every_common">
              <span>{{file.userName}}</span>
            </div>
            <div class="file_time every_common">
              <span>2018/12/29 19:20</span>
            </div>
            <div class="file_message every_common">
              <i class="iconfont icon-pinglun"></i>
              <span>12</span>
            </div>
            <div class="file_more every_common">
              <i class="iconfont icon-gengduo"></i>
            </div>
          </div>
        </div>
      </div>
    </div>
	</div>
</template>
<script>
import { mapState, mapMutations } from 'vuex';

export default {
  props: ['list'],
  data() {
    return {
      fileList: [], // 未分组文件列表
			fileGroup: [], // 文件分组
			checked: false,
      sortType: 0, // 0--按文件名排序 2--按上传者排序 3--按上传时间排序
      sortOrder: true, // 排序顺序：true--正序 false--倒序
      selectedList: [], // 获取
    };
  },
  watch: {
    sortType(val) {
      if(val) { // 2--按上传者排序 3--按上传时间排序
        for(let y  of this.fileList) {
          let indexs = this.selectedList.findIndex(ele => ele.FilePkid == y.FilePkid);
          indexs !== -1 && (y.checked = this.selectedList[indexs].checked);
        }
      }else { // 0--按文件名排序
        for(let x of this.fileGroup) {
          for(let y  of x.fileList) {
            let indexs = this.selectedList.findIndex(ele => ele.FilePkid == y.FilePkid);
            indexs !== -1 && (y.checked = this.selectedList[indexs].checked);
          }
        }
      }
    },
    selectedList(val) {
      this.CHECKEDLIST_CHANGE(val);
      // console.log(val);
    }
    
  },
  computed: {
    ...mapState([
      'checkedFileList'
    ]),
  },
  methods: {
    ...mapMutations([
      'CHECKEDLIST_CHANGE',
    ]),
    // 文件选中状态改变
    checkboxChange(val, item, flag) {
      this.selectedListChange(val, item, flag);
      if(flag) { // 没有分组
        this.fileList = this.fileList.concat();
      }else { // 有分组
        this.fileGroup = this.fileGroup.concat();
      }
    },
    // 已选列表改变
    selectedListChange(val, item, flag) {
      if(val) { // 选中
        this.selectedList.push(item);
      }else { // 取消
        let x = this.selectedList.findIndex(ele => ele.FilePkid === item.FilePkid);
        x !== -1 && (this.selectedList.splice(x, 1));
      }
    },

		// 文件排序
		sortHandle(type, sort) {
			if(type) { // 按上传者/上传日期排序
				if(type == 1) { // 按上传者排序
					if(this.sortType === 1) {
						this.sortOrder = !this.sortOrder;
					}else {
						this.sortOrder = true;
					}
					this.sortType = 1;
				}else { // 按上传日期排序
					if(this.sortType === 2) {
						this.sortOrder = !this.sortOrder;
					}else {
						this.sortOrder = true;
					}
					this.sortType = 2;
				}
				// 发送请求--排序
			}else { // 恢复默认排序
				this.sortType = 0;
				// 发送请求--恢复默认排序
			}
		},
    // 新建/编辑分组
    groupTitleBlur(group) {
      if (group.createdGroup) {
        // 新建
        // 发送请求---新建分组
        group.edit = false;
        delete group.createdGroup;
        console.log("新建分组成功");
      } else {
        // 编辑
        // 先判重，如果有重复的名字--提示，否则--发送请求
        let repeat = this.fileGroup.findIndex(
          ele => ele.groupName === group.groupName
        );
        if (repeat !== -1) {
          this.$message.warning("已含有同名分组名！");
        } else {
          // 发送修改分组名的接口
          console.log("分组名修改成功");
        }
      }
    },

    // 文件的更多操作
    fileCommand(type) {
      if (type === "download") {
        // 下载
        return;
      }
      if (type === "collect") {
        // 收藏
        return;
      }
      if (type === "transfer") {
        // 移交

        return;
      }
      if (type === "rename") {
        // 重命名

        return;
      }
      if (type === "delete") {
        // 删除

        return;
      }
    },

    // 组的更多操作
    fileGroupCommand() {
      if (type === "upload") {
        // 上传文件
        return;
      }
      if (type === "download") {
        // 下载
        return;
      }
      if (type === "collect") {
        // 收藏
        return;
      }
      if (type === "transfer") {
        // 整组移交

        return;
      }
      if (type === "rename") {
        // 重命名

        return;
      }
      if (type === "delete") {
        // 删除

        return;
      }
    },

    // 关闭此视图触发的方法
    close() {
      return this.selectedList;
    },
  },
  created() {
    this.selectedList = [...this.checkedFileList];
    this.fileGroup = [...this.list];
    this.fileList = [];

    for(let x of this.list) {
      this.fileList.push(...x.fileList);
    }
    // 进行文件多选的回选
  },
  mounted() {
  }
};
</script>
<style lang="less">
.other_view {
  width: calc(100% - 24px * 2);
  height: calc(100% - 40px - 18px * 2);
  padding: 18px 24px;

  .file_name {
		width: calc(100% - 140px - 184px - 114px - 30px);
    // width: 344px;
    .el-checkbox {
      margin-left: 5px;
    }
    img {
      display: inline-block;
      width: 24px;
      height: 30px;
      margin: 0 10px;
      vertical-align: middle;
      margin-top: -4px;
    }
    .file_title {
			display: inline-block;
			width: calc(100% - 74px);
			overflow: hidden;
			text-overflow:ellipsis;
			white-space: nowrap;
			vertical-align: middle;
			height: 38px;
			line-height: 38px;
			margin-top: -4px;

    }
  }
  .file_from {
    width: 140px;
  }
  .file_time {
    width: 184px;
  }
  .file_message {
    width: 114px;
  }
  .file_more {
    width: 30px;
  }
  .top_header {
    overflow: hidden;
    width: 100%;
    .header_item {
      color: #999;
      text-align: left;
      height: 38px;
      line-height: 40px;
			float: left;
			cursor: pointer;
      .iconfont {
        color: #999;
				font-size: 14px;
				&:hover {
					color:#999 !important;
				}
      }
      .icon-jiantou_xiangxia {
        font-size: 12px;
        color: #3684ff;
			}
		}
		.file_name:hover, .file_from:hover, .file_time:hover {
			color: #3684FF;
		}
		.file_message, .file_more {
			cursor: auto !important;
			.iconfont {
				cursor: auto !important;
			}
		}
		.sort_rotate {
			display: inline-block;
			transform: rotate(-180deg);
			height: 38px;
		}
  }
  .group_box {
		width: 100%;
    height: calc(100% - 40px);
    overflow-y: auto;
    .parths_group {
    	width: 100%;
      .group_top {
        width: 100%;
        height: 20px;
        margin-bottom: 14px;
        margin-top: 18px;
        .group_name {
          font-weight: bold;
        }
        .edit {
          width: 136px;
          height: 24px;
          border: 1px solid #999;
          text-align: left;
          &::selection {
            background: #3684ff;
            color: #fff;
          }
        }
        .group_operate {
          height: 20px;
          .el-dropdown {
            height: 20px;
          }
        }
        .group_operate {
          float: right;
        }
			}
      .group_file {
        width: 100%;
        .every_files {
          width: 100%;
          height: 40px;
          line-height: 40px;
          border: 1px solid #eeeeee;
          box-sizing: border-box;
          margin-top: 6px;
          .every_common {
            height: 38px;
            line-height: 40px;
            font-size: 12px;
            float: left;
            .iconfont {
              font-size: 12px;
            }
          }
          &:hover {
            border-color: #3684FF;
          }
        }
      }
    }
  }
}
</style>