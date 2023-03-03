<template>
  <div class="intro-page">
    <UADetective/>
    <PageHeader
      :backgroundColor="backgroundColor"
      marginHeight="0px"
      theme="theme-white"
      :isHome="true"
      :noSearch="true"
    />

    <!--弹框 -->
    <PromotionModal
      v-if="isPromotionShow"
      @close="isPromotionShow = false"
    />

    <el-row class="getgte_homeBanner">
      <el-row class="banner-getgetAi-head el_container">
        <el-col :span="24">
          <h1 class="tc fwb getgetAi-banner-title">字语智能(原Get写作) ，用AI创造更有价值的信息</h1>
<!--          <h3 class="tc getgetAi-banner-subtitle">全网热点追踪·推荐海量素材·提升原创质量</h3>-->
        </el-col>
        <el-col
          :xs="24"
          :sm="{span:18, offset:3}"
          :md="{span:20, offset:2}"
          :lg="{span:20, offset:2}"
          :xl="{span:20, offset:2}"
        >
          <div class="search-box">
            <form @submit.prevent="toWritePage">
              <!-- 搜索 -->
              <SearchV2 />
            </form>
            <!-- <nuxt-link target='_self' class="btn-epidemic" :to="`/compose?mode=Epidemic`">
              <svg-icon icon-class="epidemic-simple"></svg-icon>
              <span>疫情动态</span>
            </nuxt-link> -->
          </div>
        </el-col>
        <el-col
          :span="24"
          class="funBox-row"
        >
          <el-col
            :span="8"
            class="funBox-Col"
            v-for="(item, index) in bannerFuncList"
            :id="'pop-' + item.id"
            :key="index"
          >
            <!-- <nuxt-link :target="item.url.indexOf('/compose') !== -1 ? '_blank' : '_self'" class="funBox-Item" :to="item.url"> -->
            <nuxt-link
              :target="'_self'"
              class="funBox-Item"
              :to="item.url"
            >
              <!-- <a class="funBox-Item" @click="toCertainUrl(item.url)"> -->
              <el-popover
                width="min-content"
                class="funBox-pop"
                placement="top"
                :content="item.tip"
                trigger="hover"
                :open-delay="100"
              >
                <el-col
                  slot="reference"
                  class="funBox-ItmeDiv"
                >
                  <img
                    class="funBox-Item-img bounceIn delay-5s"
                    :src="Img[item.img]"
                    alt=""
                  >
                  <el-col class="funBox-Item-title">{{item.title}}</el-col>
                </el-col>
              </el-popover>
            </nuxt-link>
          </el-col>
        </el-col>
      </el-row>
    </el-row>

    <el-row class="list-getgetAi">
      <el-row
        type="flex"
        justify="center"
        class="el_container"
      >
        <el-col
          class="list-getgetAi-Col"
          v-for="(item, index) in funcListOne"
          :key="index"
        >
          <nuxt-link
            target="_self"
            style="display: flex;align-items: center;width: 100%;height: 80px;margin-right: 20px;border-radius: 8px 0px 0px 8px;"
            :to="item.url"
          >
            <div
              class="list-ImgBox"
              :style="item.bgcolor"
            >
              <img
                class="list-Imgicon bounceIn delay-5s"
                :src="item.img"
                alt=""
              >
            </div>
            <div class="list-Title">{{item.title}}</div>
          </nuxt-link>
        </el-col>
      </el-row>
    </el-row>

    <!-- <el-row class="list-getgetAi">
      <el-row
        type="flex"
        justify="center"
        class="el_container"
      >
        <el-col
          class="list-getgetAi-Col"
          v-for="(item, index) in funcListTwo"
          :key="index"
        >
          <nuxt-link
            target="_self"
            style="display: flex;align-items: center;width: 100%;height: 80px;margin-right: 20px;border-radius: 8px 0px 0px 8px;"
            :to="item.url"
          >
            <div
              class="list-ImgBox"
              :style="item.bgcolor"
            >
              <img
                class="list-Imgicon bounceIn delay-5s"
                :src="item.img"
                alt=""
              >
            </div>
            <div class="list-Title">{{item.title}}</div>
          </nuxt-link>
        </el-col>
      </el-row>
    </el-row> -->

    <el-row class="getgetAi-BtnGroup">
      <el-row class="el_container">
        <el-col
          :xs="24"
          :sm="24"
          :md="16"
          :lg="14"
          :xl="16"
          class="getgetAi-BtnGroup-title"
        >
          想持续了解AI写作，按 <span>Ctrl或Command</span> + <span>D</span> 收藏我们
        </el-col>
        <el-col
          :xs="24"
          :sm="24"
          :md="8"
          :lg="10"
          :xl="8"
          class="getgetAi-BtnGroup-video"
        >
          <div
            class="video_btn"
            style="cursor: pointer"
            @click="isShowVideo = true; videoUrl = Img.tutor"
          >
            <svg
              width="34px"
              height="34px"
              viewBox="0 0 34 34"
              class=" delay-5s"
              @mouseover="isHovering = true"
              @mouseout="isHovering = false"
              :class="{heartBeat: isHovering}"
            >
              <g
                id="1.5.1"
                stroke="none"
                stroke-width="1"
                fill="none"
                fill-rule="evenodd"
              >
                <g
                  id="首页"
                  transform="translate(-1013.000000, -488.000000)"
                >
                  <g
                    id="编组-3"
                    transform="translate(1014.000000, 489.000000)"
                  >
                    <circle
                      id="椭圆形"
                      stroke="#4A19BD"
                      stroke-width="1.5"
                      cx="16"
                      cy="16"
                      r="16"
                    ></circle>
                    <g
                      id="播放"
                      transform="translate(10.000000, 9.000000)"
                      fill-rule="nonzero"
                    >
                      <rect
                        id="矩形"
                        fill="#000000"
                        opacity="0"
                        x="0"
                        y="0"
                        width="14.2222222"
                        height="14.2222222"
                      ></rect>
                      <path
                        d="M12.260196,6.37704525 L12.2605371,6.37670415 L3.38911196,0.293433056 L3.3863405,0.296048167 C3.20252912,0.121503403 2.95316931,0.0140846667 2.67834053,0.0140846667 C2.11300649,0.0140846667 1.65473586,0.467821472 1.65473586,1.02792528 C1.65473586,1.33554249 1.64147551,1.93782925 1.65473586,1.92476789 L1.65473586,7.11119637 L1.65473586,12.5705207 C1.65473586,12.5750403 1.65473586,12.9212021 1.65473586,13.1946238 C1.65473586,13.7543865 2.11300649,14.2081375 2.67834053,14.2081375 C2.95420682,14.2081375 3.20461838,14.1000224 3.38841554,13.9238858 L3.38911196,13.9242411 L12.2605371,7.84115481 L12.260196,7.84061472 C12.4530608,7.6563059 12.5733846,7.3977079 12.5733846,7.11101161 C12.5733846,7.11031519 12.5733846,7.10978933 12.5733846,7.10892236 C12.5733846,7.10822594 12.5733846,7.10737319 12.5733846,7.10684732 C12.5733846,6.82012263 12.4530608,6.56133986 12.260196,6.37704525 Z"
                        id="路径"
                        fill="#4A19BD"
                      ></path>
                    </g>
                  </g>
                </g>
              </g>
            </svg>
            新手教程
          </div>
        </el-col>
      </el-row>
    </el-row>

    <el-row class="getgetAi-mainBody">
      <el-row class="el_container mainbody-row-One bodyOne">
        <el-col
          :xs="24"
          :sm="24"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-img hidden-sm-and-up"
        >
          <img
            class="img-one"
            v-lazy="Img.brief1"
          />
        </el-col>
        <el-col
          :xs="24"
          :sm="10"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-text"
        >
          <h2 class="main-title">监控全网热点</h2>
          <p>全网热点新闻每小时更新、各行业数据应有尽有</p>
          <nuxt-link
            class="button"
            to="/hot?point=completList"
          >查看更多
          </nuxt-link>
        </el-col>
        <el-col
          :xs="24"
          :sm="14"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-img hidden-xs-only"
        >
          <img
            class="img-one"
            v-lazy="Img.brief1_new"
          />
        </el-col>
      </el-row>
      <el-row
        type="flex"
        justify="space-between"
        class="el_container mainbody-row-One bodyTwo"
      >
        <el-col
          :xs="24"
          :sm="10"
          :md="10"
          :lg="10"
          :xl="10"
          class="body-img"
        >
          <img
            class="img-one"
            v-lazy="Img.brief2"
          />
        </el-col>
        <el-col
          :xs="24"
          :sm="14"
          :md="14"
          :lg="10"
          :xl="10"
          class="body-text"
        >
          <h2 class="main-title">丰富写作素材</h2>
          <h3>全网素材</h3>
          <span>收集全网优质素材，包括微信、百度、今日头条、知乎等平台</span>
          <h3>智能拆解</h3>
          <span>AI解析文章内容，提供文章、段落、图片等多维度写作素材</span>
          <h3>写作角度</h3>
          <span>AI推荐写作角度，激发您的写作灵感，实现快速写作新体验</span>
          <h3>收藏</h3>
          <span>一键快速收藏素材，大幅度提高写作效率，避免996加班苦恼</span>
        </el-col>
      </el-row>
      <el-row class="el_container mainbody-row-One bodyThree">
        <el-col
          :xs="24"
          :sm="24"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-img hidden-sm-and-up"
        >
          <img
            class="img-one"
            v-lazy="Img.brief3"
          />
        </el-col>
        <el-col
          :xs="24"
          :sm="10"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-text"
        >
          <h2 class="main-title">海量写作模板</h2>
          <p style="color: #1E1E1E">涵盖娱乐、旅游、科技、人物、干货等多种领域和写作方向</p>
          <el-col class="body-list">
            <div class="body-list-item">
              <div class="circle"></div>
              结合内容智能生成动态写作大纲
            </div>
            <div class="body-list-item">
              <div class="circle"></div>
              多个同类优质素材随意筛选替换
            </div>
            <div class="body-list-item">
              <div class="circle"></div>
              定制图库、快速配图
            </div>
          </el-col>
          <nuxt-link
            class="button"
            to="/template"
          >查看更多
          </nuxt-link>
        </el-col>
        <el-col
          :xs="24"
          :sm="14"
          :md="12"
          :lg="12"
          :xl="12"
          class="body-img hidden-xs-only"
        >
          <img
            class="img-one"
            v-lazy="Img.brief3"
          />
        </el-col>
      </el-row>
    </el-row>

    <el-row class="getgetAi-Experience">
      <el-row class="el_container getgetAi-Experience-container">
        <el-col class="exp-head">
          <h2
            class="exp-title"
            ref="enter6First"
          >3大AI创新写作功能，提升写作效率</h2>
          <p
            class="exp-subTitle"
            ref="enter6Second"
          >写作全流程体验设计，深度开发AI功能，提供一站式写作服务</p>
          <nuxt-link
            v-if="!isLogin"
            :to="{
              path: '/compose',
              query: { 'compose-type': 'search-keyword', isLeftShrink: true }
            }"
            :style="{ color: 'inherit',}"
            class="button exp-ifLogin"
          >
            <el-col
              class="btn-box"
              ref="enter6Third"
            >
              立即体验
            </el-col>
          </nuxt-link>
          <nuxt-link
            v-else
            target='_blank'
            tag='a'
            :to="{
            path: '/compose',
            query: { 'compose-type': 'search-keyword', isLeftShrink: true }
          }"
            :style="{color: 'inherit',}"
            class="button exp-notLogin"
          >
            <el-col
              class="btn-box"
              ref="enter6Third"
            >
              立即体验
            </el-col>
          </nuxt-link>
        </el-col>
      </el-row>
      <el-row
        type="flex"
        justify="space-around"
        class="el_container exp-bodyRow"
      >
        <el-col class="exp-topic">
          <el-col class="exp-topicIcon icon-clr1">
            <img
              :src="Img.icon1"
              class="exp-iconImg"
            />
            <div
              class="videoPlay"
              @click="isShowVideo = true; videoUrl = Img.video1"
            >
              <svg
                width="34px"
                height="34px"
                viewBox="0 0 34 34"
              >
                <g
                  id="1.5.1"
                  stroke="none"
                  stroke-width="1"
                  fill="none"
                  fill-rule="evenodd"
                >
                  <g
                    id="首页"
                    transform="translate(-1013.000000, -488.000000)"
                  >
                    <g
                      id="编组-3"
                      transform="translate(1014.000000, 489.000000)"
                    >
                      <circle
                        id="椭圆形"
                        stroke="#ffffff"
                        stroke-width="1.5"
                        cx="16"
                        cy="16"
                        r="16"
                      ></circle>
                      <g
                        id="播放"
                        transform="translate(10.000000, 9.000000)"
                        fill-rule="nonzero"
                      >
                        <rect
                          id="矩形"
                          fill="#000000"
                          opacity="0"
                          x="0"
                          y="0"
                          width="14.2222222"
                          height="14.2222222"
                        ></rect>
                        <path
                          d="M12.260196,6.37704525 L12.2605371,6.37670415 L3.38911196,0.293433056 L3.3863405,0.296048167 C3.20252912,0.121503403 2.95316931,0.0140846667 2.67834053,0.0140846667 C2.11300649,0.0140846667 1.65473586,0.467821472 1.65473586,1.02792528 C1.65473586,1.33554249 1.64147551,1.93782925 1.65473586,1.92476789 L1.65473586,7.11119637 L1.65473586,12.5705207 C1.65473586,12.5750403 1.65473586,12.9212021 1.65473586,13.1946238 C1.65473586,13.7543865 2.11300649,14.2081375 2.67834053,14.2081375 C2.95420682,14.2081375 3.20461838,14.1000224 3.38841554,13.9238858 L3.38911196,13.9242411 L12.2605371,7.84115481 L12.260196,7.84061472 C12.4530608,7.6563059 12.5733846,7.3977079 12.5733846,7.11101161 C12.5733846,7.11031519 12.5733846,7.10978933 12.5733846,7.10892236 C12.5733846,7.10822594 12.5733846,7.10737319 12.5733846,7.10684732 C12.5733846,6.82012263 12.4530608,6.56133986 12.260196,6.37704525 Z"
                          id="路径"
                          fill="#ffffff"
                        ></path>
                      </g>
                    </g>
                  </g>
                </g>
              </svg>
            </div>
          </el-col>
          <el-col class="exp-Intro">
            <h4>写作角度</h4>
            <span>智能推荐写作角度</span>
          </el-col>
        </el-col>
        <el-col class="exp-topic">
          <el-col class="exp-topicIcon icon-clr2">
            <img
              :src="Img.icon2"
              class="exp-iconImg"
            />
            <div
              class="videoPlay"
              @click="isShowVideo = true; videoUrl = Img.video2"
            >
              <svg
                width="34px"
                height="34px"
                viewBox="0 0 34 34"
              >
                <g
                  id="1.5.1"
                  stroke="none"
                  stroke-width="1"
                  fill="none"
                  fill-rule="evenodd"
                >
                  <g
                    id="首页"
                    transform="translate(-1013.000000, -488.000000)"
                  >
                    <g
                      id="编组-3"
                      transform="translate(1014.000000, 489.000000)"
                    >
                      <circle
                        id="椭圆形"
                        stroke="#ffffff"
                        stroke-width="1.5"
                        cx="16"
                        cy="16"
                        r="16"
                      ></circle>
                      <g
                        id="播放"
                        transform="translate(10.000000, 9.000000)"
                        fill-rule="nonzero"
                      >
                        <rect
                          id="矩形"
                          fill="#000000"
                          opacity="0"
                          x="0"
                          y="0"
                          width="14.2222222"
                          height="14.2222222"
                        ></rect>
                        <path
                          d="M12.260196,6.37704525 L12.2605371,6.37670415 L3.38911196,0.293433056 L3.3863405,0.296048167 C3.20252912,0.121503403 2.95316931,0.0140846667 2.67834053,0.0140846667 C2.11300649,0.0140846667 1.65473586,0.467821472 1.65473586,1.02792528 C1.65473586,1.33554249 1.64147551,1.93782925 1.65473586,1.92476789 L1.65473586,7.11119637 L1.65473586,12.5705207 C1.65473586,12.5750403 1.65473586,12.9212021 1.65473586,13.1946238 C1.65473586,13.7543865 2.11300649,14.2081375 2.67834053,14.2081375 C2.95420682,14.2081375 3.20461838,14.1000224 3.38841554,13.9238858 L3.38911196,13.9242411 L12.2605371,7.84115481 L12.260196,7.84061472 C12.4530608,7.6563059 12.5733846,7.3977079 12.5733846,7.11101161 C12.5733846,7.11031519 12.5733846,7.10978933 12.5733846,7.10892236 C12.5733846,7.10822594 12.5733846,7.10737319 12.5733846,7.10684732 C12.5733846,6.82012263 12.4530608,6.56133986 12.260196,6.37704525 Z"
                          id="路径"
                          fill="#ffffff"
                        ></path>
                      </g>
                    </g>
                  </g>
                </g>
              </svg>
            </div>
          </el-col>
          <el-col class="exp-Intro">
            <h4>改写、扩写</h4>
            <span>智能改写、扩写内容</span>
          </el-col>
        </el-col>
        <el-col class="exp-topic">
          <el-col class="exp-topicIcon icon-clr4">
            <img
              :src="Img.icon4"
              class="exp-iconImg"
            />
            <div
              class="videoPlay"
              @click="isShowVideo = true; videoUrl = Img.video4"
            >
              <svg
                width="34px"
                height="34px"
                viewBox="0 0 34 34"
              >
                <g
                  id="1.5.1"
                  stroke="none"
                  stroke-width="1"
                  fill="none"
                  fill-rule="evenodd"
                >
                  <g
                    id="首页"
                    transform="translate(-1013.000000, -488.000000)"
                  >
                    <g
                      id="编组-3"
                      transform="translate(1014.000000, 489.000000)"
                    >
                      <circle
                        id="椭圆形"
                        stroke="#ffffff"
                        stroke-width="1.5"
                        cx="16"
                        cy="16"
                        r="16"
                      ></circle>
                      <g
                        id="播放"
                        transform="translate(10.000000, 9.000000)"
                        fill-rule="nonzero"
                      >
                        <rect
                          id="矩形"
                          fill="#000000"
                          opacity="0"
                          x="0"
                          y="0"
                          width="14.2222222"
                          height="14.2222222"
                        ></rect>
                        <path
                          d="M12.260196,6.37704525 L12.2605371,6.37670415 L3.38911196,0.293433056 L3.3863405,0.296048167 C3.20252912,0.121503403 2.95316931,0.0140846667 2.67834053,0.0140846667 C2.11300649,0.0140846667 1.65473586,0.467821472 1.65473586,1.02792528 C1.65473586,1.33554249 1.64147551,1.93782925 1.65473586,1.92476789 L1.65473586,7.11119637 L1.65473586,12.5705207 C1.65473586,12.5750403 1.65473586,12.9212021 1.65473586,13.1946238 C1.65473586,13.7543865 2.11300649,14.2081375 2.67834053,14.2081375 C2.95420682,14.2081375 3.20461838,14.1000224 3.38841554,13.9238858 L3.38911196,13.9242411 L12.2605371,7.84115481 L12.260196,7.84061472 C12.4530608,7.6563059 12.5733846,7.3977079 12.5733846,7.11101161 C12.5733846,7.11031519 12.5733846,7.10978933 12.5733846,7.10892236 C12.5733846,7.10822594 12.5733846,7.10737319 12.5733846,7.10684732 C12.5733846,6.82012263 12.4530608,6.56133986 12.260196,6.37704525 Z"
                          id="路径"
                          fill="#ffffff"
                        ></path>
                      </g>
                    </g>
                  </g>
                </g>
              </svg>
            </div>
          </el-col>
          <el-col class="exp-Intro">
            <h4>文章质量检测</h4>
            <span>多维度解析文章质量</span>
          </el-col>
        </el-col>
        <!-- <el-col class="exp-topic">
          <el-col class="exp-topicIcon icon-clr5">
            <img :src="Img.icon5" class="exp-iconImg" />
            <div class="videoPlay" @click="isShowVideo = true; videoUrl = Img.video5">
              <svg width="34px" height="34px" viewBox="0 0 34 34">
                <g id="1.5.1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                    <g id="首页" transform="translate(-1013.000000, -488.000000)">
                        <g id="编组-3" transform="translate(1014.000000, 489.000000)">
                            <circle id="椭圆形" stroke="#ffffff" stroke-width="1.5" cx="16" cy="16" r="16"></circle>
                            <g id="播放" transform="translate(10.000000, 9.000000)" fill-rule="nonzero">
                                <rect id="矩形" fill="#000000" opacity="0" x="0" y="0" width="14.2222222" height="14.2222222"></rect>
                                <path d="M12.260196,6.37704525 L12.2605371,6.37670415 L3.38911196,0.293433056 L3.3863405,0.296048167 C3.20252912,0.121503403 2.95316931,0.0140846667 2.67834053,0.0140846667 C2.11300649,0.0140846667 1.65473586,0.467821472 1.65473586,1.02792528 C1.65473586,1.33554249 1.64147551,1.93782925 1.65473586,1.92476789 L1.65473586,7.11119637 L1.65473586,12.5705207 C1.65473586,12.5750403 1.65473586,12.9212021 1.65473586,13.1946238 C1.65473586,13.7543865 2.11300649,14.2081375 2.67834053,14.2081375 C2.95420682,14.2081375 3.20461838,14.1000224 3.38841554,13.9238858 L3.38911196,13.9242411 L12.2605371,7.84115481 L12.260196,7.84061472 C12.4530608,7.6563059 12.5733846,7.3977079 12.5733846,7.11101161 C12.5733846,7.11031519 12.5733846,7.10978933 12.5733846,7.10892236 C12.5733846,7.10822594 12.5733846,7.10737319 12.5733846,7.10684732 C12.5733846,6.82012263 12.4530608,6.56133986 12.260196,6.37704525 Z" id="路径" fill="#ffffff"></path>
                            </g>
                        </g>
                    </g>
                </g>
              </svg>
            </div>
          </el-col>
          <el-col class="exp-Intro">
            <h4>一键全网发文</h4>
            <span>快速多平台同步发文</span>
          </el-col>
        </el-col> -->
      </el-row>
    </el-row>

    <el-row class="getgetAi-hotTeam">
      <el-col class="hotTeam-title">
        <h2>实时全网热点，发现写作创意</h2>
      </el-col>
      <el-col class="hotTeam-tabCon">
        <div class="hotTeam-hotspot">
          <HotAllCustomPublic
            :isLoadMoreHot="2"
            :isContentScroll="true"
            :initSelectedName="'微博'"
            @goInformation="goInformation"
          />
        </div>
      </el-col>
      <!-- <el-col class="hotTeam-tabCon">
        <el-row type="flex" justify="space-between" class="el_container hotTeam-tabRow">
          <el-col :xs="24" :sm="24" :md="20" :lg="21" :xl="21" class="hotTeam-tabSelect">
            <div class="hotTeam-tab"
                :class="{ chooseField: hoverField === '推荐' }"
                @mouseover="hoverSelectField('推荐')">
                推荐
            </div>
            <a class="hotTeam-tab"
                :href="`/topic/search?title=${encodeURIComponent(item.title)}`"
                v-for="item in hotEvent.field"
                :class="{ chooseField: hoverField === item.title }"
                @mouseover="hoverSelectField(item.title)"
                @click.prevent="() => {}"
                :key="item.id">{{ item.title }}</a>
          </el-col>
          <el-col :xs="24" :sm="24" :md="4" :lg="3" :xl="3" class="hotTeam-tabSelectMore tr">
            <nuxt-link class="hotTeam-tabMore" to="/hot">更多热点</nuxt-link>
          </el-col>
        </el-row>
        <el-row type="flex" justify="space-between" class="el_container hotTeam-HotList">
          <template v-if="!hotEvent.events.length">
            <el-col class="hotTeam-ItemNoLoad" v-for="item in 8" :key="item"></el-col>
          </template>
          <a class="hotTeam-Item"
            :href="`/topic/search?title=${encodeURIComponent(typeof item === 'string' ? item : item.title)}`"
            v-for="item in hotEvent.events"
            :key="item.id"
            @click.prevent="goToDetail(item, 'home-hot')"
          >
            <el-col class="hotTeam-ItemCol">
              <el-row class="hotTeam-ItemCover" :style="{ backgroundImage: 'url(' + item.headImg + ')' }" style="background-position: center center;background-repeat: no-repeat;background-size:cover;">
              </el-row>
              <el-row class="hotTeam-ItemBody">
                <el-col class="hotTeam-Itemtitle">
                  <h3>#{{item.title}}</h3>
                </el-col>
                <el-col class="hotTeam-ItemIntro">
                  <h3>{{item.brief}}</h3>
                </el-col>
                <el-col class="hotTeam-ItemBottom">
                  <el-col class="hotTeam-ItemTime">{{item.updateTime | toBefore}}</el-col>
                  <el-col class="hotTeam-ItemNum">
                    <svg class="icon-drop" height="8" viewBox="0 0 7 8" width="7" xmlns="http://www.w3.org/2000/svg">
                      <path d="m50.385136 8c-.682806 2.8170403-.5869157 4.2255605.2876708 4.2255605.8745866 0 1.07719-1.0602197.6078104-3.18065897 1.6587285 1.69131067 2.4882664 3.09613247 2.4886135 4.21446557.0005206 1.6774996-2.1215715 2.7406329-3.2359186 2.7406329s-3.490289-.546291-3.5327914-2.8409909c-.028335-1.5298 1.0998701-3.24946968 3.3846153-5.1590091z"
                      fill="#C0C4CC" fill-rule="evenodd" transform="translate(-47 -8)"/></svg>
                    {{item.getRank}}
                  </el-col>
                </el-col>
              </el-row>
            </el-col>
          </a>
          <el-col class="theme-card-container-holder"></el-col>
        </el-row>
      </el-col> -->
    </el-row>

    <el-row class="getgetAi-hotTeam padding_bottom">
      <el-col class="hotTeam-title">
        <h2>海量智能模板，多种内容快速成文</h2>
      </el-col>
      <el-col class="hotTeam-tabCon">
        <el-row
          type="flex"
          justify="space-between"
          class="el_container hotTeam-tabRow"
        >
          <el-col
            :xs="12"
            :sm="12"
            :md="12"
            :lg="21"
            :xl="21"
            class="hotTeam-tabSelect"
          >
            <a
              class="hotTeam-tab chooseField"
              :class="{ chooseField: hoverField === '全部' }"
            >
              全部
            </a>
          </el-col>
          <el-col
            :xs="12"
            :sm="12"
            :md="12"
            :lg="3"
            :xl="3"
            class="hotTeam-tabSelectMore tr"
          >
            <nuxt-link
              class="hotTeam-tabMore"
              to="/template"
            >更多模板
            </nuxt-link>
          </el-col>
        </el-row>
        <el-row
          type="flex"
          justify="space-between"
          class="el_container hotTeam-HotList"
        >
          <template v-if="!templates.templ.length">
            <el-col
              class="hotTeam-ItemNoLoad"
              v-for="item in 10"
              :key="item"
            ></el-col>
          </template>
          <el-row
            class="hotTeam-ItemColSec"
            v-for="item in templates.templ"
            :key="item.id"
          >
            <div
              class="hotTeam-ItemCover"
              @click="clickTmplIntelCard(item)"
              :style="{ backgroundImage: 'url(' + item.image + '?x-oss-process=image/resize,m_lfit,limit_1,h_300)' }"
              style="background-position: center center;background-repeat: no-repeat;background-size:cover;"
            >
            </div>
            <el-row class="hotTeam-ItemBody">
              <el-col class="hotTeam-Itemtitle">
                {{item.name}}
              </el-col>
            </el-row>
          </el-row>
          <el-col class="theme-card-container-holder"></el-col>
          <el-col class="theme-card-container-holder"></el-col>
        </el-row>
      </el-col>
    </el-row>

    <el-row class="getgetAi-partners">
      <el-col class="getgetAi-partnersCol">
        <h2 class="title">合作伙伴</h2>
        <el-row
          type="block"
          justify="space-around"
          class="el_container getgetAi-partners-row"
        >
          <el-col
            class="getgetAi-partners-Col"
            v-for="(item, index) in partners"
            :key="index"
          >
            <a
              :href="item.link"
              target="_blank"
            ><img
              :src="item.img"
              alt="partners"
            /></a>
          </el-col>
        </el-row>
      </el-col>
      <el-col class="getgetAi-CommentersCol">
        <el-row class="el_container getgetAi-Commenters-row">
          <el-col
            :xs="24"
            :sm="24"
            :md="12"
            :lg="12"
            :xl="12"
            class="getgetAi-Commenters-col"
            v-for="(item, index) in commentors"
            :key="index"
          >
            <div class="cmnt_head">
              <img
                :src="item.img"
                alt=""
                class="user"
              >
              <h2>{{item.username}}</h2>
            </div>
            <div class="cmnt_body">
              <p>{{item.comment}}</p>
            </div>
            <div class="cmnt-symbl">
              <img v-lazy="Img.dot"/>
            </div>
          </el-col>
        </el-row>
      </el-col>
    </el-row>

    <PageFooter backgroundColor="#1E1E1E"/>

    <ModalArticleStructure
      v-if="isArticleStructureShow"
      :templId="intelCardTemplateId"
      @close="isArticleStructureShow = false"
    />
    <PageVideo
      v-if="isShowVideo"
      :videoUrl="videoUrl"
      @close="isShowVideo = false"
    />

    <XmtgjModal
      :modal="xmtModal"
      @close="closeModal"
    />

    <!-- 右侧悬浮工具 -->
    <Tools/>

    <!-- <PurchaseModal
      v-if="isLogin"
      :show="isShowPurchaseModal"
      @close="closePurchaseModal"
    /> -->
    <!-- <FunctionHintChain
      v-if="isLogin"
      :show="isShowPurchaseModal"
      @close="closePurchaseModal"
    /> -->

  </div>
</template>
<script>
  import { mapGetters } from 'vuex'

  import PageHeader from "~/components/Header"
  import PageFooter from '~/components/Footer'
  import SearchV2 from '~/components/SearchV2'
  import PageVideo from '~/components/home/video'
  import ModalArticleStructure from '~/components/Hotspot/ModalArticleStructure'
  import HotAllCustomPublic from '~/components/HotSetting/HotAllCustomPublic'
  // import { isMobile } from '~/utils/device-detection/isMobile.js'
  import { IntroImageList } from '~/assets/img/intro/index.js'
  import Img from '~/assets/img/homePage/index.js'
  import UADetective from '~/components/uaDetective/index.vue'

  import Tools from '~/components/Tool'

  import { checkVersionUpdate } from '~/api/client/aiWriter'
  // 请求热点事件api
  // import { GetHotEvent } from '@/api/client/aiWriter/themeTopic.js'

  // ssr 服务端请求API
  // import { GetHotEventSsr, GetThemeTopicsSsr } from '~/api/server/HotEventSsr'
  import { GetThemeTopicsSsr } from '~/api/server/HotEventSsr'
  import { GetTemplateSsr } from '~/api/server/TemplateSsr.js'

  import { getLastNotes } from '~/api/client/Note.js'
  // 请求智能模板api
  // import { POSTHometemplateListIntelligent } from '@/api/client/aiWriter/index.js'
  import PromotionModal from '~/components/PromotionModal/index'

  import Filters from '~/utils/filter'

  import XmtgjModal from '~/components/Account/noticeModal/tempDialog'

  //import PurchaseModal from '~/components/PurchaseModalNew'
  //import FunctionHintChain from '~/components/purchase/FunctionHintChain'


  import 'element-ui/lib/theme-chalk/display.css'

  export default {
    name: 'PageIntroPage',
    async asyncData({ query, redirect, store, req }) {
      // let ua = 'pc'
      // if (process.server) {
      //   ua = req.headers['user-agent']
      // } else if (process.client) {
      //   ua = navigator.userAgent
      // }
      // if (isMobile(ua)) return redirect('/m')
      // let service_cookie = {}
      // console.log('cookise', req.headers.cookie)
      // req && req.headers.cookie && req.headers.cookie.split(';').forEach(function (val) {
      //   let parts = val.split('=');
      //   service_cookie[parts[0].trim()] = (parts[1] || '').trim();
      // })
      // const headersAppend = {
      //   'X-access-token': service_cookie['X-access-token'],
      //   'X-User': service_cookie['X-User']
      // }
      // 获取领域列表
      async function getField() {
        let field = await GetThemeTopicsSsr(req)
        const arr = []
        for (let k = 0; k < 5; k++) {
          const randomField = field.splice(Math.random(field.length) * 10, 1).map(i => ({ id: i.id, title: i.title }))
          arr.push(...randomField)
        }
        return arr
      }

      // 获取智能模板列表
      async function getTemplate() {
        const template = await GetTemplateSsr()
        // console.log('template', template)
        return template.list
      }

      // let events = await GetHotEventSsr({ pageSize: 8 }, req)
      return {
        // hotEvent: {
        //   field: await getField(),
        //   events: events.list
        // },
        templates: {
          templ: await getTemplate()
        },
        funcListOne: [
          {
            title: '热点发现',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/hotspot_new.svg',
            bgcolor: 'background-image: linear-gradient(135deg, #F77D79 0%, #ED4744 100%);box-shadow: 0 4px 8px 0 rgba(237,71,68,0.30);',
            url: '/hot?hFlag=home-hotspot',
          },
          // {
          //   title: '标题推荐',
          //   img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon3.svg',
          //   bgcolor: 'background-image: linear-gradient(180deg, #72BCF9 0%, #3E87F0 100%);box-shadow: 0 4px 8px 0 rgba(62,135,240,0.30);',
          //   url: '/compose?compose-type=search-keyword&activate-func=title-recommand&hFlag=home-recommand&isLeftShrink=true',
          // },
          {
            title: '智能改写',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon2.svg',
            bgcolor: 'background-image: linear-gradient(135deg, #F9B979 0%, #F18344 100%);box-shadow: 0 4px 8px 0 rgba(241,131,68,0.30);',
            url: '/compose?compose-type=search-keyword&activate-func=intel-rewrite&hFlag=home-rewrite&isLeftShrink=true',
          },
          {
            title: '质量检测',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon-quality.svg',
            bgcolor: 'background-image: linear-gradient(180deg, #AC9BF7 0%, #7462EC 100%);box-shadow: 0 4px 8px 0 rgba(116,98,236,0.30);',
            url: '/compose?compose-type=search-keyword&activate-func=quality-detect&hFlag=home-detect&isLeftShrink=true',
          },
          {
            title: 'AI创作',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon3.svg',
            bgcolor: 'background-image: linear-gradient(180deg, #72BCF9 0%, #3E87F0 100%);box-shadow: 0 4px 8px 0 rgba(62,135,240,0.30);',
            url: '/compose?compose-type=search-keyword&ks=&mode=Section',
          },
        ],
        funcListTwo: [
          // {
          //   title: '全网发文',
          //   img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon-publish.svg',
          //   bgcolor: 'background-image: linear-gradient(180deg, #72E0F9 0%, #3EBDF0 100%);box-shadow: 0 4px 8px 0 rgba(62,189,240,0.30);',
          //   url: '/compose?compose-type=search-keyword&activate-func=publish',
          // },
        ],
        partners: [
          {
            title: '媒体星球',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner1.png',
            link: 'http://www.mstar.ai/',
          },
          {
            title: '幕布',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner2.png',
            link: 'https://mubu.com',
          },
          {
            title: '135编辑器',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner3.png',
            link: 'https://www.135editor.com',
          },
          {
            title: '新媒体管家',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner4.png',
            link: 'https://xmt.cn/',
          },
          {
            title: '音乐画布',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner5.png',
            link: 'http://www.musicanvas.com/',
          },
          {
            title: '96编辑器',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner6.png',
            link: 'http://bj.96weixin.com/',
          },
          {
            title: '考拉新媒体导航',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner7.png',
            link: 'https://www.kaolamedia.com/',
          },
          {
            title: '壹伴',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner8.png',
            link: 'https://yiban.io',
          },
          {
            title: '搜榜',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner9.png',
            link: 'https://www.bjsoubang.com',
          },
          {
            title: '标贝悦读',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner10.png',
            link: 'https://yuedu.data-baker.com/index.html?source=GETyl',
          },
        ],
        commentors: [
          {
            username: '上海｜运营',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/user1.png',
            comment: '我最初在公司当编辑的时候写文章都是靠自己绷着脑袋想话题、找素材，到头来读者的差评一大堆。 有一天朋友圈疯狂刷屏一个AI写作软件，我看了一眼介绍就付费入手了。空闲之后我找软件客服赅小推了解软件的操作说明，用起来挺简单上手的，抱着试一试的心态去创作了一篇文章发布头条，没想到阅读量竟然破万了，这是公司账号阅读量第一次破万。',
          },
          {
            username: 'Will',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/user2.png',
            comment: '新媒体小编还在为每天写文章头疼吗？写哪种类型文章比较容易火？热点天天有，究竟追哪个好？为何我的图文申请不了头条号原创标签？取标题绞尽脑汁，有什么妙招吗？何不试试GetAi写作呢？10W+文章模板套路轻松学！全网热点选题，实时帮你捕捉热度趋势！全网检测原创度，助你快速过头条号原创标！标题脑洞功能，发散你的思维，取出画龙点睛的标题！',
          }
        ],
        bannerFuncList: [
          // {
          //   id: 1,
          //   title: 'AI模板成文',
          //   img: 'iconTemplateArticle',
          //   url: '/template',
          //   tip: '汇集全网10w+文章经验',
          // },
          // {
          //   id: 2,
          //   title: 'AI热点成文',
          //   img: 'iconHotArticle',
          //   url: '/hotspot',
          //   tip: '智能预测爆款话题',
          // },
          // {
          //   id: 3,
          //   title: '新建文章',
          //   img: 'iconCreateArticle',
          //   url: '/compose?compose-type=search-keyword&isLeftShrink=true',
          //   tip: 'AI辅助深度创作',
          // },
        ]
      }
    },
    data() {
      return {
        Img,
        imgList: IntroImageList,
        backgroundColor: '#fff',
        isMobile: false,
        hoverField: '推荐',
        // hotEvent: {
        //   field: [],
        //   events: [],
        // },
        templates: {
          field: [],
          templ: [],
        },
        videoUrl: '', // 视频路径地址
        isShowVideo: false,
        isArticleStructureShow: false,
        intelCardTemplateId: '',
        bannerInput: '',

        xmtModal: '',
        isHovering: false,
        // isPromotionShow: true,
        isPromotionShow: false,
        funcList: [
          {
            title: 'AI创作',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon-rocket.svg',
            bgcolor: 'background-image: linear-gradient(135deg, #F77D79 0%, #ED4744 100%);box-shadow: 0 4px 8px 0 rgba(237,71,68,0.30);',
            url: '/compose?mode=article&compose-type=search-keyword&activate-func=intel-article',
          },
          {
            title: '标题推荐',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon3.svg',
            bgcolor: 'background-image: linear-gradient(180deg, #72BCF9 0%, #3E87F0 100%);box-shadow: 0 4px 8px 0 rgba(62,135,240,0.30);',
            url: '/compose?compose-type=search-keyword&activate-func=title-recommand',
          },
          {
            title: '智能改写',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon2.svg',
            bgcolor: 'background-image: linear-gradient(135deg, #F9B979 0%, #F18344 100%);box-shadow: 0 4px 8px 0 rgba(241,131,68,0.30);',
            url: '/compose?compose-type=search-keyword&activate-func=intel-rewrite',
          },
          {
            title: '质量检测',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon-quality.svg',
            bgcolor: 'background-image: linear-gradient(180deg, #AC9BF7 0%, #7462EC 100%);box-shadow: 0 4px 8px 0 rgba(116,98,236,0.30);',
            url: '/compose?compose-type=search-keyword&activate-func=quality-detect',
          },
          // {
          //   title: '全网发文',
          //   img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/icon-publish.svg',
          //   bgcolor: 'background-image: linear-gradient(180deg, #72E0F9 0%, #3EBDF0 100%);box-shadow: 0 4px 8px 0 rgba(62,189,240,0.30);',
          //   url: '/compose?compose-type=search-keyword&activate-func=publish',
          // },
        ],
        partners: [
          {
            title: '媒体星球',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner1.png',
            link: 'http://www.mstar.ai/',
          },
          {
            title: '幕布',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner2.png',
            link: 'https://mubu.com',
          },
          {
            title: '135编辑器',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner3.png',
            link: 'https://www.135editor.com',
          },
          {
            title: '新媒体管家',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner4.png',
            link: 'https://xmt.cn/',
          },
          {
            title: '音乐画布',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner5.png',
            link: 'http://www.musicanvas.com/',
          },
          {
            title: '96编辑器',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner6.png',
            link: 'http://bj.96weixin.com/',
          },
          {
            title: '考拉新媒体导航',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner7.png',
            link: 'https://www.kaolamedia.com/',
          },
          {
            title: '壹伴',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner8.png',
            link: 'https://yiban.io',
          },
          {
            title: '搜榜',
            img: 'https://static.getgetai.com/base/pc-nuxt/assets/img/mobile_home/partner9.png',
            link: 'https://www.bjsoubang.com',
          },
        ],
        commentors: [
          {
            username: '上海｜运营',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/user1.png',
            comment: '我最初在公司当编辑的时候写文章都是靠自己绷着脑袋想话题、找素材，到头来读者的差评一大堆。 有一天朋友圈疯狂刷屏一个AI写作软件，我看了一眼介绍就付费入手了。空闲之后我找软件客服赅小推了解软件的操作说明，用起来挺简单上手的，抱着试一试的心态去创作了一篇文章发布头条，没想到阅读量竟然破万了，这是公司账号阅读量第一次破万。',
          },
          {
            username: 'Will',
            img: 'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/user2.png',
            comment: '新媒体小编还在为每天写文章头疼吗？写哪种类型文章比较容易火？热点天天有，究竟追哪个好？为何我的图文申请不了头条号原创标签？取标题绞尽脑汁，有什么妙招吗？何不试试GetAi写作呢？10W+文章模板套路轻松学！全网热点选题，实时帮你捕捉热度趋势！全网检测原创度，助你快速过头条号原创标！标题脑洞功能，发散你的思维，取出画龙点睛的标题！',
          }
        ],
        bannerFuncList: [
          {
            id: 1,
            title: 'AI模板成文',
            img: 'iconTemplateArticle',
            url: '/template',
            tip: '汇集全网10w+文章经验',
          },
          {
            id: 2,
            title: 'AI热点成文',
            img: 'iconHotArticle',
            url: '/hotspot',
            tip: '智能预测爆款话题',
          },
          {
            id: 3,
            title: '新建文章',
            img: 'iconCreateArticle',
            url: '/compose?compose-type=search-keyword&isLeftShrink=true',
            tip: 'AI辅助深度创作',
          },
        ],
        //是否显示modal
        isShowPurchaseModal: false
      }
    },
    mounted() {
      let sourceFrom = this.$route.query.from
      if (!sourceFrom || !sourceFrom.includes('localhost')) {
        const referrer = document.referrer
        if (!!referrer && !referrer.includes(window.location.host)) {
          const referrerMap = {
            '://www.baidu.com/link': 'baidu_regular',
            '://zhidao.baidu.com': 'baidu_zhidao',
            '://www.baidu.com/s': 'sem',
            '://www.google.com': 'google_regular',
            '://www.sogou.com/link': 'sogou_regular',
            '://www.sogou.com': 'sogou_regular',
            '://sogou.com': 'sogou_regular',
            '://wap.sogou.com': 'sogou_regular',
            '://www.so.com/link': '360so_regular',
            '://m.so.com/s': '360so_regular',
            '://link.zhihu.com': 'zhihu',
            '://www.zhihu.com': 'zhihu',
            '://www.jianshu.com': 'jianshu',
            '://hao.logosc.cn': 'logosc',
            '://www.965yy.cn': '965yy',
            '://www.aewz.com': 'aewz',
            '://cn.bing.com': 'bing_regular',
            '://www.bing.com': 'bing_regular',
            '://www.kaolamedia.com': 'kaolamedia',
            '://www.koudaiyunying.com/': 'koudaiyunying',
            '://koudaiyunying.com/': 'koudaiyunying',
            '://www.utmedia.cn': 'utmedia',
            '://www.pmbaobao.com': 'pmbaobao',
            '://yuedu.data-baker.com': 'baker_yuedu',
            '://dartools.com': 'dartools',
            '://www.peterx.cn': 'peterx',
            '://www.goto-mars.com': 'goto_mars',
            '://hao.soogif.com': 'hao_soogif',
            '://123.meibp.com': '123_meibp',
            '://wanyouw.com': 'wanyouw',
            '://www.growthhk.cn': 'growthhk',
            '://www.chaoyunying.com': 'chaoyunying',
            '://yiban.io': 'yiban',
            '://yb.yibanbianji.com': 'yiban',
            '://www.chulaizou.com': 'chulaizou',
            '://sspai.com': 'sspai',
            'yahoo.com': 'yahoo',
            '://www.sxl.cn': 'sxl',
            // 百度回落
            'baidu.com': 'baidu_regular',
          }
          Object.keys(referrerMap).some(key => {
            if (referrer.includes(key)) {
              sourceFrom = referrerMap[key]
              return true
            }
            return false
          })
          if (!sourceFrom) {
            this.$store.dispatch('ga/dict', {
              tag: 'TAGReferrerNotFound',
              keywords: referrer,
              desc: '',
            })
          }
        }
      }
      if (sourceFrom) {
        localStorage.setItem('sourceFrom', sourceFrom)
      }
      setTimeout(() => {
        if (sessionStorage.getItem('need-login')) {
          this.$store.dispatch('user/SHOW_LOGIN')
        }
        if (this.isLogin) {
          this.$store.dispatch("user/HIDE_LOGIN")
        }
      }, 300)

      checkVersionUpdate(this)

      this.onScroll()
      document.addEventListener('scroll', this.onScroll)

      window.addEventListener('resize', this.onResize)
      if (this.$route.query.from === 'xmtgj' && !sessionStorage.getItem('xmtgj')) {
        this.xmtModal = 'welcome'
      }

      this.forceLogoin()

    },
    methods: {

      async forceLogoin() {
        if (!this.isLogin) {
          this.judgeShowPromotion()
        }
      },

      //显示活动弹窗
      judgeShowPromotion() {
        //显示活动弹窗
        let isPromotionShow = this.$Utils.checkPromotionShow('mayDay')
        isPromotionShow = false
        //let isPromotionShow = this.$Utils.checkDailyModal('epidemic')
        if (isPromotionShow) {
          this.showPromotion()
        }
      },

      // closePurchaseModal () {
      //   this.isShowPurchaseModal = false
      // },

      //跳转逻辑
      async jumpJudge() {
        let query = this.$route.query
        let flag = query.flag
        const userNotes = await getLastNotes({pageNum: 1, pageSize: 10, t: {}})
        if (this.isLogin) {
          this.$router.push('/folderV2')
          this.judgeShowPromotion()
        }
      },
      onScroll() {
        // 详情浮现
        const topSide = document.documentElement.scrollTop || document.body.scrollTop
        // 顶部导航栏的背景
        if (topSide > 80) {
          this.backgroundColor = '#fff'
        } else {
          this.backgroundColor = '#fff'
        }
      },
      showLogin(ev) {
        const target = ev.target
        if (target && target.href) {
          this.$store.dispatch('user/SHOW_LOGIN_WITH_PATH', target.href)
        } else {
          this.$store.dispatch('user/SHOW_LOGIN')
        }
      },
      onResize() {
        // const ua = navigator.userAgent
        // if (isMobile(ua)) {
        //   this.$router.replace('/m')
        // }
      },
      //判断是否是人物
      isPeople(item) {
        let isPeople = true
        for (let key in item) {
          if (key === 'source_title') {
            isPeople = false
            break
          }
        }
        return isPeople
      },
      //跳转到素材
      goInformation(row) {
        if (row.source_url) {
          // 前往其他网站
          window.open(row.source_url)
        } else {
          // 跳转时，极端情况下url失效
          this.$message({
            showClose: true,
            message: `获取热点事件链接失败，无法跳转`,
            type: 'error'
          });
        }
      },
      // 跳转热点详情页
      goToDetail(item, boardType) {
        const title = typeof item === 'string' ? item : item.title
        let newPagePath = this.$store.state.path.compose.path
        if (!this.isLogin) {
          this.$store.commit('user/afterLoginGoto', `/compose?compose-type=add-keyword&km=${title}&ks=${title}&tags=`)
          return
        }
        this.$store.dispatch('ga/param3', `TAGexploreHotBoardClick::${title}::${boardType}`)

        let routeData = this.$router.resolve({
          path: `${newPagePath}`,
          query: {
            'compose-type': 'add-keyword',
            ks: title,
            km: title,
            tags: item.tags ? encodeURIComponent(item.tags.join(',')) : ''
          }
        })
        this.$Utils.simulateAClick(routeData.href)
      },

      checkVip(functionType) {
        if (this.isVip) {
          return true
        }
        if (functionType) {
          this.$store.commit('purchase/setFunctionType', functionType)
        }
        //this.isShowPurchaseModal = true
        this.$store.commit('purchase/setIsp', true)
        return false
      },

      // 跳转智能模板详情页
      clickTmplIntelCard(data) {
        if (!this.isLogin) {
          this.$store.commit('user/afterLoginGoto', `/?intel-tempid=${data.id}`)
          return
        }

        if (!this.checkVip(15)) {
          return
        }

        this.isArticleStructureShow = true
        // console.log('click', data)
        this.$store.dispatch('ga/param3', `TAGexploreTemplateIntelCardClick::${data.title || data.name}::${data.id}`)
        this.intelCardTemplateId = data.id
        // console.log('intel', this.intelCardTemplateId)
      },
      // 热点领域 悬浮选择
      // async hoverSelectField(title) {
      //   this.hoverField = title
      //   const hotevent = await GetHotEvent({ fields: title, pageSize: 8 })
      //   this.hotEvent.events = hotevent.list
      // },
      toWritePage() {
        let content = this.bannerInput.trim()
        if (!content) {
          return this.$message({
            type: 'error',
            message: '请输入内容',
            showClose: true,
          })
        }
        // if (!this.isLogin) {
        //   this.$store.commit('user/afterLoginGoto', `/compose?compose-type=add-keyword&keyword=${content}`)
        //   return
        // }
        let routeData = this.$router.resolve({
          path: 'compose',
          query: {
            'compose-type': 'add-keyword',
            km: content,
            ks: content,
            mode: 'Section',
            hFlag: 'home-write',
            isLeftShrink: true
          }
        })
        this.$Utils.simulateAClickSelf(routeData.href)
      },
      toCertainUrl(url) {
        if (url.indexOf('/compose') !== -1) {
          this.$Utils.simulateAClick(url)
        } else {
          this.$router.push(url)
        }
      },
      closeModal() {
        this.xmtModal = ''
        sessionStorage.setItem('xmtgj', 1)
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
        } else {
          let routeData = this.$router.resolve({
            path: '/compose?compose-type=search-keyword'
          })
          this.$Utils.simulateAClick(routeData.href)
        }
      },
      showPromotion(activity = 'mid-autumn') {
        this.isPromotionShow = true
      }
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.onResize)
      document.removeEventListener('scroll', this.onScroll)
    },
    components: {
      PageHeader,
      PageFooter,
      PageVideo,
      HotAllCustomPublic,
      Tools,
      ModalArticleStructure,
      XmtgjModal,
      SearchV2,
      UADetective,
      PromotionModal,
      //PurchaseModal,
      //FunctionHintChain
    },
    computed: {
      ...mapGetters({
        isLogin: 'user/isLogin',
        isVip: 'user/isVip'
      }),
      // isLogin () {
      //   return this.$store.state.user.isLogin || false
      // },
      currentVersion() {
        return this.$store.getters['version/currentVersion']
      }
    },
    watch: {
      isLogin(newVal) {
        console.log('登录状态：：', newVal)
        if (newVal) {
          this.jumpJudge()
        }
      }
    },
    filters: {
      bgimg(url, sizeMode = 'cover') {
        return {
          backgroundImage: `url("${url}")`,
          backgroundSize: sizeMode === 'cover' ? 'cover' : 'contain',
        }
      },
      ...Filters
    },
  }
</script>

<style lang="scss" scoped>
  .hover:hover {
    color: #6e47ca !important;
    svg {
      #椭圆形 {
        stroke: #6e47ca !important;
      }
      #路径 {
        fill: #6e47ca !important;
      }
    }
  }

  .btn-hover:hover {
    background-color: #6e47ca !important;
  }

  .intro-page {
    background: #ffffff;
    // banner css start here
    .getgte_homeBanner {
      position: relative;
      height: 494px;
      //background-image: linear-gradient(134deg, #814cde 0%, #4a25ba 100%);
      //background: url('https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/home-bg.svg') no-repeat;
      //background: linear-gradient(134deg, #4a25ba, #4a25ba00),
      //url(https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/home-bg.svg) no-repeat;
      background: url('https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/home-bg-v2.jpg') no-repeat;
      background-size: cover;
      //background-position: 50% 100%;
      .banner-getgetAi-head {
        .getgetAi-banner-title {
          font-size: 36px;
          font-weight: 500;
          color: #ffffff;
          line-height: 69px;
          letter-spacing: 0.01em;
          padding-top: 55px;
          padding-bottom: 47px;
          margin-top: 47px;

        }
        .getgetAi-banner-subtitle {
          font-size: 22px;
          color: #ffffff;
          letter-spacing: 10.71px;
          line-height: 22px;
          margin-top: 20px;
          margin-bottom: 70px;
        }
        .search-box {
          display: flex;
          justify-content: center;
          form {
            width: 100%;
          }
          .btn-epidemic {
            background: #e67372;
            border-radius: 10px;
            color: #ffffff;
            font-size: 20px;
            font-weight: 600;
            letter-spacing: 0.71px;
            text-align: center;
            user-select: none;
            border: none;
            cursor: pointer;
            line-height: 54px;
            margin-left: 20px;
            padding: 0px 20px;
          }
        }
        .banner-searchInput {
          .el-input__inner {
            font-size: 18px;
            color: #1e1e1e;
            letter-spacing: 0.64px;
            height: 54px;
            padding: 0 24px;
            background: #ffffff;
            border-radius: 8px 0px 0px 8px;
            border: 0;
          }
          .el-input-group__append {
            height: 54px;
            width: 150px;
            padding: 0;
            background: #ffd919;
            border-radius: 0 8px 8px 0;
            border: none;
            .banner-searchBtn {
              display: block;
              width: 100%;
              height: 100%;
              font-size: 20px;
              font-weight: 600;
              color: #1e1e1e;
              letter-spacing: 0.71px;
              text-align: center;
              user-select: none;
              border: none;
              cursor: pointer;
              line-height: 54px;
            }
            &:hover,
            &:focus,
            &:active {
              border: none;
              background-color: #ffe047;
              outline: none;
              box-shadow: none;
            }
          }
        }
        .funBox-row {
          margin-top: 90px;
          .funBox-Col {
            display: flex;
            justify-content: center;
            align-items: center;
            .funBox-Item {
              display: flex;
              flex-direction: column;
              align-items: center;
              justify-content: center;
              width: 260px;
              height: 160px;
              background: #fff;
              border-radius: 16px;
              transition: all 0.15s linear;
              cursor: pointer;
              &:hover {
                box-shadow: 0 10px 30px rgba(0, 0, 0, 0.4);
                transform: translateY(-4px) scale(1.05);
              }
              .funBox-pop {
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                height: 100%;
                width: 100%;
                // border: 1px solid #EBEEF5;
                padding: 12px;
                // z-index: 2000;
                color: #606266;
                line-height: 1.4;
                text-align: justify;
                font-size: 14px;
                // box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
                word-break: break-all;
              }
              .funBox-ItmeDiv {
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                .funBox-Item-title {
                  font-size: 22px;
                  color: #1e1e1e;
                  letter-spacing: 0.93px;
                  line-height: 22px;
                  margin-top: 20px;
                  font-weight: 600;
                  text-align: center;
                  user-select: none;
                }
                img.funBox-Item-img {
                  width: 80px;
                }
              }
            }
          }
        }
      }
    }
    // list banner css start here
    .list-getgetAi {
      margin-top: 60px;
      .list-getgetAi-Col {
        display: flex;
        align-items: center;
        justify-content: flex-start;
        max-width: 216px;
        min-width: 216px;
        width: 100%;
        min-height: 80px;
        max-height: 80px;
        height: auto;
        background: #f8f8fb;
        border-radius: 8px;
        transition: all 0.1s linear;
        cursor: pointer;
        // margin: 0px 12px 20px;
        margin: 0px 42px 20px;
        &:hover {
          transform: translateY(-6px) scale(1.05);
        }
        .list-ImgBox {
          display: flex;
          align-items: center;
          justify-content: center;
          max-width: 80px;
          width: 100%;
          height: 80px;
          margin-right: 20px;
          border-radius: 8px 0px 0px 8px;
          img.list-Imgicon {
            width: 30px;
            height: 30px;
          }
        }
        .list-Title {
          font-size: 18px;
          color: #1e1e1e;
          letter-spacing: 0.34px;
          line-height: 18px;
          font-weight: 600;
        }
      }
    }
    // BtnGroup css start here
    .getgetAi-BtnGroup {
      margin-top: 80px;
      .getgetAi-BtnGroup-title {
        font-weight: 600;
        font-size: 18px;
        color: #1e1e1e;
        line-height: 60px;
        text-align: right;
        span {
          background: #ffd919;
          border-radius: 6px;
          padding: 4px 6px;
        }
      }
      .getgetAi-BtnGroup-video {
        .video_btn {
          flex-shrink: 0;
          display: flex;
          align-items: center;
          justify-content: center;
          width: 220px;
          height: 60px;
          border-radius: 30px;
          background: #fff;
          text-align: center;
          font-size: 24px;
          line-height: 60px;
          background-color: #4a25ba;
          color: #fff;
          letter-spacing: 3px;
          line-height: 24px;
          margin: 0 30px;
          box-shadow: 0 6px 20px 0 rgba(74, 25, 189, 0.3);
          &:hover,
          &:focus {
            background-color: #6e47ca;
          }
          svg {
            margin-right: 12px;
            circle {
              stroke: #fff;
            }

            g rect,
            path {
              fill: #fff;
            }
          }
        }
      }
    }
    // Main Body start here
    .getgetAi-mainBody {
      display: block;
      padding-bottom: 103px;
      .mainbody-row-One {
        margin-top: 117px;
        .body-text {
          h2.main-title {
            font-weight: 600;
            font-size: 40px;
            color: #000;
            margin-bottom: 34px;
          }
          h3 {
            font-size: 16px;
            color: #1e1e1e;
            line-height: 16px;
            margin-bottom: 12px;
          }
          p {
            font-size: 16px;
            color: #666;
            line-height: 16px;
          }
          span {
            display: block;
            font-size: 14px;
            color: #666666;
            line-height: 14px;
            margin-bottom: 30px;
          }
          .body-list {
            margin-top: 41px;
            margin-bottom: 40px;
            .body-list-item {
              border: none;
              font-size: 16px;
              color: #666666;
              line-height: 16px;
              padding: 12px 0px 12px 0px;
              div.circle {
                display: inline-block;
                background: none;
                border: 1px solid #4a19bd;
                width: 14px;
                height: 14px;
                border-radius: 50%;
                margin-right: 16px;
              }
            }
          }
          .button {
            margin-top: 59px;
            background-color: #4a19bd;
            box-shadow: 0 6px 20px 0 rgba(74, 25, 189, 0.3);
            border-radius: 30px;
            color: #fff;
            font-size: 24px;
            letter-spacing: 3px;
            line-height: 24px;
            width: 220px;
            height: 60px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            &:hover {
              background-color: #6e47ca;
            }
          }
        }
        .body-img {
          img {
            width: 100%;
          }
        }
      }
    }
    // Experience css start here
    .getgetAi-Experience {
      display: block;
      background-color: #f8f8fb;
      .getgetAi-Experience-container {
        padding-top: 120px;
        .exp-head {
          text-align: center;
          display: flex;
          justify-content: center;
          align-items: center;
          flex-flow: column wrap;
          .exp-title {
            margin-bottom: 34px;
            color: #1e1e1e;
            font-size: 40px;
            line-height: 40px;
            font-weight: 600;
          }
          .exp-subTitle {
            padding-bottom: 60px;
            font-size: 16px;
            color: #666;
            line-height: 16px;
            margin: 0px;
            font-weight: 400;
          }
          .button.exp-ifLogin .btn-box,
          .button.exp-notLogin .btn-box {
            background: #4a25ba;
            font-size: 24px;
            color: #fff;
            letter-spacing: 3px;
            line-height: 53px;
            width: 189px;
            height: 53px;
            text-align: center;
            border-radius: 30px;
            box-shadow: 0 6px 20px 0 rgba(74, 25, 189, 0.3);
            display: block;
            margin: auto;
            margin-bottom: 110px;
            &:hover,
            &:focus {
              background-color: #6e47ca;
            }
          }
        }
      }
      .exp-bodyRow {
        padding-bottom: 50px;
        .exp-topic {
          display: flex;
          justify-content: center;
          align-content: center;
          align-items: center;
          flex-flow: column wrap;
          .exp-topicIcon {
            width: 140px;
            height: 140px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 15px;
            position: relative;
            overflow: hidden;
            margin: auto;
            &:hover .videoPlay {
              height: 100%;
              opacity: 1;
              svg {
                opacity: 1;
              }
            }
            &.icon-clr1 {
              background-image: linear-gradient(135deg, #f77d79 0%, #ed4744 100%);
              box-shadow: 0 8px 10px 0 rgba(237, 71, 68, 0.3);
            }
            &.icon-clr2 {
              background-image: linear-gradient(135deg, #f9b979 0%, #f18344 100%);
              box-shadow: 0 8px 10px 0 rgba(241, 131, 68, 0.3);
            }
            &.icon-clr3 {
              background-image: linear-gradient(180deg, #72bcf9 0%, #3e87f0 100%);
              box-shadow: 0 8px 10px 0 rgba(62, 135, 240, 0.3);
            }
            &.icon-clr4 {
              background-image: linear-gradient(180deg, #f5cf63 0%, #e8a133 100%);
              box-shadow: 0 8px 10px 0 rgba(232, 161, 51, 0.3);
            }
            &.icon-clr5 {
              background-image: linear-gradient(180deg, #72e0f9 0%, #3ebdf0 100%);
              box-shadow: 0 8px 10px 0 rgba(62, 189, 240, 0.3);
            }
            .videoPlay {
              width: 100%;
              height: 0px;
              transition: all 0.3s ease-in-out;
              background-color: rgba(0, 0, 0, 0.4);
              display: flex;
              align-items: center;
              justify-content: center;
              position: absolute;
              bottom: 0px;
              left: 0px;
              z-index: 15;
              cursor: pointer;
              opacity: 0;
              svg {
                transition: opacity 0.3s ease-in-out;
                opacity: 0;
              }
            }
          }
          .exp-Intro {
            margin-top: 30px;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            h4 {
              font-size: 16px;
              color: #1e1e1e;
              line-height: 16px;
              font-weight: 600;
            }
            > span {
              margin-top: 12px;
              font-size: 14px;
              color: #666666;
              line-height: 14px;
            }
          }
        }
      }
    }
    // Hot Team css start here
    .getgetAi-hotTeam {
      display: block;
      margin-top: 120px;
      .hotTeam-title {
        h2 {
          font-weight: 600;
          font-size: 40px;
          color: #000;
          line-height: 40px;
          text-align: center;
        }
      }
      .hotTeam-hotspot {
        max-width: 1200px;
        min-width: 1200px;
        height: 855px;
        overflow-x: hidden;
        overflow-y: hidden;
      }
      .hotTeam-tabCon {
        margin-top: 80px;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-flow: column wrap;
        .hotTeam-tabRow {
          display: flex;
          align-items: center;
          justify-content: space-between;
          margin-bottom: 24px;
          .hotTeam-tabSelect {
            display: flex;
            .hotTeam-tab {
              width: 100px;
              font-weight: 600;
              font-size: 14px;
              color: #1e1e1e;
              border-radius: 18px;
              padding: 6px 3px;
              background-color: #f8f8fb;
              display: flex;
              align-items: center;
              justify-content: center;
              margin-right: 24px;
              cursor: pointer;
              &.chooseField {
                color: #4a19bd;
              }
            }
          }
          .hotTeam-tabSelectMore {
            .hotTeam-tabMore {
              width: 100px;
              font-weight: 600;
              color: #1e1e1e;
              border-radius: 18px;
              padding: 9px 33px;
              background-color: #f8f8fb;
              justify-content: center;
              cursor: pointer;
              &:hover {
                color: #4a19bd;
              }
            }
          }
        }
        .hotTeam-HotList {
          // min-height: 814px;
          display: flex;
          justify-content: space-between;
          // align-items: center;
          flex-wrap: wrap;
          .hotTeam-ItemNoLoad {
            width: 285px;
            height: 387px;
            background-color: #e0e0e0;
            border-radius: 8px;
          }
          .hotTeam-Item {
            width: 285px;
            height: 387px;
            background-color: #fff;
            border: 1px solid #f2f2f2;
            border-radius: 8px;
            overflow: hidden;
            margin-bottom: 20px;
            &:hover {
              box-shadow: 0px 6px 10px rgba(0, 0, 0, 0.2);
            }
            cursor: pointer;
            .hotTeam-ItemCol {
              .hotTeam-ItemCover {
                width: 100%;
                height: 200px;
                background-color: #999;
                background-size: cover;
                background-repeat: no-repeat;
                -webkit-transition: all 0.5s;
                -moz-transition: all 0.5s;
                -o-transition: all 0.5s;
                transition: all 0.5s;
                &:hover {
                  -ms-transform: scale(1.1);
                  -moz-transform: scale(1.1);
                  -webkit-transform: scale(1.1);
                  -o-transform: scale(1.1);
                  transform: scale(1.1);
                }
                img {
                  min-width: 100%;
                  max-height: 100%;
                  -o-object-fit: cover;
                  object-fit: cover;
                }
              }
              .hotTeam-ItemBody {
                width: 100%;
                padding: 0px 18px;
                .hotTeam-Itemtitle {
                  font-size: 16px;
                  font-weight: 700;
                  margin-top: 18px;
                  text-align: left;
                  h3 {
                    font-weight: 600;
                    letter-spacing: 0.3px;
                    line-height: 18px;
                    color: #1e1e1e;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    -webkit-line-clamp: 1;
                    -webkit-box-orient: vertical;
                    display: -webkit-box;
                  }
                }
                .hotTeam-ItemIntro {
                  margin-top: 9px;
                  height: 100px;
                  overflow: hidden;
                  h3 {
                    font-size: 13px;
                    color: #999;
                    letter-spacing: 0.25px;
                    line-height: 20px;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    -webkit-box-orient: vertical;
                    display: -webkit-box;
                    -webkit-line-clamp: 5;
                  }
                }
                .hotTeam-ItemBottom {
                  margin-top: 13px;
                  padding-bottom: 20px;
                  display: flex;
                  justify-content: space-between;
                  align-items: center;
                  .hotTeam-ItemTime {
                    font-size: 12px;
                    color: #c0c4cc;
                    letter-spacing: 0.23px;
                    line-height: 12px;
                  }
                  .hotTeam-ItemNum {
                    font-size: 12px;
                    color: #c0c4cc;
                    letter-spacing: 0.23px;
                    line-height: 12px;
                    text-align: right;
                  }
                }
              }
            }
          }
          .hotTeam-ItemColSec {
            margin-bottom: 30px;
            .hotTeam-ItemCover {
              width: 226px;
              height: 283px;
              border-radius: 6px;
              overflow: hidden;
              background: #999;
              cursor: pointer;
              margin: auto;
              background-size: cover;
              background-repeat: no-repeat;
              -webkit-transition: all 0.5s;
              -moz-transition: all 0.5s;
              -o-transition: all 0.5s;
              transition: all 0.5s;
              &:hover {
                -ms-transform: scale(1.1);
                -moz-transform: scale(1.1);
                -webkit-transform: scale(1.1);
                -o-transform: scale(1.1);
                transform: scale(1.1);
                box-shadow: 0 6px 10px rgba(0, 0, 0, 0.2);
              }
              img {
                width: 100%;
                min-height: 100%;
                -o-object-fit: cover;
                object-fit: cover;
              }
            }
            .hotTeam-ItemBody {
              width: 100%;
              padding: 0px 0px 0px 8px;
              .hotTeam-Itemtitle {
                font-size: 16px;
                color: #1e1e1e;
                letter-spacing: 0.3px;
                line-height: 16px;
                margin-top: 15px;
                text-align: left;
              }
            }
          }
          .theme-card-container-holder {
            width: 226px;
          }
        }
      }
      &.padding_bottom {
        padding-bottom: 80px;
      }
    }
    // Partners css start here
    .getgetAi-partners {
      display: block;
      padding-bottom: 98px;
      background-color: #f8f8fb;
      .getgetAi-partnersCol {
        padding-top: 114px;
        h2.title {
          font-weight: 600;
          font-size: 40px;
          color: #000000;
          line-height: 40px;
          margin-bottom: 40px;
          text-align: center;
        }
        .getgetAi-partners-row {
          .getgetAi-partners-Col {
            flex: 0 0 20%;
            max-width: 20%;
            display: flex;
            justify-content: center;
            align-items: center;
            img {
              width: 140px;
              height: auto;
              margin: 40px auto 0px;
              &:hover {
                transform: scale(1.01);
              }
            }
          }
        }
      }
      .getgetAi-CommentersCol {
        margin-top: 73px;
        .getgetAi-Commenters-row {
          display: flex;
          justify-content: space-between;
          align-items: center;
          align-content: center;
          .getgetAi-Commenters-col {
            position: relative;
            max-width: 45%;
            background-color: #fff;
            border-radius: 16px;
            padding: 30px;
            .cmnt_head {
              display: flex;
              margin-bottom: 18px;
              img.user {
                width: 60px;
                height: 60px;
                border-radius: 50%;
                margin-right: 24px;
              }
              h2 {
                font-size: 16px;
                color: #1e1e1e;
                letter-spacing: 0.3px;
                line-height: 16px;
                padding-top: 22.5px;
              }
            }
            .cmnt_body {
              p {
                font-size: 16px;
                color: #666;
                letter-spacing: 0.3px;
                line-height: 28px;
                text-indent: 2em;
                text-align: justify;
              }
            }
            .cmnt-symbl {
              position: absolute;
              top: 40px;
              right: 30px;
              width: 66px;
              z-index: 10;
              width: 52px;
              height: 36px;
              img {
                width: 100%;
              }
            }
          }
        }
      }
    }
  }

  /** responsive css **/
  @media (min-width: 993px) and (max-width: 1220px) {
    .intro-page {
      .list-getgetAi {
        .el_container {
          display: flex;
          flex-flow: row wrap;
          justify-content: center;
          .list-getgetAi-Col {
            max-width: 180px;
            min-width: 180px;
            height: 70px;
            min-height: 70px;
            .list-ImgBox {
              margin-right: 12px;
              height: 70px;
              max-width: 70px;
            }
          }
        }
      }
      .getgetAi-mainBody {
        padding: 0px 15px 90px;
      }
      .getgetAi-hotTeam {
        padding-left: 15px;
        padding-right: 15px;
        .hotTeam-tabCon {
          .hotTeam-HotList {
            .theme-card-container-holder {
              width: 285px;
            }
          }
        }
        &.padding_bottom {
          .hotTeam-tabCon {
            .hotTeam-HotList {
              .theme-card-container-holder {
                width: 226px;
              }
            }
          }
        }
      }
    }
  }

  @media (min-width: 768px) and (max-width: 992px) {
    .intro-page {
      .getgte_homeBanner {
        .banner-getgetAi-head {
          .getgetAi-banner-title {
            font-size: 32px;
          }
          .getgetAi-banner-subtitle {
            font-size: 18px;
          }
          .funBox-row {
            .funBox-Col {
              .funBox-Item {
                width: 190px;
                .funBox-ItmeDiv {
                  img.funBox-Item-img {
                    width: 60px;
                  }
                  .funBox-Item-title {
                    font-size: 18px;
                  }
                }
              }
            }
          }
        }
      }
      .list-getgetAi {
        .el_container {
          display: flex;
          flex-flow: row wrap;
          justify-content: center;
          .list-getgetAi-Col {
            max-width: 180px;
            min-width: 180px;
            height: 70px;
            min-height: 70px;
            .list-ImgBox {
              margin-right: 12px;
              height: 70px;
              max-width: 70px;
            }
          }
        }
      }
      .getgetAi-BtnGroup {
        .getgetAi-BtnGroup-title {
          text-align: center;
        }
        .getgetAi-BtnGroup-video {
          .video_btn {
            margin: 30px auto;
          }
        }
      }
      .getgetAi-mainBody {
        padding-left: 12px;
        padding-right: 12px;
        .mainbody-row-One {
          .body-text {
            text-align: left;
            h2.main-title {
              font-size: 32px;
            }
            .button {
              height: 45px;
              font-size: 20px;
            }
          }
        }
      }
      .getgetAi-Experience {
        .getgetAi-Experience-container {
          padding-top: 90px;
          .exp-head {
            .exp-title {
              font-size: 32px;
            }
          }
        }
        .exp-bodyRow {
          .exp-topic {
            .exp-topicIcon {
              width: 100px;
              height: 100px;
              img {
                width: 45px;
                height: auto;
              }
            }
          }
        }
      }
      .getgetAi-hotTeam {
        padding-left: 12px;
        padding-right: 12px;
        margin-top: 90px;
        .hotTeam-title {
          h2 {
            font-size: 32px;
          }
        }
        .hotTeam-tabCon {
          .hotTeam-tabRow {
            .hotTeam-tabSelect {
              .hotTeam-tab {
                margin-right: 18px;
              }
            }
            display: block;
            .hotTeam-tabSelectMore {
              margin-top: 24px;
              position: relative;
              right: 12px;
            }
          }
          .hotTeam-HotList {
            justify-content: space-around;
          }
        }
        &.padding_bottom {
          .hotTeam-tabCon {
            .hotTeam-tabRow {
              display: flex;
              .hotTeam-tabSelect {
                .hotTeam-tab {
                  margin-right: 28px;
                }
              }
              .hotTeam-tabSelectMore {
                margin: auto;
                position: relative;
                right: 0;
              }
            }
          }
        }
      }
      .getgetAi-partners {
        .getgetAi-partnersCol {
          h2.title {
            font-size: 32px;
          }
        }
        .getgetAi-CommentersCol {
          padding: 0px 12px;
          .getgetAi-Commenters-row {
            display: block;
            .getgetAi-Commenters-col {
              max-width: 100%;
              margin-top: 12px;
            }
          }
        }
      }
    }
  }

  @media (min-width: 320px) and (max-width: 767px) {
  }
</style>
<style lang="scss">
  .banner-searchInput {
    .el-input__inner {
      font-size: 18px;
      color: #1e1e1e;
      letter-spacing: 0.64px;
      height: 56px;
      padding: 0 24px;
      background: #ffffff;
      border-radius: 8px 0px 0px 8px;
      border: 0;
      opacity: 0.9;
    }
    .el-input-group__append {
      height: 54px;
      width: 150px;
      padding: 0;
      background: #ffd919;
      border-radius: 0 8px 8px 0;
      border: none;
      .banner-searchBtn {
        font-size: 20px;
        font-weight: 600;
        color: #1e1e1e;
        letter-spacing: 0.71px;
        text-align: center;
        user-select: none;
        border: none;
        cursor: pointer;
        line-height: 54px;
      }
      &:hover,
      &:focus,
      &:active {
        border: none;
        background-color: #ffe047;
        outline: none;
        box-shadow: none;
      }
    }
  }
</style>
