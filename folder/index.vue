<template>
  <div
    id="knowledge"
    @dragover.stop.prevent="removeFolderItemHover"
  >
    <UADetective />

    <div
      class="guide-box"
      v-if="guide"
      @click="guide = false"
    >
      <div class="img-box">
        <div class="guide-know">知道了</div>
        <img
          class="guide-img"
          :src="Img.iconGuideNote"
          alt=""
        />
      </div>
    </div>

    <PageHeader
      bgcolor="#4A25BA"
      color="#9791AA"
      selected-color="#FFFFFF"
      @loginSuccess="init()"
    />

    <VipTimeHint
      v-if="isShowVipTimeHint"
      :company="selectedCompany"
      @close="isShowVipTimeHint = false"
    />

    <div
      class="content"
      @click="hidePopup"
    >

      <div class="content-main no-select clearfix">
        <!-- 左边侧边栏 -->
        <div class="col-folder-list no-scroll-bar">
          <!--主页 -->
          <div
            class="folder-nav-item home-panel"
            @click.stop="homeClick"
            :class="{ selected: !$route.query.mode || $route.query.mode === 'home' }"
          >
            <nav class="nav-1">
              <div class="nav-1-icon"></div>
              <span class="nav-1-title">主页</span>
            </nav>
          </div>
          <div class="split-pb">
            个人
          </div>
          <!--全部文章-->
          <div
            @click.stop="allClick"
            :class="{ selected: $route.query.mode === 'all' }"
            class="folder-nav-item all-panel"
          >
            <nav
              @mouseenter="allPanelImg.status = 1"
              @mouseleave="allPanelImg.status = 0"
              class="nav-1"
            >
              <img :src="handleAllPanelImg" />&nbsp;
              <span class="nav-1-title">全部文章</span>
            </nav>
          </div>
          <hr class="folder-nav-hr" />
          <!-- 我的文集集 -->
          <div
            class="folder-nav-item folder-panel"
            :class="{
              selected: $route.query.mode === 'myarticle',
            }"
          >
            <div
              class="nav-1"
              :class="{ unfold: isFoldersFold }"
              @click="unfoldFolders()"
            >
              <i
                class="el-icon-caret-right"
                :class="{
                  unfold: isFoldersFold,
                }"
              ></i>
              <div class="nav-1-icon"></div>
              <span class="folders-collection-name">我的文集</span>
              <div
                class="folder-add-btn"
                @click.stop="ncModal.show = 1"
              ></div>
            </div>
            <div
              class="folder-list"
              ref="dragover-folder-list"
              :class="{ unfold: !isFoldersFold }"
            >
              <div
                :index="folder.uid"
                class="folder-item clickable no-select cl"
                v-for="folder in kngFolder.list"
                :class="{
                  selected: folder.uid === classifyNow.uid,
                  'light-up': folder.hover,
                }"
                @mouseover="
                  folder => {
                    folder.hover = true;
                  }
                "
                @mouseleave="folder.hover = false"
                :key="folder.uid"
                @dragover.stop.prevent="onDragOver($event, folder)"
                @drop.stop.prevent="onDrop($event, folder)"
                @click="chooseFolder(1, folder)"
              >
                <div class="fl folder-icon"></div>
                <div class="fl w85 dragcolor">
                  <span>{{ folder.uname }}</span>
                </div>
                <!-- 删除文集 -->
                <div
                  class="fr folder-del-btn"
                  v-if="folder.uid !== '0'"
                >
                  <span @click="showPopup(MODAL_TYPE_REMOVE_FOLDER, folder)">
                    <i class="el-icon-error"></i>
                  </span>
                </div>
              </div>
            </div>
          </div>
          <hr class="folder-nav-hr" />
          <div
            class="folder-nav-item collection-panel"
            @click="privateLibraries"
            :class="{ selected: $route.query.mode === 'privateLibrary' }"
          >
            <nav class="nav-1">
              <div class="nav-1-icon"></div>
              <span class="nav-1-title">素材收藏</span>
            </nav>
          </div>
          <hr class="folder-nav-hr" />
          <div
            ref="nav-recycle-bin"
            class="folder-nav-item recycle-bin"
            :class="{ selected: $route.query.mode === 'recycleBin' }"
            @click="changeRecyclebin"
            @dragover.stop.prevent="onDragOverDelete($event)"
            @drop.stop.prevent="onDropDelete($event)"
          >
            <nav class="nav-1">
              <div class="nav-1-icon"></div>
              <span class="nav-1-title">回收站</span>
            </nav>
          </div>
          <!-- <div class="split-pb">
            企业&nbsp;<img
              :src="resources.changeBusinessImg"
              style="cursor: pointer;"
              @click.stop="showBusinessSelect"
              class="el-icon-refresh"
              v-if="isShowChangeBusinessIcon"
            />
          </div> -->
          <!--企业-->
          <!-- <div class="business-wrapper"> -->
            <!--未申请时显示的菜单-->
            <!-- <template v-if="applyStatus === 0 || applyStatus === 1 || applyStatus === 3">
              <div
                v-for="item in businessMenus.stepOne"
                :key="item.code"
                @click.stop="businessMenuItemClick(item)"
                class="menu-item"
              >
                <div
                  :class="{ selected: item.code === $route.query.mode }"
                  class="item-wrapper create"
                >
                  <div class="item-img"><img :src="item.img" /></div>
                  <div class="item-text">{{item.name}}</div>
                  <svg
                    width="44px"
                    height="19px"
                    viewBox="0 0 44 19"
                    version="1.1"
                    xmlns="http://www.w3.org/2000/svg"
                    xmlns:xlink="http://www.w3.org/1999/xlink"
                  >
                    <defs>
                      <linearGradient
                        x1="-1.11022302e-14%"
                        y1="50%"
                        x2="100%"
                        y2="50%"
                        id="linearGradient-1"
                      >
                        <stop
                          stop-color="#F97A71"
                          offset="0%"
                        ></stop>
                        <stop
                          stop-color="#E74445"
                          offset="100%"
                        ></stop>
                      </linearGradient>
                    </defs>
                    <g
                      id="上线第一版"
                      stroke="none"
                      stroke-width="1"
                      fill="none"
                      fill-rule="evenodd"
                    >
                      <g
                        id="画板"
                        transform="translate(-77.000000, -63.000000)"
                      >
                        <g
                          id="免费领取"
                          transform="translate(77.000000, 63.000000)"
                        >
                          <path
                            d="M8,0 L36,0 C40.418278,-8.11624501e-16 44,3.581722 44,8 C44,12.418278 40.418278,16 36,16 L3.14285714,16 L3.14285714,16 L0,19 L0,8 C-5.41083001e-16,3.581722 3.581722,8.11624501e-16 8,0 Z"
                            id="矩形"
                            fill="url(#linearGradient-1)"
                          ></path>
                          <text
                            id="NEW"
                            font-family="PingFangSC-Medium, PingFang SC"
                            font-size="10"
                            font-weight="400"
                            letter-spacing="0.19"
                            fill="#FFFFFF"
                          >
                            <tspan
                              x="10"
                              y="12"
                            >NEW</tspan>
                          </text>
                        </g>
                      </g>
                    </g>
                  </svg>
                </div>
              </div>
            </template> -->

            <!--申请通过显示的菜单-->
            <!-- <div
              v-if="applyStatus === 2"
              class="business-menu"
            >
              <template v-for="(item, index) in businessMenus.stepTwo">
                <div
                  v-if="!item.isLevelTwo && item.isShow"
                  :key="`d1_${item.code}`"
                  class="menu-item"
                  @click.stop="businessMenuItemClick(item)"
                >
                  <div
                    :class="{ selected: item.code === $route.query.mode }"
                    class="item-wrapper"
                  >
                    <div
                      :class="{ 'recycle-item-img': item.code === 'b_recycle' }"
                      class="item-img"
                    ><img :src="item.img" /></div>
                    <div class="item-text">{{item.name}}</div>
                  </div>
                </div> -->
                <!--二级菜单-->
                <!-- <div
                  :key="`d1_${item.code}`"
                  v-if="item.isLevelTwo && item.isShow"
                  class="menu-item"
                >
                  <div
                    :class="{ selected: item.code === $route.query.mode }"
                    class="item-wrapper item-wrapper-left-right"
                    @click.stop="businessMenuItemClick(item)"
                  >
                    <div class="left">
                      <i
                        class="el-icon-caret-right"
                        :class="{unfolder: item.isExpand}"
                      />
                      <div class="item-img"><img :src="item.img" /></div>
                      <div class="item-text">{{item.name}}</div>
                    </div>
                    <div v-if="item.canEditFolder" class="right">
                      <img
                        @mouseenter="item.isHover = true"
                        @mouseleave="item.isHover = false"
                        @click.stop="showNewFolderModal"
                        :src="item.isHover ? item.rightImgHover : item.rightImg"
                      />
                    </div>
                  </div>
                  <div
                    v-show="item.isExpand"
                    class="item-dropdown"
                  >
                    <div
                      @click.stop="businessFolderClick(folder)"
                      v-for="folder in item.list"
                      :key="folder.uid"
                      class="item-wrapper item-wrapper-left-right item-wrapper-left-right-hover"
                      :class="{ selected: $route.query.b_folderuid === folder.uid && $route.query.mode === 'b_note' }"
                    >
                      <div class="left">
                        <div class="item-img"><img :src="folder.img" /></div>
                        <div class="item-text">{{folder.uname}}</div>
                      </div>
                      <div v-if="item.canEditFolder" class="right">
                        <i
                          @click.stop="deleteFolderBusiness(folder)"
                          v-if="folder.uid !== '0'"
                          class="el-icon-error"
                        />
                      </div>
                    </div>
                  </div>
                </div>
                <div
                  v-if="index !== businessMenus.length - 1 && item.isShow"
                  :key="`d2_${item.code}`"
                  class="split-line"
                ></div>
              </template>
            </div>
          </div> -->
          <!-- <div class="use-brief"><a target="_blank" href="https://mp.weixin.qq.com/s/_T7PpQKv3b1AFN74K4cAbA">使用简介</a></div> -->
          <div
            class="btn-folder-list-backto-top"
            v-show="isFolderListBacktoTopButtonShow"
            @click="folderListBacktoTop"
          >
            <div class="arrow">↑</div>
            <div class="text">回顶部</div>
          </div>
          <!-- 活动 -->
           <!--<img width="190px" src="https://static.getgetai.com/writer-poster/super-weekend-activtiy-mini.png" alt="" srcset="" @click="showActivity">-->
        </div>
        <div
          class="col-card-list clearfix no-scroll-bar"
          ref="scroll-component"
          :class="{
            'loading-data': isLoadingNote || isDragging,
          }"
        >
          <transition name="fade">
            <div
              v-if="false"
              class="line-img"
            >
              <nuxt-link
                id="jumpCode"
                style="display: none"
                to="/m?receivesecret=ed317bdab4b2471a9c1ab1b42ad383c3&codeType=1&from=&ticket_code=1X8AEB"
                target="_blank"
              ></nuxt-link>
              <img
                @click.stop="jumpCode"
                src="~/assets/img/activity/epidemic_line.jpg"
              />
              <div>
                <div
                  class="close"
                  @click.stop="lineImgClose"
                >
                  <svg
                    width="10px"
                    height="10px"
                    viewBox="0 0 13 13"
                  >
                    <g
                      id="领域选择调整"
                      stroke-width="1"
                      fill="#ffffff"
                    >
                      <g
                        data-v-3fd0f2ea=""
                        id="发现领域选择调整"
                        transform="translate(-6178.000000, -1894.000000)"
                        stroke="black"
                        stroke-width="2"
                      >
                        <g
                          data-v-3fd0f2ea=""
                          id="分组-2备份-10"
                          transform="translate(6179.000000, 1895.000000)"
                        >
                          <path
                            data-v-3fd0f2ea=""
                            d="M0,0 L11,11 L0,0 Z"
                            id="Line-2-Copy-2"
                          ></path>
                          <path
                            data-v-3fd0f2ea=""
                            d="M0,11 L11,0 L0,11 Z"
                            id="Line-2-Copy-3"
                          ></path>
                        </g>
                      </g>
                    </g>
                  </svg>
                </div>
              </div>
            </div>
          </transition>
          <!-- 搜索框 -->
          <FolderSearch
            v-if="$route.query.mode === 'myarticle' || $route.query.mode === 'all'"
            class="folder-search"
            @openArticleSet="openArticleSet"
            @gotoNoteEditDetail="gotoNoteEditDetail"
          />
          <!--主页-->
          <Home
            @deleteNote="deleteNote"
            @gotoNoteEditDetail="gotoNoteEditDetail"
            @showPurchaseModal="showPurchaseModal"
            v-if="!$route.query.mode || $route.query.mode === 'home'"
          />

          <All
            ref="allRef"
            @deleteNote="deleteNote"
            @gotoNoteEditDetail="gotoNoteEditDetail"
            @gotoAddNote="gotoAddNote"
            @showPurchaseModal="showPurchaseModal"
            v-if="$route.query.mode === 'all'"
          />

          <BusinessCreate
            v-if="(applyStatus === 0 || applyStatus === 1 || applyStatus === 3 || !isCreatedBusiness) && $route.query.mode === 'b_create'"
            :applyStatus="applyStatus"
            :isCreatedBusiness="isCreatedBusiness"
            :company="selectedCompany"
            :userid="userid"
            @changeApplyStatus="status => {
              applyStatus = status
              isCreatedBusiness = true
            }"
          />

          <BusinessStaff
            v-if="applyStatus === 2 && $route.query.mode === 'b_staff'"
            :company="selectedCompany"
            :userid="userid"
            :baw="businessAuthorityWrapper"
          />

          <BusinessDocument
            ref="businessDocument"
            v-if="applyStatus === 2 && $route.query.mode === 'b_document'"
            :company="selectedCompany"
            :userid="userid"
            :baw="businessAuthorityWrapper"
            @gotoNoteEditDetail="gotoNoteEditDetail"
            @gotoAddNote="gotoAddNote"
            @changeUsefulValue="changeUsefulValue"
          />

          <BusinessFolder
            ref="businessFolder"
            v-if="applyStatus === 2 && $route.query.mode === 'b_note'"
            :company="selectedCompany"
            :userid="userid"
            :baw="businessAuthorityWrapper"
            :folder="curFolder"
            @gotoNoteEditDetail="gotoNoteEditDetail"
            @gotoAddNote="gotoAddNote"
            @changeUsefulValue="changeUsefulValue"
          />

          <BusinessRecycle
            ref="businessRecycle"
            v-if="applyStatus === 2 && $route.query.mode === 'b_recycle'"
            :company="selectedCompany"
            @restoreFolderSuccess="restoreFolderSuccess"
          />

          <MaskLayer v-if="isShowMaskLayer" />

          <!-- 我的文集 -->
          <FoldersPanel
            v-if="$route.query.mode === 'myarticle'"
            :folder="classifyNow"
            :notesPage="notesPage"
            :showOptionListNoteUid="showOptionListNoteUid"
            :isLoadingNote="isLoadingNote"
            :navtitle="navtitle"
            :navigationCopy="navigationCopy"
            :articleStatus="articleStatus"
            :folderObj="folderObj"
            :addStatus="addStatus"
            :removeStatus="removeStatus"
            :deleteFlase="deleteFlase"
            :isloadingClampMore="isloadingClampMore"
            :folderRemove="folderRemove"
            :isNoneStatus="isNoneStatus"
            @editFolderName="editFolderName"
            @currentDragingEventIdChild="
              e => {
                currentDragingEventId = e;
              }
            "
            @isDraggingChild="
              e => {
                isDragging = e;
              }
            "
            @draggingNoteIdChild="
              e => {
                draggingNoteId = e;
              }
            "
            @dragEndNoDropChild="dragEndNoDrop"
            @publishRecord="publishRecord"
            @deleteNote="deleteNote"
            @changeRecyclebin="changeRecyclebin"
            @handleOpen="handleOpen"
            @gotoNoteEditDetail="gotoNoteEditDetail"
            @sortClick="sortClick"
            @gotoAddNote="gotoAddNote"
            @removeFolder="
              item => {
                showPopup(MODAL_TYPE_REMOVE_FOLDER, item);
              }
            "
            @renameFolder="
              item => {
                showPopup(MODAL_FOLDER_Rename_Name, item);
              }
            "
            @showPurchaseModal="showPurchaseModal"
          ></FoldersPanel>
          <!-- 我的收藏 -->
          <FolderCollection
            v-if="$route.query.mode === 'privateLibrary'"
            @openNewly="openNewly"
            @showPurchaseModal="showPurchaseModal"
          />

          <!-- 回收站 -->
          <FoldersRecycle
            v-if="$route.query.mode === 'recycleBin'"
            :showOptionListNoteUid="showOptionListNoteUid"
            :isLoadingNote="isLoadingNote"
            :articleStatus="articleStatus"
            :folderObj="folderObj"
            :recycleFoldersObj="recycleFoldersObj"
            :recycleNotesPage="recycleNotesPage"
            :isloadingClampMore="isloadingClampMore"
            :recycleNotesPageParams="recycleNotesPageParams"
            @deleteThoroughlyNote="deleteThoroughlyNote"
            @currentDragingEventIdChild="
              e => {
                currentDragingEventId = e;
              }
            "
            @isDraggingChild="
              e => {
                isDragging = e;
              }
            "
            @draggingNoteIdChild="
              e => {
                draggingNoteId = e;
              }
            "
            @dragEndNoDropChild="dragEndNoDrop"
            @deleteNote="deleteNote"
            @loadRecycleNotesPage="loadRecycleNotesPage"
            @recovery="recovery"
            @restoreFolder="restoreFolder"
            @deleteFolder="deleteFolder"
            @removeFolder="
              item => {
                showPopup(MODAL_TYPE_REMOVE_FOLDER, item);
              }
            "
          ></FoldersRecycle>
          <div
            class="bottom-mark"
            ref="bottom-mark"
          ></div>
        </div>
      </div>
    </div>

    <ModalPublishRecord
      v-if="showPublishRecord"
      :note="publishRecordNote"
      @close="showPublishRecord = false"
    />

    <!-- 新建文集 -->
    <NewFolderModal
      :ncModal="ncModal"
      @modalClose="modalClose"
      @folderRefresh="loadNoteClassify"
    ></NewFolderModal>
    <!-- 重命名文集 -->
    <RenameFolderModal
      :ncModal="ncModal"
      :userRename="userRename"
      @modalClose="modalClose"
      @folderRefresh="loadNoteClassify"
      @modifiedSuccess="modifiedSuccess"
    ></RenameFolderModal>

    <client-only>
      <div
        class="nc-popup-box-bg"
        v-show="ncModal.show"
        @click="
          ncModal.show = MODAL_TYPE_CLOSE;
          erwei = '';
          shareAction = true;
          urlPath = '';
        "
      ></div>

      <!-- 新增知识夹 -->

      <!-- 分享知识夹 -->
      <div
        class="nc-popup-box share"
        v-show="ncModal.show === MODAL_TYPE_SHARE_FOLDER"
      >
        <div class="nc-popup-title-wrapper">
          <div
            class="nc-popup-title"
            :class="{ active: !shareAction }"
            @click="(shareAction = false), shareFriend(1, ncModal.m)"
          >
            分享给好友
          </div>
          <div
            class="nc-popup-title"
            :class="{ active: shareAction }"
            @click="(shareAction = true), shareQR(SHARE_TYPE_FOLDER, ncModal.m)"
          >
            生成二维码
          </div>
        </div>
        <img
          class="nc-popup-erwei"
          v-show="!erwei"
          src="https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/img/tool/loading.gif"
          alt=""
        />
        <img
          class="nc-popup-erwei"
          v-show="erwei"
          :src="erwei"
          alt=""
        />
        <div class="nc-popup-bottom">
          <img
            class="nc-popup-hint"
            src="https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/img/tool/share-hint.png"
            alt=""
          />
          <span class="nc-popup-content">扫码登录微信分享</span>
        </div>
      </div>
      <!-- 分享路径 -->
      <div
        class="nc-popup-box path"
        v-show="ncModal.show === MODAL_TYPE_COPY_PATH"
      >
        <input
          id="nc-popup-path-id"
          class="nc-popup-path"
          v-model="urlPath"
          placeholder="正在生成"
        />
        <div class="nc-popup-toolbar">
          <div
            class="nc-popup-button"
            @click.stop="copyUrl()"
          >复制路径</div>
          <div
            class="nc-popup-button cancel"
            @click="
              ncModal.show = 0;
              urlPath = '';
            "
          >
            取消
          </div>
        </div>
      </div>

      <!-- 删除知识夹 -->
      <div
        class="getui-popup-box delete"
        v-show="ncModal.show === MODAL_TYPE_REMOVE_FOLDER"
      >
        <!-- <div class="img-bg">
          <img class="getui-popup-image" src="https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/img/tool/delete-blue.png" alt="">
        </div> -->
        <div class="getui-popup-title-delete">删除文集</div>
        <div class="getui-popup-content delete-style">
          此文集中的任何文章都将被移到回收站中
        </div>

        <div
          class="getui-popup-toolbar"
          v-show="ncModal.focused"
        >
          <div
            class="getui-popup-button"
            @click="
              ncModal.show = 0;
              ncModal.m.uid = '';
            "
          >
            取消
          </div>
          <div
            class="getui-popup-button highlight"
            @click.stop="deleteNc()"
          >
            确认删除
          </div>
        </div>
      </div>

      <!-- 彻底删除知识夹 -->
      <!-- <div
        class="getui-popup-box delete"
        v-show="ncModal.show === MODAL_FOLDER_DETAIL"
      >
        <div class="getui-popup-title-delete">彻底删除</div>
        <div class="getui-popup-content delete-style">
          即将彻底删除该我的文集<br />且删除后将无法恢复操作！
        </div>

        <div class="getui-popup-toolbar" v-show="ncModal.focused">
          <div class="getui-popup-button" @click.stop="deleteFile()">确认</div>
          <div
            class="getui-popup-button highlight"
            @click="
              ncModal.show = 0;
              ncModal.m.uid = '';
            "
          >
            取消
          </div>
        </div>
      </div> -->

      <!-- 知识夹更多信息 -->
      <div
        class="nc-popup-box more"
        v-show="ncModal.show === MODAL_TYPE_FOLDER_DETAIL"
      >
        <img
          class="nc-popup-image"
          src="https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/img/tool/note-blank.png"
          alt=""
        />
        <div class="nc-popup-count">{{ ncModal.m.count || 0 }}篇笔记</div>
        <input
          class="nc-popup-i"
          type="text"
          v-model="ncModal.m.uname"
          placeholder="我的文集名称"
        />
        <div class="nc-popup-info">
          <div class="nc-popup-info-title">基本信息</div>

          <div class="nc-popup-info-lable">
            <span style="color:#999;">
              创建时间：
            </span>
            {{ ncModal.m.createTime | toLocalDate }}
          </div>
          <div class="nc-popup-info-lable">
            <span style="color:#999;">
              修改时间：
            </span>
            {{ ncModal.m.updateTime | toLocalDate }}
          </div>
          <div class="nc-popup-info-title">公开状态</div>
          <div class="nc-popup-info-private">
            <div class="popup-private-title">
              {{ ncModal.m.isPrivate ? '不公开' : '公开' }}
            </div>
            <div
              class="popup-private-content"
              @click="changePrivate"
            >
              <div
                class="g-switch-outter"
                :class="{ active: ncModal.m.isPrivate !== 1 }"
              >
                <div
                  class="g-switch-inner"
                  :class="{ active: ncModal.m.isPrivate !== 1 }"
                ></div>
              </div>
            </div>
          </div>
        </div>
        <div
          class="nc-popup-toolbar"
          v-show="ncModal.focused"
        >
          <div
            class="nc-popup-button"
            @click.stop="updateFolderInfo"
          >保存</div>
          <div
            class="nc-popup-button cancel"
            @click="
              ncModal.show = 0;
              ncModal.m = {};
            "
          >
            取消
          </div>
        </div>
      </div>
    </client-only>
    <!-- <PurchaseModal
      :show="isShowPurchaseModal"
      @close="closePurchaseModal"
    /> -->

    <!-- <FunctionHintChain
      :show="isShowPurchaseModal"
      @close="closePurchaseModal"
    /> -->

    <ImportArticleModal
      :isShow="isShowArticleImportModal"
      :importType="showWhichModal"
      :classifyId="classifyId"
      @callback="importArticleCallback"
      @close="closeImportArticleModal"
    />

    <BusinessSelect
      :show="isShowBusinessSelect"
      :companyList="companyList"
      :company="selectedCompany"
      :userid="userid"
      :isCreatedBusiness="isCreatedBusiness"
      @close="isShowBusinessSelect = false"
      @changeSuccess="changeCompanySuccess"
    />
    <BusinessNewFolder
      :show="isShowAddFolder"
      :company="selectedCompany"
      @close="isShowAddFolder = false"
      @success="addFolderSuccess"
    />

    <!-- 活动弹窗 -->
    <!-- <img
      @click="mayDayBtnClick"
      style="position: fixed; bottom: 40px; left: 20px; z-index: 9999; width: 100px; cursor: pointer;"
      src="https://static.getgetai.com/tool/aiwriter/activity/20210615/share-tiny.png" /> -->
    <!-- <PromotionModal v-if="activityDialog.show" @close="activityDialog.show = false" /> -->
  </div>
</template>
<script>
import { mapGetters } from 'vuex';
import ImgFolder from '../../assets/img/folder/index.js';
import { PostAsFormTheSpaWay } from '../../api/client/Req.js';
import { findParentNode } from '../../utils/draggable/index.js';
import { scrollIntoViewSmoothly } from '../../utils/scroll/scrollIntoView';
import {
  GETNoteRecycleList,
  POSTrecyclePage,
  GETclassifyRecycleList,
  POSDeletePage,
  GETnoteFileDelete,
  POSRestore,
  POSTrecycleClassifies,
  GETclassifyRestore,
  GETclassifyClassifies,
} from '../../api/client/aiWriter/index';
import {
  MODAL_TYPE_CLOSE,
  MODAL_TYPE_ADD_FOLDER,
  MODAL_TYPE_SHARE_FOLDER,
  MODAL_TYPE_REMOVE_FOLDER,
  MODAL_TYPE_FOLDER_DETAIL,
  MODAL_TYPE_COPY_PATH,
  MODAL_FOLDER_DETAIL,
  MODAL_FOLDER_Rename_Name,

  // NOTE Operate
  NOTE_OPERATE_OPEN_DETAIL,
  NOTE_OPERATE_OPEN_EDITOR,
  SHARE_TYPE_FOLDER,
  SHARE_TYPE_NOTE,
} from '../../store/enum.js';
import PromotionModal from "~/components/PromotionModal/index";
import { companyList, getBusinessFolder, deleteFolderToRecycle, setDefaultCompany } from '~/api/client/business'

import businessAuthorityWrapper from '~/utils/authority/BusinessAuthorityWrapper'

import PageHeader from '~/components/Header.vue';
// import NoteCard from '~/components/Card/NoteCard.vue'
// 拆分
// import FolderClamp from "~/components/FolderClamp/index";
import FoldersPanel from '~/components/folder/FoldersPanel.vue';
import FoldersRecycle from '~/components/folder/FoldersRecycle.vue';

import FolderSearch from '~/components/FolderClamp/folderSearch';
import FolderCollection from '~/components/FolderClamp/folderCollection';
import * as NoteApi from '~/api/client/Note.js';

// filter
import FilterBgimg from '~/utils/filter/backgroundImage.js';
import FilterList from '~/utils/filter/index.js';

import vipHelper from '~/utils/common/VipHelper'

// static
import iconAddArticle from '~/plugins/svgIcon/svg/add.svg';

import memberSvg from '~/assets/img/business/member.svg'
import noteSvg from '~/assets/img/business/note.svg'
import documentImg from '~/assets/img/business/document_icon.png'
import recycleImg from '~/assets/img/business/recycle.png'

//import deleteSvg from '~/assets/img/business/delete.svg'
import folderImg from '~/assets/img/business/folder.png'

import changeBusinessImg from '~/assets/img/business/change-business.png'

import { EventBus } from '~/components/eventbus'

// for relacement

// import {
//   GetNoteClassifyList
// } from '~/api/client/Note.js'

// import { clearTempNoteIdIfMatch } from '~/api/clientStorage/index.js';

import iconNewFolder from '~/plugins/svgIcon/svg/new-folder.svg';
// import RegexUtil from "~/utils/temp/RegexUtil.js"

// ENUM

import ModalPublishRecord from '~/components/ComposePage/PublishColumn/Record';
import UADetective from '~/components/uaDetective/index.vue';
import { ShareErwei, ShareMyShare } from '~/api/client/User';

// import GtSvg from "~/components/Icon/GtSvg";
// import DtSvg from '~/components/Icon/DtSvg'
import NewFolderModal from '~/components/FolderClamp/NewFolderModal';
import RenameFolderModal from '~/components/FolderClamp/RenameFolderModal';

//import PurchaseModal from '~/components/PurchaseModalNew'
//import FunctionHintChain from '~/components/purchase/FunctionHintChain'

import Home from '~/components/folder/home'
import All from '~/components/folder/all'

import BusinessCreate from '~/components/folder/business/create'
import BusinessStaff from '~/components/folder/business/staff'
import BusinessDocument from '~/components/folder/business/document'
import BusinessSelect from '~/components/folder/business/BusinessSelect'
import BusinessFolder from '~/components/folder/business/folder'
import BusinessNewFolder from '~/components/folder/business/folder/NewFolder'
import BusinessRecycle from '~/components/folder/business/recycle'
import VipTimeHint from '~/components/folder/business/VipTimeHint'
import MaskLayer from '~/components/folder/business/MaskLayer'


import ImportArticleModal from '~/components/folder/business/document/ImportArticleModal'

import { checkVersionUpdate } from '~/api/client/aiWriter'

let lastFolderElemOver = '';

let lastFolderElemOverFolder = {};
const DRAG_OVER_TAG = 'drag-over';
const FOLDER_ITEM_TAG = 'folder-item';
const RECYCLE_BIN_TAG = 'recycle-bin';
const NOT_DOM_ELEM = 'NOT_DOM_ELEM';
const DRAGGING_ELEM_TAG = 'DRAGGING_ELEM';

function globalOnOneFolderOver (ev, folder) {
  let target = ev.target;
  ev.dataTransfer.dropEffect = 'move';

  // 找出父级 知道 folderItem
  if (!isFolderElem(target)) {
    target = findParentNode(target, isFolderElem);
  }

  if (target === NOT_DOM_ELEM) return;

  if (lastFolderElemOver === target) {
  } else {
    target.classList.add(DRAG_OVER_TAG);

    if (lastFolderElemOver) lastFolderElemOver.classList.remove(DRAG_OVER_TAG);
    lastFolderElemOver = target;
    lastFolderElemOverFolder = folder;
  }
}

/**
 * 回收站 drag hover 事件
 */
function recycleBinDragOver (ev) {
  let target = ev.target;
  ev.dataTransfer.dropEffect = 'move';

  // 找出父级 知道 folderItem
  if (!isRecycleBinElem(target)) {
    target = findParentNode(target, isRecycleBinElem);
  }

  if (target === NOT_DOM_ELEM) return;
  target.classList.add(DRAG_OVER_TAG);
}

function globalDraggingEnd () {
  // console.log('lastFolderElemOver', lastFolderElemOver);
  if (lastFolderElemOver) {
    lastFolderElemOver.classList.remove(DRAG_OVER_TAG);
    // 为了drop事件不兼容的问题
    // lastFolderElemOver = ''
  }
}

/**
 */
function isFolderElem (elem) {
  try {
    if (elem) {
      return elem.classList.contains(FOLDER_ITEM_TAG);
    }
  } catch (err) {
    console.log(err);
    return NOT_DOM_ELEM;
  }
  return NOT_DOM_ELEM;
}
/** 查找回收站导航 */
function isRecycleBinElem (elem) {
  try {
    if (elem) {
      return elem.classList.contains(RECYCLE_BIN_TAG);
    }
  } catch (err) {
    console.log(err);
    return NOT_DOM_ELEM;
  }
  return NOT_DOM_ELEM;
}

function dragendPointInItem (point) {
  // 没有拖到文件里
  if (!lastFolderElemOver) return false;
  if (!lastFolderElemOver.getBoundingClientRect) return false;

  const rect = lastFolderElemOver.getBoundingClientRect();
  lastFolderElemOver = '';
  const { left, right, top, bottom } = rect;
  if (point.x < left || point.x > right || point.y < top || point.y > bottom) {
    return false;
  }
  const folder = lastFolderElemOverFolder;
  lastFolderElemOverFolder = {};
  return folder;
}

const PageNavMixin = {
  methods: {
    /** gotoAddNote 跳转到新建笔记页面
     */
    gotoAddNote (folderId, documentType) {
      this.articleNewDropShow = false;
      let storePath = this.$store.state.path;
      let query = {
        folderid: this.classifyNow.uid || '',
        'compose-type': 'search-keyword',
        isLeftShrink: true,
      }
      //企业文档参数
      if (documentType) {
        query.documentType = documentType
        query.folderid = this.curFolder.uid || ''
      }
      this.$router.push({
        path: storePath['compose'].path,
        query: query,
      })
      // let routeData = this.$router.resolve({
      //   path: storePath['addNewNoteCompose'].path,
      //   query: {
      //     folderid: this.classifyNow.uid || '',
      //     'compose-type': 'search-keyword',
      //     isLeftShrink: true
      //   }
      // });
      // this.$Utils.simulateAClick(routeData.href);
    },
    showActivity () {
      this.activityDialog.show = true;
      this.activityDialog.m = {
        poster: 'https://static.getgetai.com/tool/aiwriter/activity/20210615/ticibao-pc.png'
      }
    }
  },
};

export default {
  name: 'FolderPage',
  components: {
    PageHeader,
    // CardSubBar,
    ModalPublishRecord,
    UADetective,
    // FolderClamp,
    FoldersPanel,
    FoldersRecycle,
    FolderSearch,
    NewFolderModal,
    RenameFolderModal,
    // GtSvg,
    FolderCollection,
    Home,
    All,
    //PurchaseModal,
    //FunctionHintChain,
    BusinessCreate,
    BusinessDocument,
    BusinessStaff,
    BusinessFolder,
    BusinessSelect,
    BusinessNewFolder,
    BusinessRecycle,
    VipTimeHint,
    MaskLayer,
    ImportArticleModal,
    // DtSvg,
    PromotionModal
  },
  mixins: [PageNavMixin],
  head () {
    return {
      title: '工作台-Get智能写作，开启AI写作新时代',
      meta: [
        { charset: 'utf-8' },
        { name: 'description', content: '管理个人文章' },
        { name: 'keywords', content: '个人文章' },
        {
          hid: 'viewport',
          name: 'viewport',
          content: 'width=device-width, initial-scale=1.0, user-scalable=no',
        },
      ],
    };
  },
  data () {
    return {
      iconNewFolder,
      noteList: [],
      notesPageParams: {
        pageSize: 100,
        pageNum: 1,
        content: '',
        title: '',
        classifyId: '0',
        isDel: 0,
        orderType: 0,
      },
      notesPage: {
        list: [],
      },
      recycleNotesPageParams: {
        pageSize: 50,
        pageNum: 1,
        t: {
          content: '',
          title: '',
          isDel: 1,
          classifyId: '',
        },
      },
      recycleNotesPage: {
        list: [],
      },
      // 默认文集
      folderDefault: {
        uname: '默认文集',
        uid: '0',
        count: this.collectionFolder ? this.collectionFolder.defaultCount : 0,
        hover: false,
      },
      listCountTotal: -1,
      Img: {
        iconFolderAdd:
          'https://get-website.oss-cn-beijing.aliyuncs.com/pc-nuxt/img/tool/add-classify-white.png',
        iconAddArticle,
        ...ImgFolder,
        folderLight:
          'https://static.getgetai.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E7%82%B9%E4%BA%AE.png',
        folderDefault:
          'https://static.getgetai.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E6%9C%AA%E9%80%89%E4%B8%AD.png',
      },

      /**
       * DATA COPY FROM PROJECT SPA
       * */
      user: {},
      searchFoucs: false,
      noteSearch: {
        pageNum: 1,
        pageSize: 100,
        title: '',
        content: '',
        classifyId: '',
      },
      kngFolder: {
        list: [],
      },
      ncModal: {
        show: false,
        focused: true,
        m: {},
      },
      noteModal: {
        show: false,
        cShow: false,
        m: {
          uid: '',
          title: '',
          img: '',
          isImg: 0,
          content: '',
          isDel: 0,
          isPrivate: 0,
          classifyId: '',
          sourceType: 3,
        },
      },
      classifyNow: {},
      showMoreUid: '',
      shareObj: {
        userId: '',
        uid: '',
        contentType: '',
        contentId: '',
      },
      ncMoreFunc: '', //鼠标经过文集时显示更多
      deleteUid: '', //准备删除知识夹时保存的uid
      ncName: '',
      erwei: '', //用户分享获取二维码
      afterSearch: '',
      shareAction: true, //true生成二维码-sharePageAfter false分享给好友-sharePage
      urlPath: '',

      MODAL_TYPE_CLOSE,
      MODAL_TYPE_ADD_FOLDER,
      MODAL_TYPE_SHARE_FOLDER,
      MODAL_TYPE_REMOVE_FOLDER,
      MODAL_TYPE_FOLDER_DETAIL,
      MODAL_TYPE_COPY_PATH,
      MODAL_FOLDER_DETAIL,
      MODAL_FOLDER_Rename_Name,

      // NOTE Operate
      NOTE_OPERATE_OPEN_DETAIL,
      NOTE_OPERATE_OPEN_EDITOR,

      SHARE_TYPE_FOLDER,
      SHARE_TYPE_NOTE,

      // selectedFolderUid: '0',

      // 正在加载笔记
      isLoadingNote: false,
      showOptionListNoteUid: '',

      // 拖动
      isDragging: false,
      draggingNoteId: '',
      // drop on this folder
      lightUpFolderId: '',

      // 知识夹栏 展示回到顶部
      isFolderListBacktoTopButtonShow: false,
      guide: false,

      currentDragingEventId: '',

      // 发文记录
      showPublishRecord: '',
      publishRecordNote: null,
      themeOrderList: [
        { title: '新建文章', key: '1', article: 'el-icon-document' },
        { title: '新建文集', key: '2', article: 'el-icon-folder-opened' },
      ],
      // 右侧导航文案
      navigationCopy: '默认文集',
      // 默认文集
      folderData: {
        count: 0,
        uid: '0',
        uname: '默认文集',
      },
      articleStatus: true,
      collectionFolder: {
        list: [],
      },
      folderObj: {},
      pageNum: 1,
      folderUidData: '',
      // 回收站文集位置
      recycleFoldersObj: {
        list: [],
        defaultCount: 0,
      },
      navtitle: '我的文集',
      addStatus: true,
      articleNewDropShow: false,
      isFoldersFold: false,
      removeStatus: true,
      privateLibrarySwitch: false,
      deleteFlase: false,
      isloadingClampMore: true,
      folderRemove: {},
      userRename: {
        uid: '',
        userId: '',
      },
      isNoneStatus: false,
      scrollElemTop: 0,
      //是否显示横幅
      isShowLineImg: false,
      //all-panel 图标变化
      allPanelImg: {
        hover: 'https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/all/all-hover.png',
        selected: 'https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/all/all-selected.png',
        unSelected: 'https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/all/all-unselected.png',
        status: 0, //0离开 1悬浮,
      },
      isShowPurchaseModal: false,
      //静态资源
      resources: {
        iconAddArticle, changeBusinessImg
      },
      //是否显示创建企业按钮 0未申请 1申请中 2已通过 3已拒绝
      applyStatus: -1,
      //用户自己是否创建创建过企业
      isCreatedBusiness: true,
      //当前选择文件夹
      curFolder: {},
      //企业菜单
      businessMenus: {
        stepOne: [
          { name: '创建企业', code: 'b_create', img: memberSvg },
        ],
        stepTwo: [
          { name: '企业成员', code: 'b_staff', img: memberSvg, isShow: true, },
          { name: '企业文档', code: 'b_document', img: documentImg, isShow: true },
          {
            name: '企业文集',
            code: 'b_note',
            isLevelTwo: true,
            isExpand: false,
            isShow: true,
            img: noteSvg,
            isHover: false,
            canEditFolder: false,
            rightImg: 'https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/folder-add-btn.png',
            rightImgHover: 'https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/folder-add-btn-hover.png',
            list: [
              { uid: '0', uname: '默认', img: folderImg }
            ]
          },
          { name: '回收站', code: 'b_recycle', img: recycleImg, isShow: false },
        ]
      },
      //企业列表
      companyList: [],
      //当前选中的企业
      selectedCompany: {
      },
      //企业权限操作相关对象
      businessAuthorityWrapper,
      //导入弹框
      isShowArticleImportModal: false,
      //哪种方式导入
      showWhichModal: 'file',
      //当前classifyId
      classifyId: '0',
      //是否显示企业切换
      isShowBusinessSelect: false,
      //是否显示添加文件夹弹框
      isShowAddFolder: false,
      //是否显示会员提醒横幅
      isShowVipTimeHint: false,
      // 活动弹窗
      activityDialog: {
        isActivityOn: true,
        show: true,
        m: {}
      }
    }
  },
  watch: {
    navtitle (val) {
      if (val === '回收站') {
        this.removeStatus = false;
      }
    },
    // '$route.query.loginSuccess': async function(newVal) {
    //   if (newVal === 'yes') {
    //     // 加载知识夹
    //     await this.loadNoteClassify();
    //     await this.loadNotes();

    //     this.folderSwitch(this.$route.query.folderuid);
    //     const that = this;
    //     document.addEventListener('click', function(e) {
    //       that.autoClose();
    //     });
    //   }
    // },
    // 文集切换
    '$route.query.folderuid': function (newVal) {
      if (newVal) {
        this.folderSwitch(newVal);
        const that = this;
        document.addEventListener('click', function (e) {
          that.autoClose();
        });
      }
    },
    // 当前列表内容
    '$route.query.mode': function (newVal) {
      if (!newVal || newVal === 'myarticle') {
        this.isFoldersFold = true;
      } else {
        this.isFoldersFold = false;
      }
    },
    '$sotre.state.user.isLogin': function (newVal) {
      if (!newVal) {
        this.$router.push('/');
      }
    },
    //监控默认企业 写入localStorage请求时带header
    selectedCompany (newVal) {
      localStorage.setItem('Enterprise-Level', newVal.id)
    }
  },
  computed: {
    ...mapGetters({
      isVip: 'user/isVip',
      isLogin: 'user/isLogin',
      userid: 'user/userid',
    }),
    isShowMaskLayer () {
      let f = this.selectedCompany.vipStatus === 2
      let mode = this.$route.query.mode || ''
      return f && mode.indexOf('b_') > -1 && mode !== 'b_create'
    },
    //是否显示企业切换图标 空|只有一个且这个企业是自己的不显示图标
    isShowChangeBusinessIcon () {
      let list = this.companyList
      let item = list.find(item => item.createUser === this.userid)
      if (list.length === 0 || (list.length === 1 && item)) {
        return false
      }
      return true
    },
    //处理allPanel url
    handleAllPanelImg () {
      let all = this.allPanelImg
      let isSelected = this.$route.query.mode === 'all'
      if (all.status === 1) {
        return all.hover
      }
      if (isSelected) {
        return all.selected
      }
      if (!isSelected) {
        return all.unSelected
      }
      return all.unSelected
    },
    tabs () {
      let storePath = this.$store.state.path;
      return ['folder', 'card'].map(key => storePath[key]);
    },
    fullPath () {
      return this.$route.fullPath;
    },
    kngFolderList () {
      let list = this.kngFolder ? this.kngFolder.list : [];
      return [
        {
          uname: '默认文集',
          uid: '0',
          count: this.kngFolder ? this.kngFolder.defaultCount : 0,
        },
        ...list,
      ];
    },
    collectionFolderList () {
      let list = this.collectionFolder ? this.collectionFolder.list : [];
      return [
        {
          uname: '默认文集',
          uid: '0',
          count: this.collectionFolder ? this.collectionFolder.defaultCount : 0,
        },
        ...list,
      ];
    },
    folderCount () {
      // add default
      return this.kngFolderList.length;
    },
    defaultFolder () {
      return {
        uname: '默认文集',
        count: this.kngFolder.defaultCount,
      };
    },
    // 当前文集状态
    folderStatus () {
      return function (item) {
        if (item.hover) return 'folderLight';
        if (item.uid === this.classifyNow.uid) return 'folderChecked';
        return 'folderDefault';
      };
    },
  },
  async mounted () {
    if (!this.$store.state.user.isLogin) {
      this.$router.push('/');
      return;
    }
    //显示弹窗
    this.judgeShowPromotion()
    //是否显示横幅
    //this.initLineImg();
    //展示引导
    if (this.$route.query.guide) {
      this.guide = true;
    }

    //企业数据初始化
    this.initBusinessData()

    // 数据初始化
    await this.init()

    // 版本更新提示
    checkVersionUpdate(this)
  },
  methods: {

    //五一活动按钮
    mayDayBtnClick () {
      EventBus.$emit('showActivityPopup')
      // window.open('https://sourl.cn/ZEpGCn', '_blank')
    },

    //判断是否显示弹窗
    judgeShowPromotion () {
      //显示活动弹窗
      let isPromotionShow = this.$Utils.checkPromotionShow('mayDay')
      isPromotionShow = false
      if (isPromotionShow) {
        EventBus.$emit('showActivityPopup')
      }
    },


    //start******************企业********************start

    //回收站菜单权限 编辑文件夹
    handlePower () {
      let r = this.getR()
      let company = this.selectedCompany
      let fl = businessAuthorityWrapper.bad.hasAuthority(company.userType, businessAuthorityWrapper.bae.RY_LIST)
      r.isShow = fl
      let f = this.getF()
      fl = businessAuthorityWrapper.bad.hasAuthority(company.userType, businessAuthorityWrapper.bae.BD_EF)
      f.canEditFolder = fl
    },

    //恢复文集成功
    restoreFolderSuccess () {
      this.initFolder()
    },

    //删除文件夹
    deleteFolderBusiness (folder) {
      if (this.selectedCompany.vipStatus === 2) {
        this.$message.warning('您的企业已到期，请续费后使用')
        return
      }
      this.$confirm('此文集中的任何文章都将被移动到回收站中', '删除文集', {
        confirmButtonText: '确认删除',
        cancelButtonText: '取消',
        type: 'warning',
        center: true
      }).then(async () => {
        let data = {
          uid: folder.uid,
          companyId: this.selectedCompany.id
        }
        let res = await deleteFolderToRecycle(data)
        if (res.code === 200) {
          this.$message.success('删除成功')
          let f = this.getF()
          let index = f.list.findIndex(item => item.uid === folder.uid)
          if (index > -1) {
            f.list.splice(index, 1)
          }
          //如果删除的是当前选中的文件夹，则默认选中全部
          if (this.curFolder.uid === folder.uid) {
            this.businessFolderClick(f.list[0])
          }
        } else {
          let msg = res.message ? res.message : '删除失败'
          this.$message.error(msg)
        }
      })
    },

    //添加文件夹
    addFolderSuccess (item) {
      item.img = folderImg
      let folder = this.getF()
      let list = folder.list
      list.push(item)
    },

    showNewFolderModal () {
      this.isShowAddFolder = true
    },

    //切换企业成功
    changeCompanySuccess (item) {
      this.handleChangeBusiness(item)
      //重新更新vipend
      vipHelper.updateVipVue(this)
      //处理路由
      this.handleRouter()
      //是否显示vip提醒
      this.handleVipTimeHint()
      //当前状态
      this.applyStatus = this.selectedCompany.status
      //重新加载文集
      this.initFolder()
      //重新菜单权限
      this.handlePower()
    },
    //处理退出|切换企业
    handleChangeBusiness (item) {
      if (item) {
        this.selectedCompany = item
      } else {
        //找我的企业，如果存在说明还未通过申请则直接选择
        let mine = this.companyList.find(i => i.createUser === this.userid)
        //退出后没有任何企业
        this.selectedCompany = mine || { status: 0 }
      }
    },

    //显示切换企业对话框
    showBusinessSelect () {
      this.isShowBusinessSelect = true
    },

    //导入成功回调
    importArticleCallback () {
      //导入成功回调 重新加载回调
      let mode = this.$route.query.mode
      if (mode === 'b_document') {
        this.$refs['businessDocument'].loadNotes(true)
      } else if (mode === 'b_note') {
        this.$refs['businessFolder'].loadNotes(true)
      }
    },
    //设置用到的数据
    changeUsefulValue (type, classifyId) {
      this.showWhichModal = type
      this.classifyId = classifyId
      this.isShowArticleImportModal = true
    },
    //关闭导入框
    closeImportArticleModal () {
      this.isShowArticleImportModal = false
    },

    //判断自己是否创建过企业
    isExistMyBusiness () {
      let index = this.companyList.findIndex(item => item.createUser === this.userid)
      //如果存在自己创建的企业则提到最前
      let b
      if (index > -1) {
        b = this.companyList[index]
        //删除
        this.companyList.splice(index, 1)
        //添加到首位
        this.companyList.splice(0, 0, b)
      }
      return b
    },
    //选择默认的企业
    findDefaultCompany () {
      let b = this.companyList.find(item => item.weights === 1)
      return b
    },
    async ifNoDefaultCompanyJustSet (company) {
      let res = await setDefaultCompany(company.id)
      if (res.code === 200) {
        return true
      } else {
        this.$message.error(res.message ? res.message : '设置默认企业失败')
        return false
      }
    },
    //处理是否显示vip提醒条幅 vipStatus1即将到期 2到期
    handleVipTimeHint () {
      let company = this.selectedCompany
      if (!company) {
        return
      }
      let time = company.cooperationEndTime || company.updateTime
      if (!time) {
        return
      }
      let isHaveShowHintPower = businessAuthorityWrapper.bad.hasAuthority(company.userType, businessAuthorityWrapper.bae.VIP_HINT)
      let isNeedShow = false
      let now = new Date()
      let vt = new Date(time)
      let cha = vt.getTime() - now.getTime()
      let day = parseInt(cha / 1000 / 60 / 60 / 24)
      if (day <= 7) {
        company.vipStatus = 1
        isNeedShow = true
      }
      if (cha <= 0) {
        company.vipStatus = 2
        isNeedShow = true
      }
      this.isShowVipTimeHint = isNeedShow && isHaveShowHintPower && company.status === 2
    },

    async initBusinessData () {
      //查询当前用户所在的企业列表
      let res = await companyList()
      if (res.code !== 200) {
        this.$message.error(res.message ? res.message : '企业查询失败')
        return
      }
      this.companyList = res.data ? res.data : []
      //如果是空则即没有被邀请也没有创建企业，则applyStatus 为 0
      if (this.companyList.length === 0) {
        this.applyStatus = 0
        return
      }
      let myBusiness = this.isExistMyBusiness()
      if (!myBusiness) {
        this.isCreatedBusiness = false
      }
      let defaultBusiness = this.findDefaultCompany()
      //如果无默认 则将选中的设置成默认
      if (!defaultBusiness) {
        let flag = await this.ifNoDefaultCompanyJustSet(myBusiness || this.companyList[0])
        console.log(flag)
      }
      //默认
      this.selectedCompany = defaultBusiness || myBusiness || this.companyList[0]
      console.log('selectedCompany::', this.selectedCompany)
      //取当前状态
      this.applyStatus = this.selectedCompany.status
      //处理路由
      this.handleRouter(true)
      //是否提醒vip到期
      this.handleVipTimeHint()
      //填充角色字段
      this.handleRole()
      //处理菜单权限
      this.handlePower()
      //有必要则初始化文集
      this.initFolder()
    },
    //处理链接
    handleRouter (isMounted) {
      let status = this.selectedCompany.status
      let mode = this.$route.query.mode || ''
      let isB = !(mode.indexOf('b_') === -1)
      if (isMounted) {
        //刷新页面
        if (!isB) {
          //如果是其他的则不处理
          return
        }
        if (mode === 'b_create' && status === 2) {
          this.$router.push('/folder')
        }
        return
      }
      let two = this.businessMenus.stepTwo[0]
      let one = this.businessMenus.stepOne[0]
      //切换企业
      if (!isB) {
        if (status === 2) {
          this.businessMenuItemClick(two)
        } else if (status !== 2) {
          this.businessMenuItemClick(one)
        }
        return
      }
      if (status === 2 && mode === 'b_create') {
        this.businessMenuItemClick(two)
      } else if (status !== 2 && mode !== 'b_create') {
        this.businessMenuItemClick(one)
      }
    },
    //是否是管理员
    handleRole () {
      let company = this.selectedCompany
      let isSuperAdmin = businessAuthorityWrapper.bre.isSuperAdmin(company.userType)
      company.isSuperAdmin = isSuperAdmin
    },

    //获取文集菜单
    getF () {
      let folder = this.businessMenus.stepTwo[2]
      return folder
    },
    //获取回收站菜单
    getR () {
      let r = this.businessMenus.stepTwo[3]
      return r
    },

    //如果有比必要则加载文集列表
    async initFolder (isNecessary) {
      let query = this.$route.query
      //let mode = query.mode
      let bfid = query.b_folderuid
      if (!bfid && !isNecessary) {
        return
      }
      let folder = this.getF()
      if (query.mode === 'b_note') {
        folder.isExpand = true
      }
      let list = folder.list
      let data = {
        isDel: 0,
        companyId: this.selectedCompany.id
      }
      let res = await getBusinessFolder(data)
      if (res.code === 200) {
        //去除其他 只保留全部
        for (let i = list.length - 1; i >= 0; i--) {
          let l = list[i]
          if (l.uid !== '0') {
            list.splice(i, 1)
          }
        }
        res.data.list.forEach(f => {
          f.img = folderImg
          list.push(f)
        })
        folder.isLoaded = true
        //查找选中当前文件夹
        let f = list.find(item => item.uid === bfid)
        if (f) {
          this.curFolder = f
        }
      } else {
        let msg = res.message ? res.message : '文集加载失败'
        this.$message.error(msg)
      }
    },

    //切换tab也
    changeTab (mode, folderuid) {
      this.privateLibrarySwitch = true
      this.removeStatus = false
      let path = this.$route.path
      let query = { ...this.$route.query }
      if (mode) {
        query.mode = mode
      }
      if (folderuid) {
        query.b_folderuid = folderuid
      }
      this.$router.push({ path: path, query: query })
    },
    //企业按钮点击
    businessMenuItemClick (item) {
      this.changeTab(item.code)
      //如果是二级菜单
      this.handleLevelTwoClick(item)
    },
    handleLevelTwoClick (item) {
      if (!item.isLevelTwo) {
        return
      }
      item.isExpand = !item.isExpand
      //文件夹列表
      let folder = this.getF()
      let list = folder.list
      //如果是展开且还未加载文件夹文集 则默认加载默认文件夹文集
      let query = { ...this.$route.query }
      if (!Object.prototype.hasOwnProperty.call(query, "b_folderuid")) {
        //则加载默认文件夹列表
        this.businessFolderClick(list[0])
      }
      //判断list为1则认为还未加载文件夹
      if (!folder.isLoaded) {
        this.initFolder(true)
      }
    },
    //文件夹点击
    businessFolderClick (item) {
      this.curFolder = item
      this.changeTab('b_note', item.uid)
    },

    //end*****************企业***************end

    showPurchaseModal () {
      this.$store.commit('purchase/setIsp', true)
      //this.isShowPurchaseModal = true
    },

    // closePurchaseModal () {
    //   this.isShowPurchaseModal = false
    // },

    //主页点击
    homeClick () {
      this.privateLibrarySwitch = true;
      this.removeStatus = false;
      let path = this.$route.path;
      let query = { ...this.$route.query };
      query.mode = 'home';
      this.$router.push({ path: path, query: query });
    },
    //全部文章点击
    allClick () {
      this.privateLibrarySwitch = true;
      this.removeStatus = false;
      let path = this.$route.path;
      let query = { ...this.$route.query };
      query.mode = 'all';
      this.$router.push({ path: path, query: query });
    },

    // //关闭横幅图片
    // lineImgClose () {
    //   this.isShowLineImg = false;
    //   //记录删除过
    //   localStorage.setItem(`np-line-img-${this.userid}`, '1');
    // },
    // //是否显示横幅
    // initLineImg () {
    //   setTimeout(() => {
    //     let lineImg = localStorage.getItem(`np-line-img-${this.userid}`);
    //     if (lineImg) {
    //       this.isShowLineImg = false;
    //     } else {
    //       this.isShowLineImg = true;
    //     }
    //   }, 500);
    // },
    // //点击横幅跳转
    // jumpCode () {
    //   document.querySelector('#jumpCode').click()
    // },
    async init () {

      switch (this.$route.query.mode) {
        case 'recycleBin':
          await this.changeRecyclebin();
          this.isFoldersFold = false;
          // await this.loadRecycleNotesPage(1);
          break;
        case 'privateLibrary':
          this.privateLibrarySwitch = true;
          this.isFoldersFold = false;
          break;
        case 'myarticle': {
          let folderGot = await this.loadNoteClassify(
            1,
            this.$route.query.folderuid
          )
          this.chooseFolder(1, folderGot);
          break;
        }
      }
      this.initScrollListeners();
    },
    initScrollListeners () {
      const that = this;
      const scrollElem = this.$refs['scroll-component'];
      if (!scrollElem) return;
      const onScroll = async () => {
        const topSide = scrollElem.scrollTop;
        const parentElementOffsetTop =
          scrollElem.parentElement.parentElement.offsetHeight;
        const scrollAll = parentElementOffsetTop + topSide;

        // console.log('topSide', topSide);
        // console.log(
        //   'parentElementOffsetTop + topSide',
        //   parentElementOffsetTop + topSide
        // );
        // console.log('scrollElem.scrollHeight', scrollElem.scrollHeight);
        /**
         *  判断是否要加载更多
         * 1、滚动时间发生
         * 2、滚动高度 + 父级高度 和 div高度 误差在3以内
         *
         */
        if (topSide > 0 && scrollElem.scrollHeight - scrollAll < 3) {
          // console.log('load more');
          // await that.listenToLoadMore();
          await that.scrollLoadMore();
        }
      };
      onScroll();
      scrollElem.addEventListener('scroll', onScroll);
    },
    async scrollLoadMore () {
      const mode = this.$route.query.mode;
      // console.log('mode', mode);
      // console.log(
      //   't',
      //   (!mode || mode === 'myarticle') &&
      //     this.notesPage.hasNextPage &&
      //     this.notesPage.nextPage
      // );
      if (
        (!mode || mode === 'myarticle') &&
        this.notesPage.hasNextPage &&
        this.notesPage.nextPage
      ) {
        // 我的文集
        await this.loadNotes(this.notesPage.nextPage);
      } else if (
        mode === 'recycleBin' &&
        this.recycleNotesPage.hasNextPage &&
        this.recycleNotesPage.nextPage
      ) {
        await this.loadRecycleNotesPage(this.recycleNotesPage.nextPage);
      } else if (mode === 'all') {
        this.$refs['allRef'].loadNotes()
      } else if (mode === 'b_document') {
        this.$refs['businessDocument'].loadNotes()
      } else if (mode === 'b_note') {
        this.$refs['businessFolder'].loadNotes()
      } else if (mode === 'b_recycle') {
        this.$refs['businessRecycle'].loadRecycleNotesPage()
      }
    },
    onFolderListWheel () {
      const elem = this.$refs['folder-list-content'];
      if (!elem) return;

      const d = elem.getBoundingClientRect();
      if (d.top < 90) {
        this.isFolderListBacktoTopButtonShow = true;
      } else {
        this.isFolderListBacktoTopButtonShow = false;
      }
    },
    folderListBacktoTop () {
      const elem = document.querySelector('.folder-item-container-head');
      if (elem) scrollIntoViewSmoothly(elem);
      this.isFolderListBacktoTopButtonShow = false;
    },
    onDragStart (ev, note) {
      this.currentDragingEventId = Date.now();

      // 移动
      ev.dataTransfer.dropEffect = 'move';
      ev.dataTransfer.effectAllowed = 'move';

      this.isDragging = true;
      // ev.target.classList.add(DRAGGING_ELEM_TAG)
      // ev.dataTransfer.setData("text/plain", note.uid)
      // this.draggingNoteId = note.uid
    },
    onDragEnd (ev) {
      this.isDragging = false;
      ev.target.classList.remove(DRAGGING_ELEM_TAG);
      globalDraggingEnd();
      event.preventDefault();
      if (this.currentDragingEventId) this.dragEndNoDrop(ev);
    },
    dragEndNoDrop (ev) {
      // 如果拖动结束，然而没有触发drop事件
      const { x, y } = ev;
      const isDrop = dragendPointInItem({ x, y });
      if (isDrop) {
        this.currentDragingEventId = '';
        this.onDrop(ev, isDrop);
      }
    },
    onDragOver (ev, folder) {
      ev.preventDefault();
      globalOnOneFolderOver(ev, folder);
    },
    removeFolderItemHover () {
      // 清除所有文集的hover事件
      // const target = ev.target
      const target = this.$refs['dragover-folder-list'];
      if (!target) return;

      // 删除所有子项的样式
      const folderItemDoms = target.querySelectorAll('.' + DRAG_OVER_TAG);
      for (const item of folderItemDoms) {
        item.classList.remove(DRAG_OVER_TAG);
      }

      // 删除所有子项的样式
      const recycleDoms = this.$refs['nav-recycle-bin'];
      if (recycleDoms) recycleDoms.classList.remove(DRAG_OVER_TAG);
    },
    async onDrop (ev, folder) {
      this.currentDragingEventId = '';
      ev.preventDefault();

      let folderUid = folder.uid;
      this.lightUpFolderId = folderUid;
      setTimeout(() => {
        // 闪动1s
        if (this.lightUpFolderId === folderUid) this.lightUpFolderId = '';
      }, 1000);
      let noteList = this.notesPage.list;
      let draggingNoteId = this.draggingNoteId;
      this.draggingNoteId = '';

      let noteItem = noteList.find(note => note.uid === draggingNoteId);
      if (noteItem.classifyId === folderUid) {
        // 不能移动到同一个我的文集
        this.$message({
          type: 'warning',
          message: `当前笔记已经在我的文集”${folder.uname}“中`,
        });
      } else {
        noteItem.classifyId = folderUid;
        // const moveNoteRes = await this.changeFlder(noteItem)
        const moveNoteRes = await NoteApi.moveNoteToFolder({
          uid: noteItem.uid,
          classifyId: noteItem.classifyId || 0,
        });
        // console.log('移动笔记', moveNoteRes);
        if (moveNoteRes) {
          this.notesPage.list.splice(
            this.notesPage.list.findIndex(item => item.uid === noteItem.uid),
            1
          );
          this.notesPage.total = this.notesPage.total - 1;
        }
      }

      this.isDragging = false;
      globalDraggingEnd();
    },
    onDragOverDelete (ev, folder) {
      // console.log('onDragOverDelete')

      ev.preventDefault();
      recycleBinDragOver(ev);
    },
    onDropDelete (ev, folder) {
      ev.preventDefault();
      // 拖拽结束，此处进行删除文章唤醒操作
      console.log(
        '拖拽结束，此处进行删除文章唤醒操作',
        this.draggingNoteId,
        this.deleteFlase
      );
      this.deleteNote({ uid: this.draggingNoteId });
      this.deleteFlase = true;
    },
    /**
     * 更改知识夹之后，保存笔记
     */
    saveNote (note) {
      return NoteApi.EditNoteLocal({
        uid: note.uid,
        title: note.title,
        content: note.content,
        // 文章摘要信息，用于非富文本展示使用
        brief: note.brief,
        classifyId: note.classifyId || 0,
        isPrivate: note.isPrivate,
        isDel: note.isDel,
        isImg: note.isImg,
        img: note.img || '',
        nouns: note.nouns || [],
        userId: note.userId,
      }).then(res => {
        if (res.code === 200) {
          this.$message({
            message: '笔记移动成功',
            type: 'success',
          });

          return true;
        } else {
          this.$message({
            message: res.message,
            type: 'error',
          });
          return false;
        }
      });
    },
    /**
     * 笔记移动
     * @param {Object} note 笔记
     */
    async changeFlder (note) {
      await NoteApi.EditNoteLocal({
        uid: note.uid,
        classifyId: note.classifyId || 0,
      });
    },
    autoClose () {
      this.showOptionListNoteUid = '';
    },
    /**
     * 笔记详情
     */
    gotoNoteEditDetail (note = {}) {
      this.$store.dispatch('ga/click', `TAGFolderArticleClick::${note.uid}`);

      let uid = note.uid;
      const folderid = this.$route.query.folderuid || ""
      if (uid) {
        let composeDev = this.$store.state.path['compose'];
        const path = `${composeDev.path}`;

        const query = {
          isLeftShrink: true,
          mode: 'Meterial',
          uid: uid,
          folderid: folderid,
          // keyword: (note.theme || note.title || '').substr(0, 30),
          km: (note.theme || '').substr(0, 55),
          ks: (note.searchQuestion || '').substr(0, 55),
        };
        //用户标志是否是企业文档
        if (note.documentType) {
          query.documentType = note.documentType
        }
        this.$router.push({
          path: path,
          query,
        })
      }
    },
    // 收藏文章新开页面 并打开收藏
    // status： 1 => 文章, 2 => 段落 3 = 图片
    openNewly (note = {}) {
      let open;
      if (note && note.statusArc === 1) {
        localStorage.setItem('openAccessOriginal', JSON.stringify(note));
        open = 'openArticle=yes';
      } else if (note.statusArc === 2) {
        localStorage.setItem('openParagraph', JSON.stringify(note));
        open = 'openParagraph=yes';
      } else if (note.statusArc === 3) {
        localStorage.setItem('openPicture', JSON.stringify(note));
        open = 'openPicture=yes';
      }
      if (note.id) {
        let routeData = this.$router.resolve({
          path: `/compose?keyword=${
            note.theme ? note.theme : ''
            }&mode=Collection&${open}`,
        });
        this.$Utils.simulateAClick(routeData.href);
      }
    },

    async loadNotes (pageNum, folderId, fresh = false) {
      const requestParam = this.notesPageParams;
      let noteList;
      if (!pageNum || pageNum === 1 || fresh) {
        pageNum = 1;
        noteList = [];
      } else {
        noteList = this.notesPage.list;
        // pageNum = requestParam.pageNum + 1
      }
      requestParam.pageNum = pageNum;
      requestParam.classifyId = folderId || requestParam.classifyId || '0';
      const res = await NoteApi.GETNoteListES(requestParam);
      // console.log(res)
      if (res) {
        // this.noteList = res.list.filter(item => !item.isDel);
        res.list.forEach(item => {
          if (!item.isDel) {
            noteList.push(item);
          }
          //初始化 导出下拉控制字段
          item.isHover = false
          item.isShowExportDropdown = false
        });
        res.list = noteList;
        this.notesPage = res;
        // this.listCountTotal = res.total;
        this.isLoadingNote = false;
      } else {
        // console.log(err);
        this.isLoadingNote = false;
      }
    },
    copyUrl () {
      const Url2 = document.getElementById('nc-popup-path-id');
      Url2.select(); // 选择对象
      document.execCommand('Copy');
      this.$message({
        message: '复制成功',
        type: 'success',
      });
      this.noteModal.show = 0;
    },
    //修改知识夹信息
    updateFolderInfo () {
      const m = this.ncModal.m;
      PostAsFormTheSpaWay(this.$Apis._note_classify_edit, {
        uid: m.uid,
        isPrivate: m.isPrivate,
        uname: m.uname || '未定义我的文集名称',
        userId: m.userId,
        isDel: m.isDel,
      })
        .then(res => {
          if (res.status === 200 && res.data.code === 200) {
            this.ncModal.show = 0;
            this.ncModal.m = {};
            this.loadNoteClassify();
            this.$message({
              message: '更改成功',
              type: 'success',
            });
          }
        })
        .catch(err => {
          this.$message({
            type: 'error',
            message: '我的文集的名字包含非法字符，请检查后重试。',
          });
          console.log(err);
        });
    },

    //更改知识夹公开状态
    changePrivate () {
      let isPrivate = this.ncModal.m.isPrivate;
      if (isPrivate) {
        isPrivate = 0;
      } else {
        isPrivate = 1;
      }
      this.ncModal.m.isPrivate = isPrivate;
    },
    // 更新文件名
    async editFolderName (folder, name) {
      // console.log("folder", folder, name);
      const res = await PostAsFormTheSpaWay(this.$Apis._note_classify_edit, {
        uid: folder.uid,
        uname: name,
      });
      if (res && res.status === 200 && res.data.code === 200) {
        this.classifyNow = res.data.data;
        this.kngFolder.list.forEach(item => {
          if (item.uid === folder.uid) {
            item.uname = name;
          }
        });
      } else {
        this.$message({
          type: 'error',
          message: '更新失败，请重试',
        });
      }
    },
    //显示各种弹窗功能  1.添加知识夹弹窗 3.删除知识夹 4.知识夹更多信息（改名、公开状态）5.彻底删除文集
    showPopup (num, nc) {
      switch (num) {
        case MODAL_TYPE_ADD_FOLDER:
          this.ncModal.show = MODAL_TYPE_ADD_FOLDER;
          break;
        case MODAL_TYPE_REMOVE_FOLDER:
          // MODAL_TYPE_REMOVE_FOLDER
          this.ncModal.show = MODAL_TYPE_REMOVE_FOLDER;
          this.deleteUid = nc.uid;
          break;
        case MODAL_TYPE_FOLDER_DETAIL:
          this.ncModal.m = this.$Utils.deepClone(nc);
          this.ncModal.show = MODAL_TYPE_FOLDER_DETAIL;
          break;
        case MODAL_TYPE_COPY_PATH:
          this.ncModal.show = MODAL_TYPE_COPY_PATH;
          break;
        case MODAL_FOLDER_DETAIL:
          this.ncModal.show = MODAL_FOLDER_DETAIL;
          this.folderUidData = nc ? nc.uid : '';
          break;
        case MODAL_FOLDER_Rename_Name:
          this.ncModal.show = MODAL_FOLDER_Rename_Name;
          this.userRename.uid = nc.uid;
          this.userRename.userId = nc.userId;
          this.folderUidData = nc ? nc.uid : '';
          break;
      }
    },

    async shareFriend (shareType, shareValue) {
      this.erwei = '';

      // 处理参数
      // let shareObj = {}
      let parameters =
        's=1&uid=' + shareValue.uid + '&authorId=' + shareValue.userId;
      // console.log('======shareValue-----', shareValue);
      switch (shareType) {
        case 1:
          parameters += '&ncName=' + shareValue.uname + '&whereGet=' + 5;
          break;
        case 2:
          parameters += '&whereGet=' + (shareValue.noteType === 4 ? '0' : '1');
          break;
      }

      // 处理请求
      const res = await ShareErwei({ parameters, path: '../sharePage/index' });
      if (res.status === 200 && res.data.code === 200) {
        this.erwei = res.data.data.qrImage;
      }
      // this.$http
      //   .get(this.$Apis._share_erwei, {
      //     params: {
      //       parameters: parameters,
      //       path: "../sharePage/index"
      //     }
      //   })
      //   .then(res => {
      //     console.log("-------erweima-------", res);
      //     if (res.status === 200 && res.data.code === 200) {
      //       this.erwei = res.data.data.qrImage;
      //     }
      //   });
    },
    // shareType-1 文集分享 -2 笔记分享
    async shareQR (shareType, shareValue) {
      this.ncModal.show = 2;
      this.ncModal.m = this.$Utils.deepClone(shareValue);

      let shareObj = {};
      let parameters = '';

      switch (shareType) {
        case SHARE_TYPE_FOLDER:
          shareObj = {
            contentId: shareValue.uid,
            contentType: 3,
            userId: shareValue.userId,
            ncName: shareValue.uname,
          };
          parameters =
            's=1&uid=' +
            shareValue.uid +
            '&authorId=' +
            shareObj.userId +
            '&ncName=' +
            shareValue.uname +
            '&whereGet=' +
            5;
          break;
        case SHARE_TYPE_NOTE:
          shareObj = {
            contentId: shareValue.uid,
            contentType: 1,
            userId: shareValue.userId,
          };
          parameters =
            's=1&uid=' +
            shareValue.uid +
            '&authorId=' +
            shareObj.userId +
            '&whereGet=' +
            (shareValue.noteType === 4 ? '0' : '1');
          break;
      }
      const res = await ShareMyShare(shareObj);
      if (res.status === 200 && res.data.code === 200) {
        shareObj.uid = res.data.data.uid;
      } else if (res.status === 200 && res.data.code === 400) {
        shareObj.uid = this.$Utils.uuid(32, 16);
        PostAsFormTheSpaWay(this.$Apis._share_make, {
          uid: shareObj.uid,
          contentId: shareObj.contentId,
          contentType: shareObj.contentType,
          essay: shareObj.essay || '知识需要分享，Get-为用户链接最有价值的知识',
          bgImg:
            shareObj.bgImg ||
            'https://get-file.oss-cn-hangzhou.aliyuncs.com/tool/share-cover.jpg',
        }).then(res => {
          if (res.status === 200 && res.data.code === 200) {
            this.erwei = res.data.data.qrImage;
          }
        });
      }

      parameters += '&shareId=' + shareObj.uid;
      const resData = await ShareErwei({
        parameters,
        path: '../sharePage/index',
      });
      if (resData.status === 200 && resData.data.code === 200) {
        this.erwei = resData.data.data.qrImage;
      }
      // this.$http
      //   .get(this.$Apis._share_my_share, {
      //     params: shareObj
      //   })
      //   .then(res => {
      //     console.log("res------", res);
      //     if (res.status === 200 && res.data.code === 200) {
      //       shareObj.uid = res.data.data.uid;
      //     } else if (res.status === 200 && res.data.code === 400) {
      //       shareObj.uid = this.$Utils.uuid(32, 16);
      //       console.log("======shareUid-=====", shareObj.uid);
      //       PostAsFormTheSpaWay(this.$Apis._share_make, {
      //         uid: shareObj.uid,
      //         contentId: shareObj.contentId,
      //         contentType: shareObj.contentType,
      //         essay:
      //           shareObj.essay ||
      //           "知识需要分享，Get-为用户链接最有价值的知识",
      //         bgImg:
      //           shareObj.bgImg ||
      //           "https://get-file.oss-cn-hangzhou.aliyuncs.com/tool/share-cover.jpg"
      //       })
      //         .then(res => {
      //           if (res.status === 200 && res.data.code === 200) {
      //             this.erwei = res.data.data.qrImage;
      //           }
      //         });
      //     }

      //     parameters += "&shareId=" + shareObj.uid;
      //     console.log("shareObj===", shareObj);
      //     this.$http
      //       .get(this.$Apis._share_erwei, {
      //         params: {
      //           parameters: parameters,
      //           path: "../sharePageAfter/index"
      //         }
      //       })
      //       .then(res => {
      //         console.log("-------erweima-------", res);
      //         if (res.status === 200 && res.data.code === 200) {
      //           this.erwei = res.data.data.qrImage;
      //         }
      //       });
      //   });
    },
    //删除文集
    async deleteNc () {
      const uid = this.deleteUid;
      const deleteData = await GETclassifyRecycleList(uid);
      if (deleteData.code === 200) {
        this.ncModal.show = 0;
        this.loadNoteClassify();

        if (uid === this.classifyNow.uid) {
          this.deleteUid = '';
          this.classifyNow = {};
          this.loadNotes(1, '0');
        }
        // 删除成功之后，需同步加载回收站数据
        const resData = this.reloadRecycleFolder();
        // this.recycleFoldersObj = resData;
        if (resData) {
          this.ncModal.show = 0;
          this.loadNoteClassify();
          if (uid === this.classifyNow.uid) {
            this.deleteUid = '';
            this.classifyNow = {};
            this.loadNotes(1, '0');
            this.folderSwitch();
          }
          const query = { ...this.$route.query };
          // 如果当前路展示文集为当前文集则更新路由
          if (query.folderuid === uid) {
            this.lightUpFolderId = '0';
            query.folderuid = '0';
            this.navigationCopy = '默认文集';
            this.$router.push({
              path: this.$route.path,
              query: query,
            });
          }
          this.$message({
            message: '删除成功',
            type: 'success',
          });
        }
      }
    },
    // 彻底删除文集
    deleteFolder (folder) {
      if (!folder.uid || !folder.uname) return;
      this.$confirm(
        `即将彻底删除文集《${folder.uname}》<br />且删除后将无法恢复操作！！`,
        {
          distinguishCancelAndClose: true,
          confirmButtonText: '彻底删除',
          cancelButtonText: '取消',
          dangerouslyUseHTMLString: true,
          showClose: false,
          center: true,
        }
      )
        .then(async () => {
          const res = await GETnoteFileDelete(folder.uid);
          if (res.code === 200) {
            await this.changeRecyclebin();
            this.folderUidData = '';
            let index = this.recycleFoldersObj.list.findIndex(
              i => i.uid === folder.uid
            );
            if (index !== -1) {
              this.recycleFoldersObj.list.splice(index, 1);
            }
            folder.uid === this.recycleNotesPageParams.t.classifyId &&
              this.loadRecycleNotesPage(1);
            this.$message({
              showClose: true,
              message: '删除成功',
              type: 'success',
            });
          } else if (res.code === 200) {
            this.$message({
              showClose: true,
              message: '删除失败，请刷新重试',
              type: 'error',
            });
          }
        })
        .catch(err => {
          console.log(err);
        });
    },

    //点击隐藏下拉列表
    hidePopup () {
      const sp = document.getElementById('note-more-wrapper-id');
      if (sp) {
        if (!sp.contains(event.target)) {
          //这句是说如果我们点击到了id为myPanel以外的区域
          this.showMoreUid = '';
        }
      }
    },

    publishRecord (note) {
      this.publishRecordNote = note;
      this.showPublishRecord = true;
    },

    //删除笔记至回收站
    async deleteNote (note, callback) {
      let recycleList = await GETNoteRecycleList(note.uid);
      // console.log(
      //   recycleList,
      //   this.notesPage.list.findIndex(item => item.uid === note.uid)
      // );
      if (recycleList.code === 200) {
        this.notesPage.list.splice(
          this.notesPage.list.findIndex(item => item.uid === note.uid),
          1
        );
        this.notesPage.total = this.notesPage.total - 1;
        this.$message({
          showClose: true,
          message: '已放入回收站',
          type: 'success',
        });
        if (callback) {
          callback.call(this, 'success')
        }
      } else {
        this.$message({
          showClose: true,
          message: '删除失败',
          type: 'error',
        });
        if (callback) {
          callback.call(this, 'fail')
        }
      }
      // await this.$confirm(
      //   '删除的文章将被移到回收站中，可在回收站中恢复',
      //   '删除文章',
      //   {
      //     confirmButtonText: '确认删除',
      //     cancelButtonText: '取消'
      //   }
      // )
      //   .then(async () => {
      //     let recycleList = await GETNoteRecycleList(note.uid);
      //     console.log(
      //       recycleList,
      //       this.notesPage.list.findIndex(item => item.uid === note.uid)
      //     );
      //     if (recycleList.code === 200) {
      //       this.notesPage.list.splice(
      //         this.notesPage.list.findIndex(item => item.uid === note.uid),
      //         1
      //       );
      //       this.notesPage.total = this.notesPage.total - 1;
      //       this.$message({
      //         showClose: true,
      //         message: '删除成功',
      //         type: 'success'
      //       });
      //     } else {
      //       this.$message({
      //         showClose: true,
      //         message: '删除失败',
      //         type: 'error'
      //       });
      //     }
      //   })
      //   .catch(action => {
      //     // this.$message({
      //     //   type: 'info',
      //     //   message:
      //     //     action === 'cancel' ? '放弃保存并离开页面' : '停留在当前页面'
      //     // });
      //   });
    },
    /**
     * loadNoteClassify 加载用户知识夹信息
     */
    async loadNoteClassify (pageNum, folderId) {
      this.articleNewDropShow = false;
      if (typeof pageNum !== 'number') pageNum = 1;
      let res = await GETclassifyClassifies(0);
      this.kngFolder = res.data;
      this.kngFolder.list = this.kngFolder.list.map(item => {
        item.hover = false;
        return item;
      });
      this.classifyNow = this.classifyNow.uid
        ? this.classifyNow
        : this.folderDefault;
      if (pageNum < 2) {
        this.kngFolder.list = [this.folderDefault, ...this.kngFolder.list];
      }
      this.ncModal.m.uname = '';
      return (
        this.kngFolder.list.find(item => item.uid === (folderId || '0')) ||
        this.folderDefault
      );
      // let that = this
      // const classifyList = NoteApi.GetNoteClassifyList()
      // .then(res => {
      //   console.log('get note classify', res)
      //   if (res.data.code === 200) {
      //     that.kngFolder = res.data.data
      //   } else if (!res.data || res.data.code === 401) {
      //     throw new Error('清先登录')
      //   }
      // })
      // this.kngFolder = classifyList
    },
    // 文集切换
    chooseFolder (pageNum, folder) {
      this.folderRemove = folder;
      this.privateLibrarySwitch = false;
      this.removeStatus = true;
      this.navtitle = '我的文集';
      this.articleStatus = true;
      let reg = /(<\/?span.*?>)/gi;
      let noteContent = folder.uname.replace(reg, '');
      folder.uname = folder.uname.replace(reg, '');
      this.navigationCopy = noteContent;
      if (this.noteModal.show) {
        this.noteModal.cShow = false;
        this.noteModal.show = false;
        this.loadNoteClassify();
      }
      this.classifyNow = folder;
      this.isFoldersFold = true;
      // this.selectedFolderUid = folder.uid
      this.folderSwitch(folder.uid);
      const query = { ...this.$route.query };
      query.folderuid = folder.uid;
      // this.$router.replace({
      //   query: query
      // });
      this.$router.push({
        path: this.$router.path,
        query,
      });
      this.loadNotes(1, folder.uid);
    },
    /**
     * @param {string} folderId 文集id 可为空
     */
    folderSwitch (folderId) {
      if (folderId && this.kngFolder.list && this.kngFolder.list.length > 0) {
        this.classifyNow = this.kngFolder.list.find(
          item => item.uid === (folderId || '0')
        );
      } else {
        this.classifyNow = {
          uid: '0',
          uname: '默认文集',
        };
      }
    },
    // handleCommand(key) {
    //   if (key === '1') {
    //     // 新开新建文章页面
    //     const query = '/compose/new?compose-type=search-keyword';
    //     let routeData = this.$router.resolve({
    //       path: query
    //     });
    //     this.$Utils.simulateAClick(routeData.href);
    //   } else {
    //     // 当前页面新建文集
    //     this.showPopup(this.MODAL_TYPE_ADD_FOLDER);
    //   }
    // },
    popCloseBtn () {
      this.ncModal.show = 0;
    },
    handleOpen (key, keyPath) {
      this.navtitle = '我的文集';
      this.chooseFolder(1, this.folderData);
      let path = this.$route.path;
      let query = { ...this.$route.query };
      query.mode = 'myarticle';
      this.$router.push({ path: path, query: query });
      // window.history.pushState({ uid, }, '', '/compose/' + uid)
    },
    handleClose (key, keyPath) { },
    // 查看回收站
    privateLibraries () {
      this.privateLibrarySwitch = true;
      this.removeStatus = false;
      let path = this.$route.path;
      let query = { ...this.$route.query };
      query.mode = 'privateLibrary';
      this.$router.push({ path: path, query: query });
    },
    // 查询回收站里面的信息
    async changeRecyclebin () {
      this.articleStatus = false;
      this.isloadingClampMore = true;
      this.privateLibrarySwitch = false;
      this.removeStatus = false;
      this.navtitle = '回收站';
      this.navigationCopy = '';
      let path = this.$route.path;
      let query = { ...this.$route.query };
      query.mode = 'recycleBin';
      this.$router.push({ path: path, query: query });
      // this.newFileArticle(1);
      const resData = await this.reloadRecycleFolder();

      this.loadRecycleNotesPage(1);
      this.isloadingClampMore = false;
      // this.recycleFoldersObj.list = resData;
      this.recycleFoldersObj = resData;
    },
    // 查询回收站默认文集的文件
    // async newFileArticle(pageNum) {
    //   let data = {
    //     pageSize: 50,
    //     pageNum: pageNum || 1,
    //     t: {
    //       content: "",
    //       title: "",
    //       classifyId: 0,
    //       isDel: 0
    //     }
    //   };
    //   let res = await POSTrecyclePage(data);
    //   this.recycleNotesPage = res;
    // },
    // 获取回收站所有文章
    async loadRecycleNotesPage (pageNum, classifyId) {
      this.articleStatus = false;
      this.isLoadingNote = true;
      // 请求页数
      this.recycleNotesPageParams.pageNum =
        pageNum || this.recycleNotesPageParams.pageNum + 1;
      this.recycleNotesPage.list =
        this.recycleNotesPageParams.pageNum === 1
          ? []
          : this.recycleNotesPage.list;
      this.recycleNotesPageParams.t.classifyId = classifyId || '';
      console.log('this.recycleNotesPageParams', this.recycleNotesPageParams);
      let res = await POSTrecyclePage(this.recycleNotesPageParams);
      if (res) {
        let noteList = this.recycleNotesPage.list || [];
        res.list.forEach(item => {
          item.checked = false;
          noteList.push(item);
          // if (!item.isDel) {

          // }
        });
        res.list = noteList;
        // 请求后数据装填
        this.recycleNotesPage = res;
        this.articleStatus = true;
        this.addStatus = false;
      }
      this.isLoadingNote = false;
    },

    // 恢复文章 POSRestore
    async recovery (note, folder) {
      const res = await this.restoreNote(note, folder);
      if (res) {
        const recycleFolderIndex = this.recycleFoldersObj.list.findIndex(
          item => item.uid === note.classifyId
        );
        this.recycleFoldersObj.list.splice(recycleFolderIndex, 1);
        await this.loadNoteClassify(1);
        // 重载文件夹数据
        await this.reloadRecycleFolder();
      }

      // let folderRecycle = this.recycleFoldersObj.list.find(
      //   item => item.uid === (note.classifyId)
      // );
      // if (folderRecycle) {
      //   this.$confirm(`文章《${note.title || '无标题'}》所在文集 【${folderRecycle.uname}】 将随文章一同恢复`, {
      //     distinguishCancelAndClose: true,
      //     confirmButtonText: '确认',
      //     dangerouslyUseHTMLString: true,
      //     cancelButtonText: '取消',
      //     showClose: false,
      //     center: true
      //   }).then(async () => {
      //     const res = await this.restoreNote(note, folderRecycle)
      //     if (res) {
      //       const recycleFolderIndex = this.recycleFoldersObj.list.findIndex(
      //         item => item.uid === (note.classifyId)
      //       );
      //       this.recycleFoldersObj.list.splice(recycleFolderIndex, 1)
      //       await this.loadNoteClassify(1);
      //       // 重载文件夹数据
      //       await this.reloadRecycleFolder()
      //     }
      //   }).catch(err => {
      //     console.log(err)
      //   })
      // } else {
      //   const folder = this.kngFolder.list.find(
      //     item => item.uid === (note.classifyId)
      //   );
      //   await this.restoreNote(note, folder)
      //   // 重载文件夹数据
      //   await this.reloadRecycleFolder()
      // }
    },

    // 重载回收站文集数据
    async reloadRecycleFolder () {
      const resData = await POSTrecycleClassifies();
      this.recycleFoldersObj = resData;
      return resData;
    },
    // 恢复笔记
    async restoreNote (note, folder) {
      const res = await POSRestore(note.uid);
      if (res.code === 200) {
        this.recycleNotesPage.list.splice(
          this.recycleNotesPage.list.findIndex(item => item.uid === note.uid),
          1
        );
        this.$message({
          showClose: true,
          message: `文章《${note.title || '无标题'}》已成功恢复至【${
            folder ? folder.uname : '默认文集'
            }】`,
          type: 'success',
        });
        return true;
      }
      return false;
    },
    // 彻底删除
    deleteThoroughlyNote (noteIds) {
      this.$confirm(`即将彻底删除文章<br />删除后将无法恢复操作！`, {
        distinguishCancelAndClose: true,
        confirmButtonText: '彻底删除',
        cancelButtonText: '取消',
        dangerouslyUseHTMLString: true,
        showClose: false,
        center: true,
      })
        .then(async () => {
          let deleteRes = await POSDeletePage(noteIds);
          if (deleteRes.code === 200) {
            for (const noteId of noteIds) {
              // 清除本地缓存可用的空文章id
              let index = this.recycleNotesPage.list.findIndex(
                i => i.uid === noteId
              );
              if (index !== -1) {
                this.recycleNotesPage.list.splice(index, 1);
              }
            }
            await this.reloadRecycleFolder();
            // this.recycleFoldersObj = resData;
            this.$message({
              showClose: true,
              message: '删除成功',
              type: 'success',
            });
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    unfoldFolders () {
      this.isFoldersFold = !this.isFoldersFold;
      if (this.$route.query.mode !== 'myarticle') {
        let path = this.$route.path;
        let query = { ...this.$route.query };
        query.mode = 'myarticle';
        this.$router.push({ path: path, query: query });
      }
      if (this.kngFolder.list.length === 0) {
        this.loadNoteClassify();
      }
      if (!this.notesPage.list || this.notesPage.list.length === 0) {
        this.loadNotes(1);
      }
    },
    // 回收站恢复文集
    async restoreFolder (uid) {
      const res = await GETclassifyRestore(uid);
      if (res.code === 200) {
        // 恢复成功之后，将回收站相应的文集删掉
        this.$message({
          showClose: true,
          message: '已成功恢复文集',
          type: 'success',
        });
        console.log('恢复笔记');
        await this.loadNoteClassify(1);
        await this.loadRecycleNotesPage(1);
        await this.reloadRecycleFolder();
        let index = this.recycleFoldersObj.list.findIndex(i => i.uid === uid);
        if (index !== -1) {
          this.recycleFoldersObj.list.splice(index, 1);
        }
      }
    },
    // 按照时间重新排序
    sortClick (item) {
      this.notesPageParams.orderType = item.value;
      this.loadNotes(1);
    },
    openArticleSet (item) {
      this.chooseFolder(1, item);
    },
    modalClose () {
      this.ncModal.show = false;
      this.articleNewDropShow = false;
    },
    modifiedSuccess (data, uid) {
      this.ncModal.show = 0;
      this.navigationCopy = data.newName;
      let index = this.kngFolderList.findIndex(i => i.uid === uid);
      this.kngFolderList[index].uname = data.newName;
      this.isNoneStatus = false;
      this.ncModal.m.uname = '';
    },
  },
  filters: {
    bgimg: FilterBgimg,
    ...FilterList,
  },
};
</script>
<style lang="scss" scoped>
.split-pb {
  margin-top: 10px;
  padding-left: 10px;
  color: #b2b2b2;
  display: flex;
  align-items: center;
  img {
    width: 15px;
  }
}
.business-wrapper {
  .split-line {
    border-top: 1px solid #eeeeee;
    width: 95%;
    margin: 0 auto;
    margin-top: 5px;
  }
  .menu-item {
    margin-top: 5px;
    cursor: pointer;
    color: #b2b2b2;
    padding: 0px 0px;
    &:hover {
      background: #f2f6fc;
    }
    .item-wrapper {
      display: flex;
      align-items: center;
      color: #666666;
      padding: 10px 0;
      padding-left: 35px;
      position: relative;
      &.item-wrapper-left-right {
        justify-content: space-between;
        padding-right: 5px;
        .left {
          display: flex;
          align-items: center;
        }
        .right {
          display: flex;
          align-items: center;
          img {
            width: 17px;
            cursor: pointer;
          }
        }
      }
      &.item-wrapper-left-right-hover {
        .el-icon-error {
          display: none;
          color: #999999;
        }
        &:hover {
          .el-icon-error {
            display: inline-block;
          }
        }
      }
      .el-icon-caret-right {
        position: absolute;
        left: 10px;
        font-size: 12px;
        transition: 0.3s;
        &.unfolder {
          transform: rotate(0.25turn);
        }
      }

      &.create {
        svg {
          position: absolute;
          top: -10px;
          left: 70px;
        }
      }

      &.selected {
        font-weight: bold;
        color: black;
      }
      &:hover {
        color: #4a25c9;
      }
      .item-img {
        display: flex;
        align-items: center;
        img {
          width: 15px;
        }
        &.recycle-item-img,
        &.document-item-img {
          img {
            width: 20px;
          }
        }
      }
      .item-text {
        margin-left: 5px;
      }
    }
    .item-dropdown {
      .item-wrapper {
        .item-img {
          img {
            width: 20px;
          }
        }
        &:hover {
          color: #4a25c9;
          background: #e4e7ed;
        }
        &.selected {
          font-weight: bold;
          color: black;
        }
      }
    }
  }
}
.use-brief {
  margin-top: 15px;
  padding-left: 40px;
  text-decoration: underline;
  a {
    color: #C0C4CC;
    font-size: 14px;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.el_container {
  background: #fff;
}
.getui-popup-box {
  $content-width: 326px;
  z-index: 9999;
  position: fixed;
  left: 50%;
  top: 50%;
  box-sizing: border-box;
  // width: 310px;
  width: $content-width;
  padding: 30px;
  transform: translate(-($content-width/2), -127px);
  display: flex;
  flex-flow: column wrap;
  align-items: center;
  background: #fff;
  // border-radius: 14px;
  box-shadow: 0 0 10px 0 rgba(201, 201, 201, 0.3);
  .getui-popup-title-delete {
    font-size: 18px;
    margin-bottom: 10px;
  }
  .get-ui-close-btn {
    position: absolute;
    top: 5px;
    right: 8px;
    font-size: 21px;
    cursor: pointer;
  }
  .getui-popup-title {
    font-size: 16px;
    letter-spacing: 0.26px;
    line-height: 24px;
    text-align: center;
    color: #050121;
  }
  .img-bg {
    width: 80px;
    height: 80px;
    padding: 20px;

    background-color: #f9f8fe;
    border-radius: 50%;

    img {
      width: 100%;
    }
  }

  .getui-popup-content {
    font-size: 16px;
    color: #06070d;
    letter-spacing: 0.26px;
    text-align: center;
    line-height: 16px;

    width: 100%;
    text-align: center;
  }
  .delete-style {
    line-height: 20px;
  }
  .getui-popup-input {
    border: 0px;
    border-bottom: 1px solid #f2f2f2;
    padding: 9px 15px;
    text-align: center;
    margin-top: 30px;
    color: #1e1e1e;
    outline: none;

    &:focus {
      border-bottom: 1px solid #06070d;
    }

    &::-webkit-input-placeholder {
      font-size: 16px;
      color: #999999;
      letter-spacing: 0.26px;
      text-align: center;
      line-height: 16px;
    }
  }
  .getui-popup-toolbar {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-around;

    width: 100%;
    margin-top: 30px;

    .getui-popup-button {
      text-align: center;
      cursor: pointer;
      border-radius: 3px;
      border: 1px solid #d4d6da;
      background-color: white;
      font-size: 14px;
      color: #5c5c5c;
      letter-spacing: 0.27px;
      line-height: 14px;
      min-width: 97px;
      padding: 9px 13px;

      &.highlight {
        background-color: #4a25ba;
        font-size: 14px;
        color: #ffffff;
        letter-spacing: 0.27px;
        line-height: 14px;
      }
    }
  }
}
// 左侧导航栏样式
.col-folder-list {
  z-index: 3;
  background-color: #fff;
  overflow-y: auto;
  overflow-x: visible;
  display: block;
  border-right: 1px solid #eee;
  margin-right: 20px;
  width: 220px;
  position: fixed;
  height: 100vh;
  padding-top: 10px;

  // 新建文章
  .article-new-panel {
    display: flex;
    justify-content: center;
    position: relative;
    margin: 30px 0px;
    .btn-article-new {
      position: relative;
      width: 150px;
      height: 40px;
      background: #4a19bd;
      border-radius: 4px;
      display: inline-block;
      color: #fff;
      text-align: center;
      line-height: 40px;
      cursor: pointer;
    }
    .el-dropdown-item-style {
      padding: 0 28px;
    }
    .article-new-drop {
      padding: 3px 10px;
      position: absolute;
      top: calc(100% + 5px);
      left: 0px;
      background: #fff;
      border-radius: 5px;
      box-shadow: 0px 0px 3px 0 #eee;
      width: 100%;
      z-index: 1;
      color: #333;
    }
    .article-new-drop-item {
      padding: 3px 5px;
      text-align: left;
      cursor: pointer;
      display: flex;
      align-items: center;
      svg {
        margin-right: 5px;
      }
      &.bottom-line {
        border-bottom: 1px solid #f9f9f9;
      }
    }
  }
  // 导航
  .folder-nav-item {
    margin: 5px 0px;
    // 公共属性
    .nav-1 {
      height: 40px;
      line-height: 40px;
      padding-left: 13px;
      color: #666;
      display: flex;
      align-items: center;
      z-index: 1;
      position: relative;
      cursor: pointer;
      .nav-1-icon {
        width: 30px;
        height: 100%;
      }
      &:hover {
        background: #f2f6fc;
        color: #4a25ba;
        .nav-1-title {
          color: #4a25ba;
        }
      }
    }
    &.selected {
      background: #f2f6fc;
      .nav-1 {
        color: #1e1e1e;
        font-weight: bold;
      }
    }
    &.all-panel {
      .nav-1 {
        padding-left: 35px;
        img {
          width: 14px;
        }
      }
    }
    // 我的文集
    &.folder-panel {
      .nav-1 {
        .el-icon-caret-right {
          transition: 0.3s;
          font-size: 10px;
          width: 15px;
          &.unfold {
            transform: rotate(0.25turn);
          }
        }
        .nav-1-icon {
          background: no-repeat center/20px
            url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/%E6%9C%AA%E9%80%89%E4%B8%AD.png');
        }
        .folder-add-btn {
          position: absolute;
          right: 0px;
          top: 0px;
          bottom: 0px;
          width: 25px;
          background: no-repeat center/16px
            url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/folder-add-btn.png');
        }
        &.selected {
          .nav-1 {
            .nav-1-icon {
              background: no-repeat center/20px
                url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/%E9%80%89%E4%B8%AD.png');
            }
          }
        }
        &:hover {
          background: #e4e7ed;
          color: #4a25ba;
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/%E7%82%B9%E4%BA%AE.png');
          }
          .folder-add-btn {
            background: no-repeat center/16px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%88%91%E7%9A%84%E6%96%87%E7%AB%A0/folder-add-btn-hover.png');
          }
        }
      }
      // 我的文集列表
      .folder-list {
        max-height: calc(100vh - 340px);
        overflow-y: auto;
        transition: 1s;
        height: auto;
        &.unfold {
          height: 0px;
          overflow-y: hidden;
        }
        .folder-item {
          line-height: 32px;
          height: 32px;
          padding-left: 26px;
          .folder-del-btn {
            display: none;
            margin-right: 3px;
          }
          .folder-icon {
            width: 30px;
            height: 100%;
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E6%9C%AA%E9%80%89%E4%B8%AD.png');
          }
          &.selected {
            background: #e4e7ed;
            color: #1e1e1e;
            font-weight: bold;
            .folder-icon {
              background: no-repeat center/20px
                url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E9%80%89%E4%B8%AD.png');
            }
          }
          &:hover {
            background: #e4e7ed;
            color: #4a25ba;
            .folder-del-btn {
              display: block;
              color: #999999;
            }
            .folder-icon {
              background: no-repeat center/20px
                url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E7%82%B9%E4%BA%AE.png');
            }
          }

          &.drag-over {
            background: #e4e7ed;
            color: #4a25ba;
            z-index: 2000;
            // transform: scale(1.3);
            .folder-del-btn {
              display: block;
              color: #999999;
            }
            .folder-icon {
              background: no-repeat center/20px
                url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%96%87%E4%BB%B6%E5%A4%B9/%E7%82%B9%E4%BA%AE.png');
            }
          }
          div {
            display: flex;
            align-items: center;
            height: 100%;
            span {
              overflow: hidden;
              white-space: nowrap;
              text-overflow: ellipsis;
            }
          }
        }
      }
    }
    // 收藏
    &.collection-panel {
      .nav-1 {
        padding-left: 28px;
        .nav-1-icon {
          background: no-repeat center/20px
            url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%94%B6%E8%97%8F/%E6%9C%AA%E9%80%89%E4%B8%AD%E5%A4%87%E4%BB%BD.png');
        }
        &:hover {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%94%B6%E8%97%8F/%E7%82%B9%E4%BA%AE%E5%A4%87%E4%BB%BD.png');
          }
        }
      }
      &.selected {
        .nav-1 {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E6%94%B6%E8%97%8F/%E9%80%89%E4%B8%AD%E5%A4%87%E4%BB%BD.png');
          }
        }
      }
    }
    &.home-panel {
      .nav-1 {
        padding-left: 28px;
        .nav-1-icon {
          background: no-repeat center/20px
            url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/home/home.png');
        }
        &:hover {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/home/home-hover.png') !important;
          }
        }
      }
      &.selected {
        .nav-1 {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/home/home.png');
          }
        }
      }
    }
    // 回收站
    &.recycle-bin {
      .nav-1 {
        padding-left: 28px;
        .nav-1-icon {
          background: no-repeat center/20px
            url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E5%9B%9E%E6%94%B6%E7%AB%99/%E6%9C%AA%E9%80%89%E4%B8%AD.png');
        }
        &:hover {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E5%9B%9E%E6%94%B6%E7%AB%99/%E7%82%B9%E4%BA%AE.png');
          }
        }
      }
      &.selected {
        .nav-1 {
          .nav-1-icon {
            background: no-repeat center/20px
              url('https://get-file.oss-cn-hangzhou.aliyuncs.com/base/pc-nuxt/assets/img/folder/%E5%9B%9E%E6%94%B6%E7%AB%99/%E9%80%89%E4%B8%AD.png');
          }
        }
      }
      &.drag-over {
        background: #f2f6fc;
        color: #4a25ba;
        .nav-1-title {
          color: #4a25ba;
        }
      }
    }
  }
  .folder-nav-hr {
    margin: 5px 3%;
    width: 94%;
  }
}
</style>
<style lang="stylus" scoped>
.guide-box {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  color: #fff;
  z-index: 9999;

  .img-box {
    padding-left: 39px;
    padding-top: 90px;
    margin: 0 auto;

    .guide-img {
      width: 425px;
      // height: 250px;
      height: auto;
    }

    .guide-know {
      border: 1px solid #fff;
      width: 102px;
      height: 36px;
      line-height: 36px;
      color: #fff;
      text-align: center;
      cursor: pointer;
      margin-bottom: 40px;
      margin-left: 30px;
    }
  }
}

input {
  outline-color: invert;
  outline-style: none;
  outline-width: 0;
  border-style: none;
  text-shadow: none;
  -webkit-appearance: none;
  -webkit-user-select: text;
  outline-color: transparent;
  box-shadow: none;
}

.nc-popup-box-bg {
  z-index: 999;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.3);
}

.nc-popup-box.share {
  padding: 0 0 20px 0;

  .nc-popup-title-wrapper {
    box-sizing: border-box;
    padding: 20px 20px 10px;
    width: 100%;
    display: flex;
    justify-content: center;
    border-bottom: 1px solid #f2f2f2;

    .nc-popup-title {
      cursor: pointer;
      margin-right: 30px;
      position: relative;
    }

    .nc-popup-title:last-child {
      margin: 0;
    }

    .nc-popup-title.active {
      color: #285fce;
    }

    .nc-popup-title.active::after {
      position: absolute;
      content: ' ';
      bottom: -10px;
      left: 50%;
      transform: translateX(-50%);
      width: 100%;
      height: 4px;
      background-color: #285fce;
      border-radius: 10px;
    }
  }

  .nc-popup-erwei {
    width: 160px;
    height: 160px;
    margin: 20px 0;
  }

  .nc-popup-bottom {
    display: flex;
    align-items: center;

    .nc-popup-hint {
      width: 30px;
      height: 30px;
      margin-right: 10px;
    }
  }
}

.nc-popup-box.path {
  height: auto;

  .nc-popup-path {
    width: 100%;
    color: #285fce;
    background-color: #fff;
    box-sizing: border-box;
    border: 1px solid #285fce;
    padding: 10px 20px;
    border-radius: 6px;
    margin: 20px 0 40px;
  }
}

.nc-popup-box.delete {
  .nc-popup-content {
    margin-bottom: 42px;
  }
}

.nc-popup-box.more {
  width: 438px;
  height: auto;
  transform: translate(-219px, -244px);

  .nc-popup-image {
    margin-bottom: 10px;
  }

  .nc-popup-count {
    font-size: 12px;
    color: #d4d6da;
  }

  .nc-popup-i {
    padding-top: 40px;
    margin-bottom: 0;
    width: 100%;
    text-align: left;
    font-size: 20px;
    border-bottom: 1px solid black;
  }

  .nc-popup-info {
    width: 100%;

    .nc-popup-info-title {
      margin-top: 30px;
      font-size: 14px;
      color: #333;
    }

    .nc-popup-info-lable {
      width: 100%;
      font-size: 12px;
      color: #666;
      margin-top: 12px;

      .nc-popup-info-avatar {
        width: 20px;
        height: 20px;
        margin: 0 10px;
        vertical-align: top;
      }
    }

    .nc-popup-info-private {
      display: flex;
      align-items: center;
      margin-bottom: 24px;
      position: relative;
      padding: 16px 0;

      .popup-private-title {
        color: #999;
        margin-right: 50px;
        font-size: 12px;
      }

      .popup-private-content {
        position: absolute;
        left: 80px;
        padding: 6px;

        .g-switch-outter {
          position: relative;
          width: 26px;
          height: 16px;
          border-radius: 60px;
          transition: all 1s ease-in-out;
          background-color: #999;

          .g-switch-inner {
            position: absolute;
            right: 11px;
            width: 14px;
            height: 14px;
            border-radius: 60px;
            background: #fff;
            transition: 0.5s ease;
            z-index: 9999;
            top: 1px;
          }

          .g-switch-inner.active {
            right: 1px;
          }
        }

        .g-switch-outter.active {
          background: #285fce;
        }
      }
    }
  }
}

.nc-popup-box {
  z-index: 999999;
  position: fixed;
  left: 50%;
  top: 50%;
  box-sizing: border-box;
  width: 310px;
  padding: 24px;
  transform: translate(-155px, -127px);
  display: flex;
  flex-flow: column wrap;
  align-items: center;
  background: #fff;
  border-radius: 14px;

  .nc-popup-image {
    box-sizing: border-box;
    width: 80px;
    height: 80px;
    padding: 18px;
    border-radius: 100%;
    background-color: #eaeffa;
    margin-bottom: 37px;
  }

  .nc-popup-i {
    box-sizing: border-box;
    width: 140px;
    padding: 10px 0;
    border-bottom: 1px solid #4377d8;
    font-size: 16px;
    text-align: center;
    margin-bottom: 24px;
  }

  .nc-popup-toolbar {
    display: flex;
    justify-content: center;

    .nc-popup-button {
      box-sizing: border-box;
      width: 70px;
      height: 26px;
      background-color: #4377d8;
      border-radius: 6px;
      color: #fff;
      text-align: center;
      line-height: 26px;
      margin-right: 53px;
      cursor: pointer;
    }

    .nc-popup-button:last-child {
      margin-right: 0;
    }

    .nc-popup-button.cancel {
      border: 1px solid #4377d8;
      background-color: #fff;
      color: #4377d8;
      line-height: 24px;
    }
  }
}

.note-more-wrapper {
  display: block;
  background-color: white;
  width: 105px;
  border-radius: 4px;
  padding: 0 4px;
  box-shadow: 0 0 10px hsla(0, 0%, 79%, 0.85);

  .note-more-item {
    display: block;
    color: #909399;
    padding: 6px 0;
    border-bottom: 1px solid #f2f2f2;
    cursor: pointer;
    font-size: 15px;
    text-align: center;

    &.last-of-type {
      border-bottom: 0px;
    }
  }
}
</style>

<style lang="stylus" scoped>
#knowledge {
  PAGE_BG_COLOR = #F3F3F8;
  min-width: 100%;
  position: relative;
  background-color: #fff;

  .content-main {
    height: calc(100vh - 58px);
    overflow: hidden;
    position: relative;
    COL_FOLDER_WIDTH = 271px;
    COL_FOLDER_HEIGHT = 52px;

    .col-folder-list {
      z-index: 3;
      background-color: #fff;
      overflow-y: auto;
      overflow-x: visible;
      display: block;
      border-right: 1px solid #eee;
      margin-right: 20px;
      width: 220px;
      position: fixed;
      height: 100vh;

      .changeRecyclebinStyle {
        border-right: solid 1px #e6e6e6;
        list-style: none;
        position: relative;
        margin: 0;
        padding-left: 0;
        background-color: #FFF;
        display: flex;

        img {
          width: 16px;
          height: 16px;
        }

        &:before {
          display: table;
          content: '';
        }

        .folder_recyclebin {
          margin: 0;
          overflow: visible;
          font-size: 14px;
          color: #303133;
          cursor: pointer;
          -webkit-transition: border-color 0.3s, background-color 0.3s, color 0.3s;
          transition: border-color 0.3s, background-color 0.3s, color 0.3s;
          box-sizing: border-box;
          height: 30px;
          line-height: 30px;
          position: relative;
          align-items: center;
          padding-left: 29px;

          &:hover {
            background-color: #ede9f8;
          }
        }
      }

      .recycleStyle {
        position: absolute;
        bottom: 110px;
        width: 100%;
        background: #d2d4dc;
        z-index: 3;
      }
    }

    .btn-folder-list-backto-top {
      position: absolute;
      bottom: 20px;
      left: 230px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      width: 50px;
      height: 50px;
      border-radius: 50%;
      background-color: white;
      font-size: 12px;
      color: #A6AAD4;
      letter-spacing: 0.23px;
      background: #FFFFFF;
      box-shadow: 3px 3px 10px 0 rgba(0, 22, 135, 0.2);
      text-align: center;
      cursor: pointer;
    }

    .folder-list-header {
      display: block;
      padding-top: 15px;
      padding-bottom: 12px;
      padding-left: 27px;
      padding-right: 17.5px;
      // position absolute
      top: 0;
      left: 0;
      max-width: 261px;
      width: 100%;
      background-color: white;
      border-top-left-radius: 5px;
      border-top-right-radius: 5px;
      border-right: 1px solid #eee;
      // border-right: 20px solid #f3f3f8
      height: COL_FOLDER_HEIGHT;
      overflow: hidden;
      z-index: 2;

      .folder-list-header-left {
        font-size: 12px;
        color: #999999;
        letter-spacing: 0.33px;
        float: left;

        .folder-count {
          // font-size: 18px;
          // color: #5383FF;
          // letter-spacing: 0.34px;
          font-size: 26px;
          color: #FFB440;
          letter-spacing: 0.49px;
          line-height: 26px;
        }
      }

      .folder-list-header-right {
        float: right;
      }

      .btn-add-folder {
        display: inline-block;
        width: 22px;
        height: 22px;
        margin-top: 3.5px;
        transition: all 0.2s ease-out;

        &:hover {
          transform: scale(1.3);
        }
      }
    }

    .folder-list-content {
      overflow: visible;

      .folder-item-container {
        display: block;
        border-top: 5px solid white;
        position: relative;
      }

      .folder-item-container-head {
        position: absolute;
        top: -52px;
      }

      .folder-item {
        display: block;
        padding: 0 24px;
        // padding-bottom 15px
        transition: all 0.2s ease-in-out;
        background-color: white;
        box-sizing: border-box;
        font-family: PingFangSC-Regular;
        font-size: 13px;
        color: #1E1E1E;
        letter-spacing: 0.25px;
        line-height: 13px;

        &.light-up {
          animation-name: light-up-border;
          animation-iteration-count: 2;
          animation-direction: both;
          animation-fill-mode: both;
          animation-duration: 0.4s;
        }

        @keyframes light-up-border {
          0% {
            border: 1px solid rgba(#5183fd, 0);
          }

          50% {
            border: 1px solid #5183fd;
          }

          100% {
            border: 1px solid rgba(#5183fd, 0);
          }
        }

        &.selected {
          background-color: #F9F8FE;
        }

        &:active {
          opacity: 0.8;
        }

        &.drag-over, &:hover {
          // background-color: #5482FF;
          background-color: #4A25BA;
          // border-radius: 5px;
          transform: scale(1.05) translateX(13px);
          z-index: 100;
          box-shadow: 0px 3px 18px 0px #A0A0A0;

          .folder-header-row {
            .folder-title {
              font-weight: bold;
              width: 110px;
              color: #FFFFFF;
              // white-space: normal;
              overflow: hidden;
              text-overflow: ellipsis;
              // -webkit-box-orient: vertical;
              // display: -webkit-box;
              // -webkit-line-clamp: 4;
              word-wrap: break-word;
              word-break: break-all;
              // height: 16px;
              // line-height: 0;
            }

            .folder-btns {
              display: flex;
            }
          }

          .folder-detail {
            color: white;
            border-bottom: 1px solid #5482FF;
          }
        }

        /* 有文件被拖到该知识夹上空 */
        .drag-over, .folder-header-row {
          display: block;
          width: 100%;
          overflow: hidden;
          margin-bottom: 10px;

          .folder-title {
            font-size: 16px;
            font-weight: 600;
            color: #333333;
            letter-spacing: 0.3px;
            line-height: 16px;
            width: 100%;
            float: left;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            // height: 16px;
            // line-height: 0;
          }

          .folder-btns {
            flex-direction: row;
            float: right;
            display: none;
            color: white;
            text-align: center;
            padding-top: 2px;

            .g-btn {
              display: inline-block;
              width: 15px;
              height: 15px;
              margin-right: 10px;
              transition: all 0.2s ease-in;

              &.no-margin-right {
                margin-right: 0px;
              }

              &:hover {
                transform: scale(1.3);
              }
            }
          }
        }

        .folder-detail {
          text-align: left;
          opacity: 0.6;
          font-size: 12px;
          color: #666666;
          letter-spacing: 0.23px;
          padding-bottom: 15px;
          border-bottom: 1px solid #E0E5FB;
          line-height: 0;
        }
      }
    }

    .col-card-list {
      position: absolute;
      left: 220px;
      display: inline-block;
      justify-content: flex-start;
      align-items: flex-start;
      background: #fff;
      width: calc(100% - 220px);
      min-height: calc(100vh - 57px);
      padding-left: 20px;
      overflow-y: auto;
      height: 100%;
      bottom: 0px;

      &.loading-data {
        opacity: 0.3;
      }

      .folder-search {
        margin-bottom: 40px;
      }

      .line-img {
        margin-top: 15px;
        display: flex;
        align-items: flex-start;
        width: 500px;
        margin-bottom: 5px;

        .close {
          display: flex;
          justify-content: center;
          align-items: center;
          width: 18px;
          height: 18px;
          margin-left: -10px;
          margin-top: -8px;
          background-color: #A0A0A1;
          border-radius: 50%;
          cursor: pointer;
        }

        img {
          width: 500px;

          &:hover {
            cursor: pointer;
          }
        }
      }
    }
  }
}

.no-scroll-bar::-webkit-scrollbar {
  display: none;
}

.float-promotion-new {
  display: flex;
  justify-content: center;

  .el-dropdown-link {
    width: 150px;
    height: 40px;
    background: #4a19bd;
    border-radius: 4px;
    display: inline-block;
    color: #fff;
    text-align: center;
    line-height: 40px;
    cursor: pointer;

    .el-dropdown-item-style {
      padding: 0 28px;
    }
  }
}

// 新建文章 文件 部分
// .article-new-panel
// display flex
// justify-content center
// position relative
// margin 30px 0px
// .btn-article-new
// position relative
// width 150px
// height 40px
// background #4a19bd
// border-radius 4px
// display inline-block
// color #fff
// text-align center
// line-height 40px
// cursor pointer
// .el-dropdown-item-style
// padding 0 28px
// .article-new-drop
// padding 3px 10px
// position absolute
// top calc(100% + 5px)
// left 0px
// background #fff
// border-radius 5px
// box-shadow 0px 0px 3px 0 #eee
// width 100%
// z-index 1
// color #333
// .article-new-drop-item
// padding 3px 5px
// text-align left
// cursor pointer
// display flex
// align-items center
// svg
// margin-right 5px
// &.bottom-line
// border-bottom 1px solid #f9f9f9
// 我的文集
.iconright {
  float: right;
  margin-right: 10px;
}

.dtIconright {
  float: right;
  margin-right: 10px;
  margin-top: 8px;
}

.fl {
  float: left;

  svg {
    vertical-align: -5%;
    margin: 0px 5px;
  }
}

.cl {
  clear: both;
}

.fr {
  float: right;
}

.w85 {
  width: 85px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.clearfix:after, .clearfix:before {
  content: '';
  display: table;
}

.clearfix:after {
  clear: both;
}

.clearfix {
  zoom: 1;
}

.recycleStyleSvg {
  position: absolute;
  left: 28px;
  top: 8px;
  z-index: 12;
}
</style>
