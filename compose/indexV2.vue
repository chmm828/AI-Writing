<template>
  <div
    class="article-composing"
    @click="isTiptop($event)"
    @mousemove="updateXY"
    @mouseup="hideToolbar"
    @keyup="hideToolbar"
  >
    <UADetective />
    <UserLogin @whetherLogin="e => whetherLogin(e)" />
    <div class="page-fixed-top-header">
      <div class="link-go-back clickable" @click="$router.push(previewRouter)">
        <el-tooltip
          class="item"
          effect="dark"
          content="返回上级"
          placement="bottom"
        >
          <SvgIcon
            color="black"
            icon-class="arrow"
            class="icon-arrow"
          ></SvgIcon>
        </el-tooltip>
      </div>
      <template>
        <span class="link-item newArticles" @click="newOpenArticle">
          <el-tooltip
            class="item"
            effect="dark"
            content="新建文档"
            placement="bottom"
          >
            <SvgIcon
              color="black"
              icon-class="icon-add"
              class="icon-arrow"
            ></SvgIcon>
          </el-tooltip>
        </span>

        <a class="link-item userSelect" @click.stop.prevent="toFolder">
          <span class="tab-item">
            <span class="text"> 工作台 </span>
          </span>
        </a>
      </template>
      <div class="link-item flx userSelect">
        <!-- <div class="manual-save" @click="manualSave">
          <img src="~/assets/img/compose/note-save-btn.png" alt="" class="save-btn">
          <span class="save-text">保存</span>
        </div> -->

        <div class="saving-status" v-if="autoSaveHintTextStatus === 101">
          文章保存中...
        </div>
        <div
          class="saving-status"
          v-else-if="autoSaveHintTextStatus === 100 && noteRemote.modifyTime"
        >
          <img
            src="~/assets/img/compose/note-save-success.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text"
            >保存成功 {{ noteRemote.modifyTime | toDateTime }}</span
          >
          <img
            @click="notePreview = true"
            src="~/assets/img/compose/note-preview.png"
            alt=""
            class="save-icon"
          />
        </div>
        <div class="saving-status" v-else-if="autoSaveHintTextStatus === 102">
          <img
            src="~/assets/img/compose/note-save-success.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text"
            >自动保存成功 {{ noteRemote.modifyTime | toDateTime }}</span
          >
          <img
            @click="notePreview = true"
            src="~/assets/img/compose/note-preview.png"
            alt=""
            class="save-icon"
          />
        </div>
        <div class="saving-status" v-else-if="autoSaveHintTextStatus === 103">
          <img
            src="~/assets/img/compose/note-save-success.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text"
            >手动保存成功 {{ noteRemote.modifyTime | toDateTime }}</span
          >
          <img
            @click="notePreview = true"
            src="~/assets/img/compose/note-preview.png"
            alt=""
            class="save-icon"
          />
        </div>
        <div class="saving-status" v-else-if="autoSaveHintTextStatus === 104">
          <img
            src="~/assets/img/compose/note-save-error.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text">网络异常暂存本地</span>
        </div>
        <div class="saving-status" v-else-if="autoSaveHintTextStatus === 105">
          <img
            src="~/assets/img/compose/note-save-error.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text">请登录后编辑</span>
        </div>
        <div class="saving-status" v-else-if="autoSaveHintTextStatus === 106">
          <img
            src="~/assets/img/compose/note-save-error.png"
            alt=""
            class="save-icon"
          />
          <span class="save-text"
            >该文章已在回收站，如需保存请移出回收站。</span
          >
        </div>

        <!-- <div class="saving-status">
          {{ autoSaveHintText }}
        </div> -->
      </div>
      <div
        class="icon_historical userSelect"
        v-show="$route.query.uid"
        @click="openHistorical()"
      >
        <svg-icon icon-class="icon-historical-version" />
        <span>历史备份</span>
      </div>

      <div class="float-right userSelect">
        <div id="laboratoryDiv" class="laboratory-content" v-if='isShowLaboratoryDropdown'>
          <span
            @click.stop="isShowLaboratoryDropdown = !isShowLaboratoryDropdown"
            >实验室</span
          >
          <div v-if="isShowLaboratoryDropdown" class="laboratory-dropdown">
            <div @click="extendWriteLaboratory" class="item">智能扩写</div>
          </div>
        </div>
        <div>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</div>
        <div style="margin-right: -18px; margin-left: 24px" class="help-box">
          <nuxt-link class="header-help" to="/purchase/indexV2" target="_blank">
            <img class="tab-item-icon" :src="ImgBase.iHot" />
            <span> 会员特权 </span>
          </nuxt-link>
        </div>
        <!-- <el-dropdown trigger="click" @command="helpClick">
          <span class="el-dropdown-link">
            帮助
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item v-for="help in helpList" :key="help.title" :command="help.title">
              <span class="help-item" v-if="!help.url">{{help.title}}</span>
              <a target="_blank" :href="help.url" class="help-item" v-if="help.url" style="color: #606266;">{{help.title}}</a>
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown> -->
        <!-- 帮助 -->
        <HelpBlock style="margin-right: -40px" class="userSelect"></HelpBlock>
        <HeaderUser
          class="header-user"
          :class="{ 'header-islogin': !isLogin }"
        />
        <el-button
          @click="manualSave()"
          type="primary"
          size="mini"
          v-if="!isBusiness && !canSaveToBusiness"
        >
          保存
        </el-button>
        <el-dropdown
          trigger="click"
          split-button
          size="mini"
          type="primary"
          @click="manualSave()"
          v-if="!isBusiness && canSaveToBusiness"
          style="margin-right: 10px"
        >
          保存
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item @click.native="manualSaveBusiness"
              >保存到企业</el-dropdown-item
            >
          </el-dropdown-menu>
        </el-dropdown>
        <el-button
          v-if="isBusiness"
          type="primary"
          size="mini"
          @click="manualSave()"
          >保存到企业
        </el-button>
        <!-- TODO 因网信办原因先下掉分享按钮 -->
        <el-button size="mini" @click="toggleShowShareColumn">
          {{ isShowUpdateShare ? '更新分享' : '分享' }}
        </el-button>
        <!--待优化 使用elementui-->
        <div class="export-note">
          <el-button size="mini" @click="exportClick">导出 </el-button>
          <ExportDropdown
            :noteId="$route.query.uid"
            :show="isShowExportNoteDropdown"
            :isInEditor="true"
            @close="isShowExportNoteDropdown = false"
          />
        </div>
        <el-button
          v-if="isBusiness"
          size="mini"
          @click="showPublishModal"
          style="margin-left: 10px"
          >发布
        </el-button>

        <!-- <el-button
          v-if="!isBusiness"
          style="margin-left: 10px;"
          size="mini"
          type="primary"
          @click="toggleShowSubmissionColumn"
          :class="{ disableStyle: !editor.title || !editor.content}"
        >模板征集
          <img
            style="position: absolute; top: 8px; width: 25px;"
            :src="resources.collectArticleBadge"
            class="gaixing-badge"
          />
        </el-button> -->
      </div>
    </div>

    <!-- 主要内容 -->
    <div class="full-page-content">
      <!-- new-scrollbar-w10px -->
      <client-only>
        <!-- <el-scrollbar :native="false" class="el_scrollBar_get"> -->
        <!-- :hotList="hotList" -->
        <!-- :style="columnLeftStyle" -->
        <ColumnLeft
          ref="columnLeft"
          :isLeftShrink="isLeftShrink"
          :currentKeyword="currentKeyword"
          :showExportBtn="true"
          :isNeedShowLoginCover="true"
          :noteUid="noteUid"
          :articlesUid="uid"
          :searchQuestion="editor.searchQuestion"
          :showTabBar="showTabBar"
          :title="editor.title"
          :columnLeftMode="columnLeftMode"
          :noteRemote="noteRemote"
          :replaceText="isShouldSyncArticleContent"
          :openArticleStatus="openStatus"
          :alreadySent="alreadySent"
          @showPurchaseModal="showPurchaseModal"
          @modeSwitch="columnLeftModeSwitch"
          @keywordSearchOutline="
            e => {
              keywordSearchOutline(e)
            }
          "
          @columnExpendChange="
            e => {
              isLeftShrink = e
            }
          "
          @changeKeyword="
            e => {
              currentKeyword = e
            }
          "
          @previewerExport="
            (e, source, opt) => {
              previewerExport(e, source, opt)
            }
          "
          @beginSelectParagraph="
            e => {
              beginSelectParagraph(e)
            }
          "
          @onSelectParagraph="
            (data, item) => {
              onSelectParagraph(data, item)
            }
          "
          @onSelectParagraphFromParagraph="
            (e, whetherGolden) => {
              onSelectParagraphFromParagraph(e, whetherGolden)
            }
          "
          @openAddResourceModal="openAddResourceModal"
          @sortHotRecommend="sortHotRecommend"
          @chooseHotRecommend="e => (currentKeyword = e.title)"
          @selectHotTab="selectHotTab"
          @setSelfTopicShow="
            e => {
              isSetSelfTopicShow = e
            }
          "
          @changeTopic="changeTopic"
          @insert="(e, source) => appendArticleToContent(e, source)"
          @ondragstart="e => ondragstart(e)"
          @changeTemplateKeyword="
            e => {
              changeTemplateKeyword(e)
            }
          "
          @flagCurontClick="flagCurontClick"
          @isLeftShrinkFun="leftOpen"
          @openPreparingeditor="openPreparingeditor"
          @addKeywordAndSelect="
            e => {
              onAddKeywordAndSelect(e)
            }
          "
          @articleComlaint="articleComlaint"
        />
        <!-- </el-scrollbar> -->
      </client-only>
      <!-- 右边 -->
      <div class="column-right" @drop="editorDrop" @dragover="dragAllow">
        <div
          class="column-resizer-col"
          :style="{ height: `${fullPageContentHeight}px` }"
        >
          <div
            class="resize-btn"
            :class="{
              highlight: isLeftShrink,
            }"
            @click="leftColumnShrinkToggle"
          >
            <SvgIcon
              :style="isLeftShrink ? '' : 'transform: rotate(180deg);'"
              icon-class="btnResize"
            />
          </div>
        </div>

        <div class="page-content-editor">
          <!-- Editor part -->
          <div class="editor-main">
            <client-only>
              <MyTinyEditor
                ref="MyEditorChild"
                v-model="editor.content"
                :noteId="uid"
                :title="editor.title"
                :editorWidth="columnRightWidth"
                :parentSuggestMaxHeight="parentSuggestEditorMaxHeight"
                :editorContentLength="editorContentLength"
                :isLeftShrink="isLeftShrink"
                :dataTransfer="dataTransfer"
                :isleftTabFlagParent="isleftTabFlagParent"
                :editorData="editor"
                :openStatus="openStatus"
                :isNoteSubmit="alreadySent"
                :isRightOperateShow="isRightOperateShow"
                @appendContent="appendContent"
                @title-change="changeTitle"
                @init="editorInitCommonFn"
                @content-editor="v => (contentEditor = v)"
                @append-image="onEditorAddImage"
                @appendImgNew="appendImgNew"
                @append-preview-image="onEditorAddPreviewImage"
                @editorSuccessErrornum="
                  e => {
                    editorSuccessErrornum = e
                  }
                "
                @Editor::Actions="onEditorActions"
                @rangechange="onEditorRangeChange"
                @titleHidden="onTitleHidden"
                @imageCutDone="e => (editor.content = e)"
                @showIntelArticle="smartOutline"
                @showIntelMeterial="
                  e => {
                    smartOutlineMeterial(e)
                  }
                "
                @isresetDlog="isresetDlog"
                @isLeftShrinkFun="leftOpen"
                @isCorrectionShow="
                  (e, len, t, limitNum) => {
                    ;(isCorrectionShow = e),
                      (canclModalStatus = e),
                      (editorContentLength = len),
                      (editorTextContent = t),
                      (limitNum = limitNum)
                  }
                "
                @progressbarnumber="
                  e => {
                    progressbarnumber = e
                  }
                "
                @editorImportArticles="editorImportArticles"
                @importClick="importClick"
                @wordCountUpdate="
                  e => {
                    editorContentLength = e
                    editorDataUpdate()
                  }
                "
                @editorEnter="editorEnter"
              >
                <!-- @showIntelArticle='showGenerateColumn' 原来智能成稿触发方法 isShowRewriteExtendTool-->
                <template v-slot:toolbar>
                  <div
                    class="suspended-frame"
                    :class="{ useRect: toolbarPosUseRect }"
                    ref="ELEMsuspendedFrame"
                    v-show="isSuspendedFrame"
                  >
                    <div
                      class="suspended-frame-item"
                      @click="popSearch(true, 'Meterial', '文章素材')"
                    >
                      找灵感
                    </div>
                    <div
                      class="suspended-frame-item"
                      @click="searchMyArticle()"
                    >
                      找文集
                    </div>
                    <div
                      class="suspended-frame-item"
                      @click="popSearch(true, 'AiPicture', 'AI配图')"
                    >
                      AI配图
                    </div>
<!--                    <div-->
<!--                      class="suspended-frame-item"-->
<!--                      @click="popSearch(true, 'Meterial', '金句素材')"-->
<!--                    >-->
<!--                      找金句-->
<!--                    </div>-->
                    <div
                      class="suspended-frame-item"
                      @click="toggleShowRewriteColumn(true)"
                    >
                      改写/英译中
                    </div>
                    <div
                      class="suspended-frame-item last"
                      @click="collectSelected"
                    >
                      收藏
                    </div>
                    <!-- <div class="suspended-frame-item" @click="toggleShowExpendColumn(true)">扩写</div> -->
                    <div
                      class="suspended-pointer"
                      ref="ELEMsuspendedFramePointer"
                    ></div>
                  </div>
                  <div
                    class="suspended-frame suspended-frame-rewrite"
                    :class="{ useRect: toolbarPosUseRect }"
                    ref="ELEMsuspendedFrameRewrite"
                    v-show="isShowRewriteExtendTool"
                  >
                    <div
                      class="suspended-frame-item"
                      @click.stop="loadRewriteOrExtendByType(1)"
                      v-show="rewriteOrExtendType === 1"
                    >
                      改写/英译中
                    </div>
                    <div
                      class="suspended-frame-item suspended-frame-item-extend last"
                      @click.stop="loadRewriteOrExtendByType(2)"
                      v-show="rewriteOrExtendType === 2"
                    >
                      扩写
                    </div>
                    <div
                      class="suspended-pointer"
                      ref="ELEMsuspendedFramePointerRewrite"
                    ></div>
                  </div>
                </template>
              </MyTinyEditor>
            </client-only>

            <div
              :class="{ show: isGenerateColumnShow }"
              class="fixed-top-right aha-title-column"
            >
              <!-- 智能成稿 -->
              <GenerateArticleColumn
                v-if="isGenerateColumnShowModal"
                :keyword="editor.title"
                @insert="e => appendArticleToContent(e)"
                @hide="hidenGenerateColumn"
              />
            </div>

            <div
              :class="{ show: isPublishColumnShow }"
              class="fixed-top-right aha-title-column"
            >
              <PublishColumn
                v-if="isPublishColumnShowModal"
                :editor="editor"
                :maxHeight="parentSuggestEditorMaxHeight"
                @showPicSelector="isPicSelectorModalShow = true"
                @showAccountAdd="isPublishAccountAddModalShow = true"
                @select-title="magicSelectTitle"
                @edit="
                  e => {
                    publishEditAccount = e
                    isPublishAccountEditModalShow = true
                  }
                "
                @hide="hidenPublishColumn"
              />
            </div>

            <!-- 智能摘要 -->
            <!-- <div v-if="isSummaryColumnShow">
              <SummaryColumn
                :content="editor.content"
                :title="editor.title"
                @summaryHide="hidenSummaryColumn"
                @utilization="onSummaryColumnReplace"
              />
            </div> -->
            <!--智能摘要-->
            <div
              :class="{ show: isSummaryColumnShow }"
              class="fixed-top-right aha-title-column"
            >
              <SummaryColumn
                v-if="isSummaryColumnShowPlugin"
                :content="editor.content"
                :title="editor.title"
                :noteId="uid || $route.query.uid"
                @summaryHide="hidenSummaryColumn"
                @utilization="onSummaryColumnReplace"
              />
            </div>
            <!-- 投稿赚钱 -->
            <div v-if="isSubmissionColumnShow">
              <Submission
                :uid="editor.uid || uid"
                @summaryHide="hidenSubmissionColumn"
              />
            </div>
          </div>
        </div>
      </div>
      <!--改写|扩写-->
      <div
        :class="{ show: isRewriteColumnShow }"
        class="fixed-top-right aha-title-column"
      >
        <RewriteOrExtendWrite
          v-if="isRewriteColumnShowModal || isExtendColumnShowMal"
          ref="rewriteOrExtend"
          :type="rewriteOrExtendType"
          :content="rewriteColumnContent"
          :articleId="editor.uid || uid"
          @changeTab="
            number => {
              rewriteOrExtendType = number
            }
          "
          @hide="hidenRewriteColumn"
          @replace="onRewriteColumnReplace"
          @linkageSuspension="linkageSuspension"
          @cancelLinkage="cancelLinkage"
        />
      </div>
      <!-- 智能改写 -->
      <!-- <div
        :class="{
          show: isRewriteColumnShow || isExpendColumnShow,
          rewriterFlag: isRewriteColumnShow ||isExpendColumnShow
        }"
        class="fixed-top-right rewrite-column rewriterHide"
      >
        <div class="clickCloase" @click="hidenRewriteColumn">
          <svg-icon icon-class="_summary_column_0" />
        </div>
        <div class="fixed-top-header">
          <div
            class="header-type-title"
            @click="confimColumnRewriter"
            :class="{ titleMode: isRewriteColumnShow }"
            >
            智能改写
          </div>
          <div
            class="header-type-title"
            @click="confimColumnExPend"
            :class="{ titleMode: isExpendColumnShow }"
            >
            智能扩写
            <img :src="experiment" alt="实验">
          </div>
        </div>
        <RewriteColumn
          v-if="isRewriteColumnShow"
          :titleSwitch="true"
          :maxHeight="parentSuggestEditorMaxHeight"
          :content="rewriteColumnContent"
          :articleId="editor.uid || uid"
          :carriedOut="isRewriteColumnShow"
          @hide="hidenRewriteColumn"
          @replace="onRewriteColumnReplace"
          @linkageSuspension="linkageSuspension"
          @cancelLinkage="cancelLinkage"
        />
        <ExpendColumn
          v-if="isExpendColumnShow"
          :titleSwitch="true"
          :maxHeight="parentSuggestEditorMaxHeight"
          :content="rewriteColumnContent"
          :articleId="editor.uid || uid"
          :carriedOut="isExpendColumnShow"
          @hide="hidenExpendColumn"
          @replace="onRewriteColumnReplace"
        />

      </div> -->
      <!-- 智能扩写 -->
      <!-- <div
        :class="{ show: isExpendColumnShow, rewriterFlag: isExpendColumnShow }"
        class="fixed-top-right rewrite-column rewriterHide"
      >
        <ExpendColumn
          :maxHeight="parentSuggestEditorMaxHeight"
          :content="rewriteColumnContent"
          :articleId="editor.uid || uid"
          :carriedOut="isExpendColumnShow"
          @hide="hidenExpendColumn"
          @replace="onRewriteColumnReplace"
        />
      </div> -->
    </div>

    <input
      type="file"
      accept="image/*"
      id="note-editor-image"
      style="display: none"
    />

    <!-- 编辑器模式下 右上角广告位 -->
    <transition name="fade">
      <div
        class="compose-top-right"
        :style="ADboxSetStyleOnEditor"
        v-show="isEditorRightZoomEnough"
      >
        <div class="advertisement-1" v-if="advertisement1Img">
          <div class="advertisement-title">广告</div>
          <a
            class="advertisement-1-link"
            onclick="_hmt.push(['_trackEvent', 'nav', 'click', 'advertisement-1'])"
            :href="advertisement1Link"
            target="_blank"
            :style="{ 'pointer-events': advertisement1Link ? 'auto' : 'none' }"
          >
            <img :src="advertisement1Img" width="100%" />
          </a>
        </div>
        <transition name="fade" v-if='showRightAdvSpace'>
          <div class="tutor-intro" v-show="isTutorVideoShow">
            <div class="header">
              <div class="title">
                使用指南
              </div>
              <div
                class="btn-mini clickable"
                @click="isTutorVideoShow = !isTutorVideoShow"
              >
                <svg
                  class="icon-mini"
                  height="12"
                  viewBox="0 0 13 12"
                  width="13"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <g
                    fill="none"
                    fill-rule="evenodd"
                    stroke="#666"
                    stroke-linecap="square"
                    stroke-width="1.5"
                    transform="translate(1)"
                  >
                    <path
                      d="m5.5-1v14"
                      transform="matrix(.70710678 -.70710678 .70710678 .70710678 -2.631728 5.646447)"
                    />
                    <path
                      d="m5.5-1v14"
                      transform="matrix(-.70710678 -.70710678 -.70710678 .70710678 13.631728 5.646447)"
                    />
                  </g>
                </svg>
              </div>
            </div>
            <video
              src="https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/2.0PC-home/img-cover/tutor.mp4"
              controls="controls"
              autoplay
              muted
              width="100%"
            ></video>
            <div
              class="intro"
              :style="{
                height: isTutorIntroDetailExpanded ? 'auto' : '50px',
              }"
            >
              <!-- <div class="subtitle">
                简介
              </div> -->
              <div class="detail">
                智能写作，详细功能，用视频带你一起探索。
              </div>
              <!-- <div
                class="intro-expand-control"
                @click.stop="
                  isTutorIntroDetailExpanded = !isTutorIntroDetailExpanded
                "
                :style="{
                  background: isTutorIntroDetailExpanded
                  ? 'none'
                  : 'linear-gradient(0deg, rgb(248,248,251), rgba(248,248,251,0.76))',
                }"
              >
                {{ isTutorIntroDetailExpanded ? '收起' : '展开' }}
              </div> -->
            </div>
          </div>
        </transition>
      </div>
    </transition>

    <!-- 编辑器模式下 右边工具按钮 -->
    <div
      v-if="isToolbox"
      class="toolbox-set-height"
      v-show="!isRightOperateShow"
      :class="{ zIndex: isDecrementLevelZIndex }"
      :style="toolboxSetStyleOnEditor"
    >
      <!-- 投稿赚钱 小按钮 -->
      <!-- <el-tooltip content="模板征集" placement="left" v-show="!isEditorRightZoomEnough && !isBusiness">
        <div
          class="btn-ring"
          :class="{
            white: !isSubmissionColumnShow,
            selected: isSubmissionColumnShow,
            disable: !isDetectFuncActivate || alreadySent,
          }"
          style="background: #4A25BA;"
          @click="toggleShowSubmissionColumn">
          <div class="btn-ring-reddot" v-show="!alreadySent"></div>
          <span style="color: white">奖</span>
        </div>
      </el-tooltip> -->
      <!-- 投稿赚钱 大按钮 -->
      <!-- <el-popover
        v-if="!isBusiness"
        placement="left"
        width="200"
        trigger="manual"
        v-model="isSubmissionHintShow"
      >

        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">标题和正文，不能为空。</div>
          </div>
          <div
            class="confirm"
            @click="isSubmissionHintShow = false"
          >知道了</div>
        </div>

        <div
          class="btn-ring extend-with-text"
          :class="{
            white: !isSubmissionColumnShow,
            selected: isSubmissionColumnShow,
            disable: !isDetectFuncActivate || alreadySent,
          }"
          slot="reference"
          @click="toggleShowSubmissionColumn"
          style="background: #4A25BA; position: relative;"
          v-show="isEditorRightZoomEnough"
        >
          <img
            style="position: absolute; top: -10px; right: 0px; width: 25px;"
            :src="resources.collectArticleBadge"
            class="gaixing-badge"
          />
          <div class="btn-text" style="color: white; text-align: center; margin-left: 0px;">{{!alreadySent ? '模板征集' : '模板征集'}}</div>
        </div>

      </el-popover> -->

      <!-- 智能改写 小按钮 -->
      <el-tooltip
        content="智能改写"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring"
          :class="{
            white: !isRewriteColumnShowModal,
            selected: isRewriteColumnShowModal,
            disable: !isRewriteFuncActivate,
          }"
          @click="toggleShowRewriteColumn(false)"
        >
          <SvgIcon icon-class="fnBtnRewriteMini" class="icon-24px" />
        </div>
      </el-tooltip>
      <!-- 智能改写 大按钮 -->
      <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isIntelRewriteHintShow"
      >
        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">智能改写前需要填写正文内容</div>
          </div>
          <div class="confirm" @click="isIntelRewriteHintShow = false">
            知道了
          </div>
        </div>
        <div
          class="btn-ring extend-with-text"
          :class="{
            white: !isRewriteColumnShowModal,
            selected: isRewriteColumnShowModal,
            disable: !isRewriteFuncActivate,
          }"
          slot="reference"
          @click="toggleShowRewriteColumn(false)"
          v-show="isEditorRightZoomEnough"
        >
          <!-- <SvgIcon icon-class="fnBtnRewriteNormal" class="icon-24px" /> -->
          <SvgIcon icon-class="fnBtnRewriteMini" class="icon-24px" />
          <div class="btn-text">智能改写</div>
        </div>
      </el-popover>
      <!-- 智能扩写 小按钮 -->
      <!-- <el-tooltip
        content="智能扩写"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring"
          :class="{
              white: !isExpendColumnShowModal,
              selected: isExpendColumnShowModal,
              disable: !isRewriteFuncActivate,
            }"
          @click="toggleShowExpendColumn(false)"
        >
          <SvgIcon
            icon-class="fnBtnExtendWriteNormal"
            class="icon-24px"
          />
        </div>
      </el-tooltip> -->
      <!-- 智能扩写 大按钮 -->
      <!-- <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isIntelExpendHintShow"
      >

        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">智能扩写前需要填写正文内容</div>
          </div>
          <div
            class="confirm"
            @click="isIntelExpendHintShow = false"
          >知道了</div>
        </div>

        <div
          class="btn-ring extend-with-text"
          :class="{
              white: !isExpendColumnShowModal,
              selected: isExpendColumnShowModal,
              disable: !isRewriteFuncActivate,
            }"
          slot="reference"
          @click="toggleShowExpendColumn(false)"
          v-show="isEditorRightZoomEnough"
        >
          <SvgIcon
            icon-class="fnBtnExtendWriteNormal"
            class="icon-24px"
          />
          <div class="btn-text">智能扩写</div>
        </div>

      </el-popover> -->
      <!-- 智能摘要 小按钮 -->
      <el-tooltip
        content="智能摘要"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring"
          :class="{
            white: !isSummaryColumnShowModal,
            selected: isSummaryColumnShowModal,
            disable: !moreThanTwoHundredWords,
          }"
          @click="toggleSummary"
        >
          <SvgIcon icon-class="fnBtnBriefMini" class="icon-24px" />
        </div>
      </el-tooltip>
      <!-- 智能摘要 大按钮 -->
      <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isIntelSummaryHintShow"
      >
        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">智能摘要需要填写正文200字以上</div>
          </div>
          <div class="confirm" @click="isIntelSummaryHintShow = false">
            知道了
          </div>
        </div>
        <div
          class="btn-ring extend-with-text"
          :class="{
            white: !isSummaryColumnShowModal,
            selected: isSummaryColumnShowModal,
            disable: !moreThanTwoHundredWords,
          }"
          slot="reference"
          @click="toggleSummary"
          v-show="isEditorRightZoomEnough"
        >
          <SvgIcon icon-class="fnBtnBriefNormal" class="icon-24px" />
          <div class="btn-text">智能摘要</div>
        </div>
      </el-popover>
      <!-- 智能纠错 小按钮 -->
      <el-tooltip
        content="智能纠错"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring"
          :class="{
            white: !isCorrectShow,
            selected: isCorrectShow,
          }"
          @click="toggleCorrection"
        >
          <SvgIcon
            v-if="correctionStatus !== 2"
            icon-class="fnBtnCorrectNormal"
            class="icon-24px"
          />
          <div class="loader-circle" v-if="correctionStatus === 2">
            <SvgIcon icon-class="fnBtnCorrectStatus2Mini" class="icon-24px" />
          </div>
          <span class="btn-errorrnum" v-if="editorSuccessErrornum > 0">{{
            editorSuccessErrornum
          }}</span>
        </div>
      </el-tooltip>
      <!-- 智能纠错 大按钮 -->
      <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isTitleRecommandHintShow"
      >
        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">智能纠错，文本需要有内容才能纠错</div>
          </div>
          <div class="confirm" @click="isTitleRecommandHintShow = false">
            知道了
          </div>
        </div>

        <div
          class="btn-ring extend-with-text"
          :class="{
            white: !isCorrectShow,
            selected: isCorrectShow,
          }"
          @click="toggleCorrection"
          slot="reference"
          v-show="isEditorRightZoomEnough"
        >
          <SvgIcon
            v-if="correctionStatus !== 2"
            icon-class="fnBtnCorrectNormal"
            class="inherit-color icon-24px"
          />
          <!-- 等待纠错结果loading -->
          <div class="loader-circle" v-if="correctionStatus === 2">
            <SvgIcon icon-class="fnBtnCorrectStatus2Normal" class="icon-24px" />
          </div>
          <div class="btn-text" v-if="correctionStatus === 2">正在纠错</div>
          <div class="btn-text" v-else>智能纠错</div>
          <span class="btn-errorrnum" v-if="editorSuccessErrornum > 0">{{
            editorSuccessErrornum
          }}</span>
        </div>
      </el-popover>

      <!-- 质量检测 小按钮 -->
      <el-tooltip
        content="质量检测"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring"
          :class="{
            white: !isDetectionModalShow,
            selected: isDetectionModalShow,
            disable: !isDetectFuncActivate,
          }"
          @click="toggleShowDetectionColumn"
        >
          <SvgIcon icon-class="fnBtnQualityCheckNormal" class="icon-24px" />
        </div>
      </el-tooltip>
      <!-- 质量检测 大按钮 -->
      <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isQualityDetectHintShow"
      >
        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">质量检测需要标题至少满5个字，标题不能为空。</div>
          </div>
          <div class="confirm" @click="isQualityDetectHintShow = false">
            知道了
          </div>
        </div>

        <div
          class="btn-ring extend-with-text np-detection-btn"
          :class="{
            white: !isDetectionModalShow,
            selected: isDetectionModalShow,
            disable: !isDetectFuncActivate,
          }"
          slot="reference"
          @click="toggleShowDetectionColumn"
          v-show="isEditorRightZoomEnough"
        >
          <SvgIcon
            v-if="curDetectionStatus !== 1"
            icon-class="fnBtnQualityCheckNormal"
            class="icon-24px"
          />
          <div v-if="curDetectionStatus !== 1" class="btn-text">质量检测</div>
          <svg-icon
            v-if="curDetectionStatus === 1"
            icon-class="hourglass-loading-one"
            size="20"
            class="icon-24"
            color="#2A9FDE"
          />
          <div v-if="curDetectionStatus === 1" class="btn-text">正在检测</div>
          <!--完成图标-->
          <svg-icon
            v-if="
              !isLookDetectionReport &&
                curDetectionStatus === 2 &&
                !isDetectionModalShow
            "
            icon-class="complete-one"
            class="detection-complete-icon"
            size="33"
          />
        </div>
      </el-popover>

      <!-- 分享预览 小按钮 -->
      <!-- <el-tooltip content="分享预览" placement="left" v-show="!isEditorRightZoomEnough">
        <div
            class="btn-ring white"
            :class="{
              disable: !isDetectFuncActivate,
            }"
            @click="toggleShowShareColumn">
          <SvgIcon icon-class="fnBtnSharePreviewNormal" class="icon-18px" />
        </div>
      </el-tooltip> -->
      <!-- 分享预览 大按钮 -->
      <!-- <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isIntelShareHintShow">

        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">分享预览需要有标题和内容</div>
          </div>
          <div class="confirm" @click="isIntelShareHintShow = false">知道了</div>
        </div>

        <div
            class="btn-ring extend-with-text white"
            :class="{
              disable: !isDetectFuncActivate,
            }"
            slot="reference"
            @click="toggleShowShareColumn"
            v-show="isEditorRightZoomEnough">
          <SvgIcon icon-class="fnBtnSharePreviewNormal" class="icon-18px" />
          <div class="btn-text">分享预览</div>
        </div>

      </el-popover> -->

      <!-- 一键复制 小按钮 -->
      <el-tooltip
        content="一键复制"
        placement="left"
        v-show="!isEditorRightZoomEnough"
      >
        <div
          class="btn-ring white"
          :class="{
            disable: !isCopyFuncActivate,
          }"
          @click="copyArticle"
        >
          <SvgIcon icon-class="fnBtnCopyMini" class="icon-18px" />
        </div>
      </el-tooltip>

      <!-- 一键复制 大按钮 -->
      <el-popover
        placement="left"
        width="200"
        trigger="manual"
        v-model="isCopyHintShow"
      >
        <div class="hint-box">
          <div class="content">
            <div class="el-icon-warning"></div>
            <div class="title">一键复制前需要填写正文内容</div>
          </div>
          <div class="confirm" @click="isCopyHintShow = false">知道了</div>
        </div>

        <div
          class="btn-ring extend-with-text white"
          :class="{
            disable: !isCopyFuncActivate,
          }"
          slot="reference"
          @click="copyArticle"
          v-show="isEditorRightZoomEnough"
        >
          <SvgIcon icon-class="fnBtnCopyNormal" class="icon-18px" />
          <div class="btn-text">一键复制</div>
        </div>
      </el-popover>
    </div>

    <div class="word-to-correct-box" :style="correctBox.style">
      <div
        class="correct-item"
        v-for="(row, index) in correctBox.btnList"
        :key="index"
        :class="[row.type]"
        @click="onErrorCorrectEvent(row)"
      >
        {{ row.title }}
      </div>
    </div>

    <!-- 添加主题 -->
    <ModalAddTopic
      v-if="isAddTopicModalShow"
      :init-topic="initSearchTopic"
      :add-topic-mode="addTopicMode"
      :forced="false"
      @submit="onAddTopic"
      @close="closeAddTopicModal"
    />

    <!-- 选择要提取的内容 -->
    <ModalSelectParagraph
      v-if="isSelectParagraphShow"
      :init-article="initSelectParagraphArticle"
      @submit="onCommitAddParagraph"
      @showProductDeclear="showProductDeclear"
      @close="isSelectParagraphShow = false"
    />

    <!-- 导入文章对话框 flag:1表示从编辑器触发 2表示从收藏触发 -->
    <!-- <ModalAddResourceForEditor
      v-if="isAddResourceModalShowForEditor"
      :resource-type="[currentKeyword]"
      @callback="importArticleCallback"
      @close="closeAddResourceModalForEditor"
    /> -->
    <!--编辑器导入文章对话框-->
    <ImportArticleModal
      :isShow="isShowArticleImportModal"
      :importType="showWhichModal"
      @callback="importArticleCallback"
      @close="closeImportArticleModal"
    />

    <!--素材导入文章对话框-->
    <ModalAddResource
      v-if="isAddResourceModalShow"
      :resource-type="[currentKeyword]"
      @submit="addResourceLoadingElem"
      @to-load="
        e => {
          loadArticleResource(e)
        }
      "
      @close="closeAddResourceModal"
    />

    <!-- 智能生成进度展示 -->
    <IntelgenProcess
      v-if="isIntelgenProcessShow"
      :title="intelgenDict.title"
      :list="intelgenDict.list"
      :isProcessing="intelgenDict.isProcessing"
      :processing="intelgenDict.processing"
      :editorStatus="editorStatus"
    />

    <!-- <QualityDetection
      v-if='isDetectionModalShow'
      :editor='editor'
      :deleteCtion="deleteCtion"
      @close='isDetectionModalShow = false'
      @highlight='highlight'
      @detectAgain='detectAgain'
    /> -->
    <QualityDetectionNew
      ref="qualityDetectionNew"
      v-show="isDetectionModalShow"
      :editor="editor"
      :deleteCtion="deleteCtion"
      @close="
        isDetectionModalShow = false
        isLookDetectionReport = true
      "
      @markContent="markContent"
      @notifyDetectionStatus="notifyDetectionStatus"
    />

    <ModalPicSelector
      v-if="isPicSelectorModalShow"
      :editor="editor"
      @close="isPicSelectorModalShow = false"
    />

    <ModalAddPublishAccount
      v-if="isPublishAccountAddModalShow"
      @close="isPublishAccountAddModalShow = false"
    />

    <ModalEditPublishAccount
      v-if="isPublishAccountEditModalShow"
      :account="publishEditAccount"
      @close="isPublishAccountEditModalShow = false"
    />

    <SelfTopicChangeModal
      v-if="isSetSelfTopicShow"
      @close="isSetSelfTopicShowClose"
    />

    <!-- 纠错等待弹框 -->
    <CorrectionWait
      v-if="isCorrectionShow"
      v-show="canclModalStatus"
      :editor="editor"
      :domContentLength="domContent.length"
      :editorContentLength="editorContentLength"
      :isStyleFalse="isStyleFalse"
      :progressbarnumber="progressbarnumber"
      :childrenStatus="childrenStatus"
      @close="canclModalStatusShow"
      @canclModal="
        e => {
          canclModalStatus = e
        }
      "
      @highlight="highlight"
      @beginCorrection="beginCorrection"
      @closeStatus="closeStatus"
      @correctionStatus="
        e => {
          correctionStatus = e
        }
      "
      @detectAgain="detectAgain"
    />

    <!-- 历史 History -->
    <History
      v-if="isHistoryModal"
      :historyList="historyList"
      :title="editor.title"
      :isHistoryLoad="isHistoryLoad"
      @close="
        e => {
          isHistoryModal = false
          historyList = []
        }
      "
      @historyReplacement="historyReplacement"
    />
    <!-- 反馈 -->
    <FaceBack
      v-if="articleComplaint.show"
      :articleId="articleComplaint.id"
      :type="articleComplaint.type"
      @close="articleComplaint.show = false"
    />

    <div class="guide-box" v-if="isGuideShow" @click="closeGuide">
      <div
        class="guide-content-box"
        :style="isGuideShow === 2 ? guideContentPosition : ''"
      >
        <div
          class="guide-hot-box"
          v-if="isGuideShow === 1"
          :style="isLeftShrink ? 'width: 70px' : 'width: 600px'"
        ></div>
        <div
          class="guide-know-box"
          :class="{ shadow: isGuideShow === 2 }"
          @click.stop="() => {}"
        >
          <div class="guide-close" @click="closeGuide">
            <img :src="ImgBase.iconCloseGray" alt="" />
          </div>
          <div class="guide-know-box-top">
            <img class="guide-icon" :src="ImgBase.iconLoudspeaker" alt="" />
            <div class="guide-title">
              {{ isGuideShow === 1 ? '素材栏' : 'AI功能区' }}
            </div>
          </div>
          <div class="guide-content">
            {{
              isGuideShow === 1
                ? '帮助您查找热点、素材、历史文章、AI初稿理清写作思路，提高效率。'
                : '改写、扩写、检测、纠错系列功能，助您更高效创作。'
            }}
          </div>
          <div class="guide-btn" @click="closeGuide">
            {{ isGuideShow === 2 ? '知道了' : '下一个' }}
          </div>
        </div>
      </div>
    </div>

    <!-- 免费次数提示框 -->
    <el-dialog
      class="cneterisDialog"
      title="体验次数耗尽提醒"
      :visible.sync="centerDialogVisible"
      width="30%"
      center
    >
      <span
        >您每个月10次的智能纠错功能体验机会已耗尽，请购买会员后继续体验</span
      >
      <span slot="footer" class="dialog-footer">
        <el-button @click="centerDialogVisible = false">暂不体验</el-button>
        <el-button type="primary" @click="isPaymentClick">立即购买</el-button>
      </span>
    </el-dialog>
    <!-- 支付弹框 -->
    <PurchaseModal
      v-if="isPaymentdialog"
      :modalShow="isPaymentdialog"
      @closePurchase="isPaymentdialog = false"
      ref="PurchaseModalName"
    />

    <!-- <PurchaseModalNew
      :show="false"
      @close="closePurchaseModal"
    /> -->

    <!--弹出支付框之前弹出功能引导-->
    <!-- <FunctionHintChain
      :show="isShowPurchaseModal"
      @close="closePurchaseModal"
    /> -->

    <ModalArticleStructure
      :templId="templateid"
      :editorTitle="editor.title"
      :editorStatus="editorStatus"
      v-if="flagCuront"
      @generateButtonGenerate="generateButtonGenerate"
      @close="flagCuront = false"
    />
    <!-- 笔记预览 -->
    <!-- <el-dialog
      :title="noteRemote.title"
      :visible.sync="notePreview"
      width="40%"
      custom-class="note-preview-dialog"
      :before-close="() => {notePreview = false}">
      <div class="note-preview-content" v-html="noteRemote.content"></div>
    </el-dialog> -->

    <!--企业发布-->
    <PublishModal
      :show="isShowPublishModal"
      :company="defaultCompany"
      :note="noteRemote"
      @close="isShowPublishModal = false"
    />

    <div class="gt-modal-wrapper" v-show="notePreview">
      <div class="note-preview-dialog">
        <i
          class="gt-icon-close el-icon-close"
          @click="
            () => {
              notePreview = false
            }
          "
        ></i>
        <div class="gt-modal-header">
          <h1 class="gt-modal-title">{{ noteRemote.title }}</h1>
        </div>
        <div class="note-preview-content" v-html="noteRemote.content"></div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { mapGetters } from 'vuex'
import { Loading } from 'element-ui'

// components
import dayjs from 'dayjs'
//import QualityDetection from '../../components/ComposePage/qualityDetection'
import QualityDetectionNew from '../../components/ComposePage/QualityDetectionNew'

import {
  scrollIntoViewSmoothly,
  TestUserAgent,
} from '../../utils/scroll/scrollIntoView'

// svg item
// api
import {
  // shadowFetchParagraphHardMode, // 为智能写作模板，获取素材专用接口
  fetchIntelTemplateParagraph, // 为智能写作模板，获取素材专用接口
  RewriteParagraphV2_2,
  // 获取写作模板详情
  GETpoint,
  POSTisAuthHasChance,
  POSTauthReduceChance,
  POSTCorrectionTest,
  POSTuploadReplaceImg,
  // 保存新文章
  POSTNoteNewAdd,
  // 获取文章历史版本
  GETHistoryVersion,
  //更新分享镜像字段
  copyToFormal,
  sensitiveWordCheckBaidu, //敏感词检测
  collectSelectedText,
  fetchUserWordLimit,
  POSTcommunityMakeMoney,
} from '../../api/client/aiWriter'

import {
  // convertToOssSrc,
  POSTUploadFileToOSSFile,
} from '../../api/client/aiWriter/image.js'

import { NoteApi } from '../../api/client/index.js'

//import RewriteColumn from '../../components/ComposePage/RewriteColumn'
//import ExpendColumn from '../../components/ComposePage/ExpendColumn'
//import SummaryColumn from '../../components/ComposePage/SummaryColumn'
import Submission from '../../components/ComposePage/Submission'
//import BrainStormTitleColumn from '../../components/ComposePage/BrainStormTitleColumn'
import BrainStormTitleColumnNew from '../../components/ComposePage/BrainStormTitleColumnNew'
import GenerateArticleColumn from '../../components/ComposePage/GenerateArticleColumn'
import PublishColumn from '../../components/ComposePage/PublishColumn'
import ModalPicSelector from '../../components/ComposePage/PublishColumn/PicSelector'
import ModalAddPublishAccount from '../../components/ComposePage/PublishColumn/AccountAdd'
import ModalEditPublishAccount from '../../components/ComposePage/PublishColumn/AccountEdit'
import SummaryColumn from '~/components/ComposePage/SummaryColumn/index'
import RewriteOrExtendWrite from '~/components/ComposePage/RewriteOrExtendWrite'
import { EventBus } from '~/components/eventbus.js'
import { GetHotEvent } from '~/api/client/aiWriter/themeTopic.js'
import {
  getTempNoteId,
  setTempNoteId,
  isShowFastGenTip,
} from '~/api/clientStorage/index.js'

import { saveBusinessNote, getDefaultCompany } from '~/api/client/business'

import SvgIcon from '~/plugins/svgIcon/SvgIcon'
import HelpBlock from '~/components/content/HelpBlock'
import UADetective from '~/components/uaDetective/index.vue'
import SelfTopicChangeModal from '~/components/Hotspot/SelfTopicModal/changeModal'
import ColumnLeft from '~/components/ComposePage/ColumnLeft'

// modal
import ModalAddTopic from '~/components/Hotspot/ModalAddTopic'
import ModalSelectParagraph from '~/components/Hotspot/ModalSelectParagraph'
import ModalAddResource from '~/components/Hotspot/ResourceModal/ModalAddResource'
//import ModalAddResourceForEditor from "~/components/Hotspot/ResourceModal/ModalAddResourceForEditor"
import PromotionModal from '~/components/PromotionModal'
import ImportArticleModal from '~/components/ComposePage/ArticleImport/importArticleModal'

import IntelgenProcess from '~/components/ComposePage/intelgenArticle/intelgenProcess'
import History from '~/components/History/index.vue'
// 反馈
import FaceBack from '~/components/FaceBack/index'

import ExportDropdown from '~/components/folder/ExportDropdown'

import MyTinyEditor from '~/components/Editor/index.vue'
import UserLogin from '~/components/Account/tempLogin.vue'

import ModalArticleStructure from '~/components/Hotspot/ModalArticleStructure'

// payment
import PurchaseModal from '~/components/PurchaseModal/index.vue'

//import PurchaseModalNew from '~/components/PurchaseModalNew'

//import FunctionHintChain from '~/components/purchase/FunctionHintChain'

//企业
import PublishModal from '~/components/folder/business/document/PublishModal'

// filters
import FilterBgimg from '~/utils/filter/backgroundImage.js'

// static
import svgNoteEditor from '~/assets/svg/note-editor/index.js'
import IconNavPre from '~/plugins/svgIcon/svg/goup.svg'
import IconNavNext from '~/plugins/svgIcon/svg/godown.svg'

import Img from '~/assets/img/compose/index.js'
import ImgHomePage from '~/assets/img/homePage/index.js'
import ImgBase from '~/assets/img/temp-base/index.js'

//utils
import { copyRichArticle } from '~/utils/copy.js'
// import Utils from '~/utils/utils.js'
import HeaderUser from '~/components/HeaderUser'
// tiptop
import CorrectionWait from '~/components/Editor/correctionWait'
import FilterList from '~/utils/filter/index.js'

// Image Utils
import { DataURLtoFile } from '~/utils/image/DataURLtoFile'

//教程json
import courseJson from '~/assets/json/course.json'

import experiment from '~/assets/img/icon/experiment.png'

import foldderCollectionArticleSvg from '~/assets/img/compose/foldder-collection-article.svg'

import collectArticleBadge from '~/assets/img/compose/collect-article-badge.png'

import documentClick from '~/utils/common/DocumentClick'

// 批量改写 魔法棒
const IMG_MAGIC_BANG =
  'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/icon/compose/icon-magic-patch.png'

// const NOTE_SAVE_STATUS_SAVED = 'saved'
// const NOTE_SAVE_STATUS_SAVE_SUCCESS = 'saveSuccess'
// const NOTE_SAVE_STATUS_UPLOADING = 'uploading'
// text
// const NOTE_SAVING_TEXT = '正在保存...'
const NOTE_SAVED_SUCCESS_TEXT = '保存成功'

// 自动保存的counter
let _counterAutoSave = 0
let _counterUpdateSave = 0

let listenerOnResize = null

let loadingInstance = null

let _addBulbCounter = null

let _magicTitleColumnShowCounter = -1
let _summaryColumnShowCounter = -1
let _rewriteColumnShowCounter = -1
// let _expendColumnShowCounter = -1
let _publishColumnShowCounter = -1
let _generateColumnShowCounter = -1

let _wordCorrectMouseoverFn = null
let loadingPercentCount = 1

// 上级路由
let lastRouter

export default {
  name: 'PageNoteEditorV2',
  asyncData({ params, store }) {
    let uid = params.id
    // let hotList = await GetHotEvent({ order: 2 })
    return {
      Img,
      ImgHomePage,
      ImgBase,
      uid,
      // hotList,
    }
  },
  head() {
    return {
      title: '智能创作-Get智能写作，开启AI写作新时代',
      meta: [
        { charset: 'utf-8' },
        { name: 'description', content: '人机写作、编辑器、创作。' },
        { name: 'keywords', content: '写作模板,10w+模板，提升写作效率' },
        {
          hid: 'viewport',
          name: 'viewport',
          content: 'width=device-width, initial-scale=1.0, user-scalable=no',
        },
      ],
    }
  },
  // beforeRouteEnter(to, from, next) {
  //   next(vm => {
  //     vm.lastPathURLBeforeEnter = from.fullPath
  //   })
  // },
  async beforeRouteLeave(to, from, next) {
    // console.log('beforeRouteLeave', to)
    await this.saveNoteData()
    next()
  },
  computed: {
    ...mapGetters({
      isVip: 'user/isVip',
      isLogin: 'user/isLogin',
      userinfo: 'user/userinfo',
      hotSpotsData: 'topic/hotSpotsData',
      userAvatar: 'user/avatar',
      isCurrentVersion: 'version/isCurrentVersion',
      shouldShowPreviewVersionSelection:
        'version/shouldShowPreviewVersionSelection',
      previewVersion: 'version/previewVersion',
      SelfTopicChangeModalShow: 'component/SelfTopicChangeModalShow',
      isDecrementLevelZIndex: 'compose/getIsDecrementLevelZIndex',
    }),
    isBusiness() {
      return this.$route.query.documentType === 'business'
    },
    headerTabList() {
      let storePath = this.$store.state.path

      return [
        'hotspot', //发现
        'template', //模板中心
      ].map(k => storePath[k])
    },
    // 跟Toggle规则左边栏大小
    colLeftWidth() {
      if (this.isLeftShrink) return 0
      return 530
    },
    columnRightWidth() {
      if (process.client) {
        return (this.windowInnerWidth || window.innerWidth) - this.colLeftWidth
      }
      return 0
    },
    toolboxSetStyleOnEditor() {
      const editorWidth = 800
      let columnRightWidth = this.columnRightWidth
      let rightPos = (columnRightWidth - editorWidth) / 2
      // 最小距离右边 60px + padding-right 10px
      rightPos = rightPos - 50 - 30 - 115
      const minRight = 50
      rightPos = rightPos < minRight ? 10 : rightPos
      return {
        right: `${rightPos}px`,
        //right: `0px`,
        // zIndex: (this.showTooltips || this.isGuideShow === 2) ? '10000' : ''
        zIndex: this.isDecrementLevelZIndex ? 500 : 1000,
      }
    },
    ADboxSetStyleOnEditor() {
      const editorWidth = 800
      const maxADWidth = 300
      let columnRightWidth = this.columnRightWidth
      let rightPos = (columnRightWidth - editorWidth) / 2
      const width = rightPos - 65
      rightPos = width > maxADWidth ? width - maxADWidth : 20
      const minRight = 20
      rightPos = rightPos < minRight ? 20 : rightPos
      return {
        right: `${rightPos}px`,
        width: `${width}px`,
      }
    },
    isEditorRightZoomEnough() {
      const editorWidth = 800
      let rightPos = (this.columnRightWidth - editorWidth) / 2
      const minRight = 160
      return rightPos >= minRight
    },
    columnLeftStyle() {
      return {
        width: `${this.colLeftWidth || 0}px`,
      }
    },

    isTitleFuncActivate() {
      let title = this.editor.title || ''

      return title && title.trim().length >= 1
    },

    isPublishFuncActivate() {
      let title = this.editor.title || ''
      return title && title.trim().length >= 5 && !!this.editor.content
    },

    isCopyFuncActivate() {
      return !!this.editor.content
    },

    isDetectFuncActivate() {
      return this.isTitleFuncActivate && !!this.editor.content
    },

    isRewriteFuncActivate() {
      return !!this.editor.content && this.correctionStatus !== 2
    },
    moreThanTwoHundredWords() {
      // 监听文章的字数判断是否可以使用智能摘要
      return (
        this.editor.content
          .replace(/<section>(([\s\S])*?)<\/section>/g, '')
          .replace(/<article>(([\s\S])*?)<\/article>/g, '')
          .replace(/<.+?>/g, '')
          .replace('\n', '').length > 200
      )
    },
    isRightOperateShow() {
      return this.isAhaTitleColumnShow || this.isRewriteColumnShow
    },
  },
  beforeRouteEnter(to, from, next) {
    // console.log('referrer', document.referrer.indexOf(location.origin))
    if (from.path.indexOf('share') < 0) lastRouter = from
    next()
  },
  async mounted() {
    // console.log('from', this.$route.matched[0].path)
    // // 记录上一个路由
    this.previewRouterInit()

    // 初始化清除本地原有 new 缓存
    localStorage.removeItem('editorNote_new')
    this.isShowVideo = this.$Utils.checkEditorIntroShow()
    this.isSpecialNavigator = TestUserAgent(window.navigator.userAgent)
    this.$store.dispatch('version/checkIsCurrentVersion')
    this.$store.dispatch('topic/loadHotSpotsData')
    this.columnLeftMode = this.$route.query.mode || 'Meterial'
    // 初始化元素高度
    this.GetEditorMaxHeight()

    this.initEventListeners()
    // 初始化文章和关键词
    this.initArticleAndKeyword()

    // 未初始化用户信息
    if (!this.$store.getters['user/isInfoInit'])
      await this.$store.dispatch('user/INIT_USER_INFO')

    this.initUser()
    if (this.user.token) {
      this.getUserWordLimit()
    }

    this.initToolboxTips()

    //document.addEventListener('click', this.bindClick)
    //监听document click
    this.addDocumentEventListener()

    this.isGuideShow = this.$Utils.checkGuideShow(this) ? 1 : false
    this.$store.dispatch('compose/UPDATE_EDITOR_TITLE', '')

    //EventBus.$on("importArticleCallback", this.importArticleCallback)
    let query = { ...this.$route.query }
    if (query.mode === 'ArticleColumn' && !query.uid) {
      this.isLeftShrink = true
    }

    //查询是否有默认企业
    this.findDefaultCompany()

    this.fetchAdVertisement()
  },
  beforeDestroy() {
    if (loadingInstance) loadingInstance.close()
    documentClick.removeClickListener('laboratoryDiv')
  },

  data() {
    return {
      previewRouter: {},
      ImgBase,
      lastPathURLBeforeEnter: '/hotspot',

      // 帮助按钮Modal
      isShowHelp: false,

      contentEditor: null,
      // 搜索主题的输入框
      keywordSearchBoxValue: '',

      toolboxTooltipShow: false,

      tempSelectedArticleSections: [],

      IMG_MAGIC_BANG,

      svgNoteEditor,
      IconNavPre,
      IconNavNext,

      uid: '',
      user: {},

      selectedCardId: '',
      selectedCardInfo: {},

      coverImage: '',
      articleTitle: '',
      editor: {
        title: '',
        content: '',
        brief: '',
        classifyId: 0,
        isDel: 0,
        isImg: 0,
      },
      noteContentLoaded: false,

      // auto save 自动保存
      isAutoSaveOn: false,
      isAutoSaveOn_Img: false,
      autoSaveHintTextStatus: 107, //101保存中,102自动保存成功,103手动保存成功,104网络异常暂存本地,105未登录无法保存,106以读取远程数据,107空状态
      autoSaveHintText: '',
      autoSaveHintTextDefault: '已自动保存',

      changeAfterLastChange: false,

      // 笔记夹列表
      folderList: [],
      folderInfo: {},

      fullPageContentHeight: 800,
      articleBoxHeight: 800,
      parentSuggestEditorMaxHeight: 0,
      topicsListMaxHeight: 0,

      minColumnLeftWidth: 500,
      windowInnerWidth: 0,

      isAddTopicModalShow: false,
      isSelectParagraphShow: false,
      initSelectParagraphArticle: {},
      // 添加主题的模式i
      addTopicMode: '',
      isAddResourceModalShow: false,
      //编辑器导入文章modal框
      isAddResourceModalShowForEditor: false,

      //是否显示导入对画框
      isShowArticleImportModal: false,
      //显示那个类型对话新
      showWhichModal: 'file', //file link

      // 活动弹窗
      isPromotionShow: false,

      initSearchTopic: '',

      isNotificationShow: false,

      // 智能生成文章
      isIntelgenProcessShow: false,
      intelgenDict: {
        title: '',
        list: [],
        isProcessing: true,
        processing: 0,
      },

      editorElem: null,

      shouldArticleRewriteEnabled: false,

      isPreviewerShow: false,
      previewArticle: {},

      // 右边栏 魔术title
      isAhaTitleColumnShow: false,
      isAhaTitleColumnShowModal: false,
      ahaTitle: '',

      isRewriteColumnShow: false, // 智能改写框
      isRewriteColumnShowModal: false, // 智能改写控制样式
      //是否显示扩写框
      isExtendColumnShowMal: false,
      //当前选择的的是改写还是扩写
      rewriteOrExtendType: 1, //1改写 2扩写
      isExpendColumnShow: false, // 智能扩写框
      isExpendColumnShowModal: false, // 智能扩写控制样式
      isSummaryColumnShow: false, // 智能摘要框
      isSummaryColumnShowPlugin: false,
      isSummaryColumnShowModal: false, // 智能摘控制样式
      isSubmissionColumnShow: false, //投稿赚钱框
      alreadySent: false, //是否已经投稿
      //纠错
      isCorrectShow: false,
      isCorrectShowModal: false,

      //智能成稿
      isGenerateColumnShow: false,
      isGenerateColumnShowModal: false,

      //全网发布
      isPublishColumnShow: false,
      isPublishColumnShowModal: false,

      //质量检测
      isDetectionModalShow: false,
      //质量加测当前状态 0未开始检测 1正在检测中 2检测成功 3检测失败
      curDetectionStatus: 0,
      //是否点击查看检测报告
      isLookDetectionReport: false,

      //图片选择
      isPicSelectorModalShow: false,

      //添加发文账号
      isPublishAccountAddModalShow: false,

      publishEditAccount: {},
      isPublishAccountEditModalShow: false,

      //收藏领域modal
      isSetSelfTopicShow: false,

      rewriteColumnContent: '',
      lastRewriteColumnContent: '',

      showTooltips: false,

      // 需要修正的信息
      correctBox: {
        target: null,
        show: false,
        btnList: [],
        style: {},
      },

      showToolboxFastGenTip: false,
      // 是否手动控制
      showToolboxFastGenTipManual: false,
      rewriteWorking: {},

      isRewriteWorking: false,

      // 左边栏收缩
      isLeftShrink: false,
      // 左边显示素材或者收藏 Meterial Collection

      lastClickElem: null,
      tinyEditor: null,

      currentKeyword: '',
      currentTemplateKeyword: '',
      isSpecialNavigator: '',

      isSavingAsNewNote: false,

      showRightAdvSpace: false, //右边广告位
      isTutorVideoShow: true, // tutorvideo

      isTitleRecommandHintShow: false,
      isIntelArticleHintShow: false,
      isIntelRewriteHintShow: false,
      isIntelExpendHintShow: false,
      isIntelSummaryHintShow: false, // 智能摘要不可点击提示
      isQualityDetectHintShow: false,
      isIntelShareHintShow: false,
      isSubmissionHintShow: false, // 投稿赚钱不可点击提示
      isPublishHintShow: false,
      isCopyHintShow: false,
      isGuideShow: false,
      isCorrectionShow: false,
      guideContentPosition: '',
      editorContentLength: 0,
      isShowVideo: false,
      videoUrl: ImgHomePage.tutor,
      routeFullPathDatas: '',
      // 免费次数提示框
      centerDialogVisible: false,
      isPaymentdialog: false,
      isSuspendedFrame: false,
      //是否显示改写扩写工具栏
      isShowRewriteExtendTool: false,
      toolbarPosUseRect: false, // 是否使用range rect 进行定位
      // 拖拽数据
      dataTransfer: null,
      whetLogin: false,
      parentOutlineSearchRecord: '',
      // 文章uid
      noteUid: '',
      // 控制左侧tab栏和编辑器里提示文案
      isleftTabFlagParent: true,
      // 提纲搜索文字
      keywordSearchOutlineText: '',
      // 纠错等待时间状态
      /**
       * 当前检测状态
       * 1、初始状态
       * 2、检测请求中
       * 5、检测成功
       */
      correctionStatus: 1,
      editorSuccessErrornum: 0,
      flagCuront: false,
      templateid: '',
      // 识别是否为编辑器页内模板唤醒
      editorStatus: 0,
      showTabBar: true,
      noteRemote: {
        title: '',
        content: '',
        brief: '',
        createTime: '',
        modifyTime: '',
      },
      // 判断该当前是否在保存
      saving: false,
      columnLeftMode: 'Meterial',
      isStyleFalse: false,
      progressbarnumber: 0,
      progressNum: 10,
      startTimer: '',
      endTimer: '',
      canclModalStatus: false,
      editorTextContent: '',
      childrenStatus: 1,
      resData: {},
      openStatus: false,
      noOpenArticleStatus: false,
      // modal 预览
      notePreview: false,
      hotList: [],
      // 文章内容已更新
      isShouldSyncArticleContent: false,
      deleteCtion: {},
      domContent: '',
      // 历史版本modal
      isHistoryModal: false,
      // 历史版本信息
      historyList: [],
      // 0 初次进入  1  选择时间
      historyTime: 0,
      //分享预览-草稿内容和发布内容是否一致(formalContent为空则为false、content === foramlContent则为false，查询出现异常为false)
      isShowUpdateShare: false,
      // 0 加载中 1 有备份 2 无备份
      isHistoryLoad: 0,
      // 是否投稿
      isNoteSubmit: false,
      // 用户反馈
      articleComplaint: {
        id: '',
        show: false,
        type: 1,
      },
      experiment,
      // 控制右下角各个图标展示
      isToolbox: true,
      isRewriteEnlarge: false, //改写扩写

      //是否显示购买框
      isShowPurchaseModal: false,

      //是否显示实验室下拉框
      isShowLaboratoryDropdown: false,
      //是否显示导出文章下拉
      isShowExportNoteDropdown: false,
      //企业笔记id
      businessNote: null,
      //是否显示发布框
      isShowPublishModal: false,
      //默认企业
      defaultCompany: {},
      //是否可以将笔记保存到企业
      canSaveToBusiness: false,
      //静态资源
      resources: {
        collectArticleBadge,
        foldderCollectionArticleSvg,
      },
      isShowFunctionHint: false,
      advertisement1Img: null,
      advertisement1Link: null,
      isTutorIntroDetailExpanded: true,
    }
  },
  components: {
    SvgIcon,
    HelpBlock,
    MyTinyEditor,

    ModalAddTopic,
    ModalSelectParagraph,
    ModalAddResource,
    //ModalAddResourceForEditor,
    ImportArticleModal,

    ModalPicSelector,
    ModalAddPublishAccount,
    ModalEditPublishAccount,
    PromotionModal,

    //BrainStormTitleColumn,
    BrainStormTitleColumnNew,
    GenerateArticleColumn,
    PublishColumn,
    //RewriteColumn, // 智能扩写
    //ExpendColumn, // 智能改写
    RewriteOrExtendWrite,
    SummaryColumn, // 智能摘要
    Submission, // 投稿赚钱
    // 智能生成的进度展示
    IntelgenProcess,

    //质量检测
    //QualityDetection,
    QualityDetectionNew,
    UADetective,
    SelfTopicChangeModal,
    ColumnLeft,
    HeaderUser,
    UserLogin,
    // payment
    PurchaseModal,
    //PurchaseModalNew,
    //FunctionHintChain,
    ModalArticleStructure,
    // 纠错进度展示
    CorrectionWait,
    // 历史版本
    History,
    // 反馈
    FaceBack,
    ExportDropdown,
    //企业
    PublishModal,
  },
  watch: {
    tempSelectedArticleSections() {
      this.changeAfterLastChange = true
    },
    'editor.content': {
      async handler(newVal, oldVal) {
        if (this.isRewriteEnlarge) return
        this.changeAfterLastChange = true
        // this.openAutoSave()
        // console.log('content update')
        delayFor(this.addSearchDOM, 1)
        // delayFor(this.uploadReplaceImg(), 1)
        // this.editorContentLength = newVal.replace(/<section>(([\s\S])*?)<\/section>/g, '').replace(/<article>(([\s\S])*?)<\/article>/g, '').replace(/<.+?>/g, '').replace('\n', '').length
        // this.editorContentLength = newVal.replace(/<[^>]+>/g, "").replace(/\s|[\r\n]/gi, '').length
        const str = newVal
          .replace(/<[^>]+>/g, '')
          // eslint-disable-next-line no-irregular-whitespace
          .replace(/(\r\n+|\s+|　+)/g, '龘')
          .replace(/[\x00-\xff]/g, 'm')
          .replace(/m+/g, '*')
          .replace(/龘+/g, '')
        this.editorContentLength = str.length
        // console.log('editorContentLength', this.editorContentLength)
        await this.uploadReplaceImg()
        // console.log('update save')
        if (!this.isAutoSaveOn) {
          this.saveNoteData()
        }
        if (this.editor.content && this.editor.content.length) {
          this.shouldArticleRewriteEnabled = true
        } else {
          this.shouldArticleRewriteEnabled = false
        }
        //如果当前是普通模板生成的文章则需要为文章里的链接加标识
        let query = this.$route.query
        if (query['templateFlag']) {
          this.bindClickForA({ title: '10W+文章' }, 1)
        }
      },
      deep: true,
    },
    'editor.title'() {
      this.changeAfterLastChange = true
      this.saveNoteData()
    },
    changeAfterLastChange() {
      if (this.changeAfterLastChange) {
        this.autoSaveHintText = ''
        this.autoSaveHintTextStatus = 100
      }
    },
    shouldArticleRewriteEnabled() {
      if (this.shouldArticleRewriteEnabled) {
        this.showToolboxFastGenTip = false
        setTimeout(() => {
          if (this.showToolboxFastGenTipManual === true)
            this.showToolboxFastGenTip = true
        }, 600)
      }
    },
    showTooltips() {
      if (this.showTooltips) {
        setTimeout(() => {
          this.toolboxTooltipShow = true
        }, 400)
      } else {
        this.toolboxTooltipShow = false
      }
    },
    isEditorRightZoomEnough(val) {
      if (!val) {
        this.isTitleRecommandHintShow = false
        this.isIntelArticleHintShow = false
        this.isIntelRewriteHintShow = false
        this.isIntelExpendHintShow = false
        this.isQualityDetectHintShow = false
        this.isIntelShareHintShow = false
        this.isPublishHintShow = false
        this.isCopyHintShow = false
      }
    },
    isLeftShrink(val) {
      setTimeout(() => {
        this.isSuspendedFrame = false
        const left = this.$Utils.getIntStyle(
          this.$refs.ELEMsuspendedFramePointer,
          'left'
        )
        if (val) {
          this.showTabBar = false
          this.$refs.ELEMsuspendedFramePointer.style.left = left + 290 + 'px'
        } else {
          this.showTabBar = true
          this.$refs.ELEMsuspendedFramePointer.style.left = left - 290 + 'px'
        }
      }, 500)
    },
    '$route.query.path': {
      handler: function(newVal, from) {
        if (newVal && newVal.indexOf('compose') < 0) {
          this.isAutoSaveOn = false
        }
      },
    },
    '$route.query.mode': {
      handler: function(newVal, from) {
        this.columnLeftMode = newVal
      },
    },
    '$route.query.km': {
      async handler(newVal, oldVal) {
        // console.log('compose km', newVal)
        if (newVal) {
          await this.saveNoteData(false)
        }
      },
    },
    '$route.query.kp': {
      async handler(newVal, oldVal) {
        if (newVal) {
          await this.saveNoteData(false)
        }
      },
    },
    '$route.query.ks': {
      async handler(newVal, oldVal) {
        // console.log('compose ks', newVal)
        await this.saveNoteData(false)
      },
    },
    '$route.query.uid': {
      handler: function(newVal, oldVal) {
        this.initArticleAndKeyword()
        let query = { ...this.$route.query }
        if (query.mode === 'ArticleColumn' && !newVal) {
          // 并且禁用展开按钮
          this.noOpenArticleStatus = true
        } else {
          this.noOpenArticleStatus = false
        }
        try {
          //质量检测重新初始化
          this.$refs['qualityDetectionNew'].goStepOne()
        } catch (e) {}
      },
    },
    saving: function(newVal, oldVal) {
      // if (newVal) {
      //   // this.autoSaveHintText = NOTE_SAVING_TEXT
      //   // this.autoSaveHintTextStatus = NOTE_SAVE_STATUS_SAVED
      //   // this.autoSaveHintTextStatus = 101
      // } else {
      //   const upTime = this.noteRemote.modifyTime
      //   this.autoSaveHintText = this.autoSaveHintTextDefault + ' ' + dayjs(upTime).format('HH:mm:ss')
      //   this.autoSaveHintTextStatus = 102
      //   console.log(this.autoSaveHintTextStatus)
      //   console.log(this.autoSaveHintText)
      // }
    },
    progressbarnumber(val) {
      if (val === 99 || !this.resData) {
        clearInterval(loadingPercentCount)
      } else if (val === 100) {
        // 100 纠错并标记成功
        clearInterval(loadingPercentCount)
        setTimeout(() => {
          this.isCorrectionShow = false
          this.progressbarnumber = 0
          this.isStyleFalse = false
        }, 1000)
      }
    },
    // 网络状态
    '$nuxt.isOffline': function(newVal, oldVal) {
      // console.log('net switched', newVal, oldVal)
      if (newVal) {
        this.autoSaveHintText = '网络异常，暂存本地'
        this.$message({
          type: 'warning',
          message: '您已断网，请尽快联网保存',
          duration: 5000,
        })
      } else {
        // console.log('net on')
        // this.autoSaveHintText = ''
        this.saveNoteData()
        // 比较当前内容和远程内容时间
      }
    },
    isStyleFalse(val) {
      if (val) {
        this.correctionStatus = 2
      } else {
        this.correctionStatus = 1
      }
    },
    // 右下角问题
    isRewriteColumnShow(val) {
      if (!val) {
        this.isToolbox = true
        this.isShowRewriteExtendTool = false
        return
      }
      this.isToolbox = false
    },
    // correctionStatus (val) {
    //   if (val === 2) {
    //     console.log(this.isRewriteFuncActivate)
    //     this.isRewriteFuncActivate = false
    //   }
    // }
    // //降低左侧z-index
    // isShowPurchaseModal (newVal) {
    //   this.$store.commit('compose/changeLevelZIndex', newVal)
    // }
  },
  filters: {
    ...FilterList,
    bgimg: FilterBgimg,
  },
  methods: {
    async getUserWordLimit() {
      const num = await fetchUserWordLimit()
      this.$store.dispatch('user/SET_WORD_LIMIT', num)
      const isConfirmed = localStorage.getItem('wordLimitConfirmed')
      if (isConfirmed !== '1') {
        this.$notify({
          title: '工作台每日字数限制',
          message:
            '为了更好的服务用户。我们决定增加工作台每日可操作字数限制，终身会员不做限制。每日可操作字数上限为80000字。您可以在工作台右下角实时看到自己今日的剩余字数',
          position: 'bottom-right',
          showClose: true,
          duration: 7000,
        })
        localStorage.setItem('wordLimitConfirmed', '1')
      }
    },

    //查询默认企业 若为空 则没有企业 0失败 1成功且有值 2成功无值
    async findDefaultCompany() {
      let res = await getDefaultCompany()
      if (res.code === 200) {
        let data = res.data
        this.defaultCompany = res.data || {}
        //false | true
        this.canSaveToBusiness =
          data &&
          data.cooperationEndTime &&
          new Date(data.cooperationEndTime).getTime() > new Date().getTime()
        return res.data ? 1 : 2
      } else {
        let msg = res.message ? res.message : '获取默认企业失败'
        this.$message.error(msg)
        return 0
      }
    },

    async showPublishModal() {
      //是否登录
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return
      }
      //获取默认企业
      let company = this.defaultCompany
      if (!company.id) {
        //请求默认企业
        let uid = this.$route.query.uid
        if (!uid) {
          this.$message.warning('请保存后发布')
          return
        }
        let flag = await this.findDefaultCompany()
        if (flag === 0) {
          return
        }
        if (flag === 2) {
          this.$message.warning('暂无默认企业')
          return
        }
      }
      this.isShowPublishModal = true
    },

    exportClick() {
      //是否登录
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return
      }
      //判断是否是已经保存的文章
      let uid = this.$route.query.uid
      if (!uid) {
        this.$message.warning('请先保存文章')
        return
      }
      //判断是否是vip
      if (!this.judgeShowPurchaseModal(13)) {
        return
      }
      this.isShowExportNoteDropdown = !this.isShowExportNoteDropdown
    },

    //收藏段落
    async collectSelected() {
      //是否登录
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return
      }
      //是否是vip
      if (!this.judgeShowPurchaseModal(10)) {
        return
      }
      setTimeout(() => {
        this.isSuspendedFrame = false
      }, 10) //傻狗异步 ^^
      if (!this.lastRewriteColumnContent) {
        return
      }
      let data = {
        content_text: this.lastRewriteColumnContent,
        author: this.userinfo.nickName,
      }
      let res = await collectSelectedText(data)
      if (res.code === 200) {
        this.$message.success('文本收藏成功')
      } else {
        this.$message.error('文本收藏失败')
      }
    },

    // 获取广告
    async fetchAdVertisement() {
      const imgKey1 = 'EDITOR_AD_1'
      const linkKey1 = 'EDITOR_AD_1_LINK'
      const resImg = await POSTcommunityMakeMoney(imgKey1)
      this.advertisement1Img = resImg
      const resLink = await POSTcommunityMakeMoney(linkKey1)
      this.advertisement1Link = resLink
    },

    //点击空白处导入文章下拉消失
    addDocumentEventListener() {
      documentClick.addClickListener('laboratoryDiv', () => {
        this.isShowLaboratoryDropdown = false
      })
    },

    //实验室 扩写按钮
    extendWriteLaboratory() {
      this.isShowLaboratoryDropdown = false
      this.rewriteOrExtendType = 2
      this.isRewriteColumnShow = true
      this.isExtendColumnShowMal = true
    },
    //加载改写扩写
    loadRewriteOrExtendByType(type) {
      this.$refs['rewriteOrExtend'].loadByType(type)
      setTimeout(() => {
        this.isShowRewriteExtendTool = false
      }, 10)
    },
    //编辑器按回车
    editorEnter() {
      setTimeout(() => {
        this.saveNoteData()
      }, 100)
    },
    previewRouterInit() {
      let pr
      if (lastRouter && lastRouter.path) {
        pr = lastRouter
      } else if (document.referrer.indexOf(location.origin) > -1) {
        const preUrl = document.referrer.replace(location.origin, '')
        if (preUrl.indexOf('/compose') > -1) {
          pr = { path: '/?flag=welcome' }
        } else {
          pr = { path: '/?flag=welcome' }
        }
      } else {
        pr = { path: '/?flag=welcome' }
      }
      this.previewRouter = pr
    },
    // 帮助列表点击事件
    // helpClick(item) {
    //   // console.log('help click', item)
    //   switch (item) {
    //     case '产品介绍':
    //       this.isShowVideo = true
    //       break
    //     case '使用教程':
    //       this.toggleShowVideoTutor()
    //       break
    //     // case '用户反馈':
    //     //   window.open('https://support.qq.com/products/66013')
    //     //   break;
    //     case '联系客服':
    //       this.isShowQrcodeServer = true
    //       break
    //     case '免责声明':
    //       this.protocolDialog.show = true
    //       break
    //   }
    // },
    //导入文章回调 flag: 1:文件 2:链接
    importArticleCallback(flag, article) {
      try {
        //如果是文件则取body中的内容
        let content = this.editor.content
        let content_html = article.content
        if (flag === 1) {
          content_html = this.fetchBodyContent(article)
        }
        let title = article.title || ''
        content_html = content_html || ''
        content_html = this.handleFormat(content_html)
        let { tmpId, div } = this.joinLocationElement()
        this.editor.content = content + div + title + content_html
        this.findMediumElementAndScrollSmoothly(tmpId)
      } catch (err) {
        console.log('err::', err)
      }
    },
    //拼接占位div
    joinLocationElement(tmp) {
      let tmpId = 'np_' + new Date().getTime()
      let div = `<div style="display: none" id="${tmpId}"></div>`
      return { tmpId, div }
    },
    //处理内容格式
    handleFormat(content_html) {
      content_html = content_html
        .replace(/<\/div>/g, '</span>')
        .replace(/<\/ul>/g, '</span>')
        .replace(/<\/li>/g, '</span>')
        .replace(/(\r\n|\n|\r)/gm, '<br />')
      content_html = content_html
        .replace(/style\s*?=\s*?(['"])[\s\S]*?\1/g, '')
        .replace(/class\s*?=\s*?(['"])[\s\S]*?\1/g, '')
      return content_html
    },
    //得到元素中間元素
    findMediumElementAndScrollSmoothly(tmpId) {
      setTimeout(() => {
        let ele = document.querySelector(`#${tmpId}`)
        ele = this.findMediumElement(ele)
        scrollIntoViewSmoothly(ele, false, true)
      }, 300)
    },
    //得到第三個元素
    findMediumElement(ele) {
      let tmp = ele
      let num = 0
      let preElement
      while (tmp) {
        preElement = tmp
        tmp = tmp.nextElementSibling
        if (num === 3) {
          break
        }
        num++
      }
      return preElement
    },

    //如果是文件则取body中的内容
    fetchBodyContent(article) {
      let content = article.content
      let index = content.indexOf('<body>')
      if (index !== -1) {
        index += 6
        let endIndex = content.indexOf('</body>')
        content = content.substring(index, endIndex)
      }
      return content
    },
    //通知质量检测当前状态
    notifyDetectionStatus(status) {
      this.isLookDetectionReport = false
      this.curDetectionStatus = status
    },
    detectAgain() {
      this.isDetectionModalShow = false
      setTimeout(() => {
        this.isDetectionModalShow = true
      }, 300)
    },
    toFolder() {
      if (this.isLogin) {
        this.$router.push({
          path: '/folderV2',
        })
      } else {
        this.$store.dispatch('user/SHOW_LOGIN')
      }
    },
    routeFullPathData() {
      this.routeFullPathDatas = this.$route.fullPath
      this.$store.dispatch(
        'ga/click',
        `TAGCommonNewArticle::${this.routeFullPathDatas}`
      )
    },
    // highlight(e) {
    //   e.forEach((i, index) => {
    //     this.editor.content = this.editor.content.replace(new RegExp(i, 'g'), `<span style="background-color: #FAEAE1;" data-mce-style="background-color: #FAEAE1;">${i}</span>`)
    //   })
    // },
    async highlight(e) {
      e.forEach((i, index) => {
        this.editor.content = this.editor.content.replace(
          new RegExp(i, 'g'),
          `<span style="background-color: #FAEAE1;" data-mce-style="background-color: #FAEAE1;">${i}</span>`
        )
      })
      await this.higthPromise(e)
      this.$refs.MyEditorChild.clickChildren()
      this.$message({
        type: 'error',
        iconClass: 'none',
        center: true,
        showClose: true,
        customClass: 'getui-toast-box',
        dangerouslyUseHTMLString: true,
        message: `<span style="color: #4A25BA;font-size: 16px;font-weight: bolder;">高相似度内容在文章标注中成功</span>
        <span style="font-size: 16px;"></span>`,
      })
    },
    markContent(res) {
      //纠错标注
      //this.markCorrect(res.correct)
      //标注相似度
      this.markSimiliarContent(res.similiar)
      //标注风险词
      this.markRiskContent(res.risk)
      //提示
      this.markHint(res)
    },
    //纠错标注
    markCorrect(data) {
      if (!data) {
        return
      }
      //清理上次的纠错结果
      if (this.$refs.MyEditorChild) {
        this.$refs.MyEditorChild.numberOfNodes()
      }
      this.$refs.MyEditorChild.editorContent(data.data.sign)
    },
    //标注提示
    markHint(res) {
      let msg1 = res.similiar ? '高相似度内容' : ''
      let msg2 = res.risk ? '风险词' : ''
      let msg
      if (msg1 && msg2) {
        msg = msg1 + '和' + msg2
      } else if (msg1 && !msg2) {
        msg = msg1
      } else {
        msg = msg2
      }
      if (!msg) {
        return
      }
      msg = msg + '在文中标注成功'
      this.$message.success(msg)
    },
    //标注相似内容
    markSimiliarContent(similiar) {
      if (!similiar) {
        return
      }
      let content = this.editor.content
      similiar.forEach((i, index) => {
        //console.log(i)
        content = content.replace(
          new RegExp(i, 'g'),
          `<span style="background-color: #FAEAE1;" data-mce-style="background-color: #FAEAE1;">${i}</span>`
        )
      })
      this.editor.content = content
      //this.$refs.MyEditorChild.clickChildren()
    },
    //标注风险词
    markRiskContent(risk) {
      if (!risk) {
        return
      }
      let content = this.editor.content
      risk.forEach((i, index) => {
        content = content.replace(
          new RegExp(i, 'g'),
          `<span style="background-color: #f67c7c;" data-mce-style="background-color: #FAEAE1;">${i}</span>`
        )
      })
      this.editor.content = content
    },
    onRewriteColumnReplace(content) {
      if (this.tinyEditor && this.tinyEditor.insertContent) {
        this.tinyEditor.insertContent(content)
      }
    },
    async onSummaryColumnReplace(content) {
      // 采用智能摘要文字  blockquote标签定义为摘要文字
      let regExp = /<blockquote id="summary">(([\s\S])*?)<\/blockquote>/
      if (regExp.test(this.editor.content)) {
        this.editor.content = this.editor.content.replace(
          regExp,
          '<blockquote id="summary"><h3>摘要</h3><p>' +
            content +
            '</p></blockquote>'
        )
      } else {
        this.editor.content =
          '<blockquote id="summary"><h3>摘要</h3><p>' +
          content +
          '</p></blockquote>' +
          this.editor.content
      }
      // 插入完成关闭摘要框并滚动到摘要部分
      this.isSummaryColumnShow = false
      this.isSummaryColumnShowPlugin = false
      const editorElem = await this.getEditorContainer()
      const sectionNode = editorElem.getElementsByTagName('blockquote')
      scrollIntoViewSmoothly(sectionNode[0])
    },
    // 标题隐藏，需要隐藏magicTitle
    onTitleHidden(bool) {
      if (bool) {
        // to hide
        this.hidenMagicTitleColumn()
      }
    },
    //框选替换
    selectionReplace(oldContent, newContent) {
      try {
        let content = this.editor.content
        this.editor.content = content.replace(oldContent, newContent)
      } catch (e) {
        console.log(e)
      }
    },
    // 移入
    linkageSuspension(content) {
      let spanContent = `<span class="span-content">${content}</span>`
      this.selectionReplace(content, spanContent)
    },
    // 移出
    cancelLinkage(content) {
      let spanContent = `<span class="span-content">${content}</span>`
      this.selectionReplace(spanContent, content)
    },
    isShowTool(selectedContent) {
      //如果选择的是代码块则不显示工具栏
      let reg = /<pre.*>(?:(\s*)[^\s]+)<\/pre>/gi
      let res = selectedContent.match(reg)
      return !res
    },
    onEditorRangeChange(selectedContent, rangeRect) {
      //如果选择的是代码块则不显示工具栏
      if (!this.isShowTool(selectedContent)) {
        return
      }
      //console.log('选中2', selectedContent)
      // console.log('文本正文', this.editor.content)
      let useRect = false
      if (rangeRect && rangeRect.width) {
        useRect = true
      }
      this.lastRewriteColumnContent = selectedContent
      // 如果没有选中内容，不执行改写
      // 选中了空白处的情况排除
      if (
        /^<span class="to-be-replace"/.test(selectedContent) &&
        /<\/span>$/.test(selectedContent)
      )
        return
      if (
        selectedContent
          .replace(/<.+?>/g, '')
          .replace(/&nbsp;/g, '')
          .replace(/\s+/g, '') === ''
      )
        return
      selectedContent.replace(/<.+?>/g, '')
      this.rewriteColumnContent = selectedContent
      if (!this.isRewriteColumnShow && !this.isExpendColumnShow) {
        setTimeout(() => {
          // 判断如果选中的是a标签，则不触发小框
          if (!this.clickATab) {
            this.toolbarPosUseRect = useRect
            this.isSuspendedFrame = true
            // absolute
            // useRect.absolute
            // 需要把 toolbar 放到 editor中
            // 顶部居中 展示工具栏
            if (useRect) {
              // const frameWidth = window.getComputedStyle(this.$refs.ELEMsuspendedFrame, null)['width'];
              let leftPosition =
                rangeRect.absolute.left +
                rangeRect.width / 2 +
                rangeRect.leftAddOn +
                20
              const frameLeft = leftPosition < 160 ? 160 : leftPosition
              const pointerLeft = 190 //156 leftPosition < 160 ? rangeRect.width / 2 + rangeRect.leftAddOn + 30 : 190

              this.$refs.ELEMsuspendedFrame.style.left = frameLeft + 'px'
              this.$refs.ELEMsuspendedFramePointer.style.left =
                pointerLeft + 'px'

              this.$refs.ELEMsuspendedFrame.style.top =
                rangeRect.absolute.top + 220 + 'px'
            } else {
              this.$refs.ELEMsuspendedFrame.style.left = this.x + 20 + 'px'
              this.$refs.ELEMsuspendedFrame.style.top = this.y + 20 + 'px'
            }
          }
        }, 0)
      } else {
        //改写扩写弹出条
        setTimeout(() => {
          // 判断如果选中的是a标签，则不触发小框
          if (!this.clickATab) {
            this.toolbarPosUseRect = useRect
            this.isShowRewriteExtendTool = true
            if (useRect) {
              let leftPosition =
                rangeRect.absolute.left +
                rangeRect.width / 2 +
                rangeRect.leftAddOn +
                20
              const frameLeft = leftPosition < 160 ? 160 : leftPosition
              const pointerLeft =
                leftPosition < 160
                  ? rangeRect.width / 2 + rangeRect.leftAddOn + 30
                  : this.rewriteOrExtendType === 1
                  ? 50
                  : 35
              this.$refs.ELEMsuspendedFrameRewrite.style.left = frameLeft + 'px'
              this.$refs.ELEMsuspendedFramePointerRewrite.style.left =
                pointerLeft + 'px'

              this.$refs.ELEMsuspendedFrameRewrite.style.top =
                rangeRect.absolute.top + 220 + 'px'
            } else {
              this.$refs.ELEMsuspendedFrameRewrite.style.left =
                this.x + 20 + 'px'
              this.$refs.ELEMsuspendedFrameRewrite.style.top =
                this.y + 20 + 'px'
            }
          }
        }, 0)
      }
      // console.log('useRect', rangeRect)
      // if (this.lastRewriteColumnContent !== selectedContent) {
      //   this.lastRewriteColumnContent = selectedContent
      //   // 如果没有选中内容，不执行改写
      //   // 选中了空白处的情况排除
      //   if (/^<span class="to-be-replace"/.test(selectedContent) && /<\/span>$/.test(selectedContent)) return
      //   if (selectedContent.replace(/<.+?>/g, '').replace(/&nbsp;/g, '').replace(/\s+/g, '') === '') return
      //   this.rewriteColumnContent = selectedContent
      //   if (!this.isRewriteColumnShow && !this.isExpendColumnShow) {
      //     setTimeout(() => {
      //       // 判断如果选中的是a标签，则不触发小框
      //       if (!this.clickATab) {
      //         this.toolbarPosUseRect = useRect
      //         this.isSuspendedFrame = true
      //         // absolute
      //         // useRect.absolute
      //         // 需要把 toolbar 放到 editor中
      //         // 顶部居中 展示工具栏
      //         if (useRect) {
      //           this.$refs.ELEMsuspendedFrame.style.left = (rangeRect.absolute.left + rangeRect.width / 2 + rangeRect.leftAddOn + 20) + "px"
      //           this.$refs.ELEMsuspendedFrame.style.top = rangeRect.absolute.top + 220 + "px"
      //         } else {
      //           this.$refs.ELEMsuspendedFrame.style.left = this.x + 20 + "px"
      //           this.$refs.ELEMsuspendedFrame.style.top = this.y + 20 + "px"
      //         }
      //       }
      //     }, 0)
      //   }
      // }
    },
    onEditorActions(payload) {
      const { content } = payload
      this.rewriteColumnContent = content
      // 展开改写栏
      this.showRewriteColumn()
    },
    goback() {
      // const preURL = this.lastPathURLBeforeEnter === '/' ? '/' : this.lastPathURLBeforeEnter
      // this.$router.push({
      //   path: preURL,
      // })
      // console.log(lastRouter)
      // if (lastRouter && lastRouter.path) {
      //   this.$router.push(lastRouter)
      //   return
      // }
      let preUrl = '/'

      console.log(document.referrer.indexOf(location.origin))
      if (document.referrer.indexOf(location.origin) > -1) {
        preUrl = document.referrer.replace(location.origin, '')
        if (preUrl.indexOf('/compose') > -1) {
          preUrl = '/'
        }
      } else if (lastRouter && lastRouter.path) {
        this.$router.push(lastRouter)
        return
      }
      // console.log(preUrl)

      // console.log(this.$router.history)
      // if (this.$router.history && )
      // console.log('document.referrer', document.referrer)
      // console.log('location.origin', location.origin)
      // console.log('preUrl', document.referrer)
      this.$router.push({
        path: preUrl,
      })
      // this.$router.go(-1)
    },

    appendImgNew(url) {
      this.appendParagraphToContent({
        content: `<img src="${url}" data-src="${url}">`,
      })
    },

    onEditorAddImage(imgId, isLocation, imgUrl) {
      try {
        if (isLocation) {
          let uploadIcon =
            'https://static.getgetai.com/base/pc-nuxt/assets/img/default/uploading_icon.jpg'
          let selection = window.getSelection()
          let range = selection.getRangeAt(0)
          let fragment = range.createContextualFragment(
            `<div><img alt="智能写作" id="${imgId}" src="${uploadIcon}"></div>`
          )
          range.insertNode(fragment.lastChild)
          selection.removeAllRanges()
        } else {
          let img = document.getElementById(imgId)
          if (!img) {
            return
          }
          img.setAttribute('src', imgUrl)
        }
      } catch (e) {
        console.log(e)
      }
      // 解决粘贴图片会粘贴两张的问题解决粘贴图片会粘贴两张的问题
      // this.appendParagraphToContent(
      //   {
      //     content: `<img src="${imgUrl}" data-src="${imgUrl}">`
      //   }
      // )
      // this.appendReferenceToContent()
    },
    onEditorAddPreviewImage(img) {
      if (img.done) {
        //替换正在上传
        this.isAutoSaveOn_Img = true
        let reg = new RegExp(
          '<div([\\s\\S]*)data-loading="true" data-id="' +
            img.id +
            '"([\\s\\S]*)>([\\s\\S]*)</div>'
        ) // eslint-disable-line
        this.editor.content = this.editor.content.replace(
          reg,
          `<p><img src="${img.src}" data-src="${img.src}"></p>`
        )
      } else {
        //插入正在上传
        this.isAutoSaveOn_Img = false
        this.appendParagraphToContent({
          content: `<div data-loading="true" data-id="${img.id}" class="pic-uploading-box"><img src="${img.src}" data-src="${img.src}"><div class='uploading' contenteditable="false">图片上传中...</div></div>`,
          isImg: true,
        })
      }
    },
    triggerEditorSave() {
      if (this.contentEditor) {
        this.contentEditor.triggerSave()
      }
    },
    // 初始化用户
    initUser() {
      let userString = localStorage.getItem('user')
      if (typeof userString === 'string') {
        try {
          this.user = JSON.parse(userString)
        } catch (err) {
          console.error(err)
        }
      }
    },
    //根据hFlag获取对应信息
    getRelativeInfo(query) {
      let hFlag = query['hFlag']
      let course = courseJson[hFlag]
      let id
      let code
      if (course) {
        id = course.id
        code = course.code
      }
      let title = courseJson.course.title
      let content = courseJson.course.content
      return {
        id,
        code,
        title,
        content,
      }
    },
    //判断是否第一次操作该功能
    isFirstVisit(code) {
      let res = localStorage.getItem(`${code}_${this.userinfo.uid}`)
      if (res) {
        return false
      }
      localStorage.setItem(`${code}_${this.userinfo.uid}`, '1')
      return true
    },
    fillCourseNoteToEditor(query) {
      let result = this.getRelativeInfo(query)
      //如果
      if (!result.id || (this.isLogin && !this.isFirstVisit(result.code))) {
        return
      }
      let title = query.keyword || query.km || query.ks
      this.fillEditor(result, title)
    },
    //编辑器显示教程文章
    fillEditor(result, sourceTitle) {
      setTimeout(() => {
        // if (!sourceTitle) {
        //   this.editor.title = result.title
        // }
        this.editor.title = result.title
        this.editor.content = result.content
        this.locationAssignCourse(result)
      }, 1000)
    },
    //定位到当前功能教程
    locationAssignCourse(result) {
      try {
        setTimeout(() => {
          let course = document.querySelector('#' + result.id)
          if (course) {
            let next = course.previousElementSibling.previousElementSibling
            if (!next) return
            next.scrollIntoView({
              behavior: 'smooth',
              block: 'start',
              inline: 'start',
            })
          }
        }, 800)
      } catch (e) {
        console.log(e)
      }
    },
    // 搜索词过滤
    searchContentFilter(searchContent) {
      let searchWord
      let searchHotListName
      if (searchContent && /#(.*)#/.test(searchContent)) {
        const content = searchContent.match(/#(.*)#/)[1]
        const splitIndex = content.indexOf(' ')
        if (splitIndex > -1) {
          searchWord = content.slice(splitIndex + 1)
          searchHotListName = content.substring(0, splitIndex)
        } else {
          searchWord = content
        }
      } else {
        searchWord = searchContent
      }
      return {
        searchWord,
        searchHotListName,
      }
    },
    // 初始化文章和关键词
    async initArticleAndKeyword(addType) {
      let uid = this.uid || this.$route.query.uid
      let folderid = this.$route.query.folderid || 0
      this.editor.classifyId = folderid
      // 空文章
      if (!uid) {
        this.noteContentLoaded = true
        // init
        // 创作该主题
        const query = { ...this.$route.query }
        const composeType = query['compose-type']
        const activateFunc = query['activate-func']
        let titleTags = query['tags'] || ''
        const composeKeyword = query.keyword || query.km || query.ks
        // 清空 内容 标题 dom
        // 根据不同模式初始化各类参数
        switch (composeType) {
          case 'add-keyword':
            // 处理tags
            if (titleTags) {
              titleTags = decodeURIComponent(titleTags)
                .split(',')
                .map(newKeywordDict)
            } else {
              titleTags = []
            }
            this.currentKeyword = composeKeyword
            this.editor.title = this.searchContentFilter(
              composeKeyword
            ).searchWord
            break
          case 'search-keyword':
            // 弹出搜索主题的框
            query.title && this.selectTopicAndSearch(query.title)
            break
          // 普通模板
          case 'template':
            // 模板初始化，默认关闭左边栏
            this.isLeftShrink = false
            this.initTemplateData(query['tmpid'])
            break
          // 智能模板
          case 'template-intel':
            try {
              let hasChance = await POSTisAuthHasChance(7)
              if (!hasChance) {
                return this.$message({
                  type: 'error',
                  message:
                    '免费版用户每月可使用10次模板成稿功能，你本月剩余使用机会已使用完毕',
                })
              }
              const templateId = query['tmpid']
              const principleId = query['principleid']
              const keyword = query.keyword || query.km || query.ks
              const h1List = decodeURIComponent(query.plist)
                .split(',')
                .map(i => i.trim())

              // 模板初始化，默认关闭左边栏
              this.isLeftShrink = true

              await this.initTemplateIntel(templateId, {
                principleId,
                title: keyword,
                h1List,
              })
            } catch (err) {
              console.error(err)
              this.$message({
                type: 'error',
                message: err.message,
              })
            }
            break
        }
        this.initHint(activateFunc)
      } else {
        // 获取文章详情，并开启自动保存
        try {
          await this.goGetNoteDetail(uid, folderid)
          //分享预览-草稿内容和发布内容是否一致
          this.checkShareData()
          // 开启自动保存
          this.openAutoSave()
        } catch (err) {
          console.error(err)
          // 错误信息
          this.$message({
            message: err.message,
            type: 'error',
          })
          // 没有获取到文章的后续处理
          // this.whenNoArticle()
        }
      }
      //根据参数填充对应功能教程文章
      let query = this.$route.query
      this.fillCourseNoteToEditor(query)
    },
    // 初始化文章和关键词
    async appendContentByTemplate(templateParams) {
      let uid = this.uid
      this.editor.classifyId = this.$route.query.folderid || 0
      let folderid = this.$route.query.folderid || 0

      // 空文章
      if (!uid) {
        this.noteContentLoaded = true
        // init
      } else {
        // 获取文章详情，并开启自动保存
        try {
          await this.goGetNoteDetail(uid, folderid)
          // 开启自动保存
          this.openAutoSave()
        } catch (err) {
          console.error(err)
          // 错误信息
          this.$message({
            message: err.message,
            type: 'error',
          })
          // 没有获取到文章的后续处理
          // this.whenNoArticle()
        }
      }

      // 创作该主题
      // const query = this.$route.query
      const composeType = templateParams['compose-type']
      const activateFunc = templateParams['activate-func']
      let titleTags = templateParams['tags'] || ''
      const composeKeyword = templateParams.keyword

      // TODO
      if (composeType === 'add-keyword') {
        // 处理tags
        if (titleTags) {
          titleTags = decodeURIComponent(titleTags)
            .split(',')
            .map(newKeywordDict)
        } else {
          titleTags = []
        }
        this.currentKeyword = composeKeyword
      }

      // 进入主动显示添加主题框
      if (composeType === 'search-keyword') {
        // 弹出搜索主题的框
        let title = this.$route.query.title
        if (title) {
          this.selectTopicAndSearch(title)
        }
      }

      if (composeType === 'template') {
        const templateId = templateParams['tmpid']

        // 模板初始化，默认关闭左边栏
        this.isLeftShrink = true

        this.initTemplateData(templateId)
      }

      // 智能写作模板
      if (composeType === 'template-intel') {
        try {
          let hasChance = await POSTisAuthHasChance(7)
          if (!hasChance) {
            return this.$message({
              type: 'error',
              message:
                '免费版用户每月可使用10次模板成稿功能，你本月剩余使用机会已使用完毕',
            })
          }
          const templateId = templateParams['tmpid']
          const principleId = templateParams['principleid']
          const keyword = templateParams.keyword
          const h1List = decodeURIComponent(templateParams.plist)
            .split(',')
            .map(i => i.trim())

          // 模板初始化，默认关闭左边栏
          this.isLeftShrink = true

          this.initTemplateIntel(templateId, {
            principleId,
            title: keyword,
            h1List,
          })
        } catch (err) {
          console.error(err)
          this.$message({
            type: 'error',
            message: err.message,
          })
        }
      }

      this.initHint(activateFunc)
      if (!this.editor.title && !this.uid) this.editor.title = composeKeyword
    },

    // 获取添加内容
    async generateNewNote(templateParams) {
      let uid = this.uid
      let folderid =
        this.$route.query.folderid || this.noteRemote.classifyId || 0
      this.editor.classifyId = folderid
      // const note = { ...this.editor }

      // 空文章
      if (!uid) {
        this.noteContentLoaded = true
        // init
      } else {
        // 获取文章详情，并开启自动保存
        try {
          await this.goGetNoteDetail(uid, folderid)

          // 开启自动保存
          this.openAutoSave()
        } catch (err) {
          console.error(err)
          // 错误信息
          this.$message({
            message: err.message,
            type: 'error',
          })
          // 没有获取到文章的后续处理
          // this.whenNoArticle()
        }
      }
      try {
        const templateId = templateParams['tmpid']
        const principleId = templateParams['principleid']
        const keyword = templateParams.keyword
        const h1List = decodeURIComponent(templateParams.plist)
          .split(',')
          .map(i => i.trim())

        // 模板初始化，默认关闭左边栏
        this.isLeftShrink = true
        // 获取模板生成内容
        const contentNew = await this.getIntelligentTemplateNote(templateId, {
          principleId,
          title: keyword,
          h1List,
        })

        // // 笔记
        // const noteToUpdate = {
        //   uid: uid,
        //   title: (note.title || templateParams.keyword).substr(0, 30),
        //   content: clearIllegalBeforeSave(contentNew),
        //   // 文章摘要信息，用于非富文本展示使用
        //   brief: parseHtmlToText(contentNew),
        //   classifyId: folderid
        // }

        // const res = await this.updateNote(noteToUpdate)
        // if (res) {
        //   this.saving = false
        // }

        this.editor.content = contentNew
        await this.saveNoteData(false)
      } catch (err) {
        console.error(err)
        this.$message({
          type: 'error',
          message: err.message,
        })
      }
    },

    async updateNote(noteToUpdate) {
      // 编辑笔记
      const res = await NoteApi.EditNoteLocal(noteToUpdate)
      // console.log('showSuccessMessage', res.code, showSuccessMessage)
      if (res.code === 200) {
        this.uid = res.data.uid || this.$route.query.uid
        this.editor.uid = this.uid
        this.noteRemote = res.data
        // this.noteRemote.isSubmit = this.alreadySent
        // this.updateRoute(res.data)
        this.saving = false
        return true
      } else {
        this.$message({
          message: (res.message.length > 10 ? '请求异常' : res.message) || res,
          type: 'error',
        })
        this.saving = false
        return false
      }
    },
    // 显示工具栏提示
    initHint(activateFunc, delay = 1000) {
      if (!activateFunc) return
      if (delay) {
        this.isLeftShrink = true
      }
      let operate = {
        // 智能成稿
        'intel-article': () => {
          this.toggleShowGenerateColumn()
        },
        // 标题推荐
        'title-recommand': () => {
          // let HintOfTitleRecommand = localStorage.getItem('HintOfTitleRecommand')
          // let timeoutCount = 1
          // clearTimeout(timeoutCount)
          // timeoutCount = setTimeout(() => { this.isTitleRecommandHintShow = true }, delay)
          // localStorage.setItem('HintOfTitleRecommand', 1)
          this.toggleAhaTitle()
        },
        // 智能改写
        'intel-rewrite': () => {
          // let HintOfIntelRewrite = localStorage.getItem('HintOfIntelRewrite')
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isIntelRewriteHintShow = true
          }, delay)
          // localStorage.setItem('HintOfIntelRewrite', 1)
        },
        // 智能扩写
        'intel-expend': () => {
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isIntelExpendHintShow = true
          }, delay)
        },
        // 智能摘要
        'intel-summary': () => {
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isIntelSummaryHintShow = true
          }, delay)
        },
        // 智能纠错
        'error-correction': () => {
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isTitleRecommandHintShow = true
          }, delay)
        },
        // 质量检测
        'quality-detect': () => {
          // let HintOfQualityDetect = localStorage.getItem('HintOfQualityDetect')
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isQualityDetectHintShow = true
          }, delay)
          // localStorage.setItem('HintOfQualityDetect', 1)
        },
        // 分享预览
        share: () => {
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isIntelShareHintShow = true
          }, delay)
        },
        submission: () => {
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isSubmissionHintShow = true
          }, delay)
        },
        // 全网发文
        publish: () => {
          // let HintOfPublish = localStorage.getItem('HintOfPublish')
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isPublishHintShow = true
          }, delay)
          // localStorage.setItem('HintOfPublish', 1)
        },
        // 一键复制
        copy: () => {
          // let HintOfPublish = localStorage.getItem('HintOfPublish')
          let timeoutCount = 1
          clearTimeout(timeoutCount)
          timeoutCount = setTimeout(() => {
            this.isCopyHintShow = true
          }, delay)
          // localStorage.setItem('HintOfPublish', 1)
        },
      }
      operate[activateFunc] && operate[activateFunc]()
    },
    initEventListeners() {
      const that = this
      /**
       * 事件监听
       */
      // 监听 ctrl-s ==> 保存
      // 监听 esc ==> 停止更改 keycode === 27
      document.onkeydown = async function(e) {
        let keyCode = e.keyCode || e.which || e.charCode
        let ctrlKey = e.ctrlKey || e.metaKey
        // console.log('按键监听', ctrlKey, keyCode)
        // ctrl-s
        if (ctrlKey && keyCode === 83) {
          // console.log('save ctrl s', that.noteContentLoaded && that.isAutoSaveOn)
          if (that.noteContentLoaded && that.isAutoSaveOn) {
            if (this.isRewriteWorking) {
              return this.$message({
                type: 'error',
                message: '改写过程中，不能保存。',
              })
            }
            await that.manualSave()
          } else {
            console.log('prevent to save on CTRL-S')
          }

          e.preventDefault()
          return false
        }
        return true
      }

      // 页面退出前，保存当前编辑内容
      window.addEventListener('beforeunload', ev => {
        return true
      })

      // EVENT: resize
      let delayCounter = 0
      if (!listenerOnResize) {
        listenerOnResize = () => {
          this.windowInnerWidth = window.innerWidth

          clearTimeout(delayCounter)

          delayCounter = setTimeout(() => {
            this.GetEditorMaxHeight()
          }, 400)

          // 悬浮框位置
        }
        window.onresize = listenerOnResize
      }
    },

    initToolboxTips() {
      // setHiddenShowFastGenTip
      if (isShowFastGenTip()) {
        this.showToolboxFastGenTipManual = true
      }
    },
    // 修正菜单的事件
    onErrorCorrectEvent(row) {
      this.correctBox.show = false
      const target = this.correctBox.target
      this.correctBox.target = null
      this.correctBox.btnList = []

      if (row.type === 'ignore') {
        // 忽略
        target.classList = ''
        target.dataset.id = ''
        target.dataset.correct = ''
      } else {
        const currectWord = target.dataset.correct
        target.innerText = currectWord
        target.classList = ''
        target.dataset.id = ''
        target.dataset.correct = ''
      }

      const elem = getEditorElem()
      this.editor.content = elem.innerHTML
    },
    // 全局修改菜单
    addErrorCorrect() {
      const elem = getEditorElem()
      if (!elem) return

      if (!_wordCorrectMouseoverFn) {
        _wordCorrectMouseoverFn = onWrongWordMouseover(
          this,
          (target, event) => {
            const correctWord = target.dataset.correct
            this.correctBox.show = true
            this.correctBox.target = target

            const dict = target.getBoundingClientRect()
            this.correctBox.style = {
              top: dict.bottom + 'px',
              left: dict.left + dict.width / 2 + 'px',
            }
            this.correctBox.btnList = [
              {
                title: correctWord,
                type: 'correct-word',
              },
              {
                title: '忽略',
                type: 'ignore',
              },
            ]
          },
          otherElem => {
            this.correctBox.target = null
            this.correctBox.show = false
            this.correctBox.btnList = []
          }
        )
      }

      // 只绑定一次
      elem.removeEventListener('mouseover', _wordCorrectMouseoverFn)
      elem.addEventListener('mouseover', _wordCorrectMouseoverFn)
    },
    magicSelectTitle(title) {
      this.editor.title = title
      this.hidenMagicTitleColumn()
    },

    toggleShowRewriteColumn(empty) {
      if (!this.judgeShowPurchaseModal(2)) {
        return
      }
      //消失工具栏
      setTimeout(() => {
        this.isSuspendedFrame = false
      }, 10)
      // 展开/关闭智能改写 empty这个变量为是否清空选中内容 为true不清空
      if (!this.isRewriteFuncActivate) {
        return this.isEditorRightZoomEnough
          ? this.initHint('intel-rewrite', 0)
          : this.$message({
              type: 'error',
              message: '正文没有内容',
            })
      }
      clearTimeout(_rewriteColumnShowCounter)
      if (this.isRewriteColumnShowModal) {
        _rewriteColumnShowCounter = setTimeout(() => {
          this.hidenRewriteColumn()
        }, 300)
      } else {
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
          return this.$message({
            type: 'warning',
            message: '登录后可体验此功能',
          })
        }
        // 关闭左边栏
        this.isLeftShrink = true
        _rewriteColumnShowCounter = setTimeout(() => {
          this.showRewriteColumn(empty)
        }, 300)
      }
      //初始化为改写tab
      this.rewriteOrExtendType = 1
    },
    toggleShowExpendColumn(empty) {
      // 展开/关闭智能扩写 empty这个变量为是否清空选中内容 为true不清空
      if (!this.isRewriteFuncActivate) {
        return this.isEditorRightZoomEnough
          ? this.initHint('intel-expend', 0)
          : this.$message({
              type: 'error',
              message: '正文没有内容',
            })
      }
      clearTimeout(_rewriteColumnShowCounter)
      if (this.isExpendColumnShowModal) {
        _rewriteColumnShowCounter = setTimeout(() => {
          this.hidenExpendColumn()
        }, 300)
      } else {
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
          return this.$message({
            type: 'warning',
            message: '登录后可体验此功能',
          })
        }
        // 关闭左边栏
        this.isLeftShrink = true
        _rewriteColumnShowCounter = setTimeout(() => {
          this.showExpendColumn(empty)
        }, 300)
      }
    },
    toggleSummary() {
      if (!this.judgeShowPurchaseModal(3)) {
        return
      }
      if (!this.moreThanTwoHundredWords) {
        return this.isEditorRightZoomEnough
          ? this.initHint('intel-summary', 0)
          : this.$message({
              type: 'error',
              message: '智能摘要需要填写正文200字以上',
            })
      }
      this.isSummaryColumnShow = true
      this.isSummaryColumnShowPlugin = true
    },
    toggleShowGenerateColumn() {
      clearTimeout(_generateColumnShowCounter)

      if (this.isGenerateColumnShowModal) {
        _generateColumnShowCounter = setTimeout(() => {
          this.hidenGenerateColumn()
        }, 300)
      } else {
        // 关闭左边栏
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
          return this.$message({
            type: 'warning',
            message: '登录后可体验此功能',
          })
        }
        this.isLeftShrink = false

        _generateColumnShowCounter = setTimeout(() => {
          this.showGenerateColumn()
        }, 300)
      }
    },
    // 智能纠错
    toggleCorrection() {
      // 关闭左边栏
      let htmlContent
      if (this.$refs.MyEditorChild) {
        htmlContent = this.$refs.MyEditorChild.getEditorContent()
        this.domContent = this.$refs.MyEditorChild.getEditorContentText()
      }
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      } else if (!htmlContent) {
        return this.isEditorRightZoomEnough
          ? this.initHint('error-correction', 0)
          : this.$message({
              type: 'error',
              message: '正文不能为空',
            })
      }
      if (this.$refs.MyEditorChild) {
        this.$refs.MyEditorChild.resetTypos()
      }
    },
    toggleShowDetectionColumn() {
      if (!this.isDetectionModalShow) {
        if (!this.isTitleFuncActivate) {
          return this.isEditorRightZoomEnough
            ? this.initHint('quality-detect', 0)
            : this.$message({
                type: 'error',
                message: '标题内容不能为空',
              })
        }
        if (!this.editor.content.length) {
          return this.isEditorRightZoomEnough
            ? this.initHint('quality-detect', 0)
            : this.$message({
                type: 'error',
                message: '正文不能为空',
              })
        }
      }
      if (!this.isLogin && !this.isDetectionModalShow) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      }
      //点击质量检测按钮关闭完成提示icon
      if (this.curDetectionStatus === 2) {
        this.isLookDetectionReport = true
      }
      this.isDetectionModalShow = !this.isDetectionModalShow
      let htmlContent
      if (this.$refs.MyEditorChild) {
        htmlContent = this.$refs.MyEditorChild.getEditorContent()
      }
      this.deleteCtion.content = htmlContent
      if (this.isDetectionModalShow) {
        this.hidenRewriteColumn()
        this.hidenMagicTitleColumn()
        this.hidenPublishColumn()
        this.hidenGenerateColumn()
        this.$store.dispatch(
          'ga/click',
          `TAGEditorArticleQualityClick::${this.editor.uid}`
        )
      }
    },
    async toggleShowShareColumn() {
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      }
      //
      if (!this.judgeShowPurchaseModal(14)) {
        return
      }
      if (!this.isTitleFuncActivate) {
        // return this.isEditorRightZoomEnough ? this.initHint('share', 0) : this.$message({
        //   type: 'error',
        //   message: '标题和正文不能为空'
        // })
        return this.$message({
          type: 'warning',
          message: '对不起您的文章没有标题，请完善标题后再分享',
        })
      }
      if (!this.editor.content.length) {
        return this.$message({
          type: 'warning',
          message: '对不起您的文章没有内容，请创作内容后再分享',
        })
      }
      //检查当前文章是否和镜像数据一致，若不一致则提示更新
      let flag = await this.checkShareData()
      if (flag === 2) {
        this.shareUpdateModalShow()
      } else {
        this.goSharePage()
      }
    },
    //去分享页
    goSharePage() {
      let query = { ...this.$route.query }
      sessionStorage.setItem('SHARING_PARAMETERS', JSON.stringify(query))
      this.$router.push({
        path: '/share/' + this.editor.uid,
      })
    },
    //检查当前文章是否和镜像数据一致，若不一致则提示更新
    //1: 一致|未发布 2：不一致 3：出现异常
    checkShareData() {
      let flag = 3
      let data = this.noteRemote
      flag = !data.formalContent || data.content === data.formalContent ? 1 : 2
      //设置全局字段
      this.isShowUpdateShare = flag === 2
      return flag
    },
    //modal框提示
    shareUpdateModalShow() {
      this.$confirm('分享预览的内容不是最新的，是否更新？', '', {
        confirmButtonText: '更新',
        cancelButtonText: '暂不',
        center: true,
      })
        .then(async () => {
          if (!(await this.shareUpdateConfirm())) return
          this.goSharePage()
        })
        .catch(() => {
          this.goSharePage()
        })
    },
    //敏感词检测
    sensitiveWordCheck(article) {
      return new Promise(async (resolve, reject) => {
        //let title = await sensitiveWordCheckBaidu(article.title)
        let content = await sensitiveWordCheckBaidu(
          article.title + ' ' + article.content
        )
        if (content.code !== 200) {
          let msg =
            content.res.error_code === 282131 ? '文章内容过长' : '检测失败'
          resolve({ code: 400, message: msg })
          return
        }
        let words = []
        //this.readSensitiveWords(title, words)
        this.readSensitiveWords(content, words)
        resolve({ code: 200, data: words })
      })
    },
    //遍历敏感词
    readSensitiveWords(result, words) {
      result.res.sort((i1, i2) => {
        return parseFloat(i2) - parseFloat(i1)
      })
      result.res[0].words.forEach(item => {
        if (words.indexOf(item) === -1) {
          words.push(item)
        }
      })
    },
    async sensitiveWordDetection() {
      let article = {
        title: this.editor.title,
        content: this.editor.content
          .replace(/<[^>]+>/g, '\n')
          .replace(/&lt;/g, '<')
          .replace(/&gt;/g, '>')
          .replace(/&amp;/g, '&')
          .trim(),
        uid: this.editor.uid,
      }
      const resData = await this.sensitiveWordCheck(article)
      if (resData.code !== 200) {
        this.$message({
          type: 'error',
          message: resData.message,
        })
        return false
      }
      if (resData.data.length) {
        this.$message({
          type: 'error',
          message: `本文存在敏感词 ，为避免法律风险，请使用质量检测修改内容【${resData.data.join(
            '，'
          )}】`,
        })
        return false
      }
      return true
    },
    //分享更新提示框-确认操作
    async shareUpdateConfirm() {
      //更新之前检测是否存在敏感词
      if (!(await this.sensitiveWordDetection())) return false
      let res = await copyToFormal(this.editor.uid)
      if (res && res.code === 200) {
        this.$message.success('镜像数据更新成功')
      } else {
        this.$message.error('镜像数据更新失败')
      }
      return true
    },
    // 点击提稿赚钱提示
    promptSubmission() {
      if (!this.editor.content && !this.editor.title) {
        return this.$message({
          type: 'warning',
          message: '空文章不能投稿',
        })
      }
      if (!this.editor.title) {
        return this.$message({
          type: 'warning',
          message: '标题为空不能投稿',
        })
      }
      if (!this.editor.content) {
        return this.$message({
          type: 'warning',
          message: '正文为空不能投稿',
        })
      }
    },
    toggleShowSubmissionColumn() {
      if (!this.isDetectionModalShow) {
        this.promptSubmission()
        if (this.alreadySent) {
          return this.$message({
            type: 'error',
            message: '文章已经投稿，请点击导航栏的“+”重新写一篇',
          })
        }
        if (!this.isTitleFuncActivate) {
          return this.isEditorRightZoomEnough
            ? this.initHint('submission', 0)
            : this.$message({
                type: 'error',
                message: '标题内容不能为空',
              })
        }
        if (!this.editor.content.length) {
          return this.isEditorRightZoomEnough
            ? this.initHint('submission', 0)
            : this.$message({
                type: 'error',
                message: '正文不能为空',
              })
        }
      }
      this.$store.dispatch(
        'ga/click',
        `TAGEditorContributeMoneyClick::投稿赚钱::${this.uid ||
          ''}::点击投稿赚钱`
      )
      if (!this.isLogin && !this.isDetectionModalShow) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      }
      this.isSubmissionColumnShow = true
    },
    toggleShowPublishColumn() {
      if (!this.isPublishColumnShowModal) {
        if (!this.isPublishFuncActivate) {
          return this.isEditorRightZoomEnough
            ? this.initHint('publish', 0)
            : this.$message({
                type: 'error',
                message: '标题内容不能少于五个字',
              })
        }
        if (!this.editor.content.length) {
          return this.isEditorRightZoomEnough
            ? this.initHint('publish', 0)
            : this.$message({
                type: 'error',
                message: '正文不能为空',
              })
        }
      }
      clearTimeout(_publishColumnShowCounter)
      if (this.isPublishColumnShowModal) {
        _publishColumnShowCounter = setTimeout(() => {
          this.hidenPublishColumn()
        }, 300)
      } else {
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
          return this.$message({
            type: 'warning',
            message: '登录后可体验此功能',
          })
        }
        // 关闭左边栏
        this.isLeftShrink = true

        _publishColumnShowCounter = setTimeout(() => {
          this.showPublishColumn()
        }, 300)
      }
    },

    // tutorvideo show
    toggleShowVideoTutor() {
      this.isTutorVideoShow = !this.isTutorVideoShow
      this.isLeftShrink = true
    },

    showPublishColumn() {
      this.hidenMagicTitleColumn()
      this.hidenRewriteColumn()
      this.hidenGenerateColumn()
      this.isDetectionModalShow = false

      this.isPublishColumnShowModal = true

      clearTimeout(_publishColumnShowCounter)
      _publishColumnShowCounter = setTimeout(() => {
        this.isPublishColumnShow = true
      }, 300)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticlePublishClick::${this.editor.uid}`
      )
    },
    hidenPublishColumn() {
      this.isPublishColumnShow = ''
      clearTimeout(_publishColumnShowCounter)
      _publishColumnShowCounter = setTimeout(() => {
        this.isPublishColumnShowModal = ''
        // 清空改写内容
      }, 300)
    },
    showRewriteColumn(empty) {
      // 打开改写框
      this.hidenMagicTitleColumn()
      this.hidenPublishColumn()
      this.hidenGenerateColumn()
      this.hidenExpendColumn(empty)
      this.isDetectionModalShow = false
      this.isRewriteColumnShowModal = true
      clearTimeout(_rewriteColumnShowCounter)
      _rewriteColumnShowCounter = setTimeout(() => {
        this.isRewriteColumnShow = true
      }, 300)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticleRewriteClick::${this.editor.uid}`
      )
    },
    showExpendColumn(empty) {
      // 打开扩写框
      this.hidenMagicTitleColumn()
      this.hidenPublishColumn()
      this.hidenGenerateColumn()
      this.hidenRewriteColumn(empty)
      this.isDetectionModalShow = false
      this.isExpendColumnShowModal = true
      clearTimeout(_rewriteColumnShowCounter)
      _rewriteColumnShowCounter = setTimeout(() => {
        this.isExpendColumnShow = true
      }, 300)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticleRewriteClick::${this.editor.uid}`
      )
    },
    showGenerateColumn() {
      this.hidenMagicTitleColumn()
      this.hidenPublishColumn()
      this.hidenRewriteColumn()
      this.isDetectionModalShow = false
      // this.isGenerateColumnShowModal = true

      clearTimeout(_generateColumnShowCounter)
      _generateColumnShowCounter = setTimeout(() => {
        this.isGenerateColumnShow = true
      }, 300)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticleGenerateClick::${this.editor.uid}`
      )
    },
    hidenGenerateColumn() {
      this.isGenerateColumnShow = ''
      clearTimeout(_generateColumnShowCounter)
      _generateColumnShowCounter = setTimeout(() => {
        this.isGenerateColumnShowModal = ''
        // 清空改写内容
      }, 300)
    },
    hidenRewriteColumn(empty = false) {
      // 关闭改写框 empty为是否清空选中内容
      this.isRewriteColumnShow = false
      this.isExpendColumnShowModal = false
      this.isRewriteColumnShow = false
      setTimeout(() => {
        this.isRewriteColumnShowModal = false
        this.isExtendColumnShowMal = false
      }, 300)
      this.isExpendColumnShow = false
      if (!empty) {
        this.rewriteColumnContent = ''
      }
    },
    hidenExpendColumn(empty) {
      // 关闭扩写框
      this.isRewriteColumnShow = false
      this.isExpendColumnShowModal = false
      this.isRewriteColumnShowModal = false
      this.isExpendColumnShow = false
      if (!empty) {
        this.rewriteColumnContent = ''
      }
    },
    hidenSummaryColumn() {
      this.isSummaryColumnShow = false
      clearTimeout(_summaryColumnShowCounter)
      _summaryColumnShowCounter = setTimeout(() => {
        this.isSummaryColumnShowPlugin = false
      }, 300)
    },
    hidenSubmissionColumn() {
      // 关闭弹窗保存数据拿到返回
      this.isSubmissionColumnShow = false
      let uid = this.uid || this.editor.uid
      NoteApi.GetNoteDetail(uid).then(res => {
        if (res && res.code === 200 && res.data.isSubmit)
          this.alreadySent = true
      })
    },
    hidenMagicTitleColumn() {
      this.isAhaTitleColumnShow = false
      clearTimeout(_magicTitleColumnShowCounter)
      _magicTitleColumnShowCounter = setTimeout(() => {
        this.isAhaTitleColumnShowModal = false
      }, 300)
    },

    showAhaTitle(title) {
      this.ahaTitle = title

      this.hidenRewriteColumn()
      this.hidenPublishColumn()
      this.hidenGenerateColumn()
      this.hidenExpendColumn()
      this.isDetectionModalShow = false

      this.isAhaTitleColumnShowModal = true

      clearTimeout(_magicTitleColumnShowCounter)
      _magicTitleColumnShowCounter = setTimeout(() => {
        this.isAhaTitleColumnShow = true
      }, 300)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticleTitleClick::${this.editor.uid}`
      )
    },
    toggleAhaTitle() {
      if (!this.judgeShowPurchaseModal(1)) {
        return
      }
      if (this.isAhaTitleColumnShowModal) {
        // 已经打开，应该关闭
        this.hidenMagicTitleColumn()
      } else {
        // if (!this.isTitleFuncActivate) {
        //   return this.$message({
        //     type: 'error',
        //     message: '标题内容不能为空'
        //   })
        // }
        if (!this.isLogin) {
          this.$store.dispatch('user/SHOW_LOGIN')
          return this.$message({
            type: 'warning',
            message: '登录后可体验此功能',
          })
        }
        this.showAhaTitle(this.editor.title)
      }
    },
    previewerExport(content, source, opt = {}) {
      const article = this.previewArticle

      const data = {
        articleId: article.id,
        content,
        isImg: content.indexOf('<img') === 0,
        _item: article,
      }

      this.$store.dispatch('ga/dict', {
        tag: 'TAGEditorPreviewArticleQuote',
        keywords: article.id,
        desc: `${article.title},${content}`,
      })
      let defaultOpt = { noCopyRight: true }
      opt = { ...defaultOpt, ...opt }
      this.appendParagraphToContent(data, true, opt)
      this.appendReferenceToContent(source)
    },
    // 开始提取内容
    beginSelectParagraph(articleItem) {
      if (articleItem === null) articleItem = this.previewArticle

      this.initSelectParagraphArticle = articleItem
      this.isSelectParagraphShow = true
    },
    onCommitAddParagraph(paragraphList, source) {
      this.isSelectParagraphShow = false
      // 确认选取的内容
      paragraphList.forEach(item => {
        this.appendParagraphToContent(
          {
            ...item,
            _item: item,
          },
          true
        )
      })

      this.appendReferenceToContent(source)
    },
    // 编辑器初始化函数
    editorInitCommonFn(editor) {
      this.addErrorCorrect()
      this.addEditorContentClickListeners()
      this.addEditorContentSelectionChange()

      window.addRowTitle = this.addSearchDOM.bind(this)
      delayFor(this.addSearchDOM, 1000)

      this.tinyEditor = editor
    },
    // 处理用户拖动内容
    async addEditorContentSelectionChange() {
      // const that = this
      if (this.editorElem) return
      let editorElem = await this.getEditorContainer()
      this.editorElem = editorElem
      editorElem.onselectionchange = ev => {}
      editorElem.addEventListener('selectionchange', ev => {})
    },
    getEditorContainer() {
      let interval = 1
      return new Promise(resolve => {
        let editorElem = document.querySelector('#editor-container')
        if (!editorElem) {
          interval = setInterval(() => {
            editorElem = document.querySelector('#editor-container')
            if (editorElem) {
              clearInterval(interval)
              resolve(editorElem)
            }
          }, 100)
        } else {
          resolve(editorElem)
        }
      })
    },
    async addEditorContentClickListeners() {
      const that = this
      if (this.editorElem) return

      const editorElem = await this.getEditorContainer()
      this.editorElem = editorElem
      if (editorElem) {
        editorElem.addEventListener('click', ev => {
          // that.addBulbListener(ev, editorElem)
          that.addZoomScopeListener(ev, editorElem)
          that.addSelectionTracker(ev, editorElem)

          that.handleHintTextReplacement(editorElem)

          // 处理占位文本
          const eventPath = Array.from(ev.path || [])
          const target = eventPath.find(
            i => i.classList && i.classList.contains('gw-row-title-placeholder')
          )
          if (target) {
            that.handleRowTitlePlceholderText(target, editorElem)
          }
        })
      }
    },
    handleRowTitlePlceholderText(target, editor) {
      if (target) {
        const n = document.createElement('span')
        // 空格
        n.innerHTML = '&nbsp;'
        target.replaceWith(n)
      }
    },
    handleHintTextReplacement(editor, tagText = 'span[data-mce-selected="1"]') {
      // 替换占位文本
      const selectedElem = editor.querySelector(tagText)
      if (!selectedElem) return

      try {
        const select = document.getSelection()
        const newRange = document.createRange()
        let focusElem = null

        if (selectedElem && selectedElem.previousElementSibling) {
          focusElem = selectedElem.previousElementSibling
        } else {
          const span = document.createElement('span')
          span.innerHTML = '&nbsp;'
          selectedElem.parentElement.insertBefore(span, selectedElem)
          focusElem = span
        }

        newRange.selectNode(focusElem)
        select.addRange(newRange)
        focusElem.focus()
        select.selectAllChildren(selectedElem.parentElement)

        // 主动删除占位
        selectedElem.parentElement.removeChild(selectedElem)
      } catch (err) {
        console.log(err)
      }
    },
    // 对灯泡加点击事件监听
    addBulbListener(ev, editorElem) {
      // const that = this
      const target = ev.target

      if (target.classList.contains('bulb')) {
        if (target.classList.contains('handling')) return

        // isBulb
        const parent = target.parentNode
        if (this.isRewriteWorking) {
          return this.$message({
            type: 'warning',
            message: '每次只能改写一个段落，请等待当前改写。',
          })
        }
        parent.classList.add('handling')
        if (parent.classList.contains('g-bulb')) {
          target.classList.add('handling')
          // 滚动到屏幕
          scrollIntoViewSmoothly(parent)

          this.rewriteWorking = {
            elemBulb: target,
            item: parent,
          }

          this.isRewriteWorking = true

          // RewriteParagraph(parent.innerText).then(text => {
          RewriteParagraphV2_2(parent.innerText)
            .then(text => {
              // const elemBulb = document.querySelector('.bulb')

              if (text) {
                // parent.innerText = text
                // 使用富文本替换
                parent.innerHTML = text.replace(/<br><\/br>/g, '<br/><br/>')
                parent.classList.remove('handling')
                parent.classList.add('done')

                parent.classList.remove('g-bulb')
                parent.classList.remove('done')
                parent.dataset['gac'] = 'g-done'

                this.isRewriteWorking = false
                this.editor.content = editorElem.innerHTML

                // this.$nextTick(() => {
                //   this.addBulbDOM('recover')
                // })

                // if (!elemBulb) {
                //   that.shouldArticleRewriteEnabled = false
                // } else {
                //   that.shouldArticleRewriteEnabled = false
                // }
              }
            })
            .catch(err => {
              // this.$message
              console.log(err)
              parent.classList.remove('handling')
              parent.classList.add('done')

              parent.classList.remove('g-bulb')
              parent.classList.remove('done')
              parent.dataset['gac'] = 'g-done'

              this.isRewriteWorking = false
            })
        }
      }
    },
    addZoomScopeListener(ev, editorElem) {
      const target = ev.target
      try {
        const parentNode = target.parentNode

        if (
          parentNode.classList.contains('g-search') &&
          target.classList.contains('search')
        ) {
          // this.$refs.columnLeft.changeColumnLeftMode('Meterial')
          EventBus.$emit('addZoomScopeListener')
          // this.onAddKeywordAndSelect(parentNode.textContent || parentNode.innerText)
          // this.$refs.columnLeft.templateSearch(parentNode.textContent || parentNode.innerText)
          // this.leftOpen(parentNode.textContent || parentNode.innerText, 'Meterial')
          let query = { ...this.$route.query }
          query.km = parentNode.textContent || parentNode.innerText
          query.mode = 'Meterial'
          // console.log('add zoom scope listener', query)
          this.$router.push({
            path: this.$route.path,
            query: query,
          })
          if (this.isLeftShrink) {
            this.isLeftShrink = false
          }
        }
      } catch (err) {
        console.log(err)
      }
    },
    addSelectionTracker(ev, editorElem) {
      const target = ev.target
      // 点击的是a标签
      if (target.tagName === 'A') {
        this.clickATab = true
        const href = target.href
        if (this.lastRewriteColumnContent === '') {
          if (this.href === href) {
            window.open(href)
          }
          this.href = href
        }
        // this.$Utils.simulateAClick(href)
        // EventBus.$emit('previewArticleByUrl', {
        //   href,
        //   content: target.textContent || target.innerWidth,
        // })
      } else {
        this.clickATab = false
      }
      if (target.tagName === 'ARTICLE') {
        this.clickATab = true
      }

      // 元素被点击
      try {
        const selection = document.getSelection()
        const range = selection.getRangeAt(0)
        let elem = range.endContainer || range.commonAncestorContainer
        let lastElem = elem
        while (elem && elem.id !== 'editor-container') {
          lastElem = elem
          elem = elem.parentNode
        }
        if (elem && elem.id === 'editor-container') {
          // 寻找editor最外层的p元素 或者 h1元素
          if (
            !range.collapsed &&
            range.endOffset === 0 &&
            range.startOffset === 0
          ) {
            this.lastClickElem =
              lastElem.nextElementSibling.previousSibling || null
          } else {
            this.lastClickElem = lastElem.nextElementSibling || null
          }
        }
      } catch (err) {
        console.log(err)
      }
    },
    // 初始化theme，主题词
    initArticleTheme(themeText) {
      let list = themeText.split(',')
      this.currentKeyword = this.$route.query['keyword'] || list[0]
    },
    // 检测网络状态

    async initTemplateData(templateId) {
      // const data = await GETpoint(templateId)
      const res = await GETpoint({
        pageNum: 1,
        pageSize: 100,
        t: {
          id: templateId,
        },
      })
      const data = res.creativeTemplate
      this.editor.content = data.content
      // 打开范文
      const sourceUrl = data.originalLink
      try {
        EventBus.$emit('previewArticleByUrl', {
          sourceUrl,
          title: data.title,
          defaultShrink: true,
        })
      } catch (e) {}
      //为a标签绑定点击事件
      this.bindClickForA(data)
    },
    //普通模板 为a绑定click
    bindClickForA(data, timer) {
      try {
        timer = timer || 1500
        setTimeout(() => {
          let html = document.querySelector('#editor-container')
          let aa = html.getElementsByTagName('a')
          for (let i = 0; i < aa.length; i++) {
            let ee = aa[i]
            let sourceUrl = ee.href
            console.log('sourceUrl::', sourceUrl)
            ee.addEventListener('click', e => {
              e.preventDefault()
              EventBus.$emit('previewArticleByUrl', {
                sourceUrl,
                title: data.title,
                defaultShrink: false,
                callback: flag => {
                  if (flag === 'fail') {
                    window.open(sourceUrl, '_blank')
                  }
                },
              })
              return false
            })
          }
        }, timer)
      } catch (e) {
        console.log(e)
      }
    },
    // 智能模板初始化 DEBUG
    async initTemplateIntel(templateId, opts = {}) {
      let editorText = this.editor.content
      const intelTempData = await GETpoint({
        pageNum: 1,
        pageSize: 100,
        t: {
          id: templateId,
        },
      })
      // console.log('intelTempData', intelTempData)
      intelTempData.list = intelTempData.list.filter(
        i => i.id === opts.principleId
      )

      this.editor.title = this.editor.title || opts.title
      const h1List = opts.h1List.map(keyword => {
        return {
          keyword: keyword,
          content: '',
          articleId: '',
          processing: 0,
          isProcessing: false,
          noticeText: '你可以自己写一些相关的内容',
        }
      })
      this.isIntelgenProcessShow = true
      this.intelgenDict = {
        title: opts.title,
        list: h1List,
        articleId: '',
        isProcessing: true,
        processing: 0,
      }

      const h1ListMax = h1List.length

      // 计算总进度
      const countTotalProcessing = (itemIndex, itemProcessing) => {
        const pieceRatio = 100 / h1ListMax
        return parseInt(
          pieceRatio * itemIndex + (itemProcessing / 100) * pieceRatio
        )
      }
      let intervalCount = 0
      // 处理每一个大纲
      // async
      for (let i = 0; i < h1ListMax; i++) {
        const h1item = h1List[i]
        h1item.processing = 0
        h1item.isProcessing = true

        clearInterval(intervalCount)

        intervalCount = setInterval(() => {
          h1item.processing += parseInt((100 - h1item.processing) / 15) || 1
          this.intelgenDict.processing = countTotalProcessing(
            i,
            h1item.processing
          )
          if (h1item.processing >= 99) clearInterval(intervalCount)
        }, 100)

        // 替换XXX为关键词
        // noticeText为提示语
        // console.error('h1item.keyword', h1item.keyword, opts.title)

        const {
          content,
          articleId,
          noticeText,
        } = await this.intelgenHandlingItem(
          h1item.keyword,
          opts.title,
          intelTempData,
          h1List
        )

        // console.log('文章生成', h1item.keyword, opts.title, intelTempData, h1List,)

        clearInterval(intervalCount)
        h1item.content = content
        h1item.articleId = articleId
        h1item.noticeText = noticeText
        h1item.processing = 100
        h1item.isProcessing = false

        this.intelgenDict.list = h1List
      }

      // 更新总进度
      this.intelgenDict.isProcessing = false
      this.intelgenDict.processing = 100

      // 所有内容完成
      let editorContent = h1List
        .map(item => {
          const replacedTitle = item.keyword.replace('XXX', opts.title)

          // 无匹配内容
          if (!item.content) {
            // 替换为占位符内容
            return `<h1>${replacedTitle}</h1>
            <p>
              <span class="to-be-replace" contenteditable="false" style="color: #9E9E9E;">${item.noticeText ||
                ''}</span>
            </p>`
          }

          // 有匹配内容
          const gid = `${item.articleId} - `
          const splitedContent = item.content
            .split(/<br><\/?br>/)
            .map(text => {
              return `<p data-gid="${gid}">${text}</p>`
            })
            .join('')

          return `<h1>${replacedTitle}</h1>
          ${splitedContent}
          <p><span class="to-be-replace" contenteditable="false" style="color: #9E9E9E;">${item.noticeText ||
            ''}</span></p>`
        })
        .join('')
      this.isIntelgenProcessShow = false
      const contentFinal = editorText + editorContent
      // 搜索title，展开左侧
      // await this.onAddKeywordAndSelect(opts.title)
      this.isLeftShrink = false
      this.editor.content = contentFinal
      await this.saveNoteData(false, null, opts.title)
      POSTauthReduceChance(7)
      // await this.saveAsNewNote(false)
    },
    // 智能模板初始化 DEBUG
    async getIntelligentTemplateNote(templateId, opts = {}) {
      let editorText = this.editor.content
      const intelTempData = await GETpoint({
        pageNum: 1,
        pageSize: 100,
        t: {
          id: templateId,
        },
      })
      // console.log('intelTempData', intelTempData)
      intelTempData.list = intelTempData.list.filter(
        i => i.id === opts.principleId
      )

      this.editor.title = this.editor.title || opts.title
      const h1List = opts.h1List.map(keyword => {
        return {
          keyword: keyword,
          content: '',
          articleId: '',
          processing: 0,
          isProcessing: false,
          noticeText: '你可以自己写一些相关的内容',
        }
      })
      this.isIntelgenProcessShow = true
      this.intelgenDict = {
        title: opts.title,
        list: h1List,
        articleId: '',
        isProcessing: true,
        processing: 0,
      }

      const h1ListMax = h1List.length

      // 计算总进度
      const countTotalProcessing = (itemIndex, itemProcessing) => {
        const pieceRatio = 100 / h1ListMax
        return parseInt(
          pieceRatio * itemIndex + (itemProcessing / 100) * pieceRatio
        )
      }
      let intervalCount = 0
      // 处理每一个大纲
      // async
      for (let i = 0; i < h1ListMax; i++) {
        const h1item = h1List[i]
        h1item.processing = 0
        h1item.isProcessing = true

        clearInterval(intervalCount)

        intervalCount = setInterval(() => {
          h1item.processing += parseInt((100 - h1item.processing) / 15) || 1
          this.intelgenDict.processing = countTotalProcessing(
            i,
            h1item.processing
          )
          if (h1item.processing >= 99) clearInterval(intervalCount)
        }, 100)

        // 替换XXX为关键词
        // noticeText为提示语
        // console.error('h1item.keyword', h1item.keyword, opts.title)

        const {
          content,
          articleId,
          noticeText,
        } = await this.intelgenHandlingItem(
          h1item.keyword,
          opts.title,
          intelTempData,
          h1List
        )

        // console.log('文章生成', h1item.keyword, opts.title, intelTempData, h1List,)

        clearInterval(intervalCount)
        h1item.content = content
        h1item.articleId = articleId
        h1item.noticeText = noticeText
        h1item.processing = 100
        h1item.isProcessing = false

        this.intelgenDict.list = h1List
      }

      // 更新总进度
      this.intelgenDict.isProcessing = false
      this.intelgenDict.processing = 100

      // 所有内容完成
      let editorContent = h1List
        .map(item => {
          const replacedTitle = item.keyword.replace('XXX', opts.title)

          // 无匹配内容
          if (!item.content) {
            // 替换为占位符内容
            return `<h1>${replacedTitle}</h1>
            <p>
              <span class="to-be-replace" contenteditable="false" style="color: #9E9E9E;">${item.noticeText ||
                ''}</span>
            </p>`
          }

          // 有匹配内容
          const gid = `${item.articleId} - `
          const splitedContent = item.content
            .split(/<br><\/?br>/)
            .map(text => {
              return `<p data-gid="${gid}">${text}</p>`
            })
            .join('')

          return `<h1>${replacedTitle}</h1>
          ${splitedContent}
          <p><span class="to-be-replace" contenteditable="false" style="color: #9E9E9E;">${item.noticeText ||
            ''}</span></p>`
        })
        .join('')
      this.isIntelgenProcessShow = false
      const contentFinal = editorText + editorContent
      // 搜索title，展开左侧
      // await this.onAddKeywordAndSelect(opts.title)
      this.isLeftShrink = false
      POSTauthReduceChance(7)
      return contentFinal
      // await this.saveNoteData(false, null, opts.title)
      // await this.saveAsNewNote(false)
    },
    // 获取智能改写文章
    async intelgenHandlingItem(
      title,
      keyword,
      intelTempDetail = {},
      siblingList = []
    ) {
      try {
        // { category: '领域，不传则搜全领域', entities: 'XXX变量，与question相同‘, tags: '限定搜索范围的标签'}
        // const principleList = intelTempDetail.structure.templates[0]
        const principleList = intelTempDetail.creativeTemplate
        const principleItem =
          intelTempDetail.list[0].paragraphTitles.find(
            i => i.title === title
          ) || {}

        const replacedTitle = title.replace('XXX', keyword)
        const data = await fetchIntelTemplateParagraph(replacedTitle, 1, {
          features: principleItem.tagWord || '',
          content_keywords: keyword || '',
          category: principleList.fieldLabel || '',
          entities: keyword || '',
          type: principleItem.source === 'baike' ? 'baike' : 'writer',
        })
        let sections = data.sections
        sections = sections.filter(item => {
          const articleids = siblingList
            .filter(item => item.processing === 100)
            .map(i => i.articleId)

          return articleids.indexOf(item.id) === -1
        })
        // 去除兄弟已经使用的ID
        if (sections.length <= 0) {
          // throw new Error('搜索不到结果')
          throw new Error(
            'PRESET-' +
              (principleItem.promptCopy || '你可以自己写一些相关的内容')
          )
        } else {
          // 只返回第一个条的内容
          return {
            content: sections[0].content_text,
            articleId: sections[0].id || '',
            // 替代文本
            noticeText:
              principleItem.promptCopy || '你可以自己写一些相关的内容',
          }
        }
        // 选出一项，并返回
      } catch (err) {
        console.log(err)
        const msg = err.message
        // this.$message({
        //   type: 'warning',
        //   message: '网络出现问题，请尝试刷新'
        // })
        if (!msg) {
          return ''
        }

        return {
          noticeText:
            msg.indexOf('PRESET-') === 0 ? msg.replace('PRESET-', '') : '',
        }
      }
    },
    selectTopicAndSearch(item, mode = '') {
      if (item) {
        const title = item.title || item
        this.initSearchTopic = title
      }

      this.openAddTopicModal(mode)
    },
    // 添加元素
    openAddResourceModal() {
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      }
      this.isAddResourceModalShow = true
    },
    closeAddResourceModal() {
      this.isAddResourceModalShow = false
    },
    //关闭编辑器上导入文章对话框
    closeAddResourceModalForEditor() {
      this.isAddResourceModalShowForEditor = false
    },
    addResourceLoadingElem(articleUrl) {
      this.closeAddResourceModal()
      // 埋点
      this.$store.dispatch(
        'ga/param3',
        `TAGEditorAddResourceClick::${articleUrl}::${this.currentKeyword}`
      )
      EventBus.$emit('addResourceLoadingElem', articleUrl)
    },
    loadArticleResource(e) {
      // console.log(e)
      this.closeAddResourceModal()
      //EventBus.$emit('handleLoadArticleResource', e)
      //处理导入文章的方法
      this.$refs.columnLeft.backToTopClick(e)
    },
    // 添加主题
    openAddTopicModal(mode) {
      this.isAddTopicModalShow = true
      this.addTopicMode = mode
    },
    closeAddTopicModal() {
      this.initSearchTopic = ''
      this.isAddTopicModalShow = false
    },
    onAddKeywordAndSelect(keyword) {
      // if (typeof keyword === 'string') {
      //   this.currentKeyword = keyword
      // } else {
      //   this.currentKeyword = keyword.text
      // }
      // if (!this.editor.title) this.editor.title = keyword
      // 关键词更改，保存数据 [静默保存]
      // await this.saveNoteData(false)
    },
    async onAddTopic(data) {
      const keyword = data.text
      if (keyword) {
        this.currentKeyword = keyword
        this.closeAddTopicModal()
      }

      // 关键词更改，保存数据 [静默保存]
      await this.saveNoteData(false)
    },

    leftColumnShrinkToggle() {
      if (!this.isLeftShrink) {
        this.isLeftShrink = true
      } else {
        // this.isLeftShrink = false
        this.leftOpen(this.editor.title, this.columnLeftMode)
      }
    },
    onSelectParagraphFromParagraph(item, whetherGolden) {
      let div = document.createElement('div')
      if (whetherGolden) {
        if (item.author || item.book_name) {
          div.innerHTML = `${item.content_text} —— ${item.author ||
            ''} ${item.book_name || ''}`
        } else {
          div.innerHTML = `${item.content_text}`
        }
      } else {
        div.innerHTML = item.content_text
      }
      this.appendParagraphToContent({
        content: div.innerText,
        // content: item.content_text,
        _item: item,
        articleId: item.article_id,
      })
      this.appendReferenceToContent(item)
    },
    onSelectParagraph(data, item) {
      this.appendParagraphToContent({
        ...data,
        _item: item,
        articleId: item.id,
      })
      this.appendReferenceToContent(item)
    },
    appendArticleToContent(htmlContent, source) {
      let div = document.createElement('div')
      const tagD = 't-' + Date.now()
      const htmlContentReplace = htmlContent.replace(
        /<h1>/,
        `<h1 class="${tagD}">`
      )
      div.innerHTML = htmlContentReplace
      this.appendParagraphToContent({
        content: div.innerHTML,
      })
      this.appendReferenceToContent(source)
    },
    /**
     * 添加元素到文章末尾
     * @param {Object} 文章段落
     * @param {Boolean} scollContentToScreen 滑动刚添加的内容到屏幕上
     */
    ifTopInsert(opt, htmlContent) {
      let which = opt.scrollWhich
      if (which === 1) {
        let tmp = '<p>&nbsp;</p>' //用于定位
        htmlContent = tmp + htmlContent
      }
      return htmlContent
    },
    async appendParagraphToContent(
      data,
      scollContentToScreen = true,
      opt = {}
    ) {
      // title内容用 p > strong
      // content用p

      // 2.0版本
      // g-id 段落id
      // g-tp 类型 content title
      // g-done 已经修改过
      // g-will 可以修改 新建才哟 g-will
      // 没有g-will直接不处理
      const editorElem = await this.getEditorContainer()
      let htmlContent = ''
      // 插入到光标后一段模式
      // 记住后一段，
      try {
        const gid = `${data.articleId} - ${data.index || ''}`
        let content = data.content
        const gac = 'g-will'
        const headerStr = data.title ? `<h1>${data.title}</></h1>` : ''
        // let match
        // let img
        let contentStr
        contentStr = `<p data-gid="${gid}" data-gac="${gac}">${content}</p>`
        htmlContent = headerStr + contentStr
      } catch (err) {
        console.log(err)
      }
      // let blockquote = document.getElementById('reference')
      // console.log('blockquote', blockquote)
      // 通过div预加载元素，通过遍历，将所有元素加到文章中
      try {
        const div = document.createElement('div')
        //判断是否需要滚动到插入元素顶部
        htmlContent = this.ifTopInsert(opt, htmlContent)
        div.innerHTML = htmlContent
        let lastChild = null
        let children = Array.from(div.children)
        let firstChild = children[0]
        children.forEach(i => {
          lastChild = i
          if (
            this.lastClickElem &&
            this.lastClickElem.parentNode === editorElem
          ) {
            // 最后一个是空段落则删除
            // if (new RegExp(/^<br(.*?)>$/).test(editorElem.lastChild.innerHTML) || (!editorElem.lastChild.innerHTML && editorElem.lastChild.tagName !== 'IMG')) {
            if (
              !editorElem.lastChild.innerHTML &&
              editorElem.lastChild.tagName !== 'IMG'
            ) {
              editorElem.removeChild(editorElem.lastChild)
            }
            try {
              if (i.tagName.toLowerCase() === 'img') {
                setTimeout(() => {
                  let editorContent = document.querySelector(
                    '#editor-container'
                  )
                  Array.from(editorContent.children).forEach(item => {
                    if (item.tagName.toLowerCase() === 'blockquote') {
                      editorElem.insertBefore(i, item)
                    }
                  })
                }, 1)
              } else {
                editorElem.insertBefore(i, this.lastClickElem)
              }
            } catch (e) {
              console.log(e)
              editorElem.appendChild(i)
            }
          } else {
            // 最后一个是空段落则删除
            // if (editorElem.lastChild.innerHTML === '<br data-mce-bogus="1">') {
            // if (new RegExp(/^<br(.*?)>$/).test(editorElem.lastChild.innerHTML) || (!editorElem.lastChild.innerHTML && editorElem.lastChild.tagName !== 'IMG')) {
            if (
              !editorElem.lastChild.innerHTML &&
              editorElem.lastChild.tagName !== 'IMG'
            ) {
              editorElem.removeChild(editorElem.lastChild)
            }
            if (i.tagName.toLowerCase() === 'img') {
              setTimeout(() => {
                let editorContent = document.querySelector('#editor-container')
                Array.from(editorContent.children).forEach(item => {
                  if (item.tagName.toLowerCase() === 'blockquote') {
                    editorElem.insertBefore(i, item)
                  }
                })
              }, 1)
            } else {
              editorElem.appendChild(i)
            }
          }
        })
        if (lastChild && scollContentToScreen) {
          this.$nextTick(async () => {
            this.scrollWhichByOpt(firstChild, lastChild, opt)
            this.editor.content = editorElem.innerHTML
            if (data.isImg) return
            //获取lastChild的索引
            let nodes = editorElem.childNodes
            let index = 0
            for (index = 0; index < nodes.length; index++) {
              if (nodes[index] === lastChild) {
                break
              }
            }
            if (data.isImg) return
            await this.uploadReplaceImg()
            setTimeout(() => {
              try {
                let editor = document.querySelector('#editor-container')
                let node = editor.childNodes[index]
                node.focus()
                let sel = window.getSelection()
                sel.selectAllChildren(node)
                sel.collapseToEnd()
              } catch (e) {
                console.log(e)
              }
            }, 500)
          })
        }
      } catch (err) {
        console.log(err)
      }
    },
    //滚动分句参数scrollWhicht 1滚动到第一个元素 2滚动到最后一个元素
    scrollWhichByOpt(firstChild, lastChild, opt) {
      let which = opt.scrollWhich
      switch (which) {
        case 1:
          scrollIntoViewSmoothly(firstChild, false, true)
          break
        default:
          scrollIntoViewSmoothly(lastChild, true, false)
      }
    },
    async appendContent(data, scollContentToScreen = true) {
      // title内容用 p > strong
      // content用p

      // 2.0版本
      // g-id 段落id
      // g-tp 类型 content title
      // g-done 已经修改过
      // g-will 可以修改 新建才哟 g-will
      // 没有g-will直接不处理

      const editorElem = await this.getEditorContainer()
      console.log('appendContent', data)
      // 通过div预加载元素，通过遍历，将所有元素加到文章中
      try {
        const div = document.createElement('div')
        div.innerHTML = data
        console.log('append div', div)
        console.log('append div', div.children)
        let lastChild = null
        Array.from(div.children).forEach(async i => {
          lastChild = i
          console.log('lastClick', this.lastClickElem, i)
          if (this.lastClickElem) {
            // 最后一个是空段落则删除
            try {
              editorElem.insertBefore(i, this.lastClickElem)
            } catch (e) {
              await this.appendEnd(i)
            }
          } else {
            await this.appendEnd(i)
          }
        })
        const imgDoms = editorElem.querySelectorAll('img')
        imgDoms.forEach(item => {
          let imgObj = new Image()
          imgObj.src = item.getAttribute('src')
          imgObj.onload = function() {
            item.setAttribute('width', imgObj.width)
            item.style.width = imgObj.width + 'px'
            item.setAttribute('height', imgObj.height)
            item.style.height = imgObj.height + 'px'
          }
        })

        if (lastChild && scollContentToScreen) {
          this.$nextTick(async () => {
            scrollIntoViewSmoothly(lastChild, true, false)
            this.editor.content = editorElem.innerHTML
            lastChild.focus()

            const selection = window.getSelection()
            if (selection.rangeCount > 0) selection.removeAllRanges()
            if (data.isImg) return
            try {
              const range = document.createRange()
              if (lastChild) range.selectNode(lastChild)
              selection.addRange(range)
            } catch (err) {
              console.log(err)
            }
            await this.uploadReplaceImg()
          })
        }
        const imgReg = /<img.*?src="(.*?)".*?\/?>/gi
        let arr = data.match(imgReg)
        if (arr) {
          this.uploadReplaceImg()
        }
      } catch (err) {
        console.log(err)
      }
    },
    // 内容最后添加标签
    async appendEnd(insertDom) {
      const editorElem = await this.getEditorContainer()
      let blockquote = document.querySelector('blockquote')
      if (blockquote) {
        editorElem.insertBefore(insertDom, blockquote)
      } else {
        editorElem.appendChild(insertDom)
      }
    },
    /**
     * 添加引用源到文章
     * @source
     *  - source_name
     *  - source_type
     *  - title
     *  - source_url
     */
    async appendReferenceToContent(source) {
      const editorElem = await this.getEditorContainer()
      const regExp = /<blockquote id="reference">(([\s\S])*?)<\/blockquote>/
      if (!source || !source.title || !source.source_url) {
        if (!regExp.test(this.editor.content)) return
        const articleNode = document.getElementById('reference')
        editorElem.appendChild(articleNode)
        return
      }
      if (regExp.test(this.editor.content)) {
        // 已经有article标签判断是否有重复a链接
        const articleNode = document.getElementById('reference')
        const articleChildP = articleNode.getElementsByTagName('p')
        articleChildP &&
          Array.from(articleChildP).forEach(i => {
            if (!source || i.children[0].href === source.source_url) {
              this.duplicateLink = true
              return false
            }
          })
        // 有重复直接return
        if (this.duplicateLink) {
          this.duplicateLink = false
          editorElem.appendChild(articleNode)
          return
        }
        const p = document.createElement('p')
        p.innerHTML = `<a href="${source.source_url}">${source.title &&
          source.title.replace(/<\/?.+?>/g, '')} ${source.source_url}</a>`
        articleNode.appendChild(p)
        editorElem.appendChild(articleNode)
      } else {
        // id为reference的blockquote标签定义为引用链接标签 走到这里说明还没有article标签需创建
        const section = document.createElement('blockquote')
        section.setAttribute('id', 'reference')
        section.innerHTML = `<h3>引用文献</h3><p><a href="${
          source.source_url
        }">${source.title && source.title.replace(/<\/?.+?>/g, '')} ${
          source.source_url
        }</a></p>`
        const wrap = document.createElement('p')
        wrap.innerHTML = '<br/>'
        editorElem.appendChild(wrap)
        editorElem.appendChild(section)
      }
    },
    addBulb(timeoutDuration = 500) {
      return new Promise((resolve, reject) => {
        clearTimeout(_addBulbCounter)
        _addBulbCounter = setTimeout(() => {
          this.addBulbDOM()

          resolve()
        }, timeoutDuration)
      })
    },
    addBulbDOM() {
      this.addSearchDOM()

      const editorElem = document.querySelector('#editor-container')
      if (!editorElem) return

      const elemList = Array.from(
        editorElem.querySelectorAll('p[data-gac="g-will"]')
      )

      elemList.forEach(elem => {
        if (elem.querySelector('span.bulb')) return
        if (!elem.textContent.trim()) return

        elem.classList.add('g-bulb')
        const bulb = document.createElement('span')
        bulb.setAttribute('contenteditable', 'false')
        bulb.classList.add('bulb')
        elem.appendChild(bulb)

        // this.shouldArticleRewriteEnabled = true
      })
    },
    // 增加搜索🔍到内容
    addSearchDOM(isRecover = '') {
      const editorElem = document.querySelector('#editor-container')
      if (!editorElem) return

      const elemList = Array.from(editorElem.querySelectorAll('h1,h2,h3'))

      elemList.forEach(elem => {
        // if (elem.querySelector('span.search')) return
        if (elem.querySelector('span.search')) {
          const searchSpans = elem.querySelectorAll('span.search')
          searchSpans.forEach(item => {
            elem.removeChild(item)
          })
        }

        elem.classList.add('g-search')
        elem.classList.add('gw-row-title')
        const bulb = document.createElement('span')
        bulb.setAttribute('contenteditable', 'false')
        bulb.classList.add('search')
        // bulb.title = "段落改写"
        elem.appendChild(bulb)
      })
    },
    //判断图片链接是否是本源
    isSelfImg(src) {
      if (!src) {
        return false
      }
      return (
        src.indexOf(
          'https://static.getgetai.com/base/pc-nuxt/assets/img/default/replace_icon.png'
        ) > -1 ||
        src.indexOf('https://static.getgetai.com/img') > -1 ||
        src.indexOf('https://static.getgetai.com/img_for_copy') > -1 ||
        src.indexOf('http://get-file.oss-cn-hangzhou.aliyuncs.com/') > -1 ||
        src.indexOf('img1.wanxiangzixun.net') > -1
      )
    },
    // 上传并替换所有图片
    uploadReplaceImg() {
      return new Promise(resolve => {
        // 获取所有图片
        const editorElem = document.querySelector('#editor-container')
        if (!editorElem) {
          resolve(true)
          return
        }
        //图片可能延迟加载
        setTimeout(async () => {
          let imgDataList = editorElem.getElementsByTagName('img')
          // console.log('imgDatalist::', imgDataList)
          if (!imgDataList) {
            resolve(true)
            return
          }
          //console.log('imgList::', imgDataList.length)
          for (let index = 0; index < imgDataList.length; index++) {
            let item = imgDataList[index]
            let originalImgSrc = item.getAttribute('alt')
            let imgSrc = item.getAttribute('src')
            let imgURL
            let uploadImgUrl
            // console.log('Get智能写作', originalImgSrc, imgSrc)
            if (!imgSrc) continue
            if (this.isSelfImg(imgSrc)) continue
            if (
              originalImgSrc === 'Get智能写作' &&
              imgSrc &&
              imgSrc.indexOf('blob') === -1
            )
              continue
            if (this.isSelfImg(originalImgSrc)) {
              imgURL = originalImgSrc
            } else if (originalImgSrc && originalImgSrc.indexOf('blob') === 0) {
              imgURL = await this.getBlobUploadedUrl(originalImgSrc)
            } else if (imgSrc && imgSrc.indexOf('blob') === 0) {
              // console.log('blob', originalImgSrc, imgSrc)
              imgURL = await this.getBlobUploadedUrl(imgSrc)
            } else if (imgSrc && imgSrc.indexOf('data:image') === 0) {
              imgURL = await this.getBase64UploadedUrl(imgSrc)
            } else if (
              originalImgSrc &&
              originalImgSrc.indexOf('data:image') === 0
            ) {
              imgURL = await this.getBase64UploadedUrl(originalImgSrc)
            } else if (
              imgSrc.indexOf('https://static.getgetai.com/base') > -1 &&
              originalImgSrc.indexOf('Get智能写作') === -1
            ) {
              let res = await POSTuploadReplaceImg(originalImgSrc)
              if (res.code === 200) {
                imgURL = res.data
              } else {
                uploadImgUrl = originalImgSrc
              }
            } else if (
              imgSrc.indexOf('https://static.getgetai.com/base') === -1
            ) {
              // console.log('img src', imgSrc)
              let res = await POSTuploadReplaceImg(imgSrc)
              if (res.code === 200) {
                imgURL = res.data
              } else {
                uploadImgUrl = imgSrc
              }
            }
            imgURL =
              imgURL ||
              'https://static.getgetai.com/base/pc-nuxt/assets/img/default/replace_icon.png'
            if (
              imgURL ===
              'https://static.getgetai.com/base/pc-nuxt/assets/img/default/replace_icon.png'
            ) {
              let pImg = document.createElement('p')
              pImg.innerHtml = `<span>请点击链接重新下载：</span><br><a href="${uploadImgUrl}">${uploadImgUrl}</a>`
              try {
                editorElem.insertAfter(pImg, item)
              } catch (e) {
                console.log(e)
              }
            }
            let imgObj = new Image()
            imgObj.src = imgURL
            imgObj.onload = function() {
              let tmpItem = imgDataList[index]
              tmpItem.setAttribute('src', imgURL)
              tmpItem.setAttribute('data-mce-src', imgURL)
              tmpItem.setAttribute('alt', 'Get智能写作')
              tmpItem.style.maxWidth = '100%'
              tmpItem.removeAttribute('width')
              tmpItem.style.width = ''
              tmpItem.removeAttribute('height')
              tmpItem.style.height = ''
            }
          }
          resolve(true)
        }, 5)
      })
    },
    // 上传base64图片后的 URL
    async getBase64UploadedUrl(base64ImgCode) {
      let imageName = null
      if (base64ImgCode.includes('png')) {
        imageName = 'uploaded-image.png'
      } else if (base64ImgCode.includes('gif')) {
        imageName = 'uploaded-image.gif'
      } else if (base64ImgCode.includes('bmp')) {
        imageName = 'uploaded-image.bmp'
      } else {
        imageName = 'uploaded-image.jpg'
      }
      let file = DataURLtoFile(base64ImgCode, imageName)
      let form = new FormData()
      form.append('file', file, file.name)
      const imgURL = await POSTUploadFileToOSSFile(form)
      return imgURL
    },
    // 上传blob图片文件 并返回 图片url
    async getBlobUploadedUrl(blobUrl) {
      try {
        const response = await axios({
          method: 'get',
          url: blobUrl,
          responseType: 'blob',
        })
        let imageBlob = response.data
        if (!imageBlob) {
          return false
        }
        // console.log('imageBlob', imageBlob)
        // console.log('response', response)
        // const imageBlob = this.response;
        const imageType = imageBlob.type
        let imageName = null

        // 根据文件的类型确定文件名，否则服务端得到的文件名为 blob，没有后缀
        if (imageType.includes('png')) {
          imageName = 'uploaded-image.png'
        } else if (imageType.includes('gif')) {
          imageName = 'uploaded-image.gif'
        } else if (imageType.includes('bmp')) {
          imageName = 'uploaded-image.bmp'
        } else {
          imageName = 'uploaded-image.jpg'
        }
        // 上传 Blob 图片
        const form = new FormData()
        form.append('file', imageBlob, imageName) // 第三个参数为文件名
        const imgURL = await POSTUploadFileToOSSFile(form)
        // console.log('getBlobUploadedUrl imgURL', imgURL)
        return imgURL
      } catch (error) {
        console.log('error img', error)
        return false
      }
      // const xhr = new XMLHttpRequest();
      // xhr.open('GET', originalImgSrc); // blob:http://localhost:8080/da126298-1b6b-4dfb-8a92-2e3ccbee611d
      // xhr.responseType = 'blob';
      // xhr.onreadystatechange = async function () {
      //   if (this.readyState === 4 && this.status === 200) {
      //     const imageBlob = this.response;
      //     const imageType = imageBlob.type;
      //     let imageName = null;

      //     // 根据文件的类型确定文件名，否则服务端得到的文件名为 blob，没有后缀
      //     if (imageType.includes('png')) {
      //       imageName = 'uploaded-image.png';
      //     } else if (imageType.includes('gif')) {
      //       imageName = 'uploaded-image.gif';
      //     } else if (imageType.includes('bmp')) {
      //       imageName = 'uploaded-image.bmp';
      //     } else {
      //       imageName = 'uploaded-image.jpg';
      //     }
      //     // 上传 Blob 图片
      //     const form = new FormData();
      //     form.append('file', imageBlob, imageName); // 第三个参数为文件名
      //     imgURL = await POSTUploadFileToOSSFile(form)
      //   }
      // };
      // xhr.send();
    },
    GetEditorMaxHeight() {
      const availHeight = window.innerHeight
      const delta = 57
      // 57是顶部栏高度
      this.fullPageContentHeight = availHeight
      this.articleBoxHeight = availHeight - 300 + delta
      // this.parentSuggestEditorMaxHeight = availHeight - 61 - 50
      this.parentSuggestEditorMaxHeight = availHeight - 61
      this.topicsListMaxHeight = availHeight - 370
    },
    changeTitle(newTitle) {
      if (this.editor.title !== newTitle) {
        this.editor.title = newTitle || this.editor.title
        return true
      }
      return false
    },
    // 没有获取到文章
    whenNoArticle() {
      setTimeout(() => {
        this.$router.replace({
          path: '/folder',
        })
      }, 1500)
    },
    // 笔记保存条件
    saveNoteFieldPromise(newNote, noteRemote) {
      // 首要条件，必须有内容
      let reg = /<\/?.+?\/?>/g
      let imgReg = /<img.*?src="(.*?)".*?\/?>/gi
      const content = newNote.content
      if (!content) return
      // 去除html标签
      let regContent = content.replace(reg, '')
      // 检测img标签
      let imgContent = content.match(imgReg)
      if (!newNote.content || (!regContent && !imgContent)) {
        // this.$message({
        //   message: '保存内容不能为空',
        //   warning: 'warning'
        // })
        return false
      }
      // 远程的笔记和本地笔记对比
      // if (newNote.uid && noteRemote.uid) {
      //   if (newNote.uid !== noteRemote.uid) {
      //     console.log('本地笔记和远程笔记不相符')
      //     return false
      //   }
      //   if (newNote.updateTime < noteRemote.updateTime) {
      //     console.log('本地笔记更新时间小于远程笔记')
      //     return false
      //   }
      // }
      // 其次，四个重要字段有一个变化即提交
      if (
        newNote.title === noteRemote.title &&
        newNote.content === noteRemote.content &&
        newNote.theme === noteRemote.theme &&
        newNote.searchQuestion === noteRemote.searchQuestion
      ) {
        return false
      }
      return true
    },
    // 获取最新过滤后的笔记内容
    getFiltedContent(originalContent) {},
    /**
     * 获取本地笔记副本
     */
    getLocalNoteCopy(noteId) {
      return new Promise(resolve => {
        if (!noteId) {
          resolve(false)
          return
        }
        const noteLocalKey = 'editorNote_' + noteId
        const noteLocalStr = localStorage.getItem(noteLocalKey)
        // console.log('noteLocalStr', noteLocalKey, noteLocalStr)
        if (noteLocalStr) {
          try {
            let noteLocal = JSON.parse(noteLocalStr)
            //console.log('noteLocal:::', noteLocal)
            // console.log('服务器保存文章比本地文章更新，是否创建版本 1')
            // console.log('modify time', noteLocal.modifyTime, this.noteRemote.modifyTime)
            if (
              this.isLogin &&
              noteLocal.modifyTime &&
              dayjs(noteLocal.modifyTime).isBefore(this.noteRemote.modifyTime)
            ) {
              // console.log('r服务器保存文章比本地文章更新，是否创建版本 new')
              // noteLocal = JSON.parse(noteLocalStr)
              this.$confirm('服务器保存文章比本地文章更新，是否创建版本', {
                distinguishCancelAndClose: true,
                confirmButtonText: '创建副本',
                cancelButtonText: '替换本地',
                showClose: false,
              })
                .then(() => {
                  noteLocal.title = '副本 ' + noteLocal.title
                  noteLocal.uid = ''
                  resolve(noteLocal)
                })
                .catch(err => {
                  console.log(err)
                  resolve(false)
                  localStorage.removeItem(noteLocalKey)
                })
            } else {
              resolve(noteLocal)
            }
            localStorage.removeItem(noteLocalKey)
          } catch (error) {
            console.error(error)
            resolve(false)
          }
        } else {
          resolve(false)
        }
      })
    },
    // 获取更新笔记
    getModifiedNote() {
      return new Promise(resolve => {
        let htmlContent
        if (this.$refs.MyEditorChild) {
          htmlContent = this.$refs.MyEditorChild.getEditorContent()
          // console.log('get content', htmlContent)
          // this.editor.content = htmlContent || this.editor.content
        }
        const noteId = this.uid || this.$route.query.uid
        const noteLocalKey = 'editorNote_' + noteId
        let note = {
          uid: noteId,
          title: this.editor.title,
          content: clearIllegalBeforeSave(htmlContent),
          brief: parseHtmlToText(htmlContent),
          modifyTime: new Date(),
          classifyId:
            this.$route.query.folderid || this.noteRemote.classifyId || '0',
          theme: (this.$route.query.km || this.noteRemote.theme || '').substr(
            0,
            50
          ),
          searchQuestion: (
            this.$route.query.ks ||
            this.noteRemote.searchQuestion ||
            ''
          ).substr(0, 50),
          isPrivate: this.noteRemote.isPrivate === 0 ? 0 : 1,
          img: getFirstImg(htmlContent),
        }
        this.ifFillDocumentType(note)
        if (this.$nuxt.isOffline && noteId) {
          localStorage.setItem(noteLocalKey, JSON.stringify(note))
          this.$message({
            type: 'warning',
            message: '您已断网，请尽快联网保存',
          })
          this.autoSaveHintText = '您的网络存在问题'
          this.autoSaveHintTextStatus = 104
          resolve(false)
        } else if (!this.isLogin && noteId) {
          localStorage.setItem(noteLocalKey, JSON.stringify(note))
          this.$message({
            type: 'warning',
            message: '您暂未登录，文档保存到本地',
          })
          this.autoSaveHintText = '您暂未登录，无法为您自动保存文章'
          this.autoSaveHintTextStatus = 105
          resolve(false)
        } else {
          resolve(note)
        }
      })
    },
    // API methods
    /**
     * 获取文章详情
     * @param {String} uid 文章id
     * @param {String} folderid 文集ID
     */
    async goGetNoteDetail(uid, folderid) {
      if (!uid) return
      const res = await NoteApi.GetNoteDetail(uid)
      // error handling
      if (res.code !== 200) {
        if (getTempNoteId() === uid) setTempNoteId('')
        throw new Error(res.message)
      }
      let data = res.data || {}
      // res.data.modifyTime = dayjs().add(1, 'year')
      this.noteRemote = res.data
      // 获取本地笔记
      const noteLocal = await this.getLocalNoteCopy(uid)
      if (noteLocal) {
        const saveRes = await this.saveLocalCopy(noteLocal)
        if (saveRes) {
          data = noteLocal
        }
      }
      this.alreadySent = res.data.isSubmit
      this.editor = {
        // ...data,
        uid: uid,
        title: data.title,
        content: clearIllegalBeforeSave(data.content),
        // content: data.content,
        classifyId: data.classifyId,
        isPrivate: data.isPrivate === 0 ? 0 : 1,
      }
      // console.log('filter html', data.content.replace(/<[^>]+>/g, ""))
      // 初始化文章长度
      this.editorContentLength = data.content
        ? data.content.replace(/<[^>]+>/g, '').replace(/\s|[\r\n]/gi, '').length
        : 0
      // 封面
      if (data.isImg) {
        this.coverImage = data.img
        this.editor.img = data.img
      }

      // 设置
      this.noteContentLoaded = true
      this.setFolderInfo(res.classifyId)

      // 主题
      // if (data.theme) this.initArticleTheme(data.theme)
      if (!this.currentKeyword) {
        let loadingInstance = Loading.service({
          fullscreen: false,
          text: '题材加载中...',
          target: '.topics-list',
        })

        try {
          await this.$store.dispatch('topic/loadHotSpotsData')
        } catch (err) {
          console.log(err)
        } finally {
          if (loadingInstance) loadingInstance.close()
        }
      }
    },
    //保存到企业
    async manualSaveBusiness() {
      const noteToUpdate = await this.getModifiedNote()
      if (!this.isBusiness) {
        //classifyId
        noteToUpdate.classifyId = '0'
      }
      if (!noteToUpdate) return
      if (!this.saveNoteFieldPromise(noteToUpdate, {})) {
        this.$message.warning('内容不能为空')
        return true
      }
      let data = {
        documentType: 'business',
        ...noteToUpdate,
      }
      if (this.businessNote) {
        data.uid = this.businessNote.uid
      } else {
        data.uid = ''
      }
      let res = await saveBusinessNote(data)
      if (res.code === 200) {
        this.$message({
          iconClass: 'none',
          center: true,
          showClose: true,
          customClass: 'getui-toast-box',
          dangerouslyUseHTMLString: true,
          message: `<div style="display:block;width: 100%;text-align: center;">
        文章已保存至
        <a href="/folder?mode=b_note&b_folderuid=${data.classifyId}" target="_blank" style="color: #FFB440;font-weight: bold;text-decoration: none;">企业文集</a>
        </div>`,
          type: 'success',
        })
        this.businessNote = res.data
      } else {
        let msg = res.message ? res.message : '保存失败'
        this.$message.error(msg)
      }
    },
    // 手动保存
    async manualSave() {
      try {
        if (this.autoSaveHintTextStatus === 101) return
        this.autoSaveHintTextStatus = 101
        const noteToUpdate = await this.getModifiedNote()
        // console.log('manualSave', noteToUpdate)
        if (!noteToUpdate) return
        // 如果当前更新内容与 服务器内容一样则不更新
        if (!this.saveNoteFieldPromise(noteToUpdate, {})) {
          this.autoSaveHintTextStatus = 100
          this.$message({
            type: 'warning',
            message: `内容不能为空`,
            showClose: true,
          })
          return true
        }
        const res = await NoteApi.EditNoteLocal(noteToUpdate)
        // console.log('showSuccessMessage', res.code, showSuccessMessage)
        if (res.code === 200) {
          //返回classifyId为null，则保持原有
          //如果当前是文集tab项,则需要将当前正在编辑的文章置顶
          this.topEditingNote(res.data)
          this.showMessageOnSaveNote()
          this.uid = res.data.uid || this.$route.query.uid
          this.editor.uid = this.uid
          this.noteRemote = res.data
          this.updateRoute(
            res.data,
            noteToUpdate.theme,
            noteToUpdate.searchQuestion
          )
          this.autoSaveHintTextStatus = 103
          return true
        } else if (res.code === 10007) {
          // this.$message({
          //   message: res.message,
          //   type: "warning"
          // })
          this.autoSaveHintTextStatus = 103
          return true
        } else if (res.code === 400) {
          // this.$message({
          //   message: res.message,
          //   type: "warning"
          // })
          this.autoSaveHintTextStatus = 104
          return true
        } else {
          this.$message({
            message:
              (res.message.length > 10 ? '请求异常' : res.message) || res,
            type: 'error',
          })
          this.autoSaveHintTextStatus = 104
          return false
        }
      } catch (error) {
        console.log(error)
        this.autoSaveHintTextStatus = 104
      }
      // this.saveNoteData(false)
      // this.autoSaveHintTextStatus = 103
    },
    //如果当前是文集tab项,则需要将当前正在编辑的文章置顶
    topEditingNote(editingNote) {
      try {
        //保留topNumber、createTime字段(edit接口未返回topNumber字段)
        editingNote.topNumber = this.noteRemote.topNumber
        editingNote.createTime = this.noteRemote.createTime
        let query = this.$route.query
        if (query.mode === 'ArticleColumn') {
          this.$refs['columnLeft'].$refs['ArticleColumn'].noReloadSortLocation(
            editingNote
          )
        }
      } catch (e) {
        console.log(e)
      }
    },
    //如果是教程文档则不需保存
    noSaveCourseNote() {
      let query = this.$route.query
      let hFlag = query.hFlag
      return hFlag
    },

    //如果带documentType 则填充documentType参数
    ifFillDocumentType(note) {
      let query = this.$route.query
      let documentType = query.documentType
      if (documentType) {
        note['documentType'] = documentType
      }
      return note
    },

    // 编辑保存笔记
    async saveNoteData(
      showSuccessMessage = false,
      defaultShowMessage = NOTE_SAVED_SUCCESS_TEXT,
      km,
      ks
    ) {
      try {
        //如果是教程则不保存
        if (this.noSaveCourseNote() && !this.isLogin) {
          return
        }
        // 获取本地笔记
        const noteLocal = await this.getLocalNoteCopy(
          this.uid || this.noteRemote.uid
        )
        if (noteLocal) {
          //this.ifFillDocumentType(noteLocal)
          const saveRes = await this.saveLocalCopy(noteLocal)
          if (saveRes) {
            return
          }
        }
        if (this.autoSaveHintTextStatus === 101) return false
        this.autoSaveHintTextStatus = 101
        const noteToUpdate = await this.getModifiedNote()
        // console.log('edit note', noteToUpdate.content)
        if (!noteToUpdate) return false
        // 如果当前更新内容与 服务器内容一样则不更新
        if (!this.saveNoteFieldPromise(noteToUpdate, this.noteRemote)) {
          this.autoSaveHintTextStatus = 100
          return true
        }
        // 编辑笔记
        //this.ifFillDocumentType(noteToUpdate)
        const res = await NoteApi.EditNoteLocal(noteToUpdate)
        // console.log('showSuccessMessage', res.code, showSuccessMessage)
        if (res.code === 200) {
          //如果当前是文集tab项,则需要将当前正在编辑的文章置顶
          this.topEditingNote(res.data)
          // localStorage.removeItem(noteLocalKey)
          if (showSuccessMessage) {
            this.showMessageOnSaveNote()
          }
          this.uid = res.data.uid || this.$route.query.uid
          this.editor.uid = this.uid
          this.noteRemote = res.data
          this.updateRoute(res.data, km, ks)
          this.autoSaveHintTextStatus = 102
          return true
        } else if (res.code === 10006) {
          this.$message({
            message: res.message,
            type: 'warning',
          })
          this.autoSaveHintTextStatus = 106
          return true
        } else if (res.code === 10007) {
          // this.$message({
          //   message: res.message,
          //   type: "warning"
          // })
          this.autoSaveHintTextStatus = 107
          return true
        } else {
          this.$message({
            message:
              (res.message.length > 50 ? '请求异常' : res.message) || res,
            type: 'error',
          })
          this.autoSaveHintTextStatus = 104
          return false
        }
      } catch (error) {
        this.autoSaveHintTextStatus = 104
        console.error('note save error', error)
        // 更新内容到本地
      }
    },
    // 创建保存副本
    async saveLocalCopy(noteLocal) {
      try {
        if (this.autoSaveHintTextStatus === 101) return false
        this.autoSaveHintTextStatus = 101
        // console.log('edit local note', noteLocal)
        if (!noteLocal) return false
        // 编辑笔记
        const res = await NoteApi.EditNoteLocal(noteLocal)
        // console.log('showSuccessMessage', res.code, showSuccessMessage)
        if (res.code === 200) {
          this.uid = res.data.uid || this.$route.query.uid
          this.editor.uid = this.uid
          this.noteRemote = res.data
          this.updateRoute(res.data, noteLocal.theme, noteLocal.searchQuestion)
          this.autoSaveHintTextStatus = 102
          return true
        } else if (res.code === 10007) {
          // this.$message({
          //   message: res.message,
          //   type: "warning"
          // })
          this.autoSaveHintTextStatus = 102
          return true
        } else {
          this.$message({
            message:
              (res.message.length > 10 ? '请求异常' : res.message) || res,
            type: 'error',
          })
          this.autoSaveHintTextStatus = 104
          return false
        }
      } catch (error) {
        this.autoSaveHintTextStatus = 104
        console.error('note save error', error)
        return false
        // 更新内容到本地
      }
    },
    //企业文集保存提示
    showMessageOnSaveNoteBusiness() {
      let classifyId = this.noteRemote.classifyId || '0'
      this.$message({
        iconClass: 'none',
        center: true,
        showClose: true,
        customClass: 'getui-toast-box',
        dangerouslyUseHTMLString: true,
        message: `<div style="display:block;width: 100%;text-align: center;">
        文章已保存至
        <a href="/folder?mode=b_note&b_folderuid=${classifyId}" target="_blank" style="color: #FFB440;font-weight: bold;text-decoration: none;">企业文集</a>
        </div>`,
        type: 'success',
      })
    },
    // 展示保存提示
    showMessageOnSaveNote() {
      let classifyId = this.$route.query.folderid || this.noteRemote.classifyId
      this.folderInfo =
        this.folderList.find(item => item.uid === classifyId) || {}
      let folderName = '我的文集-' + (this.folderInfo.uname || '默认文集')
      if (this.isBusiness) {
        this.showMessageOnSaveNoteBusiness()
        return
      }
      const folderid = this.folderInfo.uid || 0

      this.$message({
        iconClass: 'none',
        center: true,
        showClose: true,
        customClass: 'getui-toast-box',
        dangerouslyUseHTMLString: true,
        message: `<div style="display:block;width: 100%;text-align: center;">
        文章已保存至
        <a href="/folder?folderuid=${folderid}" target="_blank" style="color: #FFB440;font-weight: bold;text-decoration: none;">${folderName}</a>
        </div>`,
        type: 'success',
      })
    },

    loadImg(src) {
      return new Promise(resolve => {
        let img = new Image()
        img.onload = () => {
          resolve(src)
        }
        img.src = src
      })
    },

    openAutoSave() {
      if (this.isAutoSaveOn) return

      const that = this
      this.isAutoSaveOn = true
      clearInterval(_counterAutoSave)

      // 1s = 1000ms
      const SECOND = 1000
      let totalSecond = 10
      _counterAutoSave = setInterval(async () => {
        //使没隔一段时间保存一次，不受任何动作限制，不加此句只能在回车状态下触发保存
        that.changeAfterLastChange = true
        if (that.changeAfterLastChange) {
          // that.autoSaveHintTextStatus = NOTE_SAVE_STATUS_UPLOADING
          // that.autoSaveHintTextStatus = 101

          that.changeAfterLastChange = false

          if (that.isAutoSaveOn) {
            // 根据模式，选择保存函数
            const isSave = await that.saveNoteData(false)
            if (isSave) {
              clearTimeout(_counterUpdateSave)
              // that.autoSaveHintTextStatus = NOTE_SAVE_STATUS_SAVE_SUCCESS

              _counterUpdateSave = setTimeout(() => {
                // that.autoSaveHintText = that.autoSaveHintTextDefault
                // that.autoSaveHintTextStatus = NOTE_SAVE_STATUS_SAVED
                // that.autoSaveHintTextStatus = 102
                if (that.autoSaveHintTextStatus !== 101) {
                  that.autoSaveHintTextStatus = 100
                }
              }, 8000)
            }
          }
        }
      }, totalSecond * SECOND)
    },

    setFolderInfo(folderId) {
      if (this.folderList.length === 0) {
        // 获取文集信息
        return NoteApi.GetNoteClassifyList({ isDel: 0 }).then(list => {
          this.folderList = list
          // this.setFolderInfo(folderId)
        })
      }

      let folderList = this.folderList

      let note = this.editor
      if (folderId && note.classifyId !== folderId) {
        note.classifyId = folderId
      }

      if (this.noteContentLoaded && folderList.length) {
        let classifyId = note.classifyId
        let folder = folderList.find(item => item.uid === classifyId)

        const defaultFolder = {
          uid: 0,
          uname: '默认文集',
        }
        this.folderInfo = folder || defaultFolder
      }
    },
    // 找我的文集
    searchMyArticle() {
      //vip判断
      if (!this.judgeShowPurchaseModal(12)) {
        return
      }
      // 点击选中小框的查找素材首先过滤到所有标签按钮搜索素材并展开左侧
      let checkFilter = this.rewriteColumnContent.replace(/<[^>]+>/g, '')
      // 去除空格
      checkFilter = checkFilter.replace(/&nbsp;/gi, '')
      if (checkFilter && checkFilter.length > 20) {
        checkFilter.substr(0, 19)
      }
      this.$store.dispatch('compose/UPDATE_EDITOR_TITLE', checkFilter)
      let query = { ...this.$route.query }
      query.ka = checkFilter
      query.mode = 'ArticleColumn'
      // console.log('search for meterial', query)
      this.$router.push({
        path: this.$route.path,
        query,
      })
      // this.onAddKeywordAndSelect(checkFilter)
      // await this.saveNoteData(false)

      // this.$refs.columnLeft.changeColumnLeftMode('Meterial');
      this.isLeftShrink = false
    },

    copyArticle() {
      if (!this.isCopyFuncActivate) {
        return this.isEditorRightZoomEnough
          ? this.initHint('copy', 0)
          : this.$message({
              type: 'error',
              message: '正文没有内容',
            })
      }
      if (!this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return this.$message({
          type: 'warning',
          message: '登录后可体验此功能',
        })
      }
      const area = document.querySelector('#editor-container')
      copyRichArticle(area, this)
      this.$store.dispatch(
        'ga/click',
        `TAGEditorArticleCopyClick::${this.editor.uid}`
      )
    },
    // 选择领域
    async selectHotTab(fields, order) {
      this.hotList = await GetHotEvent({ fields, order })
    },
    // 换一换
    async changeTopic(fields, order) {
      if (this.hotList.pageNum * this.hotList.pageSize >= this.hotList.total)
        return
      let _hotList = await GetHotEvent({
        fields,
        order,
        pageNum: ++this.hotList.pageNum,
      })
      _hotList.list = [...this.hotList.list, ..._hotList.list]
      this.hotList = _hotList
    },
    // 更改排序
    async sortHotRecommend(fields, order) {
      this.hotList = await GetHotEvent({ fields, order })
    },

    closeGuide() {
      this.isGuideShow = this.isGuideShow === 2 ? false : 2
      if (this.isGuideShow === 2) {
        let toolbox = document
          .querySelector('.toolbox-set-height')
          .getBoundingClientRect()
        this.guideContentPosition = `padding-top: ${toolbox.top ||
          toolbox.y}px;padding-left: ${(toolbox.left || toolbox.x) - 330}px;`
      }
    },
    toFeedBack() {
      this.$Utils.simulateAClick('https://support.qq.com/products/66013')
    },
    toProductIntro() {
      this.$Utils.simulateAClick(this.$store.getters['version/versionIntro'])
    },
    bindClick() {
      let e = window.event
      const helpBtn = this.$refs['help']
      const modal = document.querySelector('.help-content')
      const qrcode = document.querySelector('.qrcodeModal')
      if (modal && !modal.contains(e.target) && !helpBtn.contains(e.target)) {
        this.isShowHelp = false
      }

      if (qrcode && !qrcode.contains(e.target) && !modal.contains(e.target)) {
        this.isShowHelp = false
        this.isShowQrcodeServer = false
      }
      if (
        this.isNotificationShow &&
        !document.querySelector('.notification-box').contains(e.target)
      ) {
        this.isNotificationShow = false
      }
    },
    showProductDeclear() {
      let that = this
      setTimeout(() => {
        that.isNotificationShow = true
      }, 300)
    },
    isresetDlog() {
      this.centerDialogVisible = true
    },
    isPaymentClick() {
      this.isPaymentdialog = true
      // 调取智能纠错 支付弹框
    },
    // 纠错替换弹框消失
    isTiptop(e) {
      if (this.$refs['MyEditorChild']) {
        this.$refs['MyEditorChild'].isTiptopDialog(e)
        if (this.isSuspendedFrame) {
          this.isSuspendedFrame = false
        }
      }
    },
    updateXY(event) {
      // 已被替换：使用rect计算
      // 备用
      this.x = event.clientX
      this.y = event.clientY
    },
    hideToolbar() {
      // 隐藏工具栏
      this.isSuspendedFrame = false
      this.isShowRewriteExtendTool = false
    },
    // 编辑器内部划选 找素材
    searchForMaterial() {
      // 点击选中小框的查找素材首先过滤到所有标签按钮搜索素材并展开左侧
      let checkFilter = this.rewriteColumnContent.replace(/<[^>]+>/g, '')
      // 去除空格
      checkFilter = checkFilter.replace(/&nbsp;/gi, '')
      if (checkFilter && checkFilter.length > 20) {
        checkFilter.substr(0, 19)
      }
      this.$store.dispatch('compose/UPDATE_EDITOR_TITLE', checkFilter)
      let query = { ...this.$route.query }
      query.km = checkFilter
      query.mode = 'Meterial'
      // console.log('search for meterial', query)
      this.$router.push({
        path: this.$route.path,
        query,
      })
      // this.onAddKeywordAndSelect(checkFilter)
      // await this.saveNoteData(false)

      // this.$refs.columnLeft.changeColumnLeftMode('Meterial');
      this.isLeftShrink = false
      // 如果当前页面的全文预览打开，隐藏全文预览
      // 原有方法替换 寻找素材
      // this.$refs.columnLeft.templateSearch(checkFilter)
    },

    //显示购买框
    showPurchaseModal(functionType) {
      if (functionType) {
        this.$store.commit('purchase/setFunctionType', functionType)
      }
      //显示购买狂
      this.$store.commit('purchase/setIsp', true)
      //this.isShowPurchaseModal = true
    },

    //关闭购买框
    // closePurchaseModal () {
    //   this.isShowPurchaseModal = false
    // },

    //购买框判断
    judgeShowPurchaseModal(functionType) {
      if (!this.isVip) {
        this.showPurchaseModal(functionType)
        return false
      }
      return true
    },

    judgeShowPurchaseModalByParam(tab) {
      if (tab && tab === '文章素材') {
        return true
      }
      let flag = 0
      switch (tab) {
        case '金句素材':
          flag = 8
          break
        case 'AI配图':
          flag = 17
          break
        default:
          flag = 16
      }
      return this.judgeShowPurchaseModal(flag)
    },

    popSearch(isLeftOpen, navigation, tab) {
      if (!this.isLogin) return
      //判断是否显示购买框
      if (!this.judgeShowPurchaseModalByParam(tab)) {
        return
      }
      if (isLeftOpen) this.isLeftShrink = false
      let checkFilter = this.rewriteColumnContent.replace(/<[^>]+>/g, '')
      // 去除空格
      checkFilter = checkFilter.replace(/&nbsp;/gi, '')
      if (checkFilter && checkFilter.length > 20) {
        checkFilter.substr(0, 19)
      }
      let query = { ...this.$route.query }
      if (navigation === 'Meterial') {
        query.km = checkFilter
      }else if (navigation === 'AiPicture') {
        query.kp = checkFilter
      } else {
        query.ks = checkFilter
      }
      query.tab = tab

      query.mode = navigation
      // console.log('search for  meterial', query)
      this.$router.push({
        path: this.$route.path,
        query,
      })
      this.isSuspendedFrame = false
    },
    // 接收拖拽过来的数据
    ondragstart(items) {
      this.dataTransfer = items
    },
    //登录成功，如果是从首页进来的则跳转到工作台
    ifFromHomeJump() {
      let query = this.$route.query
      let hFlag = query.hFlag
      if (hFlag) {
        this.$router.push('/folderV2?folderuid=0')
        return false
      }
      return true
    },
    // 登录后保存数据提示
    async whetherLogin(e) {
      //登录成功，如果是从首页进来的则跳转到工作台
      if (!this.ifFromHomeJump()) {
        return
      }
      // 执行被保存的信息
      // if (e) this.whetLogin = true
      // const noteId = this.uid || this.$route.query.uid
      // // 本地localstorage 保存笔记 key
      // const noteLocalKey = 'editorNote_' + (noteId)
      // let localStorageData = JSON.parse(localStorage.getItem(noteLocalKey))
      // if (!localStorageData) return false
      // if (!localStorageData.content.content && !localStorageData.content.title) return false
      // setTimeout(async () => {
      //   await this.saveNoteData()
      // }, 1000)
      await this.init()
    },
    async init() {
      localStorage.removeItem('editorNote_new')
      this.isShowVideo = this.$Utils.checkEditorIntroShow()
      this.isSpecialNavigator = TestUserAgent(window.navigator.userAgent)
      this.$store.dispatch('version/checkIsCurrentVersion')
      this.$store.dispatch('topic/loadHotSpotsData')
      this.columnLeftMode = this.$route.query.mode || 'Meterial'
      // 初始化元素高度
      this.GetEditorMaxHeight()

      this.initEventListeners()
      // 初始化文章和关键词
      this.initArticleAndKeyword()

      // 未初始化用户信息
      if (!this.$store.getters['user/isInfoInit'])
        await this.$store.dispatch('user/INIT_USER_INFO')

      this.initUser()

      this.initToolboxTips()

      // document.addEventListener('click', this.bindClick)

      this.isGuideShow = this.$Utils.checkGuideShow(this) ? 1 : false
      this.$store.dispatch('compose/UPDATE_EDITOR_TITLE', '')

      //EventBus.$on("importArticleCallback", this.importArticleCallback)
      let query = { ...this.$route.query }
      if (query.mode === 'ArticleColumn' && !query.uid) {
        this.isLeftShrink = true
      }
    },
    // 接收编辑器内部点击唤起
    smartOutline(e) {
      this.$refs.columnLeft.columnLeftModeText(e)
      this.isLeftShrink = false
    },
    smartOutlineMeterial(e) {
      this.$refs.columnLeft.columnLeftModeText(e)
      this.isLeftShrink = false
    },
    changeTemplateKeyword(e) {
      this.isleftTabFlagParent = e
    },
    async keywordSearchOutline(val) {
      if (!this.isLogin) return false
      if (val) {
        this.keywordSearchOutlineText = val
        let that = this
        await that
          .saveNoteData(false)
          .then(isSave => {
            if (isSave) {
              clearTimeout(_counterUpdateSave)
              that.autoSaveHintTextStatus = 102
              _counterUpdateSave = setTimeout(() => {
                that.autoSaveHintText = that.autoSaveHintTextDefault
                that.autoSaveHintTextStatus = 100
              }, 1000)
            }
          })
          .catch(err => {
            console.log(err)
          })
      }
    },

    flagCurontClick(templateid, flagCuront, editorStatus) {
      this.templateid = templateid
      this.flagCuront = flagCuront
      this.editorStatus = editorStatus
    },
    async generateButtonGenerate(routeData, keyword) {
      this.flagCuront = false
      // console.log('get generate keyword', keyword)
      let hasChance = await POSTisAuthHasChance(7)
      if (!hasChance) {
        this.$message({
          type: 'error',
          message:
            '免费版用户每月可使用10次模板成稿功能，你本月剩余使用机会已使用完毕',
        })
        return
      }
      let query = routeData.route.query
      // await this.appendContentByTemplate(query)
      await this.generateNewNote(query)
      await this.saveNoteData(false, null, keyword)
      // 初始化文章和关键词
      // let queryParam = { ...this.$route.query }
      // queryParam.mode = 'Meterial'
      // queryParam.km = this.$route.query.km || keyword
      // queryParam.ks = this.$route.query.ks || keyword
      // this.$router.push({
      //   path: this.$route.path,
      //   query: queryParam
      // })
      this.leftOpen(keyword, 'Meterial')

      this.flagCuront = false
      // this.$refs.columnLeft.templateSearch(keyword)
      // this.$refs.columnLeft.templateSection(keyword)
    },
    isSetSelfTopicShowClose() {
      this.isSetSelfTopicShow = false
      this.$store.commit('component/SelfTopicChangeModalStatusChange', false)
    },
    editorDrop(event) {
      // let article = {
      //   content: event.dataTransfer.getData("content"),
      //   title: event.dataTransfer.getData("title"),
      //   source_article_title: event.dataTransfer.getData("source_article_title"),
      //   source_url: event.dataTransfer.getData("source_url"),
      // }
      const articleStr = event.dataTransfer.getData('article')
      if (articleStr && articleStr !== '') {
        const article = JSON.parse(articleStr)
        // console.log('editor drop content', event, article)
        const content = `<h1>${article.title ||
          ''}</h1><p>${article.content_text || ''}</p>`
        const source = {
          source_name: article.sourceName,
          title: article.title,
          source_url: article.sourceUrl,
        }
        //插入文章
        this.appendArticleToContent(content, source)
      }
    },
    dragAllow(event) {
      // console.log('drag allow', item)
      event.preventDefault()
    },
    // 更新路由当前笔记 保存后发生变化，更新路由
    updateRoute(note, km, ks) {
      // console.log(this.$route.path.indexOf('compose') < 0)
      // console.log('this.$route.path', this.$route.path !== '/compose')
      // console.log('this.$route.path', this.$route.path)
      // console.log('note.uid ', note.uid === this.$route.query.uid)
      // 如果当前不是编辑页面，不更新当前路由
      if (this.$route.path.indexOf('compose') < 0) {
        return
      }
      // 如果当前当前路���UID为当前更新 uid 则不更新路由
      if (note.uid === this.$route.query.uid) {
        return
      }
      let query = { ...this.$route.query }
      // console.log('got query', query)
      query.uid = note.uid
      // 含有plist 字段代表是模板生成内容需添加搜索字段
      if (query.plist) {
        query.km = km || note.title
        query.ks = ks || note.title
      } else {
        query.km = km || query.km
        query.ks = ks || query.ks
      }
      //如果当前是普通模板，则添加标识
      if (query['tmpid']) {
        query['templateFlag'] = query['tmpid']
      }
      // console.log('title init ', query)
      delete query['compose-type']
      delete query['activate-func']
      delete query.tags
      delete query.plist
      delete query.tmpid
      delete query.principleid
      delete query.loginSuccess
      // this.editor = res.data
      // console.log('updateRouter', query)
      this.$router.push({
        path: this.$route.path,
        query: query,
      })
    },
    /**
     * columnLeft 模式切换
     *
     */
    columnLeftModeSwitch(modeItem) {
      if (modeItem.loginRequire && !this.isLogin) {
        this.$store.dispatch('user/SHOW_LOGIN')
        return
      }
      if (this.columnLeftMode === modeItem.mode) {
        this.isLeftShrink = !this.isLeftShrink
        return
      }
      this.leftOpen('', modeItem.mode)
    },
    /**
     * 打开 columnLeft
     * 1、左侧边栏按钮
     * 2、编辑器内小火箭
     * 3、编辑器内搜索素材
     */
    leftOpen(defaultSearch, mode) {
      this.isLeftShrink = false
      this.columnLeftMode = mode || 'Meterial'
      const Q = { ...this.$route.query }
      // km, ks两者有一个为空时候 AND 有标题 AND 左边栏打开
      // console.log('compose query', Q)
      switch (mode) {
        case 'Meterial':
          Q.km = Q.km || defaultSearch || this.editor.title
          break
        case 'Section':
          Q.ks = Q.ks || defaultSearch || this.editor.title
          break
        // default:
        //   Q.km = Q.km || defaultSearch || this.editor.title
        //   break
      }
      Q.mode = mode || Q.mode || 'Meterial'
      delete Q.isLeftShrink
      delete Q['activate-func']
      delete Q.nt
      delete Q.loginSuccess
      delete Q.tab
      // console.log('Q', Q)
      // Q.mode = this.$route.Q.mode || 'Section'
      console.log('query:::', Q)
      this.$router.push({
        path: this.$route.path,
        query: Q,
      })
    },
    // 点击开始纠错
    async beginCorrection() {
      this.progressbarnumber = 0
      this.editorSuccessErrornum = 0
      this.loadingCorrectionAdd()
      this.isStyleFalse = true
      // 清理上次的纠错结果
      if (this.$refs.MyEditorChild) {
        if (!this.isStyleFalse) return false
        this.$refs.MyEditorChild.numberOfNodes()
      }
      try {
        let res = await POSTCorrectionTest(this.editorTextContent)
        if (typeof res === 'string') return
        this.resData = res
        if (this.$refs.MyEditorChild) {
          if (!this.isStyleFalse) return false
          this.$refs.MyEditorChild.correction(res, this.limitNum)
        }
      } catch (err) {
        console.error(err)
        // 请求中断
        this.closeStatus(true)
      }
    },
    loadingPercentFastAddToMax() {
      let that = this
      const INTERVAL = 10 //增速间隔
      const TIMEBEFORECLOSE = 1000 //到100后停留时间
      return new Promise(resolve => {
        clearInterval(loadingPercentCount)
        loadingPercentCount = setInterval(() => {
          that.progressbarnumber++
          if (that.progressbarnumber === 100) {
            clearInterval(loadingPercentCount)
            setTimeout(() => {
              resolve()
            }, TIMEBEFORECLOSE)
          }
        }, INTERVAL)
      })
    },
    loadingCorrectionAdd() {
      let that = this
      const INTERVAL = 200
      const SLOWINTERVAL = 1000 //慢速间隔
      const SLOWPERCENT = 70 //慢速百分比
      return new Promise(resolve => {
        loadingPercentCount = setInterval(() => {
          that.progressbarnumber++
          //如果到70了数据仍没返回则减慢百分比递增
          if (that.progressbarnumber === SLOWPERCENT) {
            clearInterval(loadingPercentCount)
            loadingPercentCount = setInterval(() => {
              that.progressbarnumber++
            }, SLOWINTERVAL)
          }
        }, INTERVAL)
      })
    },
    // 中断请求
    closeStatus(e) {
      this.correctionStatus = e
      this.progressbarnumber = 0
      this.isStyleFalse = false
      this.isCorrectionShow = false
      clearInterval(loadingPercentCount)
    },
    canclModalStatusShow(e) {
      if (this.isStyleFalse) {
        this.canclModalStatus = false
      } else {
        this.correctionStatus = 1
        this.isCorrectionShow = false
      }
    },
    editorImportArticles() {
      this.isAddResourceModalShowForEditor = true
    },
    //关闭文章导入对话框
    closeImportArticleModal() {
      this.isShowArticleImportModal = false
    },
    //导入文章 type:1文件 2链接导入
    importClick(type) {
      if (!this.judgeShowPurchaseModal(13)) {
        return
      }
      this.showWhichModal = type === 1 ? 'file' : 'link'
      this.isShowArticleImportModal = !this.isShowArticleImportModal
    },
    clearEditorContent() {
      let query = { ...this.$route.query }
      this.editor.uid = ''
      delete query.uid
      this.$router.push({
        path: this.$route.path,
        fullPath: '',
        query: query,
      })
      this.uid = ''
      if (this.$refs.MyEditorChild) {
        this.$refs.MyEditorChild.getEditor()
      }
    },
    switchReady() {
      return new Promise(async resolve => {
        try {
          if (this.autoSaveHintTextStatus === 101) {
            let saveInterval = setInterval(() => {
              if (
                this.autoSaveHintTextStatus === 102 ||
                this.autoSaveHintTextStatus === 103 ||
                this.autoSaveHintTextStatus === 100
              ) {
                this.clearEditorContent()
                clearInterval(saveInterval)
                resolve(true)
              }
            }, 100)
          } else {
            await this.saveNoteData(false)
            this.clearEditorContent()
            resolve(true)
          }
        } catch (error) {
          console.error('switch ready error', error)
          resolve(false)
        }
      })
    },
    // 编辑文章  http://192.168.50.187:19005/api/note
    // 1 当前编辑器无文章    2  有现有文章，需走保存文章通道
    async openPreparingeditor(data, status) {
      // 等待当前保存完成
      this.isShouldSyncArticleContent = false

      let query = { ...this.$route.query }

      if (query.documentType) {
        delete query.documentType
      }

      if (status === 1) {
        // 初始化文章和关键词
        this.uid = data.uid
        query.uid = data.uid
        query.mode = 'ArticleColumn'
        query.ks = data.searchQuestion || ''
        query.km = data.theme || ''
        this.$router.push({
          path: this.$route.path,
          query: query,
        })
      } else if (status === 2) {
        if (this.autoSaveHintTextStatus === 101) {
          this.$message({
            type: 'warning',
            message: '当前文章正在保存，请稍后重试~',
            duration: 2000,
            showClose: true,
          })
          return
        }
        await this.saveNoteData(false)
        if (
          this.autoSaveHintTextStatus === 102 ||
          this.autoSaveHintTextStatus === 103 ||
          this.autoSaveHintTextStatus === 100
        ) {
          this.isShouldSyncArticleContent = true
          // 保存成功，开始初始化新的文章, 并提示
          this.editor.content = ''
          this.editor.title = ''
          this.uid = data.uid
          query.uid = data.uid
          query.mode = 'ArticleColumn'
          query.ks = data.searchQuestion || ''
          query.km = data.theme || ''
          this.$router.push({
            path: this.$route.path,
            query: query,
          })
          this.$message({
            type: 'success',
            message: '上一篇文章已保存在您的文章库中',
            showClose: true,
            duration: 2000,
          })
        } else if (this.autoSaveHintTextStatus === 104) {
          this.$message({
            message: '网络问题，保存失败，请检查网络后再试',
          })
        } else {
          this.$message({
            type: 'warning',
            message: '当前文章保存失败，请手动保存',
            duration: 2000,
            showClose: true,
          })
        }
      }
    },
    // 保存新文章
    saveNewArticle() {
      return new Promise(async resolve => {
        try {
          let data = {
            content: '',
            title: '',
            isImg: '',
            classifyId: '0',
          }
          let res = await POSTNoteNewAdd(data)
          resolve(res)
        } catch (error) {
          console.error('switch ready error', error)
          resolve(false)
        }
      })
    },
    async newOpenArticle() {
      const result = await this.switchReady()
      let query = { ...this.$route.query }
      if (result) {
        this.openStatus = false
        try {
          if (
            this.autoSaveHintTextStatus === 102 ||
            this.autoSaveHintTextStatus === 103 ||
            this.autoSaveHintTextStatus === 100
          ) {
            delete query.km
            delete query.ks
            delete query.ka
            delete query.kp
            this.editor.content = ''
            if (this.$refs.MyEditorChild) {
              this.$refs.MyEditorChild.getEditor()
            }

            // 保存新文章
            let res = await this.saveNewArticle()
            if (res.uid) {
              this.editor.uid = res.uid
              this.uid = res.uid
              query.uid = res.uid
              query.mode = 'ArticleColumn'
            }
            this.$router.push({
              path: this.$route.path,
              query: query,
            })
            setTimeout(() => {
              this.openStatus = true
              this.noteRemote = {}
            }, 10)
            // 关闭左边栏
            this.isLeftShrink = false
            //质量检测重新初始化
            this.curDetectionStatus = 0
            this.$message({
              type: 'warning',
              message: `上篇文章已保存，开始编辑新的文章`,
              showClose: true,
              duration: 2000,
            })
          } else {
            this.$message({
              type: 'warning',
              message: '当前文章保存失败，请手动保存',
              duration: 2000,
              showClose: true,
            })
          }
        } catch (err) {
          console.error(err)
        }
      } else {
        this.$message({
          type: 'warning',
          message: '请先保存您的文章~',
          showClose: true,
          duration: 2000,
        })
      }
    },
    openHistorical() {
      this.isHistoryLoad = 0
      let query = { ...this.$route.query }
      this.isHistoryModal = true
      this.requestBackup(query.uid)
      // isHistoryLoad
    },
    // 请求备份数据
    async requestBackup(data) {
      let res = await GETHistoryVersion(data)
      if (res && res.length > 0) {
        res.forEach(item => {
          item.backupTime = this.timeConversion(item.backupTime)
          if (!item.title.trim()) {
            item.title = '无标题'
          }
          if (!item.content.trim()) {
            item.content = '无内容'
          }
        })
        this.isHistoryLoad = 1
        this.historyList = res
      } else {
        this.isHistoryLoad = 2
      }
    },
    // 时间处理
    timeConversion(time) {
      let date = new Date(+time)
      // 年
      let y = date.getFullYear()
      // 月
      let M = (date.getMonth() + 1).toString().padStart(2, '0')
      // 日
      let d = date
        .getDate()
        .toString()
        .padStart(2, '0')
      // 小时
      let h = date
        .getHours()
        .toString()
        .padStart(2, '0')
      // 分钟
      let m = date
        .getMinutes()
        .toString()
        .padStart(2, '0')
      // 秒数
      let s = date
        .getSeconds()
        .toString()
        .padStart(2, '0')
      return y + '-' + M + '-' + d + ' ' + h + ':' + m + ':' + s
    },
    async historyReplacement(history) {
      if (!history) return
      this.editor.title = history.title
      this.editor.content = history.content

      if (
        history.title === this.noteRemote.title &&
        history.content === this.noteRemote.content &&
        history.theme === this.noteRemote.theme &&
        history.searchQuestion === this.noteRemote.searchQuestion
      ) {
        // return false
        const res = await NoteApi.EditNoteLocal({
          uid: history.id,
          title: history.title,
          content: history.content,
        })
        if (res.code === 200) {
          this.autoSaveHintTextStatus = 102
          this.noteRemote = res.data
        } else {
          this.$message({
            message:
              (res.message.length > 10 ? '请求异常' : res.message) || res,
            type: 'error',
          })
          this.autoSaveHintTextStatus = 104
          return false
        }
      } else {
        this.$message({
          type: 'success',
          message: `已经将版本恢复到${history.backupTime}`,
        })
        this.isHistoryModal = false
      }
    },
    // 用户反馈文章
    articleComlaint(data) {
      this.articleComplaint.id = data.id
      this.articleComplaint.show = data.show
      this.articleComplaint.type = data.type
    },
    confimColumnRewriter() {
      this.isRewriteColumnShow = true
      this.isExpendColumnShow = false
    },
    confimColumnExPend() {
      this.isRewriteColumnShow = false
      this.isExpendColumnShow = true
    },
    editorDataUpdate() {
      // alert('s')
      delayFor(this.addSearchDOM, 1)
    },
  },
}

let _delayCount = 0

function delayFor(fn, duration = 1000) {
  clearTimeout(_delayCount)
  _delayCount = setTimeout(fn, duration)
}

/** parseHtmlToText 从html内容提取出纯文本
 * @param htmlStr       {String}    HTML富文本
 * @param maxLength     {Number}    纯文本的长度
 */
function parseHtmlToText(htmlStr, maxLength = 100) {
  if (!htmlStr) return ''

  let elem = document.createElement('div')
  elem.innerHTML = htmlStr
  let text = elem.innerText || elem.textContent

  return text.substr(0, maxLength)
}

/** getFirstImg 从html内容获取第一张图片
 * @param htmlStr       {String}    HTML富文本
 * @param maxLength     {Number}    纯文本的长度
 */
function getFirstImg(htmlStr) {
  if (!htmlStr) return ''

  const imgReg = /<img.*?src="(.*?)".*?\/?>/gi
  const arr = htmlStr.match(imgReg)
  const srcReg = /src=['"]?([^'"]*)['"]?/
  const src = arr && arr.length > 0 ? arr[0].match(srcReg)[1] : ''
  return src.startsWith('http') ? src : ''
}

/**
 * 清理格式
 * @param contentStr {String} 富文本内容
 */
function clearIllegalBeforeSave(contentStr) {
  if (!contentStr) return contentStr
  // 清除灯泡、改写中的状态 删除空标签， 替换换行
  return contentStr
    .replace(/<p(.+?)?class="g-bulb handling"(.+?)?>/, '<p$1$2>')
    .replace(/<p(.+?)?class="handling g-bulb"(.+?)?>/, '<p$1$2>')
    .replace(/<([a-z]+?)(?:\s+?[^>]*?)?>\s*?<\/\1>/gi, '')
    .replace(/(<u .*?>)/gi, '')
    .replace(/<\/u>/gi)
}

function newKeywordDict(text) {
  return {
    text,
    isSelected: false,
    isDeleted: false,
    width: null,
  }
}

/**
 * @params {Any} VueInstance
 * @params {Function} trigger 在目标元素上触发
 * @params {Function} triggerOnOther 在其他元素上触发
 */
function onWrongWordMouseover(
  that,
  trigger = () => {},
  triggerOnOther = () => {},
  targetElemClass = 'to-correct-hint'
) {
  return ev => {
    const target = ev.target
    if (!target) return
    if (target && !target.classList.contains(targetElemClass))
      return triggerOnOther(target, ev)
    trigger(target, ev)
  }
}

let _editorElem = null

function getEditorElem() {
  if (_editorElem) return _editorElem
  const EditorTag = '.editor-container-box'
  _editorElem = document.querySelector(EditorTag)
  return _editorElem
}
</script>

<style lang="scss" scoped>
::v-deep .buy-fixed-modal{
  top: 0px;
}

// scss分组说明： svg icon 管理<SvgIcon>的class
.article-composing {
  .icon-24px {
    font-size: 24px;
  }
  .icon-18px {
    width: 18px;
    height: 18px;
  }
}
</style>

<style lang="scss" scoped>
@import '~/assets/page-style-scss/compose/page-compose.scss';
@import '~/assets/page-style-scss/common-variable.scss';

.np-detection-btn {
  position: relative;
  .detection-complete-icon {
    position: absolute;
    left: 110px;
    top: -18px;
    font-size: 33px;
  }
}

.el-dropdown-link {
  color: #1e1e1e;
  padding: 0px 15px 0px 30px;
  cursor: pointer;
}

.article-composing {
  overflow-y: hidden;
  .full-page-content {
    height: calc(100vh - 52px);
    margin-top: 52px;
  }
  .gt-modal-wrapper {
    z-index: 2001;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    overflow: auto;
    margin: 0;
    display: flex;
    align-items: center;
    background: rgba(0, 0, 0, 0.3);
    overflow-y: auto;
    padding-top: 30px;
    .note-preview-dialog {
      z-index: 2002;
      position: relative;
      margin: 0 auto;
      border-radius: 2px;
      -webkit-box-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
      box-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
      box-sizing: border-box;
      width: 50%;
      max-width: 500px;
      min-width: 400px;
      background: #fff;
      max-height: 800px;
      min-height: 200px;
      height: 80%;
      padding-top: 20px;
      overflow: auto;
      .gt-icon-close {
        position: absolute;
        right: 10px;
        top: 10px;
        cursor: pointer;
      }
      .gt-modal-header {
        text-align: center;
      }
      .note-preview-content {
        width: 100%;
        padding: 20px;
        height: calc(100% - 80px);
        overflow: auto;
        img {
          max-width: 100%;
        }
      }
    }
  }
}

.guide-box {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  color: #1e1e1e;
  z-index: 1500;
  overflow: hidden;

  .guide-content-box {
    display: flex;
    align-items: center;
    width: fit-content;
    margin-top: $HEADER_HEIGHT;
    z-index: 10000;
    .guide-hot-box {
      width: 70px;
      height: calc(100vh - 52px);
      margin-right: 30px;
      border-radius: 6px;
      box-shadow: 0px 0px 0px 10000px rgba(0, 0, 0, 0.5);
    }
    .guide-know-box {
      position: relative;
      background: #ffffff;
      border-radius: 4px;
      padding: 14px;
      max-width: 300px;
      &::before {
        position: absolute;
        left: -20px;
        top: 50%;
        transform: translateY(-50%);
        width: 0;
        height: 0;
        content: '';
        border-color: transparent #fff transparent transparent;
        border-style: solid;
        border-width: 10px;
      }
      &.shadow {
        box-shadow: 0px 0px 0px 10000px rgba(0, 0, 0, 0.5);
        &::before {
          left: auto;
          right: -20px;
          border-color: transparent transparent transparent #fff;
        }
      }
      .guide-close {
        position: absolute;
        right: 10px;
        top: 10px;
        padding: 4px;
        cursor: pointer;
      }
      .guide-know-box-top {
        display: flex;
        align-items: center;
        margin-bottom: 14px;
        .guide-icon {
          width: 38px;
          margin-right: 6px;
        }
        .guide-title {
          font-size: 16px;
          color: #1e1e1e;
          letter-spacing: 0.26px;
          line-height: 16px;
        }
      }
      .guide-content {
        margin-bottom: 14px;
      }
      .guide-btn {
        width: fit-content;
        margin: 0 auto;
        color: #fff;
        padding: 8px 20px;
        border-radius: 4px;
        background: #4a25ba;
        user-select: none;
        cursor: pointer;
      }
    }
  }
}

.float-right {
  .el-popover {
    margin-top: 20px;
  }
}

.export-note {
  margin-left: 10px;
}

//实验室
.laboratory-content {
  margin-right: -37px;
  position: relative;
  cursor: pointer;
  .laboratory-dropdown {
    position: absolute;
    background: white;
    width: 80px;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    padding: 5px 0px;
    top: 45px;
    left: -15px;
    .item {
      color: black;
      padding: 2px 2px;
      text-align: center;
      line-height: 35px;
      &:hover {
        background: #dbd3f1;
        cursor: pointer;
      }
    }
    &::after {
      position: absolute;
      content: '';
      top: -10px;
      left: 38%;
      border-left: 10px solid transparent;
      border-right: 10px solid transparent;
      border-bottom: 10px solid white;
    }
  }
}

// 禁用展开按钮
.noOpenArticle {
  pointer-events: none;
  cursor: default;
}

.flex-content {
  .popover-title {
    font-size: 20px;
    color: #1e1e1e;
    letter-spacing: 0.26px;
    line-height: 30px;
    margin-bottom: 10px;
    font-weight: bold;
  }
  .popover-content {
    text-align: left;
    font-size: 14px;
    color: #1e1e1e;
    letter-spacing: 0.2px;
    line-height: 24px;
    text-indent: 2em;
    margin: 10px 0px;
    &.purple {
      color: #4a25ba;
    }
  }
}

.popover-box {
  .popover-title {
    font-size: 16px;
    color: #1e1e1e;
    letter-spacing: 0.26px;
    line-height: 16px;
    margin-bottom: 10px;
  }
  .popover-content {
    font-size: 12px;
    color: #1e1e1e;
    letter-spacing: 0.2px;
    line-height: 24px;
    text-indent: 2;
    &.purple {
      color: #4a25ba;
    }
  }
  .popover-confirm {
    float: right;
    background: #4a25ba;
    color: #fff;
    width: 50px;
    padding: 4px 10px;
    margin-top: 10px;
    text-align: center;
    user-select: none;
    cursor: pointer;
  }
}

.btn-errorrnum {
  position: absolute;
  right: -15px;
  top: 0px;
  width: 22px;
  height: 22px;
  background: red;
  color: #fff;
  border-radius: 20px;
  line-height: 22px;
  text-align: center;
  font-size: 12px;
}

p {
  word-break: break-word;
}

::v-deep .el_scrollBar_get {
  flex-shrink: 0;
  /* position: relative; */
  /* height: 100vh; */
  /* overflow-y: auto; */
  overflow-x: hidden;
  transition: width 0.4s ease-in-out;
  box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.1);
  transition: 0.4s ease-in-out all;
  background-color: white;
  z-index: 998;
  // padding-left: 70px;
  .el-scrollbar__wrap {
    margin-right: 0px !important; /* if we need columnleft scroll */
    overflow-y: auto;
    overflow-x: hidden;
  }
}

::v-deep .el-dialog--center .el-dialog__header {
  padding: 20px 20px 10px;
}

::v-deep .cneterisDialog .el-dialog--center {
  margin-top: 30vh !important;
}

::v-deep .el-dialog__header {
  padding: 20px 20px 10px;
  color: #000;
  font-weight: bold;
}

::v-deep .el-dialog--center .el-dialog__body {
  text-align: center;
  line-height: 25px;
  font-size: 16px;
  color: #000;
  width: 80%;
  margin: auto auto;
}

.suspended-frame {
  position: absolute;
  top: 100px;
  left: 900px;
  z-index: 2000;
  min-width: 312px;
  display: flex;
  align-items: center;
  background: #080808;
  box-shadow: 0 4px 10px 0 rgba(0, 0, 0, 0.1);
  border-radius: 3px;
  &.suspended-frame-rewrite {
    min-width: 80px;
    .suspended-frame-item-extend {
      width: 100%;
    }
  }
  .suspended-pointer {
    content: '';
    position: absolute;
    top: 100%;
    left: calc(50% - 3px);
    width: 0px;
    height: 0px;
    border: 6px solid #080808;
    border-top-color: #080808;
    border-bottom-color: transparent;
    border-right-color: transparent;
    border-left-color: transparent;
  }

  // 使用在顶部定位
  &.useRect {
    transform: translateX(-50%) translateY(-130%);
  }

  .suspended-frame-item {
    height: 40px;
    padding: 3px 8px;
    font-size: 16px;
    color: #ffffff;
    letter-spacing: 0.15px;
    line-height: 36px;
    text-align: center;
    cursor: pointer;
    white-space: nowrap;
    border-right: 1px solid #ccc;
    &:hover {
      background: #4a19bd;
    }
    &:first-child {
      border-radius: 3px 0px 0px 3px;
    }
    &:last-child {
      border-radius: 0px 3px 3px 0px;
      border-right: 0px;
    }
    &.last {
      border-right: 0px;
    }
  }
  .suspended-frame-line {
    width: 1px;
    height: 100%;
    background-color: #ccc;
  }
}

::v-deep .icon_historical {
  font-size: 12px;
  color: #909399;
  letter-spacing: 0.3px;
  text-align: right;
  line-height: 52px;
  float: left;
  height: 52px;
  padding-left: 15px;
  padding-right: 20px;
  cursor: pointer;
  &:hover {
    color: #4a25ba;
  }
  span {
    font-family: PingFangSC-Medium;
    font-size: 12px;
    color: #606266;
    letter-spacing: 0.23px;
  }
}

.userSelect {
  user-select: none;
}

::v-deep .el-dialog--center {
  .el-dialog__body {
    border-bottom: 1px solid #f0f2fc;
    border-top: 1px solid #f0f2fc;
    width: 100%;
  }
}

::v-deep .disableStyle {
  color: #fff;
  background-color: #a592dd;
  border-color: #a592dd;
  cursor: not-allowed;
  &:hover,
  &:focus,
  &:active {
    color: #fff;
    background-color: #a592dd;
    border-color: #a592dd;
    cursor: not-allowed;
  }
}
</style>
