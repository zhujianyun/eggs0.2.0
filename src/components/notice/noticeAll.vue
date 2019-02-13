<template>
    <div class="all_notice">
        <div class="all_notice_top">
            <span class="fl top_fl">全部通知</span>
            <i class="iconfont icon-guanbijiantou fr" @click="returnBack"></i>
            <span v-if='isAllRead' class="fr top_fr" @click='clickAllRead'>标记全部已读</span>
            <span v-else class="fr top_fr cur_dis">标记全部已读</span>
        </div>
        <div class="all_notice_list">
            <div 
                class="all_notice_every clearfix"
                :class="item.isRead ? 'is_read' : false"
                v-for='(item, index) in noticeList'
                :key='item.noticeId'
                >
                <span class="left_header fl">
                    <img :src="item.noticeFromHeader" alt="">
                </span>
                <div class="right_detail fl">
                    <div class="notice_from">{{item.noticeFromName}}</div>
                    <div class="notice_type" @click="enterDetail">{{item.npticeContent}}</div>
                </div>
                <div class="hover_show">
                    <span class="notice_time">{{item.noticeTime}}</span>
                    <i 
                        v-if='!item.isRead'
                        class='iconfont icon-yidu'
                        @click='clickRead(index)'
                    ></i>
                </div>
                <div class="bottom_line"></div>
            </div>
            <div class="load_more">查看更多通知</div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            fromPath: '',
            noticeList: [
                {
                    noticeId: 1,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后：这里是评论的内容，只显示一行，然后',
                    isRead: false,
                },
                {
                    noticeId: 2,
                    noticeFromId: 12,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后...',
                    isRead: false,
                },
                {
                    noticeId: 3,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后...',
                    isRead: false,
                },
                {
                    noticeId: 4,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '刚刚',
                    npticeContent: '评论时@你：具体的内容信息 @原娣 具体的是...',
                    isRead: false,
                },
                {
                    noticeId: 5,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '刚刚',
                    npticeContent: '评论时@你：具体的内容信息 @原娣 具体的是...',
                    isRead: true,
                },
                {
                    noticeId: 11,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后：这里是评论的内容，只显示一行，然后评论：这里是评论的内容，只显示一行，然后：这里是评论的内容，只显示一行，然后评论：这里是评论的内容，只显示一行，然后：这里是评论的内容，只显示一行，然后',
                    isRead: true,
                },
                {
                    noticeId: 12,
                    noticeFromId: 12,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后评论：这里是评论的内容，只显示一行，然后：这里是评论的内容，只显示一行，然后',
                    isRead: true,
                },
                {
                    noticeId: 13,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '2019/02/13 11:11:00',
                    npticeContent: '评论：这里是评论的内容，只显示一行，然后...',
                    isRead: true,
                },
                {
                    noticeId: 14,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '刚刚',
                    npticeContent: '评论时@你：具体的内容信息 @原娣 具体的是...',
                    isRead: true,
                },
                {
                    noticeId: 15,
                    noticeFromId: 11,
                    noticeFromName: '卢洪臣',
                    noticeFromHeader: '/upload/user/b2a50343-9ebe-42de-845b-dae8d6889f1f.png',
                    noticeType: 1,
                    noticeTime: '刚刚',
                    npticeContent: '评论时@你：具体的内容信息 @原娣 具体的是...',
                    isRead: true,
                },
            ]
        }
    },
    computed: {
        isAllRead() {
            let have = this.noticeList.findIndex(ele => !ele.isRead);
            if(have !== -1) {
                return true;
            }else {
                return false;
            }
        }
    },
   
    methods: {
        returnBack() {
            if(this.fromPath) {
                this.$router.push(this.fromPath);
            }else {
                this.$router.push('/project');
            }
        },
        // 标记全部已读
        clickAllRead() {
            this.noticeList.map(ele => ele.isRead = true);
        },
        // 点击标记已读
        clickRead(index) {
            this.noticeList[index].isRead = true;
            this.noticeList = this.noticeList.concat();
        },
        // 进入通知详情
        enterDetail() { 
            this.$router.push('/noTask');
        },
    },
    created() {
    },
    beforeRouteEnter(to, from, next) {
        next((vm) => {
            vm.fromPath = from.path;
        });

    }
}
</script>
<style lang='less'>
@import "../../assets/css/base.less";
    .all_notice {
        width: 100%;
        height: 100%;
        .all_notice_top {
            height: 50px;
            line-height: 50px;
            border-bottom: 1px solid #EEEEEE;
            .top_fl {
                margin-left: 24px;
                font-size: 16px;
                font-weight: bold;
            }
            .top_fr {
                margin-right: 24px;
                color: @grayNight;
                .cur;
                &:hover {
                    color: @mainColor;
                }

            }
            .icon-guanbijiantou {
                margin-right: 24px;
                font-size: 14px;
                font-weight: bold;
                color: @graySix;
                &:hover {
                    color: @mainColor;
                }
            }
        }
        .all_notice_list {
            width: 100%;
            height: calc(100% - 51px);
            overflow-x: hidden;
            overflow-y: auto;
            .all_notice_every {
                width: 100%;
                padding: 16px 0 16px 24px;
                .box_sizing;
                position: relative;
                .left_header {
                    width: 28px;
                    height: 28px;
                    img {
                        width: 100%;
                        height: 100%;
                        overflow: hidden;
                        .border_radius(@br: 4px;);
                    }
                }
                .right_detail {
                    margin-left: 10px;
                    width: calc(100% - 24px - 28px - 10px);
                    .notice_from {
                        font-weight: bold;
                        text-align: left;
                        line-height: 1;
                    }
                    .notice_type {
                        text-align: left;
                        line-height: 1;
                        margin-top: 10px;
                        width: 100%;
                        .word_over;
                    }
                }
                .hover_show {
                    position: absolute;
                    top: 16px;
                    right: 24px;
                    .notice_time {
                        color: @grayNight;
                        font-size: 12px;
                    }

                    .icon-yidu {
                        display: none;
                        font-size: 12px;
                        color: @grayNight;
                        margin-left: 10px;
                        &:hover {
                            color: @mainColor;
                        }
                    }
                }
                .bottom_line {
                    width: calc(100% - 24px * 2);
                    position: absolute;
                    bottom: 0;
                    padding: 0 24px;
                    .box_sizing;
                    border-bottom: 1px solid @bg-f2f2f2;

                }
                &:hover {
                    background: @bg-f2f2f2;
                    .icon-yidu {
                        display: inline-block;
                    }
                }
            }
            .is_read {
                opacity: 0.6;
            }
        }
        .load_more {
            color: @mainColor;
            line-height: 50px;
            .cur;
            margin-left: 24px;
            &:hover {
                text-decoration: underline;
            }
        }
    }
</style>

