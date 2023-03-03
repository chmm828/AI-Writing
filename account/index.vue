<template>
  <div id="page-account">
    <PageHeader bgcolor="#4A25BA" color="#9791AA" selected-color="#FFFFFF" />

    <div class="content-center getai__page_max_width getai__page_min_width">
      <div class="column-left">
        <div class="nav-left">
          <nuxt-link v-for="item in navList" :key="item.lable" :to="item.link">
            <div
              class="nav-item"
              :class="{
                selected: item.key === selectedNavItem
              }"
            >
              <div class="label">
                {{ item.label }}
              </div>
            </div>
          </nuxt-link>
        </div>
      </div>
      <div class="column-main">
        <div class="title-box">
          <div class="title-text">
            <div class="text">
              我的账户
            </div>

            <div class="right">
              <a
                class="code-active"
                @click="showCodeDialog($route.query.receivesecret)"
              >
                激活码兑换VIP
              </a>
              <nuxt-link class="btn-purchase" to="/purchase">
                查看方案
              </nuxt-link>
            </div>
          </div>

          <div class="plan-cols">
            <div class="col-item">
              <div class="title">方案类型</div>
              <div
                class="value"
                :class="{
                  member: isMember
                }"
              >
                {{ userInfo.vipEnd | vipTime }}
              </div>
            </div>

            <div class="col-item">
              <div class="title">购买方案</div>
              <div
                class="value"
                :class="{
                  member: isMember
                }"
              >
                {{ membershipType }}
                <!-- - -->
              </div>
            </div>

            <div class="col-item">
              <div class="title">到期日期</div>
              <div
                class="value"
                :class="{
                  member: isMember
                }"
              >
                {{ userInfo.vipEnd | timeMode }}
              </div>
            </div>
          </div>
        </div>

        <!-- 优惠券 -->
        <div class="title-box tickets-box">
          <div class="title-text">
            <div
              class="text clickable"
              :class="{
                selected: ticketType === '优惠券'
              }"
              @click="ticketType = '优惠券'"
            >
              优惠券
            </div>
            <div
              class="text clickable"
              :class="{
                selected: ticketType === '体验券'
              }"
              @click="ticketType = '体验券'"
            >
              体验券
            </div>

            <div class="right">
              <p
                class="hint-text"
                @click="exchangeActivation(ticketType)"
                style="cursor: pointer; user-select: none"
              >
                兑换{{ ticketType }}
              </p>
            </div>
          </div>

          <!-- 优惠券 -->
          <div class="tickets-list" v-if="ticketType === '优惠券'">
            <div class="ticket-type-header">
              <div
                class="col clickable"
                :class="{ selected: couTicketType === '未使用' }"
                @click="changeCouponTicketType('未使用')"
              >
                未使用（{{ couTicketCount["未使用"] }}）
              </div>
              <div
                class="col clickable"
                :class="{ selected: couTicketType === '已使用' }"
                @click="changeCouponTicketType('已使用')"
              >
                已使用（{{ couTicketCount["已使用"] }}）
              </div>
              <div
                class="col clickable"
                :class="{ selected: couTicketType === '已过期' }"
                @click="changeCouponTicketType('已过期')"
              >
                已过期（{{ couTicketCount["已过期"] }}）
              </div>
            </div>
            <div
              class="exp-ticket-container"
              v-if="coupons.list.total && ticketType === '优惠券'"
            >
              <div
                class="ticket-item"
                :class="{
                  avalible:
                    ticket.status === 1 &&
                    ticket.cashCoupon.endTime > new Date().getTime(),
                  used: ticket.status === 2,
                  'out-of-date':
                    ticket.status === 1 &&
                    ticket.cashCoupon.endTime <= new Date().getTime()
                }"
                @click="useCoupon(ticket)"
                v-for="ticket in coupons.list.list"
                :key="ticket.id"
              >
                <img :src="ticket.cashCoupon.img" alt="" class="ticket-img" />
              </div>
            </div>
            <div class="placeholder" v-show="!coupons.list.total">
              暂无优惠券
            </div>
          </div>
          <!-- <div v-if="!coupons.list.total && ticketType === '优惠券'" class="ticket-alert">暂无优惠券</div> -->

          <el-pagination
            v-if="coupons.list.total && ticketType === '优惠券'"
            background
            layout="total, sizes, prev, pager, next"
            :page-sizes="[8, 16, 24, 32]"
            style="padding: 20px 0px;"
            @size-change="sizeChangeCoupon"
            @current-change="
              getCouTicketList($event, searchCoupon.pageSize, configType)
            "
            :total="coupons.list.total"
            :current-page="coupons.list.pageNum"
          >
          </el-pagination>

          <!-- 体验券 -->
          <div class="exp-tickets" v-show="ticketType === '体验券'">
            <div class="ticket-type-header">
              <div
                class="col clickable"
                :class="{ selected: expTicketType === '未使用' }"
                @click="changeExpTicketType('未使用')"
              >
                未使用（{{ expTicketCount["未使用"] }}）
              </div>
              <div
                class="col clickable"
                :class="{ selected: expTicketType === '已使用' }"
                @click="changeExpTicketType('已使用')"
              >
                已使用（{{ expTicketCount["已使用"] }}）
              </div>
              <div
                class="col clickable"
                :class="{ selected: expTicketType === '已过期' }"
                @click="changeExpTicketType('已过期')"
              >
                已过期（{{ expTicketCount["已过期"] }}）
              </div>
            </div>

            <!-- 体验券 -->
            <div class="exp-ticket-container">
              <div
                class="exp-ticket-item"
                v-for="(item, index) in expTicketList"
                :class="{
                  avalible:
                    item.status === 1 &&
                    item.dayTicket.endTime > new Date().getTime(),
                  used: item.status === 2,
                  'out-of-date':
                    item.status === 1 &&
                    item.dayTicket.endTime <= new Date().getTime()
                }"
                :style="item.dayTicket.img | bgimg"
                :key="expTicketType + index"
              >
                <!-- <div class="btn-exchange"
                    v-if="item.status === 1"
                    @click="activateExpTicket(item.id)">兑换时长</div> -->
                <div
                  class="btn-exchange"
                  v-if="
                    item.status === 1 &&
                      item.dayTicket.endTime > new Date().getTime()
                  "
                  @click="
                    (showExchangeExperience = true),
                      (expImgUrl = item.dayTicket.img),
                      (exchangeExpId = item.id)
                  "
                >
                  兑换时长
                </div>
              </div>

              <div class="placeholder" v-show="!expTicketQuery.total">
                暂无体验券
              </div>
            </div>
          </div>

          <el-pagination
            v-if="expTicketQuery.total && ticketType === '体验券'"
            background
            layout="total, sizes, prev, pager, next"
            style="padding: 20px 0px;"
            @size-change="sizeChangeExp"
            @current-change="
              getExpTicketList($event, searchExp.pageSize, configType)
            "
            :page-sizes="[8, 16, 24, 32]"
            :total="expTicketQuery.total"
            :current-page="expTicketQuery.pageNum"
          >
          </el-pagination>
        </div>

        <!-- <div class="title-box tickets-box">
          <div class="title-text">
            <div class="text">
              我的邀请码
            </div>
            <div class="text invitation">
              成功邀请新用户使用，你和ta各得150元优惠券
            </div>
          </div>
          <div class="invitaion-box">
            <input
              id="invitation"
              :value="
                userInfo.uid
                  ? `https://getgetai.com/purchase?rid=${
                      userInfo.uid
                    }&rname=${encodeURIComponent(userInfo.nickName)}`
                  : ''
              "
            />
            <button class="invite-link" @click="generateInvitationLink">
              复制邀请链接
            </button>
          </div>
        </div> -->

        <div class="title-box payment-box">
          <div class="title-text bill-title">
            <div class="text">
              支付记录
            </div>
            <div
              @click.stop="isShowBillModal = !isShowBillModal"
              class="bill-btn"><strong>开具发票</strong></div>
          </div>

          <div class="empty-center" v-if="payRecord.list.length <= 1">
            暂无支付记录
          </div>
          <div class="plan-list" v-if="payRecord.list.length > 1">
            <div class="plan-row" v-for="plan in payRecord.list" :key="plan.id">
              <div
                :class="{'order-col': index === 0}"
                class="col" v-for="(col, index) in plan" :key="index">
                {{ col }}
              </div>
            </div>
          </div>
          <el-pagination
            background
            layout="total, sizes, prev, pager, next"
            style="padding: 20px 0px;"
            @size-change="sizeChangePaid"
            @current-change="loadPaidList"
            :total="payRecord.total"
            :current-page="payRecord.pageNum"
          >
          </el-pagination>
        </div>
      </div>
    </div>

    <exchange-experience
      :show="showExchangeExperience"
      :imgUrl="expImgUrl"
      :exchangeCode="exchangeExpId"
      @close="
        (showExchangeExperience = false), (expImgUrl = ''), (exchangeExpId = '')
      "
      @reloadFn="reloadList(false)"
    />
    <exchange-activation
      :show="showExchangeActivation"
      :isCoupon="isCoupon"
      @close="showExchangeActivation = false"
      @reloadFn="reloadList(isCoupon)"
    />
    <BillModal
      v-if="isShowBillModal"
      @close="isShowBillModal = !isShowBillModal"
    />
    <client-only>
      <active-code-modal
        v-if="isShowCodeDialog"
        :sponsor="code.sponsor"
        :time="code.time"
        @closeDialog="isShowCodeDialog = false"
        @updateStoreUserInfo='updateStoreUserInfo'
      />
    </client-only>
    <BuyGlobalModal :coupon="couponChoose"></BuyGlobalModal>

    <PurchaseModal
      :show="isShowPurchaseModal"
      :selectedCouponId="selectedCouponId"
      @close="closePurchaseModal"
    />

  </div>
</template>
<script>
// js
import dayjs from 'dayjs'
import { GETexpTicketNum, GETcouTicketNum, POSTdayTicketPage, POSTcashCouponPage } from '../../api/client/aiWriter'
import PageHeader from "~/components/Header.vue"
import FilterBgimg from '~/utils/filter/backgroundImage.js'
// component
import ActiveCodeModal from '~/components/Account/activeCodeModal.vue'
import ExchangeActivation from '~/components/Account/exchangeActivation.vue'
import ExchangeExperience from '~/components/Account/exchangeExperience.vue'
import BuyGlobalModal from '~/components/PurchaseModal/buyGlobalModal'
import PurchaseModal from '~/components/PurchaseModalNew'

// import { GETexpTicketNum, POSTdayTicketPage, GETactivateExpTicket } from '../../api/client/aiWriter'
import { FetchUserDetail, FriendlyBusinessChannel, PurchasePayList } from '~/api/client/User'
// import { HOST } from '~/api/config.js'

import BillModal from '~/components/Account/BillModal'

export default {
  name: 'PageAccount',
  components: {
    PageHeader,
    ExchangeActivation,
    ExchangeExperience,
    ActiveCodeModal,
    BuyGlobalModal,
    PurchaseModal,
    BillModal
  },
  computed: {
    pagePath () {
      return this.$route.path
    },
    userInfo () {
      return this.$store.state.user.info || {}
    },
    isMember () {
      return true
    },
    membershipType () {
      if (!this.userInfo.vipEnd || dayjs(this.userInfo.vipEnd).isBefore(dayjs())) return '-'
      return dayjs(this.userInfo.vipEnd).diff(dayjs(), 'years') ? '年付' : '月付'
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
      ticketType: '优惠券',
      expTicketType: '未使用',
      couTicketType: '未使用',
      expTicketCount: {
        未使用: '0',
        已使用: '0',
        已过期: '0',
      },
      couTicketCount: {
        未使用: '0',
        已使用: '0',
        已过期: '0',
      },
      configType: {},
      expTicketList: [],

      isShowCodeDialog: false, // 展示激活码兑换模态框
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
      // 选中优惠券
      couponChoose: {

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
      selectedNavItem: 'my-account',
      isCoupon: true,
      showExchangeActivation: false,
      expImgUrl: '',
      showExchangeExperience: false,
      exchangeExpId: '',
      ua: null,
      //是否显示开具发票提示框
      isShowBillModal: false,
      //是否显示购买框
      isShowPurchaseModal: false,
      //选中的优惠券id
      selectedCouponId: null,
    }
  },
  filters: {
    bgimg: FilterBgimg,
    timeMode (val) {
      if (!val) return '-'
      return dayjs(val).format('YYYY-MM-DD')
    },
    vipTime (val) {
      if (dayjs(val).isBefore(dayjs()) || !val) return '免费版'
      return '会员版'
    },
  },
  async mounted () {
    const receivesecret = this.$route.query.receivesecret || ''
    const codeType = this.$route.query.codeType || ''
    if (!localStorage.getItem('loginTime') && !dayjs(localStorage.getItem('loginTime')).add(30, 'day') < dayjs()) {
      localStorage.setItem('turnTo', location.href)
    }

    if (receivesecret && codeType) {
      await this.showCodeDialog(receivesecret)
    }


    this.changeCouponTicketType('未使用')
    this.loadPaidList(1)
    this.updateStoreUserInfo()
    this.getExpTicketCountNum()
    this.getCouTicketCountNum()

    this.$store.dispatch('user/INIT_USER_INFO', 'forcedReload')
  },
  watch: {
    ticketType (val) {
      if (val === '体验券') {
        this.changeExpTicketType('未使用')
      }
      if (val === '优惠券') {
        this.changeCouponTicketType('未使用')
      }
    }
  },
  methods: {
    //关闭购买框
    closePurchaseModal () {
      this.isShowPurchaseModal = false
    },
    async showCodeDialog (receivesecret) {
      if (receivesecret) { // 有渠道商
        const result = await FriendlyBusinessChannel({ receivesecret })
        // const result = await this.$http.get(`${HOST}/api/auth/vip/code/detail?receivesecret=${receivesecret}`)
        if (result.data.code === 200) {
          this.code.sponsor = result.data.data.company || ''
          this.code.time = result.data.data.time.toString() || ''
          this.isShowCodeDialog = true
        } else { // 渠道商secret错误， 默认显示
          this.code.sponsor = ''
          this.code.time = ''
          this.isShowCodeDialog = true
        }
      } else { // 官方渠道
        this.code.sponsor = ''
        this.code.time = ''
        this.isShowCodeDialog = true
      }
    },
    reloadList (isCoupon) {
      if (isCoupon) {
        this.changeCouponTicketType('已使用')
      } else {
        // 刷新体验券显示数量
        this.getExpTicketCountNum()
        this.changeExpTicketType('未使用')
        // 刷新到期日期
        this.updateStoreUserInfo()
      }
    },
    exchangeActivation (ticketType) {
      this.showExchangeActivation = true
      this.isCoupon = (ticketType === '优惠券')
    },
    async getExpTicketCountNum () {
      const result = await GETexpTicketNum()

      if (result) {
        this.expTicketCount['未使用'] = result.noUseNum
        this.expTicketCount['已使用'] = result.useNum
        this.expTicketCount['已过期'] = result.expiredNum
      }
    },
    async getCouTicketCountNum () {
      const result = await GETcouTicketNum()

      if (result) {
        this.couTicketCount['未使用'] = result.noUseNum
        this.couTicketCount['已使用'] = result.useNum
        this.couTicketCount['已过期'] = result.expiredNum
      }
    },
    changeExpTicketType (type) {
      this.expTicketType = type

      if (type === '未使用') {
        const config = {
          status: 1,
          dayTicket: {
            status: 1
          }
        }
        this.configType = config
        this.getExpTicketList(1, this.searchExp.pageSize, config)
      }

      if (type === '已使用') {
        const config = {
          status: 2,
          dayTicket: {
            status: ''
          }
        }
        this.configType = config
        this.getExpTicketList(1, this.searchExp.pageSize, config)
      }

      if (type === '已过期') {
        const config = {
          status: 1,
          dayTicket: {
            status: 0
          }
        }
        this.configType = config
        this.getExpTicketList(1, this.searchExp.pageSize, config)
      }
    },
    changeCouponTicketType (type) {
      this.couTicketType = type

      if (type === '未使用') {
        const config = {
          status: 1,
          cashCoupon: {
            status: 1
          }
        }
        this.configType = config
        this.getCouTicketList(1, this.searchCoupon.pageSize, config)
      }

      if (type === '已使用') {
        const config = {
          status: 2,
          cashCoupon: {
            status: ''
          }
        }

        this.configType = config
        this.getCouTicketList(1, this.searchCoupon.pageSize, config)
      }

      if (type === '已过期') {
        const config = {
          status: 1,
          cashCoupon: {
            status: 2
          }
        }

        this.configType = config
        this.getCouTicketList(1, this.searchCoupon.pageSize, config)
      }
    },
    async getExpTicketList (pageNum, pageSize, searchConfig) {
      const data = await POSTdayTicketPage(pageNum, pageSize, searchConfig)
      if (data.code === 200) {
        this.expTicketList = data.data.list
        const { pageNum, pagesize, pages, total } = data.data
        this.expTicketQuery = { pageNum, pagesize, pages, total }
      }
      return data
    },
    async getCouTicketList (pageNum, pageSize, searchConfig) {
      const data = await POSTcashCouponPage(pageNum, pageSize, searchConfig)
      if (data.code === 200) {
        this.coupons.list = data.data
        // console.log('coupons', this.coupons.list)
        // console.log('list', this.coupons.list.total)
      }
    },
    async updateStoreUserInfo () {
      const res = await FetchUserDetail()
      let vipEnd = res.vipEnd
      let user = JSON.parse(localStorage.getItem("user"))
      user.vipEnd = vipEnd
      this.$store.dispatch("user/CHANGE_VIPEND", vipEnd)
      localStorage.setItem("userDetail", JSON.stringify(res));
      localStorage.setItem("user", JSON.stringify(user));
      // this.$http.get(this.$Apis._auth_user_detail).then(res => {
      //   if (res.status === 200 && res.data.code === 200) {
      //     let vipEnd = res.data.data.vipEnd
      //     let user = JSON.parse(localStorage.getItem("user"))
      //     user.vipEnd = vipEnd
      //     this.$store.dispatch("user/CHANGE_VIPEND", vipEnd)
      //     localStorage.setItem("userDetail", JSON.stringify(res.data.data));
      //     localStorage.setItem("user", JSON.stringify(user));
      //   }
      // })
    },
    generateInvitationLink () {
      const Url = document.getElementById("invitation")
      Url.select() // 选择对象
      document.execCommand("Copy")
      this.$message({
        message: '邀请链接复制成功，去粘贴发送给好友吧~',
        type: 'success'
      })
    },
    async loadPaidList (pageNum) {
      if (typeof pageNum === 'number') {
        this.searchPaid.pageNum = pageNum
      }
      const res = await PurchasePayList(this.searchPaid)
      if (res.data.code === 200) {
        const headers = [
          { title: '订单号', key: 'orderId', },
          { title: '方案名称', key: 'title', },
          { title: '支付金额', key: 'amount', },
          { title: '支付方式', key: 'payWay', },
          { title: '方案时长', key: 'duration', },
          { title: '支付时间', key: 'payTime', },
        ]
        const data = res.data.data.list.map(i => {
          return {
            id: i.id,
            title: '会员版',
            orderId: i.orderId,
            amount: `￥${Math.abs(i.payAmount)}`,
            payWay: i.platform.indexOf('wx') !== -1 ? '微信支付' : i.platform,
            duration: i.goods.effectiveDay < 30 ? `${i.goods.effectiveDay}天` : `${i.goods.effectiveDay / 30}个月`,
            payTime: dayjs(i.updateTime).format('YYYY-MM-DD'),
          }
        })
        this.payRecord = {
          ...res.data.data,
          list: [
            headers.map(item => item.title),
            ...data.map(item => {
              return headers.map(({ key }) => {
                return item[key]
              })
            }),
          ]
        }
      }
      // this.$http.post(this.$Apis._purchase_pay_list, this.searchPaid).then(res => {
      //   if (res.data.code === 200) {
      //     const headers = [
      //       { title: '方案名称', key: 'title', },
      //       { title: '支付金额', key: 'amount', },
      //       { title: '支付方式', key: 'payWay', },
      //       { title: '方案时长', key: 'duration', },
      //       { title: '支付时间', key: 'payTime', },
      //     ]
      //     const data = res.data.data.list.map(i => {
      //       return {
      //         id: i.id,
      //         title: '会员版',
      //         amount: `￥${Math.abs(i.amount)}`,
      //         payWay: i.platform.indexOf('wx') !== -1 ? '微信支付' : i.paltform,
      //         duration: i.goods.effectiveDay < 30 ? `${i.goods.effectiveDay}天` : `${i.goods.effectiveDay / 30}个月`,
      //         payTime: dayjs(i.updateTime).format('YYYY-MM-DD'),
      //       }
      //     })
      //     this.payRecord = {
      //       ...res.data.data,
      //       list: [
      //         headers.map(item => item.title),
      //         ...data.map(item => {
      //           return headers.map(({ key }) => {
      //             return item[key]
      //           })
      //         }),
      //       ]
      //     }
      //   }
      // })
    },
    sizeChangeCoupon (pageSize) {
      this.searchCoupon.pageSize = pageSize
      // this.loadCouponList(1)
      this.getCouTicketList(1, this.searchCoupon.pageSize, this.configType)
    },
    sizeChangeExp (pageSize) {
      this.searchExp.pageSize = pageSize
      // this.loadCouponList(1)
      this.getExpTicketList(1, this.searchExp.pageSize, this.configType)
    },
    sizeChangePaid (pageSize) {
      this.searchPaid.pageSize = pageSize
      this.loadPaidList(1)
    },
    // 使用优惠券
    useCoupon (ticket) {
      if (ticket.status === 1 && ticket.cashCoupon.endTime > new Date().getTime()) {
        //this.$store.commit('global/BuyModalModalChange', ticket);
        this.selectedCouponId = ticket.id
        this.isShowPurchaseModal = true
      }
    }
  }
}
</script>

<style lang="stylus">
.el-pagination
  text-align center
</style>


<style lang="stylus" scoped>
@import '../../components/Nav/CardSubBar.styl'
COL_LEFT_WIDTH = 215px
CONTENT_PADDING_TOP = 30px
COL_MARGIN = 24px
#page-account
  background #eee
  // padding-top: CONTENT_PADDING_TOP;
  // margin-top: CONTENT_PADDING_TOP;
.content-center
  margin 0 auto
  margin-top CONTENT_PADDING_TOP
.column-left
  display block
  float left
  width COL_LEFT_WIDTH
.column-main
  display block
  margin-left (COL_LEFT_WIDTH + COL_MARGIN)
.nav-left
  background #FFFFFF
  box-shadow 0 1px 6px 0 rgba(0, 0, 0, 0.05)
  padding 6px 0
  .nav-item
    display flex
    flex-direction row
    align-items center
    justify-content flex-start
    padding 12px 24px
    cursor pointer
    .icon
      background-color #D8D8D8
      background-size cover
      background-repeat no-repeat
      width 20px
      height 20px
      border-radius 50%
      margin-right 12px
    .label
      font-size 16px
      color #5C5C5C
      letter-spacing 0.3px
      line-height 16px
    &:hover, &.selected
      .label
        color #06070D
.title-box
  background-color white
  margin-bottom COL_MARGIN
  box-shadow 0 1px 6px 0 rgba(0, 0, 0, 0.05)
  .title-text
    padding 12px 24px
    overflow hidden
    border-bottom 1px solid #F2F2F2
    &.bill-title {
      display: flex
      justify-content: space-between
      align-items: center
      .bill-btn {
        font-size: 12px;
        cursor: pointer;
      }
    }
    .text
      font-size 16px
      // color: #06070D;
      color #1E1E1E
      letter-spacing 0.3px
      line-height 30px
      margin-right 40px
      display inline-block
      &.selected
        color #4A25BA
      &.coupon
        cursor pointer
        font-size 14px
        font-weight 300
        text-decoration underline
        margin-left 20px
      &.invitation
        font-size 12px
        color #999999
        letter-spacing 0.23px
        line-height 12px
        margin-left 20px
    .bill-btn
      color: #6446C4
    .right
      float right
      line-height 30px
      .code-active
        border 1px solid #4A25BA
        padding 5px 10px
        background none
        color #4A25BA
        margin-right 10px
        text-align center
        cursor pointer
  .hint-text
    font-size 14px
    color #666
    letter-spacing 0.27px
    padding-top 3px
  .invitaion-box
    display flex
    align-items center
    justify-content space-between
    padding 18px 24px
    #invitation
      flex 1
      display inline-block
      height 30px
    .invite-link
      cursor pointer
      font-size 14px
      color #FFFFFF
      letter-spacing 0.27px
      line-height 16px
      background #4A25BA
      width 103px
      height 30px
      line-height 30px
      border 0
      margin-left 300px
  .btn-purchase
    background #4A25BA
    color white
    font-size 14px
    color #FFFFFF
    letter-spacing 0.27px
    line-height 30px
    width 103px
    text-align center
    display inline-block
  .count-status
    font-size 14px
    color #000000
    text-align left
    // line-height: 20px;
    .num
      color #F04769
  .plan-cols
    display block
    overflow hidden
    padding 29px 0
    .col-item
      width 33%
      float left
      text-align center
      border-right 1px solid #F2F2F2
      .title
        font-size 14px
        color #C2C2CA
        letter-spacing 0.27px
        line-height 16px
        margin-bottom 19px
      .value
        font-weight bolder
        font-size 26px
        color #4A25BA
        letter-spacing 0.49px
        line-height 26px
      &:last-of-type
        border-right 0px
  .ticket-alert
    text-align center
    padding 20px
  .tickets-list
    display flex
    flex-flow row wrap
    align-items center
    justify-content space-between
    .placeholder
      width 100%
      height 103px
      display flex
      align-items center
      justify-content center
    .exp-ticket-container
      display flex
      flex-wrap wrap
      flex-direction row
      align-items flex-start
      justify-content flex-start
      width 100%
      // min-height: 103px;
      padding-left 20px
      padding-right 20px
      .ticket-item
        width 220px
        height 93px
        margin-bottom 10px
        margin-right 10px
        position relative
        overflow hidden
        &.avalible
          cursor pointer
        &.used, &.out-of-date
          &::after
            content ''
            display block
            width 100%
            height 100%
            position absolute
            top 0
            right 0
            bottom 0
            left 0
            background white
            opacity 0.5
        img
          width 100%
  .ticket-type-header
    display flex
    flex-direction row
    align-items flex-start
    justify-content flex-start
    width 100%
    padding-top 14px
    padding-bottom 14px
    padding-left 20px
    padding-right 20px
    .col
      font-size 14px
      color #5C5C5C
      text-align left
      line-height 20px
      display block
      position relative
      padding-right 8.7px
      padding-left 6px
      max-width 150px
      &:nth-child(1)
        padding-left 0px
      &.selected
        color #4A25BA
      &:nth-child(1):after, &:nth-child(2):after
        display inline-block
        position absolute
        height 20px
        width 3px
        top 0px
        right 0px
        background-color #C0C4CC
        content ' '
  .exp-ticket-container
    display flex
    flex-wrap wrap
    flex-direction row
    align-items flex-start
    justify-content flex-start
    width 100%
    // min-height: 103px;
    padding-left 20px
    padding-right 20px
    .exp-ticket-item
      width 220px
      height 93px
      margin-bottom 10px
      margin-right 10px
      // background-color: #D8D8D8;
      position relative
      background-size cover
      &.avalible
        cursor pointer
      &.used, &.out-of-date
        &::after
          content ''
          display block
          width 100%
          height 100%
          position absolute
          top 0
          right 0
          bottom 0
          left 0
          background white
          opacity 0.5
      .btn-exchange
        position absolute
        bottom 5px
        right 5px
        color white
        font-size 12px
        background-color #6B6B6B
        border-radius 20px
        cursor pointer
        padding 2px 5px
    .placeholder
      width 100%
      height 103px
      display flex
      align-items center
      justify-content center
  .plan-list
    display block
    padding 0 24px
    padding-bottom 30px
    padding-top 11px
    .plan-row
      display block
      border-bottom 1px solid #F2F2F2
      padding 17px 0
      overflow hidden
      // header
      &:nth-of-type(1)
        .col
          display inline-block
          font-weight bolder
          font-size 16px
          color #06070D
          letter-spacing 0.3px
          line-height 16px
      .col
        display inline-block
        float left
        width 15%
        text-align center
        font-size 14px
        color #8E8D8D
        letter-spacing 0.27px
        line-height 14px
        &.order-col {
          width: 20%
        }
.empty-center
  text-align center
  width 100%
  margin-top 30px
  padding-bottom 30px
</style>

