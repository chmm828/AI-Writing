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
            <div class="text">
              账号信息
            </div>
          </div>

          <div class="info-list">
            <div class="info-item"
                v-for="info in infoList"
                :key="info.key">
              <div class="left">
                <div class="title">
                  {{ info.title }}
                </div>
                <div class="value"
                    v-if="info.label">
                  <div class="avatar" v-if="info.key === 'avatar'">
                    <img :src="info.label" alt="" />
                  </div>
                  <span class="text" v-else>
                    {{ info.label }}
                  </span>
                </div>
              </div>

              <div class="right actions">
                <div class="btn-change"
                    @click="changeRow(info)"
                    v-if="info.isActionEnabled">
                  {{ info.actionsName }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import PageHeader from "~/components/Header.vue"
import FilterBgimg from '~/utils/filter/backgroundImage.js'

export default {
  name: 'PageAccount',
  components: { PageHeader, },
  computed: {
    pagePath () {
      return this.$route.path
    },
    userinfo () {
      return this.$store.state.user.info || {}
    },
    infoList () {
      const userinfo = this.userinfo

      const headers = [
        { 
          title: '头像',
          key: 'avatar',
          label: userinfo.avatarUrl,

          isActionEnabled: false,
          actionsName: '更换头像',
        },
        { 
          title: '昵称',
          key: 'nickname',
          label: userinfo.nickName,
          isActionEnabled: false,
          actionsName: '修改',
        },
        { 
          title: '密码',
          key: 'password',
          label: '已设置',
          isActionEnabled: false,
          actionsName: '修改',
        },
        { 
          title: '绑定手机',
          key: 'phone',
          label: userinfo.mobileNum,
          isActionEnabled: false,
          actionsName: '修改绑定',
        },
        { 
          title: '绑定微信',
          key: 'wechatAccount',
          label: userinfo.unionId ? '已绑定' : '未绑定',
          isActionEnabled: false,
          actionsName: '修改绑定',
        },
      ]
      return headers
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
      selectedNavItem: 'account-info',
    }
  },
  filters: {
    bgimg: FilterBgimg,
  },
  mounted () {
  },
  methods: {
    changeRow (row) {
      console.log(row.title)
    },
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
        color: #06070D;
        letter-spacing: 0.3px;
        line-height: 30px;

        display: inline-block;
      }

      .right {
        float: right;
        line-height: 30px;
      }
    }

    .info-list {
      display: block;
      padding: 0 24px;
      padding-bottom: 30px;

      .info-item {
        padding-top: 16px;
        padding-bottom: 12px;
        border-bottom: 1px solid #F2F2F2;

        overflow: hidden;

        .left {
          display: inline-block;
        }
        .title {
          display: inline-block;
          font-size: 14px;
          color: #8E8D8D;

          width: 89px;
        }
        .value {
          display: inline-block;
          font-size: 16px;
          color: #06070D;
          letter-spacing: 0.3px;
          line-height: 16px;
        }

        .avatar {
          vertical-align: top;

          img {
            background-color: #D8D8D8;
            border: 1px solid #979797;
            border-radius: 50%;

            height: 42px;
            width: 42px;
          }
        }
        .right {
          display: inline-block;
          float: right;
        }

        .btn-change {
          font-size: 14px;
          color: #4A25BA;
          letter-spacing: 0.27px;
          line-height: 14px;

          cursor: pointer;
        }
      }
    }
  }
</style>

