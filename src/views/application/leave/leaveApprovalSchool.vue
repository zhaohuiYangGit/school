<template>
  <div class="leaveApprovalSchool">
    <div v-if="navback">
      <back-button></back-button>
    </div>
    <van-nav-bar
      title="请假审批"
      left-text="返回"
      left-arrow
      fixed
      @click-left="onClickLeft"/>
    <div class="top">
      <div class="flexbox personal-info between" slot="header">
        <div class="flexbox center">
          <div class="avatar">
            <img :src="$store.state.system.sftpPath + leaveData.file"
          :onerror="defaultImg" alt="">
          </div>
          <div>
            <div class="name">{{leaveData.userName}}</div>
            <div class="result warning" v-if="leaveData.offState === 0">待审核</div>
            <div class="result success" v-else-if="leaveData.offState === 1">已通过</div>
            <div class="result warning" v-else-if="leaveData.offState === 3">审核中</div>
            <div class="result danger" v-else>未通过</div>
          </div>
        </div>
      </div>
      <ul class="list">
        <li>
          <span class="title">所在院系</span>
          <span class="text">{{leaveData.professionName}}</span>
        </li>
        <li>
          <span class="title">所在班级</span>
          <span class="text">{{leaveData.className}}</span>
        </li>
        <li>
          <span class="title">请假类型</span>
          <span class="text">{{leaveData.leaveTypeText}}</span>
        </li>
        <li>
          <span class="title">开始时间</span>
          <span class="text">{{leaveData.startTime}}</span>
        </li>
        <li>
          <span class="title">结束时间</span>
          <span class="text">{{leaveData.endTime}}</span>
        </li>
        <li>
          <span class="title">请假时长</span>
          <span class="text">{{leaveData.leaveDay}}</span>
        </li>
        <li>
          <span class="title">请假理由</span>
          <span class="text">{{leaveData.leaveReason}}</span>
        </li>
        <li>
          <span class="title">图片</span>
          <div class="img">
            <img v-if="leaveData.photo" :src="$store.state.system.sftpPath + leaveData.photo">
            <span v-if="!leaveData.photo">暂无图片</span>
          </div>
        </li>
      </ul>
    </div>
    <div class="top">
      <div class="between">
        <ul class="state">
          <li v-for="(item, index) in leaveFlow" :key="index">
            <img :src="$store.state.system.sftpPath + item.zp"
            :onerror="defaultImg" alt="">
            <span></span>
            <span class="gray">{{item.userName}}</span>
            <span class="gray" v-if="!item.status">{{item.operator}}</span>
            <span class="success" v-if="item.status === '已通过'">已通过</span>
            <span class="danger" v-if="item.status === '未通过'">未通过</span>
            <span class="warning" v-if="item.status === '审核中'">审核中</span>
            <span>
              {{item.operatorTime}}
            </span>
            <br>
            <p class="describe">{{item.option}}</p>
          </li>
        </ul>
      </div>
    </div>
    <div class="btn-group">
      <van-button size="large" class="btn blue" @click="agree()">同意</van-button>
      <van-button size="large" class="btn" @click="disagree()">拒绝</van-button>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import { getLeaveDetail } from '@/services/leave';
import backButton from '@/components/backButton/backButton.vue';

export default {
  name: 'leaveApprovalSchool',
  data() {
    return {
      defaultImg: `this.src="${require('@/assets/tx@2x.png')}"`,
      leaveData: [],
      leaveFlow: [],
      leaveInfo: {
        name: '',
        leaveStatus: '',
        leaveDept: '',
        leaveClass: '',
        leaveType: '',
        leaveStart: '',
        leaveEnd: '',
        leaveDuration: '',
        leaveReason: '',
        leaveImg: [],
      },
      appJSObject: window.AppJSObject,
      navback: false,
    };
  },
  components: {
    'back-button': backButton,
  },
  computed: {
    ...mapGetters('system', ['userInfo']),
  },
  methods: {
    onClickLeft() {
      this.$router.go(-1);
    },
    async getDetail() {
      if (!this.$route.params.item) {
        this.navback = true;
        const { data } = await getLeaveDetail({ id: this.appJSObject.getDetailId() });
        this.leaveData = data;
        if (this.leaveData.applyOption) {
          this.leaveFlow = JSON.parse(this.leaveData.applyOption);
        }
      } else {
        const { data } = await getLeaveDetail({ id: this.$route.params.id });
        this.leaveData = data;
        if (this.leaveData.applyOption) {
          this.leaveFlow = JSON.parse(this.leaveData.applyOption);
        }
      }
    },
    agree() {
      let now = new Date();
      now = this.$moment(now).format('YYYY-MM-DD HH:mm:ss');
      this.leaveData.applyStatethree = '1';
      this.leaveFlow.push({
        userName: this.userInfo.userName,
        operator: '分管领导审核',
        operatorTime: now,
        option: '',
        status: '已通过',
      });
      if (this.leaveData.leaveDay > 15) {
        this.leaveData.offState = '1';
      } else {
        this.leaveData.offState = '3';
      }
      if (!this.$route.params.item) {
        this.$router.push({
          name: 'leaveOption',
          params: {
            approval: 'agree',
            leaveData: this.leaveData,
            leaveFlow: this.leaveFlow,
            itemId: this.appJSObject.getBeanId(),
          },
        });
      } else {
        this.$router.push({
          name: 'leaveOption',
          params: {
            approval: 'agree',
            leaveData: this.leaveData,
            leaveFlow: this.leaveFlow,
            itemId: this.$route.params.item.id,
          },
        });
      }
    },
    disagree() {
      let now = new Date();
      now = this.$moment(now).format('YYYY-MM-DD HH:mm:ss');
      this.leaveData.applyStatethree = '1';
      this.leaveFlow.push({
        userName: this.userInfo.userName,
        operator: '分管领导审核',
        operatorTime: now,
        option: '',
        status: '未通过',
      });
      if (this.leaveData.leaveDay > 15) {
        this.leaveData.offState = '2';
      }
      if (!this.$route.params.item) {
        this.$router.push({
          name: 'leaveOption',
          params: {
            approval: 'disagree',
            leaveData: this.leaveData,
            leaveFlow: this.leaveFlow,
            itemId: this.appJSObject.getBeanId(),
          },
        });
      } else {
        this.$router.push({
          name: 'leaveOption',
          params: {
            approval: 'disagree',
            leaveData: this.leaveData,
            leaveFlow: this.leaveFlow,
            itemId: this.$route.params.item.id,
          },
        });
      }
    },
  },
  mounted() {
    this.getDetail();
    // this.leaveData = this.$route.params.item;
    // this.leaveFlow = JSON.parse(this.leaveData.applyOption);
  },
};
</script>

<style lang="scss" scoped>
.leaveApprovalSchool {
  padding-top: 46px;
  padding-bottom: 50px;
  .success {
    color: #51bf61 !important;
  }
  .warning{
    color: #ea9542 !important;
  }
  .danger{
    color: #ff5757 !important;
  }
  .between{
    margin:0 12px;
  }
  .top{
    margin-top: 11px;
    background: #fff;
  }
  .personal-info{
    padding:15px 0px;
    border-bottom:1px solid #e7e7e7;
    .avatar{
      width: 48px;
      height: 48px;
      border-radius: 50%;
      overflow: hidden;
      margin-right: 4.266vw;
    }
    .name{
      font-size: 16px;
      color: rgb(51, 51, 51);
      line-height: 6.4vw;
    }
    .result{
      font-size: 14px;
      line-height: 5.3333vw;
    }
  }
  .list{
    padding: 12px;
    li{
      line-height: 30px;
      color:#333;
      .title{
        display: inline-block;
        width:66px;
        color:#999;
        margin-right: 14px;
        text-align: right;
      }
      .img {
        display: inline-block;
      }
    }
  }
  .state {
    padding-top: 15px;
    li {
      line-height: 48px;
      font-size: 15px;
      height: 76px;
      overflow: hidden;
      position: relative;
      img {
        width:48px;
        height: 48px;
        border-radius: 50%;
        margin: 0 15px 0 4px;
        float: left;
        position: relative;
        z-index: 999999;
      }
      span {
        display: inline-block;
      }
      .gray{
        color:#666;
      }
      .describe {
        font-size: 15px;
        line-height: 20px;
        margin-left: 67px;
        color:#333;
      }
    }
    li span:nth-child(2) {
      width: 2px;
      height: 100%;
      border-left: 1px dashed #c3c3c3;
      position: absolute;
      left: 26px;
      top: 2px;
    }
    li span:nth-child(3) {
      width: 60px;
      display: inline-block;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    li span:nth-child(4) {
      margin: 0 0 0 3.74vw;
    }
    li span:nth-child(5) {
      margin: 0 3.74vw 0 0;
      float: right;
      color: #999999;
      font-size: 1.95vh;
    }
  }
  li:last-child span:nth-child(2) {
    border: none;
  }
  .detail{
    padding: 15px 0;
    border-bottom:1px solid #e7e7e7;
  }
  .btn-group{
    padding: 0 38px;
    margin: 20px 0;
    .btn{
        font-size: 17px;
        margin: 0 auto 15px;
        border-radius: 30px;
        height: 6.8vh;
        line-height: 6.8vh;
        background-color: #FFFFFF;
        color: #1890ff;
        border: 1px solid #1890FF;
    }
    .blue{
        background-color: #1890ff;
        color: #FFFFFF;
    }
  }
}
</style>
