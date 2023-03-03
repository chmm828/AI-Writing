<template>
  <div id="page-account">
    <PageHeader
        bgcolor="#4A25BA"
        color="#9791AA"
        selected-color="#FFFFFF"
        />

    <div class="content-center getai__page_max_width getai__page_min_width">
      <div class="column-left">
        <div class="nav-left">
          <nuxt-link
              v-for="item in navList"
              :key="item.lable"
              :to="item.link">
            <div class="nav-item"
                :class="{
                  selected: item.key === selectedNavItem
                }">
                <!-- <div class="icon" :style="item.icon | bgimg"></div> -->
                <div class="label">
                  {{ item.label }}
                </div>
              </div>
            </nuxt-link>
        </div>
      </div>
      <div class="column-main">
        <div class="title-box info-box">
          <div class="title-text">
            <div class="text" :class="{selected: item === titleSelected}" v-for="item in titlelist" :key="item" @click="titleSelected = item">
              {{item}}
            </div>
          </div>
          <div v-if="titleSelected === '已添加平台账号'">
            <div class="info-list" v-if="platformList && platformList.length">
              <div class="item" v-for="(item, index) in platformList" :key="index">
                <div class="top">
                  <img :src="item.img" alt="" class="item-img">
                  <div class="part">
                    <div class="item-name">{{item.nickname || '暂无昵称'}}</div>
                    <div class="item-account">{{item.accountNumber}}</div>
                  </div>
                </div>
                <div class="bottom">
                  <div class="status">状态：<span :class='{red: !item.status}' @click.stop='editAccount(item)'>{{item.status ? '正常' : '未登录'}}</span></div>
                  <div class="disbind" @click="disbind(item)">解绑</div>
                </div>
              </div>
            </div>
            <div class="info-list info new-scrollbar-w10px" v-else>
              <div class="info">还未绑定平台账号，请<span @click="isPublishAccountAddModalShow = true">添加账号</span></div>
            </div>
          </div>

          <div v-if="titleSelected === '添加账号'">
            <div class="info-list">
              <AccountToAddList
                :isPlain='true'
                @add='addPlatform'/>
            </div>
          </div>
          
        </div>
      </div>
    </div>

    <ModalAddPublishAccount
        v-if='isPublishAccountAddModalShow'
        @close='isPublishAccountAddModalShow = false'/>

    <ModalAddSinglePublishAccount
        v-if='isAddSingle'
        :account='singleAccount'
        @close='isAddSingle = false'/>

    <ModalEditPublishAccount
        v-if='isPublishAccountEditModalShow'
        :account='publishEditAccount'
        @close='isPublishAccountEditModalShow = false'/>

    <ModalDisbindPublishAccount
        v-if='isPublishAccountDisbindModalShow'
        :account='publishDisbindAccount'
        @close='isPublishAccountDisbindModalShow = false'/>

  </div>
</template>
<script>
import { mapGetters } from 'vuex'
import PageHeader from "~/components/Header.vue"
import FilterBgimg from '~/utils/filter/backgroundImage.js'
import PublishFilter from '~/utils/filter/publishColumn.js'
import ModalAddPublishAccount from '~/components/ComposePage/PublishColumn/AccountAdd'
import ModalAddSinglePublishAccount from '~/components/ComposePage/PublishColumn/AccountAddSingle'
import ModalEditPublishAccount from '~/components/ComposePage/PublishColumn/AccountEdit'
import ModalDisbindPublishAccount from '~/components/ComposePage/PublishColumn/AccountDisbind'
import AccountToAddList from '~/components/ComposePage/PublishColumn/AccountToAddList'

export default {
  name: 'PagePlatForm',
  components: { 
    PageHeader,
    ModalAddPublishAccount,
    ModalAddSinglePublishAccount,
    ModalEditPublishAccount,
    ModalDisbindPublishAccount,
    AccountToAddList,
  },
  computed: {
    ...mapGetters({
      platformList: 'platform/platformList'
    }),
    pagePath () {
      return this.$route.path
    },
    userinfo () {
      return this.$store.state.user.info || {}
    },
  },
  head () {
    return {
      title: '个人中心-Get智能写作，开启AI写作新时代',
      meta: [
        { charset: 'utf-8' },
        { name: 'description', content: '个人中心，查看个人账户' },
        { name: 'keywords', content: '个人中心' },
        { hid: 'viewport', name: 'viewport', content: 'width=device-width, initial-scale=1.0, user-scalable=no' },
      ]
    }
  },
  data () {
    return {
      navList: [
        {
          label: '我的账户',
          key: 'my-account',
          icon: '',
          link: '/account',
        },
        {
          label: '账号信息',
          key: 'account-info',
          icon: '',
          link: '/account/info',
        },
        // {
        //   label: '平台账号列表',
        //   key: 'platform-list',
        //   icon: '',
        //   link: '/account/platform',
        // },
      ],
      titleSelected: '已添加平台账号',
      titlelist: ['已添加平台账号', '添加账号'],
      selectedNavItem: 'platform-list',
      isPublishAccountAddModalShow: false,
      publishEditAccount: {},
      publishDisbindAccount: {},
      isPublishAccountEditModalShow: false,
      singleAccount: '',
      isAddSingle: false,
      isPublishAccountDisbindModalShow: false
    }
  },
  filters: {
    ...PublishFilter,
    bgimg: FilterBgimg,
  },
  async mounted () {
    await this.$store.dispatch('platform/loadPlatformList')
  },
  methods: {
    editAccount (item) {
      if (item.status) return
      this.publishEditAccount = item
      this.isPublishAccountEditModalShow = true
    },
    addPlatform (e) {
      this.singleAccount = e
      this.isAddSingle = true
    },
    disbind (item) {
      this.publishDisbindAccount = item
      this.isPublishAccountDisbindModalShow = true
    }
  }
}
</script>
<style lang="stylus" scoped>
  @import "../../components/Nav/CardSubBar.styl"

  COL_LEFT_WIDTH = 215px;
  CONTENT_PADDING_TOP = 30px;
  COL_MARGIN = 24px;

  #page-account {
    background: #eee;
    // padding-top: CONTENT_PADDING_TOP;
    // margin-top: CONTENT_PADDING_TOP;
  }
  .content-center {
    margin: 0 auto;

    margin-top: CONTENT_PADDING_TOP;
  }

  .column-left {
    display block
    float left

    width COL_LEFT_WIDTH
  }
  .column-main {
    display: block;
    margin-left: (COL_LEFT_WIDTH + COL_MARGIN);
  }

  .nav-left {
    background: #FFFFFF;
    box-shadow: 0 1px 6px 0 rgba(0,0,0,0.05);
    padding: 6px 0;

    .nav-item {
      display: flex;
      flex-direction row
      align-items center
      justify-content flex-start
      padding: 12px 24px;
      cursor: pointer;

      .icon {
        background-color: #D8D8D8;
        background-size: cover;
        background-repeat: no-repeat;

        width 20px;
        height 20px

        border-radius 50%
        margin-right: 12px;
      }
      .label {
        font-size: 16px;
        color: #5C5C5C;
        letter-spacing: 0.3px;
        line-height: 16px;
      }

      &:hover,
      &.selected {
        .label {
          color: #06070D;
        }
      }
    }
  }

  .title-box {
    background-color: white;
    margin-bottom: COL_MARGIN;
    box-shadow: 0 1px 6px 0 rgba(0,0,0,0.05);

    .title-text {
      padding: 12px 24px;
      overflow hidden
      border-bottom: 1px solid #F2F2F2;

      .text {
        font-size: 16px;
        letter-spacing: 0.3px;
        line-height: 30px;
        color: #999;
        display: inline-block;
        margin-right: 24px;
        cursor: pointer;
        &.selected {
          color: #06070D;
        }
      }

      .right {
        float: right;
        line-height: 30px;
      }
    }

    .info-list {
      display: flex;
      align-items flex-start;
      padding: 24px;
      padding-bottom: 30px;
      min-height: 20vh;
      &.info {
        display flex
        align-items center
        justify-content center
      }
      .info {
        font-size: 12px;
        color: #C0C4CC;
        letter-spacing: 0.23px;
        line-height: 18px;
        span {
          color: #4A25BA;
          cursor: pointer;
        }
      }
      .item {
        background: #F8F8FB;
        border-radius: 8px;
        margin-right 10px;
        padding 10px;
        .top {
          display: flex;
          align-items center;
          margin-bottom 10px;
          .item-img {
            width: 36px;
            height 36px;
            border-radius 100%
            margin-right 10px
          }
          .part {
            width 150px
            overflow hidden
            .item-name {
              font-size: 14px;
              color: #1E1E1E;
              letter-spacing: 0.27px;
              white-space nowrap
              text-overflow ellipsis
              overflow hidden
            }
            .item-account {
              font-size: 12px;
              color: #666666;
              letter-spacing: 0.23px;
              white-space nowrap
              text-overflow ellipsis
              overflow-x hidden
            }
          }
        }
        .bottom {
          display: flex;
          align-items center;
          justify-content space-between;
          .status {
            font-size: 12px;
            color: #999999;
            letter-spacing: 0.23px;
            line-height: 12px;
            span {
              color #2CC26A
              &.red {
                color #ED4744
                cursor pointer
              }
            }
          }
          .disbind {
            font-size: 12px;
            color: #999999;
            letter-spacing: 0.23px;
            line-height: 12px;
            cursor pointer
          }
        }
      }
    }
  }
</style>

