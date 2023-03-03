<template>
  <div id="page-account">
    <active-code-modal 
      v-if="isShowCodeDialog"
      :sponsor="code.sponsor"
      :time="code.time"
      @closeDialog="getVipConcer" />
    <PageHeader
        bgcolor="#4A25BA"
        color="#9791AA"
        selected-color="#FFFFFF"
        />

    <div class="getai__page_max_width_content">
    </div>
    <!-- <UserLogin />  -->
  </div>
</template>
<script>
import { mapGetters } from 'vuex';
import PageHeader from "~/components/Header.vue"
import ActiveCodeModal from '~/components/Account/activeCodeModal.vue'
// import UserLogin from '~/components/Account/tempLogin.vue'

// import { HOST } from '~/api/config.js'


export default {
  name: 'PageAccount',
  components: { PageHeader, ActiveCodeModal },
  computed: {
    ...mapGetters({
      isLogin: 'user/isLogin',
      operationSystem: 'version/operationSystem',
    }),

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
      configType: {},
      expTicketList: [],
      isShowCodeDialog: false, // 展示激活码兑换模态框
      searchCoupon: {
        pageNum: 1,
        pageSize: 8,
        t: {
          userId: '',
          status: 1, //用户已领取并且未使用
          channel: '',
          cashCoupon: {
            status: 1 //后台未设置下架
          }
        }
      },
      searchExp: {
        pageNum: 1,
        pageSize: 8,
      },
      searchPaid: {
        pageNum: 1,
        pageSize: 10,
        t: {}
      },
      coupons: {
        pageNum: 1,
        pageSize: 12,
        total: 0,
        list: {
          list: []
        }
      },
      expTicketQuery: {
        pageNum: 1,
        pageSize: 20,
        total: 0,
        list: [],
      },
      payRecord: {
        pageNum: 1,
        pageSize: 10,
        total: 0,
        list: []
      },
      code: {
        sponsor: '',
        time: ''
      },
    }
  },
  mounted () {
    console.log(this.isLogin)
    const sourceFrom = this.$route.query.from
    if (sourceFrom) {
      localStorage.setItem('sourceFrom', sourceFrom)
    }
    setTimeout(() => {
      if (this.isLogin) {
        this.isShowCodeDialog = true
      } else {
        this.isShowCodeDialog = true
        this.$store.dispatch('user/SHOW_LOGIN')
      }
    }, 1000)
   
  },
  methods: {
    // 展示
    isShowCodeDialogShow () {
      this.isShowCodeDialog = true
    },
    isShowCodeDialogHide () {
      this.isShowCodeDialog = false
    },
    getVipConcer () {
      console.log(this.isLogin)
      if (this.isLogin) {
        this.$router.push("/account")
      } else {
        this.$router.push("/")
      }
    }
  }
}
</script>

<style lang="stylus">
.el-pagination {
  text-align center
}
html{
    background:rgba(0,0,0,0.7)
}
</style>

