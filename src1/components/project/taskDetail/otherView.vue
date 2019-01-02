<template>
	<div class="other_view">
		<div class="top_header">
			<div class="file_name header_item">
				<span>文件</span>
				<i v-if='sortType === 1' class="iconfont icon-jiantou_xiangxia"></i>
			</div>
			<div class="file_from header_item">
				<span>上传者</span>
				<i v-if='sortType === 2' class="iconfont icon-jiantou_xiangxia"></i>

			</div>
			<div class="file_time header_item">
				<span>上传日期</span>
				<i v-if='sortType === 3' class="iconfont icon-jiantou_xiangxia"></i>
			</div>
			<div class="file_message header_item">
				<i class="iconfont icon-pinglun"></i>
			</div>
			<div class="file_more header_item">
				<i class="iconfont icon-gengduo"></i>
			</div>
		</div>
		<div class="group_box">
			<div 
	            class="parths_group"
	            v-for="(group, index) in fileGroup"
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
	            		v-for="file in group.list"
	            		:key='file.Pkid'
	            		>
	            		<div class="file_name every_common">
	            			<el-checkbox v-model='checked'></el-checkbox>
	            			<img :src="'http://server.apexgame.cn'+file.Url" alt="">
							<span>{{file.Title}}</span>
						</div>
						<div class="file_from every_common">
							<span>{{file.Uname}}</span>
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
export default {
	data() {
		return {
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
		            "/upload/file/20181226081930/14707eb6-6f74-4819-844c-76afc08de9b9/8f37b596-48ae-4262-b241-b	eee53d646a3_s1.png",
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
		          id: 1,
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
		    fileGroup: [], // 文件分组
		    sortType: 1, // 1--按文件名排序 2--按上传者排序 3--按上传时间排序
		    checked: false,
		}
	},
	methods: {
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

	},
	created() {
		let obj = {
			id: 0,
	        groupTitle: "未分组文件",
	        border: false,
	        dragDisabled: false,
	        packUp: null,
	        list: this.fileList
		}
		this.fileGroup.push(obj);
		this.fileGroup.push(...this.parthsGroup);

	}
}

</script>
<style lang="less">
	.other_view {
		width: calc(100% - 24px * 2);
		height: calc(100% - 18px * 2);
		padding: 18px 24px;

		.file_name {
			width: 344px;
			.el-checkbox {
				margin-left: 5px;
			}
			img {
				width: 24px;
				height: 30px;
				margin: 4px 10px;
				vertical-align: middle;
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
			width: 814px;
			.header_item {
				color: #999;	
				text-align: left;
				height: 38px;
				line-height: 40px;
				float: left;
				.iconfont {
					color: #999;
					font-size: 14px;
				}
				.icon-jiantou_xiangxia {
					font-size: 12px;
					color: #3684FF;
				}

			}
		}
		.group_box {
			width: 830px;
			height: calc(100% - 40px);
			overflow-y: auto;
			.parths_group {
				width: 814px;
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
	                  background: #3684FF;
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
	            		border: 1px solid #EEEEEE;
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
	            	}
	            }
			}
		}
	}
</style>