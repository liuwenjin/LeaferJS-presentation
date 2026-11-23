<template data-edit-id="1">
  <div class="tw-relative tw-px-3 tw-pt-3 wizard-bg tw-flex tw-flex-col tw-items-center tw-w-full tw-h-full"
    ref="rootComponentContainer"
    data-edit-id="2">
    <div v-if="(isSolo || content.isSolo) && dataEditable"
      class="wizard-navbar tw-flex tw-items-center tw-justify-between tw-max-w-7xl tw-mx-auto tw-w-full"
      data-edit-id="100">
      <div :style="{ width: flag && step !== 0 ? '100%' : '' }"
        class="tw-flex tw-items-center tw-gap-3 logoInfoArea"
        data-edit-id="101">
        <h3 class="tw-text-2xl tw-text-gray-900 tw-tracking-tight tw-m-0 tw-font-medium logo-title tw-w-full"
          data-edit-id="102">
          <slot v-if="step === 0" data-edit-id="292">
            <div class="logo-badge tw-flex tw-items-center tw-justify-center"
              data-edit-id="409">
              <span class="logo-badge-icon" data-edit-id="410">{{
                icon || "∑"
              }}</span>
            </div>
            <span class="logo-text" data-edit-id="242">
              <span class="title-text" data-edit-id="368">{{ title }}</span>
              <span class="slogn-text" data-edit-id="369">{{ introduce }}</span>
            </span>
          </slot>
          <slot v-else data-edit-id="308">
            <span title="返回"
              class="goBackBtn"
              @click="goBack()"
              data-edit-id="295">
              <el-icon data-edit-id="384">
                <arrow-left-bold data-edit-id="385" />
              </el-icon>
            </span>
            <slot v-if="
                accountData && Object.keys(accountData).length && !isStreaming
              "
              data-edit-id="357">
              <el-dropdown @command="handleContentCommand"
                style="vertical-align: middle"
                :teleported="false"
                append-to="parent"
                trigger="click"
                data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  操作
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>

                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item command="htmlDownload">
                      存档 HTML
                    </el-dropdown-item>

                    <el-dropdown-item command="markdownDownload">
                      存档 Markdown
                    </el-dropdown-item>

                    <el-dropdown-item command="imageDownload">
                      导出图片
                    </el-dropdown-item>

                    <el-dropdown-item command="editContent">
                      {{ viewMode ? "查看内容" : "手动修改" }}
                    </el-dropdown-item>

                    <el-dropdown-item command="copyLink">
                      复制链接
                    </el-dropdown-item>

                    <el-dropdown-item command="readLocal">
                      本地读档
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <slot v-if="viewMode">
                <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
                </el-tag>
                <el-button class="startModifyBtn"
                  size="small"
                  text
                  type="primary"
                  @click="startModify">开始修改</el-button
                >
              </slot>
              <el-input :style="{ width: flag ? '300px' : '300px' }"
                v-model="modifyInfo"
                v-else
                placeholder="输入变更描述..."
                class="custom-input-group"
                clearable
                @keyup.enter="handleConfirm"
                data-edit-id="342">
                <template #append>
                  <el-button :loading="modifyLoading"
                    :disabled="modifyLoading"
                    style="color: #fff"
                    class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary"
                    @click="handleConfirm">
                    <span class="font-medium px-2">
                      <i class="bi bi-magic"></i>
                    </span>
                  </el-button>
                </template>
              </el-input>
            </slot>
            <span v-else style="font-size: 14px"> {{ title }}</span>
            <span class="versionListArea"
              style="float: right; margin-right: 10px"
              v-if="versionList && versionList.length !== 0 && !isStreaming">
              <el-dropdown @command="handleVersionChange"
                trigger="click"
                :teleported="false"
                append-to="parent"
                style="vertical-align: middle"
                v-if="step === 1"
                data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  版本
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>
                <template #dropdown>
                  <el-dropdown-menu>
                    <slot v-for="(item, index) in versionList">
                      <el-dropdown-item :style="{
                          fontWeight: index === currentVersionIndex ? 800 : 300,
                        }"
                        :command="index"
                        class="tw-justify-between">
                        <slot v-if="index === currentVersionIndex">
                          {{ getVersionLabel(item) }}
                        </slot>
                        <slot v-else>
                          {{ getVersionLabel(item) }}
                          <el-button style="margin-left: 5px"
                            @click.stop="checkDiff(index)"
                            size="small">对比</el-button
                          >
                        </slot>
                      </el-dropdown-item>
                    </slot>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <span v-if="!flag" class="versionLabel">{{
                getVersionLabel(versionList[currentVersionIndex])
              }}</span>
            </span>
          </slot>
        </h3>
      </div>
      <div class="navbar-actions" data-edit-id="103">
        <slot v-if="step === 0 && !flag">
          <el-button class="wizard-action-btn navbar-action-btn"
            @click="openFeedbackDialog()">
            问题反馈
          </el-button>
        </slot>
        <span class="navbar-divider" aria-hidden="true"></span>
        <div class="navbar-account">
          <div appName="loginRegItem"
            component="CardItem"
            :documentId="query ? query.documentId : ''"
            class="login-bar-card refined-user-card"
            data-edit-id="104"></div>
        </div>
      </div>
    </div>

    <div ref="loadingEffectArea"
      style="height: 100%; position: relative; z-index: 0"
      class="tw-max-w-7xl tw-y-full tw-mx-auto tw-w-full"
      data-edit-id="3">
      <!-- 输入控制面板 -->
      <div v-if="step === 0"
        class="wizard-input-panel tw-relative tw-bg-white tw-border tw-border-slate-100 tw-rounded-2xl tw-shadow-sm tw-p-5 tw-transition-all hover:tw-shadow-md"
        data-edit-id="4">
        <div class="tw-flex tw-flex-col md:tw-flex-row tw-gap-5 tw-items-stretch">
          <div class="tw-flex-1 tw-flex tw-flex-col tw-justify-between tw-min-w-0">
            <div class="input-spell-box tw-w-full" data-edit-id="122">
              <el-input :placeholder="inputPlaceholder"
                :rows="4"
                resize="none"
                v-model="content.inputText"
                type="textarea"
                class="feature-input tw-w-full"
                data-edit-id="131"></el-input>
            </div>

            <div class="tw-flex tw-items-center tw-flex-wrap tw-gap-2 tw-mt-4">
              <span v-if="!flag"
                class="tw-text-sm tw-text-slate-400 tw-font-medium tw-whitespace-nowrap">
                试试看：
              </span>
              <button v-for="d in exampleList"
                :key="d.title || d.name"
                @click="inputExampleText(d)"
                class="example-chip tw-group tw-flex tw-items-center tw-gap-1.5 tw-px-3 tw-py-1.5 tw-text-[13px] tw-bg-slate-50 hover:tw-bg-blue-50 hover:tw-text-blue-600 tw-text-slate-600 tw-rounded-lg tw-transition-colors tw-whitespace-nowrap active:tw-scale-95">
                <span class="chip-icon">{{ d.icon || "" }}</span>
                <span>{{ d.name }}</span>
              </button>
            </div>
          </div>

          <div class="tw-h-px md:tw-hidden tw-bg-slate-100 tw-w-full"></div>
          <div class="tw-hidden md:tw-block tw-w-px tw-bg-slate-100 tw-my-1 tw-self-stretch"></div>

          <div class="aigcOptionArea tw-flex tw-flex-row md:tw-flex-col tw-justify-between md:tw-justify-end tw-gap-3 tw-w-full md:tw-w-32 tw-shrink-0">
            <el-button :style="{ marginTop: flag ? '5px' : '' }"
              type="primary"
              :disabled="!content.inputText && !description"
              :loading="loading"
              @click="handleGenerate"
              class="cast-spell-btn tw-flex-1 md:tw-flex-none tw-mt-6 tw-h-10 tw-shadow-sm"
              data-edit-id="11">
              <i class="bi bi-lightning-charge-fill tw-mr-1.5"
                data-edit-id="12"></i>
              立即生成
            </el-button>

            <el-button v-if="!flag"
              text
              type="info"
              :loading="readingFile"
              :disabled="readingFile"
              @click="triggerFileInput"
              class="tw-flex-1 md:tw-flex-none tw-justify-center tw-h-10 hover:!tw-bg-slate-50 tw-text-slate-500 tw-mt-8">
              <i class="bi bi-folder2-open tw-mr-1"></i>
              本地读档
            </el-button>
          </div>
        </div>
      </div>
      <span v-if="!(isSolo || content.isSolo) && step === 1 && dataEditable"
        style="
          float: right;
          position: relative;
          width: 100%;
          text-align: right;
          margin-top: 10px;
          margin-bottom: 3px;
          right: 0px;
          display: inline-block;
        "
        data-edit-id="357">
        <span title="返回"
          class="goBackBtn"
          @click="goBack()"
          data-edit-id="295">
          <el-icon data-edit-id="384">
            <arrow-left-bold data-edit-id="385" />
          </el-icon>
        </span>
        <span class="versionListArea"
          style="float: left"
          v-if="versionList && versionList.length !== 0 && !isStreaming">
          <el-dropdown @command="handleVersionChange"
            trigger="click"
            :teleported="false"
            append-to="parent"
            v-if="step === 1"
            data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              版本
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>
            <template #dropdown>
              <el-dropdown-menu>
                <slot v-for="(item, index) in versionList">
                  <el-dropdown-item class="tw-justify-between"
                    :command="index"
                    :style="{
                      fontWeight: index === currentVersionIndex ? 800 : 300,
                    }">
                    <slot v-if="index === currentVersionIndex">
                      {{ getVersionLabel(item) }}
                    </slot>
                    <slot v-else>
                      {{ getVersionLabel(item) }}
                      <el-button style="margin-left: 5px"
                        @click.stop="checkDiff(index)"
                        size="small">对比</el-button
                      >
                    </slot>
                  </el-dropdown-item>
                </slot>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <span v-if="!flag" class="versionLabel">{{
            getVersionLabel(versionList[currentVersionIndex])
          }}</span>
        </span>
        <slot v-if="step === 1 && !isStreaming">
          <el-dropdown @command="handleContentCommand"
            trigger="click"
            :teleported="false"
            append-to="parent"
            data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              操作
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>

            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item command="htmlDownload">
                  存档 HTML
                </el-dropdown-item>

                <el-dropdown-item command="markdownDownload">
                  存档 Markdown
                </el-dropdown-item>

                <el-dropdown-item command="imageDownload">
                  导出图片
                </el-dropdown-item>

                <el-dropdown-item command="editContent">
                  {{ viewMode ? "查看内容" : "手动修改" }}
                </el-dropdown-item>

                <el-dropdown-item command="copyLink">
                  复制链接
                </el-dropdown-item>

                <el-dropdown-item command="readLocal">
                  本地读档
                </el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <slot v-if="viewMode">
            <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
            </el-tag>
            <el-button class="startModifyBtn"
              text
              type="primary"
              size="small"
              @click="startModify">开始修改</el-button
            >
          </slot>
          <el-input v-else
            :style="{ width: flag ? '260px' : '400px' }"
            v-model="modifyInfo"
            placeholder="输入变更描述..."
            class="custom-input-group"
            clearable
            @keyup.enter="handleConfirm"
            data-edit-id="342">
            <template #append>
              <el-button :loading="modifyLoading"
                :disabled="modifyLoading"
                style="color: #fff"
                class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary"
                @click="handleConfirm">
                <span class="font-medium px-2"><i class="bi bi-magic"></i
                ></span>
              </el-button>
            </template>
          </el-input>
        </slot>
      </span>
      <!-- 结果展示面板 -->
      <div class="wizard-result-panel tw-overflow-hidden"
        :style="{
          height: resultPanelHeight,
          'margin-top': '0px',
          zIndex: 0,
        }"
        data-edit-id="110">
        <div v-if="step === 1"
          data-edit-id="117"
          class="resultComponentArea tw-w-full tw-relative"
          ref="resultComponentItem"></div>
        <div v-else
          class="tw-flex-1 tw-w-full tw-text-center tw-py-20 tw-flex tw-flex-col tw-items-center"
          data-edit-id="112">
          <div class="waiting-rune tw-mb-4" data-edit-id="118">
            <!-- 外环 -->
            <div class="rune-ring rune-ring--outer" data-edit-id="276"></div>
            <!-- 内环 -->
            <div class="rune-ring rune-ring--inner" data-edit-id="277"></div>
            <!-- 中心图标 -->
            <el-icon :size="32" class="rune-icon" data-edit-id="119">
              <i class="bi bi-hourglass-split"></i>
            </el-icon>
          </div>
          <p class="waiting-tips" data-edit-id="121">{{ currentTips }}</p>
          <!-- 装饰性点 -->
          <div class="magic-dots" data-edit-id="281">
            <span data-edit-id="282"></span><span data-edit-id="283"></span
            ><span data-edit-id="284"></span>
          </div>
        </div>
      </div>
    </div>
    <el-dialog v-model="feedbackDialog.visible"
      title="反馈与建议"
      width="500"
      draggable>
      <el-input type="textarea"
        :placeholder="feedbackDialog.placeholder"
        v-model="feedbackDialog.message"
        resize="none"></el-input>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="feedbackDialog.visible = false">取消</el-button>
          <el-button type="primary" @click="confirmSendFeedback()">
            确认
          </el-button>
        </div>
      </template>
    </el-dialog>
    <input ref="hiddenFileInput"
      type="file"
      :accept="myAccept"
      class="tw-hidden"
      @change="onFileSelected($event)"
      data-edit-id="109" />
  </div>
</template>

<style scoped>
/* ============================================
   RESET
   ============================================ */
.el-card {
  border: none;
  box-shadow: none;
}

/* ============================================
   DESIGN TOKENS — 学术 · 精准 · 启发
   ============================================ */
* {
  /* 主色：活力蓝（学术蓝） */
  --academic-primary: #0066CC;
  --academic-primary-hover: #0052A3;
  --academic-primary-active: #004C99;
  --academic-primary-soft: rgba(0, 102, 204, 0.08);
  --academic-primary-ring: rgba(0, 102, 204, 0.16);

  /* 背景：冰川灰 */
  --academic-bg: #EEF3F0;
  --academic-bg-deep: #F6F9F7;

  /* 文字：深石板青 */
  --academic-text: #1e293b;
  --academic-text-secondary: #475569;
  --academic-text-tertiary: #94a3b8;

  /* 强调：琥珀金 */
  --academic-accent: #f59e0b;
  --academic-accent-soft: rgba(245, 158, 11, 0.12);

  /* 辅助 */
  --academic-border: rgba(30, 41, 59, 0.08);
  --academic-border-strong: rgba(30, 41, 59, 0.14);

  /* 玻璃面板 */
  --glass-bg: rgba(255, 255, 255, 0.72);
  --glass-shadow: 0 1px 2px rgba(30, 41, 59, 0.04),
    0 8px 24px rgba(30, 41, 59, 0.06);
  --glass-shadow-hover: 0 2px 4px rgba(30, 41, 59, 0.05),
    0 12px 32px rgba(30, 41, 59, 0.09);

  /* 圆角 */
  --radius-card: 16px;
  --radius-control: 10px;

  --font-system: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI",
    "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", Arial, sans-serif;
  --font-mono: "JetBrains Mono", "Fira Code", "SF Mono", Menlo, "Cascadia Code",
    Consolas, monospace;
}

/* ============================================
   全局背景 — 冰川灰 + 学术点阵纹理
   ============================================ */
.wizard-bg {
  font-family: var(--font-system);
  color: var(--academic-text);
  background-color: var(--academic-bg);
  background-image: radial-gradient(
      ellipse 80% 60% at 20% 0%,
      rgba(0, 102, 204, 0.06) 0%,
      transparent 55%
    ),
    radial-gradient(
      ellipse 60% 50% at 100% 100%,
      rgba(245, 158, 11, 0.04) 0%,
      transparent 55%
    ),
    radial-gradient(
      circle at 1px 1px,
      rgba(30, 41, 59, 0.07) 1px,
      transparent 0
    );
  background-size: auto, auto, 22px 22px;
  background-position: 0 0, 0 0, 0 0;
  position: relative;
}

.wizard-bg::before {
  content: "";
  position: absolute;
  inset: 0;
  background-image: linear-gradient(
      to right,
      rgba(0, 102, 204, 0.035) 1px,
      transparent 1px
    ),
    linear-gradient(to bottom, rgba(0, 102, 204, 0.035) 1px, transparent 1px);
  background-size: 88px 88px;
  pointer-events: none;
  z-index: 0;
}

/* ============================================
   导航栏
   ============================================ */
.wizard-navbar {
  position: relative;
  z-index: 1000;
  gap: 20px;
  padding: 10px 4px 18px;
  box-sizing: border-box;
}

.versionIndexSpan {
  font-size: 14px;
  font-weight: 900;
}

.wizard-navbar::after {
  content: "";
  position: absolute;
  bottom: -1px;
  left: 0;
  right: 0;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(0, 102, 204, 0.22) 30%,
    rgba(0, 102, 204, 0.22) 70%,
    transparent 100%
  );
}

/* 右侧操作区：按钮与账号区共用同一条垂直中心线 */
.navbar-actions {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 0 0 auto;
}

.navbar-action-btn {
  height: 40px;
  padding: 0 16px;
}

.navbar-action-btn:focus-visible {
  outline: 2px solid var(--academic-primary-ring);
  outline-offset: 2px;
}

.navbar-divider {
  flex: 0 0 auto;
  width: 1px;
  height: 22px;
  background: var(--academic-border-strong);
  border-radius: 1px;
}

/* 登录 / 用户区：容纳 loginBar 44px 的切换按钮并垂直居中 */
.navbar-account {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  flex: 0 0 auto;
  width: 135px;
  height: 44px;
}

/* loginBar 由 CardItem 渲染，尺寸跟随账号区 */
.login-bar-card {
  width: 100%;
  height: 100%;
}

.logoInfoArea {
  flex: 1 1 auto;
  min-width: 0;
}

.logo-title {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 0;
  font-family: var(--font-system);
}

.logo-badge {
  flex: 0 0 auto;
  width: 40px;
  height: 40px;
  border-radius: 12px;
  background: linear-gradient(135deg, var(--academic-accent) 0%, #fbbf24 100%);
  color: #fff;
  box-shadow: 0 2px 8px rgba(245, 158, 11, 0.26),
    inset 0 1px 0 rgba(255, 255, 255, 0.34);
}

.logo-badge-icon {
  font-size: 20px;
  font-weight: 700;
  line-height: 1;
}

.refined-logo {
  width: 56px;
  height: 56px;
  border-radius: 14px;
  border: 1px solid var(--academic-border);
  background: #fff;
  box-shadow: 0 1px 2px rgba(30, 41, 59, 0.06),
    0 4px 12px rgba(0, 102, 204, 0.08);
  display: inline-block;
  transition: transform 0.25s ease, box-shadow 0.25s ease;
  overflow: hidden;
}

.refined-logo:hover {
  transform: translateY(-1px);
  box-shadow: 0 2px 4px rgba(30, 41, 59, 0.08),
    0 8px 20px rgba(0, 102, 204, 0.14);
}

.logo-text {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 3px;
  flex: 1 1 auto;
  min-width: 0;
  color: var(--academic-text);
  letter-spacing: -0.01em;
  line-height: 1.3;
}

.upload-btn {
  background: #fff !important;
  border: 1px solid var(--academic-border-strong) !important;
  color: var(--academic-text) !important;
  border-radius: 10px !important;
  font-size: 13px !important;
  transition: all 0.18s ease;
}

.upload-btn:hover {
  border-color: var(--academic-primary) !important;
  color: var(--academic-primary) !important;
  box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14);
  transform: translateY(-1px);
}

.title-text {
  font-size: 16px;
  font-weight: 600;
  line-height: 1.35;
  color: var(--academic-text);
  letter-spacing: 0.01em;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.slogn-text {
  font-size: 12px;
  font-weight: 400;
  line-height: 1.4;
  color: var(--academic-text-secondary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.refined-user-card {
  opacity: 1 !important;
}

.el-dropdown-menu__item {
  font-weight: 800;
}

/* ============================================
   面板 — 卡片式架构（2xl 圆角 + 磨砂玻璃）
   ============================================ */
.wizard-input-panel {
  margin-top: -5px;
  margin-bottom: 5px;
}

.wizard-input-panel,
.wizard-result-panel {
  position: relative;
  background: var(--glass-bg);
  border: 1px solid var(--academic-border);
  border-radius: var(--radius-card);
  box-shadow: var(--glass-shadow);
  backdrop-filter: saturate(160%) blur(18px);
  -webkit-backdrop-filter: saturate(160%) blur(18px);
  overflow: hidden;
  transition: box-shadow 0.25s ease, border-color 0.25s ease;
  z-index: 1;
}

.wizard-result-panel {
  position: relative;
  width: 100%;
  border-radius: var(--radius-card);
  overflow: hidden;
  transition: box-shadow 0.25s ease, border-color 0.25s ease;
  z-index: 1;
}

.wizard-input-panel:hover {
  box-shadow: var(--glass-shadow-hover);
}

/* 面板顶部细线强调 —— 学术精准感 */
.wizard-input-panel::before {
  content: "";
  position: absolute;
  top: 0;
  left: 24px;
  right: 24px;
  height: 2px;
  background: linear-gradient(
    90deg,
    transparent 0%,
    var(--academic-primary) 20%,
    var(--academic-primary) 80%,
    transparent 100%
  );
  opacity: 0.55;
  border-radius: 2px;
}

.panel-glow-strip--result {
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(0, 102, 204, 0.5) 25%,
    rgba(0, 102, 204, 0.55) 55%,
    rgba(245, 158, 11, 0.35) 80%,
    transparent 100%
  );
  background-size: 200% 100%;
}

@keyframes shimmerStrip {
  0% {
    background-position: 0% 0;
  }

  100% {
    background-position: 200% 0;
  }
}

/* ============================================
   上传区域（保留样式）
   ============================================ */
.upload-zone {
  width: 80px;
  height: 80px;
  border: 1.5px dashed rgba(0, 102, 204, 0.35);
  border-radius: var(--radius-control);
  background: rgba(0, 102, 204, 0.04);
  cursor: pointer;
  transition: all 0.2s cubic-bezier(0.34, 1.3, 0.64, 1);
  position: relative;
  overflow: hidden;
}

.upload-zone::before {
  content: "";
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at 50% 0%,
    rgba(0, 102, 204, 0.1),
    transparent 70%
  );
  opacity: 0;
  transition: opacity 0.2s ease;
}

.upload-zone:hover {
  border-color: var(--academic-primary);
  border-style: solid;
  background: rgba(0, 102, 204, 0.07);
  transform: translateY(-1px);
  box-shadow: 0 4px 14px rgba(0, 102, 204, 0.16);
}

.upload-zone:hover::before {
  opacity: 1;
}

.upload-zone__icon {
  color: var(--academic-primary) !important;
  transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.upload-zone:hover .upload-zone__icon {
  transform: scale(1.15) rotate(90deg);
}

.upload-zone__label {
  font-size: 11px;
  margin-top: 6px;
  color: var(--academic-primary);
  font-weight: 500;
  letter-spacing: 0.01em;
  font-family: var(--font-system);
}

.selected-image-wrap {
  display: inline-block;
}

.preview-thumbnail {
  width: 80px !important;
  height: 80px !important;
  border-radius: var(--radius-control) !important;
  border: 1px solid var(--academic-border) !important;
  box-shadow: 0 2px 8px rgba(30, 41, 59, 0.08), 0 1px 2px rgba(30, 41, 59, 0.04) !important;
  transition: all 0.2s ease;
}

.preview-thumbnail:hover {
  border-color: var(--academic-primary) !important;
  box-shadow: 0 4px 16px rgba(0, 102, 204, 0.18),
    0 1px 4px rgba(30, 41, 59, 0.06) !important;
  transform: translateY(-1px);
}

.removePictureBtn {
  position: absolute;
  background: #ffffff !important;
  border: 1px solid var(--academic-border-strong) !important;
  color: #dc2626 !important;
  right: -9px;
  top: -9px;
  z-index: 3;
  display: inline-block;
  box-shadow: 0 2px 6px rgba(30, 41, 59, 0.12),
    0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
  transition: all 0.18s ease !important;
}

.removePictureBtn:hover {
  background: #fee2e2 !important;
  border-color: rgba(220, 38, 38, 0.4) !important;
  box-shadow: 0 3px 10px rgba(220, 38, 38, 0.22) !important;
  transform: scale(1.08);
}

.el-popper {
  z-index: 10000 !important;
}

.versionLabel {
  font-size: 12px;
  color: #999;
  vertical-align: middle;
}

.startModifyBtn {
  margin-left: 12px;
}

/* ============================================
   文本输入区 — 对话驱动
   ============================================ */
.input-spell-box {
  background: #ffffff;
  border: 1px solid var(--academic-border-strong);
  border-radius: var(--radius-control);
  padding: 2px;
  transition: all 0.2s ease;
  box-shadow: 0 1px 2px rgba(30, 41, 59, 0.03) inset,
    0 1px 2px rgba(255, 255, 255, 0.8);
}

.input-spell-box:focus-within {
  border-color: var(--academic-primary);
  box-shadow: 0 0 0 3px var(--academic-primary-ring),
    0 1px 2px rgba(30, 41, 59, 0.03) inset;
}

.feature-input .el-textarea__inner {
  height: 70px;
  padding: 12px 14px;
  background: transparent !important;
  color: var(--academic-text) !important;
  box-shadow: none !important;
  font-size: 14px !important;
  font-family: var(--font-system) !important;
  line-height: 1.6 !important;
  border: none !important;
  resize: none !important;
  letter-spacing: 0 !important;
}

.feature-input .el-textarea__inner::placeholder {
  color: var(--academic-text-tertiary);
  font-family: var(--font-system);
}

/* ============================================
   示例芯片
   ============================================ */
.example-hint {
  color: var(--academic-text-tertiary);
  font-weight: 500;
}

.example-chip {
  background: rgba(255, 255, 255, 0.7);
  border: 1px solid var(--academic-border);
  color: var(--academic-text-secondary);
  border-radius: 999px;
  font-family: var(--font-system);
  font-weight: 500;
  backdrop-filter: blur(8px);
  transition: all 0.18s ease;
  cursor: pointer;
}

.example-chip:hover {
  background: #ffffff;
  border-color: var(--academic-primary);
  color: var(--academic-primary);
  box-shadow: 0 2px 8px rgba(0, 102, 204, 0.14);
  transform: translateY(-1px);
}

.example-chip .chip-icon {
  filter: grayscale(40%);
  transition: filter 0.2s ease;
}

.example-chip:hover .chip-icon {
  filter: grayscale(0%);
}

/* ============================================
   生成按钮 — 学术蓝 CTA
   ============================================ */
.cast-spell-btn {
  position: relative;
  background: var(--academic-primary) !important;
  border: 1px solid var(--academic-primary) !important;
  border-radius: var(--radius-control) !important;
  font-weight: 600 !important;
  font-size: 14px !important;
  font-family: var(--font-system) !important;
  letter-spacing: 0.01em !important;
  color: #fff !important;
  padding: 10px 18px !important;
  box-shadow: 0 2px 6px rgba(0, 102, 204, 0.32), 0 1px 2px rgba(30, 41, 59, 0.1),
    inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
  transition: all 0.2s cubic-bezier(0.34, 1.2, 0.64, 1) !important;
  overflow: hidden;
}

.cast-spell-btn::before {
  content: "";
  position: absolute;
  top: 0;
  left: -100%;
  width: 55%;
  height: 100%;
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.18),
    transparent
  );
  transition: left 0.6s ease;
}

.cast-spell-btn:hover::before {
  left: 160%;
}

.cast-spell-btn:hover {
  background: var(--academic-primary-hover) !important;
  border-color: var(--academic-primary-hover) !important;
  transform: translateY(-2px) !important;
  box-shadow: 0 6px 18px rgba(0, 102, 204, 0.4),
    0 2px 5px rgba(30, 41, 59, 0.12), inset 0 1px 0 rgba(255, 255, 255, 0.22) !important;
}

.cast-spell-btn[active="true"] {
  background: var(--academic-primary-active) !important;
}

.cast-spell-btn:active {
  transform: translateY(0px) scale(0.99) !important;
  background: var(--academic-primary-active) !important;
  box-shadow: 0 1px 3px rgba(0, 102, 204, 0.3),
    inset 0 1px 2px rgba(0, 0, 0, 0.1) !important;
}

/* ============================================
   等待状态 — 精密坐标圆环 + 十字准星
   ============================================ */
.waiting-rune {
  position: relative;
  width: 112px;
  height: 112px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.waiting-rune::before,
.waiting-rune::after {
  content: "";
  position: absolute;
  background: rgba(0, 102, 204, 0.15);
}

.waiting-rune::before {
  width: 100%;
  height: 1px;
  top: 50%;
}

.waiting-rune::after {
  width: 1px;
  height: 100%;
  left: 50%;
}

.rune-ring {
  position: absolute;
  border-radius: 50%;
  border: 1.5px solid transparent;
}

.rune-ring--outer {
  inset: 0;
  border-top-color: var(--academic-primary);
  border-right-color: rgba(0, 102, 204, 0.2);
  border-bottom-color: rgba(245, 158, 11, 0.4);
  border-left-color: transparent;
  animation: spinRune 2s linear infinite;
  box-shadow: 0 0 14px rgba(0, 102, 204, 0.14);
}

.rune-ring--inner {
  inset: 14px;
  border-top-color: transparent;
  border-right-color: rgba(245, 158, 11, 0.7);
  border-bottom-color: transparent;
  border-left-color: rgba(0, 102, 204, 0.4);
  animation: spinRune 1.4s linear infinite reverse;
}

@keyframes spinRune {
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
}

.rune-icon {
  color: var(--academic-primary) !important;
  filter: drop-shadow(0 0 6px rgba(0, 102, 204, 0.45))
    drop-shadow(0 0 14px rgba(0, 102, 204, 0.18)) !important;
  animation: runeBreath 2.2s ease-in-out infinite;
  z-index: 1;
}

@keyframes runeBreath {
  0%,
  100% {
    transform: scale(0.95);
    opacity: 0.82;
  }

  50% {
    transform: scale(1.05);
    opacity: 1;
  }
}

.goBackBtn {
  cursor: pointer;
  font-size: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
  line-height: 32px;
  height: 32px;
  width: 30px;
  text-align: center;
  float: left;
  vertical-align: middle;
  margin-right: 10px;
}

.waiting-tips {
  color: var(--academic-text-secondary);
  font-size: 14px;
  font-weight: 400;
  font-family: var(--font-system);
  letter-spacing: 0.01em;
  margin: 0 0 14px;
}

.magic-dots {
  display: flex;
  gap: 6px;
  align-items: center;
}

.magic-dots span {
  display: inline-block;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--academic-primary);
  animation: dotBounce 1.2s ease-in-out infinite;
  opacity: 0.5;
}

.magic-dots span:nth-child(2) {
  animation-delay: 0.18s;
  background: var(--academic-accent);
}

.magic-dots span:nth-child(3) {
  animation-delay: 0.36s;
}

@keyframes dotBounce {
  0%,
  80%,
  100% {
    transform: translateY(0);
    opacity: 0.35;
  }

  40% {
    transform: translateY(-8px);
    opacity: 1;
  }
}

/* ============================================
   结果区域
   ============================================ */
.resultComponentArea {
  position: relative;
  float: left;
  display: inline-block;
  width: 100%;
  height: 100%;
}

/* ============================================
   操作按钮组
   ============================================ */
.action-bar {
  border-top: 1px solid var(--academic-border);
  background: rgba(248, 250, 252, 0.6);
  gap: 8px;
}

.wizard-action-btn {
  background: #ffffff !important;
  border: 1px solid var(--academic-border-strong) !important;
  color: var(--academic-text) !important;
  border-radius: 10px !important;
  font-size: 13px !important;
  font-weight: 500 !important;
  font-family: var(--font-system) !important;
  letter-spacing: 0.01em !important;
  box-shadow: 0 1px 3px rgba(30, 41, 59, 0.05),
    0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
  transition: all 0.18s ease !important;
}

.wizard-action-btn:hover {
  background: #ffffff !important;
  border-color: var(--academic-primary) !important;
  color: var(--academic-primary) !important;
  transform: translateY(-1px) !important;
  box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14),
    0 1px 3px rgba(30, 41, 59, 0.04) !important;
}

.wizard-action-btn:active {
  transform: translateY(0) scale(0.99) !important;
  box-shadow: 0 1px 2px rgba(30, 41, 59, 0.08) !important;
}

.wizard-action-btn--primary {
  background: var(--academic-primary-soft) !important;
  border-color: rgba(0, 102, 204, 0.3) !important;
  color: var(--academic-primary) !important;
}

.wizard-action-btn--primary:hover {
  background: rgba(0, 102, 204, 0.14) !important;
  border-color: rgba(0, 102, 204, 0.45) !important;
  color: var(--academic-primary-active) !important;
  box-shadow: 0 3px 12px rgba(0, 102, 204, 0.2) !important;
}

/* ============================================
   Loading 遮罩
   ============================================ */
.el-loading-mask {
  background: rgba(248, 250, 252, 0.82) !important;
  backdrop-filter: saturate(160%) blur(12px) !important;
  -webkit-backdrop-filter: saturate(160%) blur(12px) !important;
  border-radius: var(--radius-card) !important;
}

.el-loading-spinner .el-loading-text {
  color: var(--academic-text-secondary) !important;
  font-size: 12.5px !important;
  font-weight: 500 !important;
  font-family: var(--font-system) !important;
  letter-spacing: 0.01em !important;
  margin-top: 10px !important;
}

.el-loading-spinner .circular {
  width: 36px !important;
  height: 36px !important;
}

.el-loading-spinner .path {
  stroke: var(--academic-primary) !important;
  stroke-width: 2.5 !important;
}

/* ============================================
   布局辅助
   ============================================ */
.topHeaderArea {
  position: relative;
  width: 100%;
}

.aigcOptionArea {
  position: relative;
  display: inline-block;
}

.designInfoArea {
  position: relative;
  display: inline-block;
}

/* ============================================
   Element Plus 覆盖 — 输入框 append
   ============================================ */
.custom-input-group .el-input__wrapper {
  border-radius: 10px 0 0 10px !important;
  background: #ffffff !important;
  box-shadow: 0 0 0 1px var(--academic-border-strong) inset !important;
  transition: box-shadow 0.2s ease !important;
}

.custom-input-group .el-input__wrapper:hover {
  box-shadow: 0 0 0 1px var(--academic-primary) inset !important;
}

.custom-input-group .el-input__wrapper.is-focus {
  box-shadow: 0 0 0 1px var(--academic-primary) inset,
    0 0 0 3px var(--academic-primary-ring) !important;
}

.custom-input-group .el-input__inner {
  color: var(--academic-text) !important;
  font-family: var(--font-system) !important;
  font-size: 13.5px !important;
}

.custom-input-group .el-input__inner::placeholder {
  color: var(--academic-text-tertiary) !important;
}

.custom-input-group .el-input-group__append {
  background: transparent !important;
  padding: 0 !important;
  border-radius: 0 10px 10px 0 !important;
  box-shadow: none !important;
  border: none !important;
}

.custom-input-group .el-input-group__append .el-button {
  border-radius: 0 10px 10px 0 !important;
  background: var(--academic-primary) !important;
  border: 1px solid var(--academic-primary) !important;
  color: #fff !important;
  font-family: var(--font-system) !important;
  font-weight: 600 !important;
  margin: 0 !important;
  height: 100% !important;
  box-shadow: 0 1px 2px rgba(0, 102, 204, 0.25),
    inset 0 1px 0 rgba(255, 255, 255, 0.18) !important;
  transition: all 0.18s ease !important;
}

.custom-input-group .el-input-group__append .el-button:hover {
  background: var(--academic-primary-hover) !important;
  border-color: var(--academic-primary-hover) !important;
  box-shadow: 0 3px 8px rgba(0, 102, 204, 0.35),
    inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
}

/* ============================================
   Element Plus 覆盖 — 下拉弹层
   ============================================ */
.el-select-dropdown {
  background: rgba(255, 255, 255, 0.96) !important;
  border: 1px solid var(--academic-border) !important;
  border-radius: 14px !important;
  box-shadow: 0 10px 32px rgba(30, 41, 59, 0.12),
    0 2px 8px rgba(30, 41, 59, 0.06) !important;
  backdrop-filter: saturate(160%) blur(18px) !important;
  -webkit-backdrop-filter: saturate(160%) blur(18px) !important;
  overflow: hidden !important;
}

.el-select-dropdown__item {
  color: var(--academic-text) !important;
  font-size: 13.5px !important;
  font-family: var(--font-system) !important;
  border-radius: 8px !important;
  margin: 2px 5px !important;
  padding: 8px 12px !important;
  transition: all 0.14s ease !important;
  letter-spacing: 0.01em !important;
}

.el-select-dropdown__item:hover,
.el-select-dropdown__item.hover {
  background: var(--academic-primary-soft) !important;
  color: var(--academic-primary) !important;
}

.el-select-dropdown__item.selected {
  background: var(--academic-primary-soft) !important;
  color: var(--academic-primary) !important;
  font-weight: 600 !important;
}

/* ============================================
   图片预览弹层
   ============================================ */
.el-image__inner {
  max-height: 80vh;
}

.el-image-viewer__wrapper {
  background: rgba(15, 23, 42, 0.85) !important;
  backdrop-filter: blur(12px) !important;
}

/* ============================================
   滚动条美化
   ============================================ */
.wizard-result-panel ::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}

.wizard-result-panel ::-webkit-scrollbar-track {
  background: transparent;
  border-radius: 6px;
}

.wizard-result-panel ::-webkit-scrollbar-thumb {
  background: rgba(30, 41, 59, 0.18);
  border-radius: 6px;
  transition: background 0.2s ease;
}

.wizard-result-panel ::-webkit-scrollbar-thumb:hover {
  background: var(--academic-primary);
}

/* ============================================
   响应式微调
   ============================================ */
@media (max-width: 640px) {
  .wizard-input-panel,
  .wizard-result-panel {
    border-radius: 16px;
  }

  .cast-spell-btn {
    font-size: 13.5px !important;
    padding: 9px 14px !important;
  }

  /* 窄屏：操作区换行，避免登录按钮被挤出可视区 */
  .wizard-navbar {
    flex-wrap: wrap;
    row-gap: 10px;
    padding: 10px 4px 14px;
  }

  .navbar-actions {
    margin-left: auto;
    gap: 8px;
  }

  .logo-title {
    gap: 10px;
  }

  .logo-badge {
    width: 36px;
    height: 36px;
    border-radius: 10px;
  }

  .logo-badge-icon {
    font-size: 18px;
  }

  .logo-text {
    font-size: 15px;
  }

  .navbar-divider {
    height: 18px;
  }

  .refined-logo {
    width: 36px;
    height: 36px;
    border-radius: 10px;
  }

  .wizard-bg {
    background-size: auto, auto, 18px 18px;
  }
}

/* ============================================
   工具类补充
   ============================================ */
.tw-bg-gray-50\/50 {
  background-color: rgba(246, 249, 247, 0.6);
}

.tw-w-800 {
  max-width: 800px;
}

.tw-margin-auto {
  margin: auto;
}

/* Muse 风格统一：品牌蓝控件、弹窗与表单 */
:deep(.el-button--primary) {
  --el-button-bg-color: #0066CC;
  --el-button-border-color: #0066CC;
  --el-button-hover-bg-color: #0052A3;
  --el-button-hover-border-color: #0052A3;
  --el-button-active-bg-color: #004C99;
  --el-button-active-border-color: #004C99;
  border-radius: 999px;
}

:deep(.el-dialog) {
  border-radius: 16px !important;
  overflow: hidden;
  box-shadow: 0 25px 50px -12px rgba(15, 23, 42, 0.18) !important;
}

:deep(.el-dialog__header) {
  margin-right: 0;
  padding: 20px 24px 12px;
  border-bottom: 1px solid #EEF3F0;
}

:deep(.el-dialog__title) {
  font-weight: 700;
  font-size: 1.125rem;
  color: #0f172a;
}

:deep(.el-dialog__body) {
  padding: 20px 24px;
}

:deep(.el-dialog__footer) {
  padding: 12px 24px 20px;
  border-top: 1px solid #EEF3F0;
}

:deep(.el-select__wrapper),
:deep(.el-input__wrapper) {
  border-radius: 10px;
}

:deep(.el-tag) {
  --el-tag-bg-color: #f0f9ff;
  --el-tag-border-color: #BAE6FD;
  --el-tag-text-color: #0052A3;
}

.tw-text-blue-600,
.tw-text-blue-500 {
  color: #0066CC !important;
}

.tw-bg-blue-50 {
  background-color: #F0F9FF !important;
}

.tw-bg-blue-500,
.tw-bg-blue-600 {
  background-color: #0066CC !important;
}

.tw-border-blue-200 {
  border-color: #BAE6FD !important;
}
</style>

<script>export default 
          {"name":"","data":function(
) {
return {
      loading: false,
      loadingText: "已收指令...",
      waitingText: "等待输入描述...",
      gettedTips: "已输入描述，待点击 “立即生成” 确认...",
      modifyInfo: "",
      modifyLoading: false,
      modifyEnabled: false,
      readingFile: false,
      isStreaming: false,
      versionList: [],
      currentVersionIndex: 0,
      step: 0,
      currentAppItem: {
									className: "ElementVueItem",
									task: {
										style: {
											overflow: "auto",
										},
										template: "data:text/plain;charset=utf-8;base64,PHRlbXBsYXRlPgogIDxkaXYgY2xhc3M9InB2LXJvb3QiPgogICAgPGRpdiBjbGFzcz0icHYtY29udGFpbmVyIiA6Y2xhc3M9InsgJ3B2LWNvbnRhaW5lci0tZm91cic6IHRvdGFsU2xpZGVzID09PSA0IH0iPgogICAgICA8IS0tID09PT09PT09PT09PSDmvJTnpLrmlofnqL/mtY/op4jlmaggPT09PT09PT09PT09IC0tPgogICAgICA8aGVhZGVyIGNsYXNzPSJwdi1oZWFkZXIiPgogICAgICAgIDxkaXYgY2xhc3M9InB2LWhlYWRlci1jb3B5Ij4KICAgICAgICAgIDxkaXYgY2xhc3M9InB2LWtpY2tlciI+CiAgICAgICAgICAgIDxzcGFuIGNsYXNzPSJwdi1raWNrZXItbGluZSI+PC9zcGFuPgogICAgICAgICAgICA8c3Bhbj5QUkVTRU5UQVRJT04gREVDSzwvc3Bhbj4KICAgICAgICAgICAgPHNwYW4gY2xhc3M9InB2LWtpY2tlci1jb3VudCI+e3sgcGFkKHRvdGFsU2xpZGVzKSB9fSBQQUdFUzwvc3Bhbj4KICAgICAgICAgIDwvZGl2PgogICAgICAgICAgPGgxIGNsYXNzPSJwdi10aXRsZSI+e3sgYWNjb3VudERhdGEudGl0bGUgfX08L2gxPgogICAgICAgICAgPHAgY2xhc3M9InB2LXN1YnRpdGxlIj4KICAgICAgICAgICAgPHNwYW4+e3sgYWNjb3VudERhdGEucHJlc2VudGVyIH19PC9zcGFuPgogICAgICAgICAgICA8c3BhbiBjbGFzcz0icHYtbWV0YS1kb3QiPjwvc3Bhbj4KICAgICAgICAgICAgPHNwYW4+e3sgYWNjb3VudERhdGEuc3VidGl0bGUgfHwgdG90YWxTbGlkZXMgKyAiIOmhtea8lOekuuaWh+eovyIgfX08L3NwYW4+CiAgICAgICAgICA8L3A+CiAgICAgICAgPC9kaXY+CgogICAgICAgIDxidXR0b24gY2xhc3M9InB2LXBsYXktYnRuIiB0eXBlPSJidXR0b24iIEBjbGljaz0iZW50ZXJQcmVzZW50YXRpb24oMCkiPgogICAgICAgICAgPHN2ZyBjbGFzcz0icHYtcGxheS1pY29uIiB2aWV3Qm94PSIwIDAgMjQgMjQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGggZD0iTTggNS41djEzbDEwLjUtNi41TDggNS41eiIgZmlsbD0iY3VycmVudENvbG9yIj48L3BhdGg+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICAgIDxzcGFuPnt7IGVuZ2luZVN0YXR1cyA9PT0gInJlYWR5IiA/ICLmlL7mmKDlubvnga/niYciIDogIuWKoOi9vea4suafk+W8leaTjiIgfX08L3NwYW4+CiAgICAgICAgPC9idXR0b24+CiAgICAgIDwvaGVhZGVyPgoKICAgICAgPGRpdiB2LWlmPSJlbmdpbmVTdGF0dXMgPT09ICdlcnJvciciIGNsYXNzPSJwdi1lbmdpbmUtYmFubmVyIiByb2xlPSJhbGVydCI+CiAgICAgICAgPHNwYW4gY2xhc3M9InB2LWVuZ2luZS1iYW5uZXItZG90Ij48L3NwYW4+CiAgICAgICAgPHNwYW4gY2xhc3M9InB2LWVuZ2luZS1iYW5uZXItdGV4dCI+CiAgICAgICAgICBMZWFmZXIg5riy5p+T5byV5pOO5Yqg6L295aSx6LSl77yae3sgZW5naW5lRXJyb3IgfX0KICAgICAgICA8L3NwYW4+CiAgICAgICAgPGJ1dHRvbiBjbGFzcz0icHYtZW5naW5lLXJldHJ5IiB0eXBlPSJidXR0b24iIEBjbGljaz0icmV0cnlFbmdpbmUiPgogICAgICAgICAg6YeN5paw5Yqg6L29CiAgICAgICAgPC9idXR0b24+CiAgICAgIDwvZGl2PgoKICAgICAgPCEtLSA9PT09PT09PT09PT0g5bm754Gv54mH57yp55Wl5Zu+572R5qC8ID09PT09PT09PT09PSAtLT4KICAgICAgPG1haW4KICAgICAgICBjbGFzcz0icHYtZ3JpZCIKICAgICAgICA6Y2xhc3M9InsgJ3B2LWdyaWQtLWZvdXInOiB0b3RhbFNsaWRlcyA9PT0gNCB9IgogICAgICAgIGFyaWEtbGFiZWw9IuW5u+eBr+eJh+WIl+ihqCIKICAgICAgPgogICAgICAgIDxhcnRpY2xlCiAgICAgICAgICB2LWZvcj0iKHNsaWRlLCBpZHgpIGluIGFjY291bnREYXRhLnNsaWRlcyIKICAgICAgICAgIDprZXk9InNsaWRlLmlkIgogICAgICAgICAgY2xhc3M9InB2LWNhcmQiCiAgICAgICAgICByb2xlPSJidXR0b24iCiAgICAgICAgICB0YWJpbmRleD0iMCIKICAgICAgICAgIDphcmlhLWxhYmVsPSIn5omT5byA56ysICcgKyAoaWR4ICsgMSkgKyAnIOmhte+8micgKyBzbGlkZS50aXRsZSIKICAgICAgICAgIEBjbGljaz0ib3BlblNsaWRlQXQoaWR4KSIKICAgICAgICAgIEBrZXl1cC5lbnRlcj0ib3BlblNsaWRlQXQoaWR4KSIKICAgICAgICAgIEBrZXl1cC5zcGFjZS5wcmV2ZW50PSJvcGVuU2xpZGVBdChpZHgpIgogICAgICAgID4KICAgICAgICAgIDxkaXYKICAgICAgICAgICAgY2xhc3M9InB2LXRodW1iIgogICAgICAgICAgICA6Y2xhc3M9InNsaWRlLnR5cGUgPT09ICd0aXRsZScgPyAncHYtdGh1bWItLWNvdmVyJyA6ICdwdi10aHVtYi0tY29udGVudCciCiAgICAgICAgICA+CiAgICAgICAgICAgIDxkaXYKICAgICAgICAgICAgICByZWY9InRodW1iSG9zdHMiCiAgICAgICAgICAgICAgY2xhc3M9InB2LXRodW1iLWNhbnZhcyIKICAgICAgICAgICAgICBhcmlhLWhpZGRlbj0idHJ1ZSIKICAgICAgICAgICAgPjwvZGl2PgoKICAgICAgICAgICAgPGRpdgogICAgICAgICAgICAgIHYtaWY9ImVuZ2luZVN0YXR1cyAhPT0gJ3JlYWR5JyIKICAgICAgICAgICAgICBjbGFzcz0icHYtdGh1bWItbG9hZGluZyIKICAgICAgICAgICAgICBhcmlhLWhpZGRlbj0idHJ1ZSIKICAgICAgICAgICAgPgogICAgICAgICAgICAgIDxzcGFuIGNsYXNzPSJwdi10aHVtYi1zaGltbWVyIj48L3NwYW4+CiAgICAgICAgICAgICAgPHNwYW4gdi1pZj0iZW5naW5lU3RhdHVzID09PSAnZXJyb3InIiBjbGFzcz0icHYtdGh1bWItbG9hZGluZy10ZXh0Ij4KICAgICAgICAgICAgICAgIOmihOiniOS4jeWPr+eUqAogICAgICAgICAgICAgIDwvc3Bhbj4KICAgICAgICAgICAgPC9kaXY+CiAgICAgICAgICA8L2Rpdj4KCiAgICAgICAgICA8ZGl2IGNsYXNzPSJwdi1jYXJkLW1ldGEiPgogICAgICAgICAgICA8c3BhbiBjbGFzcz0icHYtY2FyZC1pbmRleCI+e3sgcGFkKGlkeCArIDEpIH19PC9zcGFuPgogICAgICAgICAgICA8c3BhbiBjbGFzcz0icHYtY2FyZC1raW5kIj57eyBnZXRTbGlkZVR5cGVMYWJlbChzbGlkZSkgfX08L3NwYW4+CiAgICAgICAgICAgIDxzcGFuIGNsYXNzPSJwdi1jYXJkLW9wZW4iPgogICAgICAgICAgICAgIOafpeeciwogICAgICAgICAgICAgIDxzdmcgdmlld0JveD0iMCAwIDI0IDI0IiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgICAgICA8cGF0aAogICAgICAgICAgICAgICAgICBkPSJNNSAxMmgxNE0xMyA2bDYgNi02IDYiCiAgICAgICAgICAgICAgICAgIGZpbGw9Im5vbmUiCiAgICAgICAgICAgICAgICAgIHN0cm9rZT0iY3VycmVudENvbG9yIgogICAgICAgICAgICAgICAgICBzdHJva2Utd2lkdGg9IjEuOCIKICAgICAgICAgICAgICAgICAgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIgogICAgICAgICAgICAgICAgICBzdHJva2UtbGluZWpvaW49InJvdW5kIgogICAgICAgICAgICAgICAgPjwvcGF0aD4KICAgICAgICAgICAgICA8L3N2Zz4KICAgICAgICAgICAgPC9zcGFuPgogICAgICAgICAgPC9kaXY+CiAgICAgICAgPC9hcnRpY2xlPgogICAgICA8L21haW4+CgogICAgICA8Zm9vdGVyIGNsYXNzPSJwdi1mb290ZXIiPgogICAgICAgIDxzcGFuPgogICAgICAgICAge3sgYWNjb3VudERhdGEucHJlc2VudGVyIH19CiAgICAgICAgICA8dGVtcGxhdGUgdi1pZj0iYWNjb3VudERhdGEub2NjYXNpb24iPiDCtyB7eyBhY2NvdW50RGF0YS5vY2Nhc2lvbiB9fTwvdGVtcGxhdGU+CiAgICAgICAgPC9zcGFuPgogICAgICAgIDxzcGFuPueCueWHu+S7u+aEj+mhtemdouW8gOWni+aUvuaYoDwvc3Bhbj4KICAgICAgPC9mb290ZXI+CiAgICA8L2Rpdj4KCiAgICA8IS0tID09PT09PT09PT09PSDlhajlsY/mlL7mmKAgPT09PT09PT09PT09IC0tPgogICAgPGRpdgogICAgICB2LWlmPSJpc1ByZXNlbnRhdGlvbiIKICAgICAgY2xhc3M9InB2LW92ZXJsYXkiCiAgICAgIHJvbGU9ImRpYWxvZyIKICAgICAgYXJpYS1tb2RhbD0idHJ1ZSIKICAgICAgYXJpYS1sYWJlbD0i5bm754Gv54mH5pS+5pigIgogICAgPgogICAgICA8ZGl2IGNsYXNzPSJwdi10b29sYmFyIiA6Y2xhc3M9InsgJ3B2LXRvb2xiYXItLWlkbGUnOiB0b29sYmFySWRsZSB9Ij4KICAgICAgICA8YnV0dG9uIGNsYXNzPSJwdi10b29sLWJ0biIgdHlwZT0iYnV0dG9uIiBhcmlhLWxhYmVsPSLkuIrkuIDpobUiIEBjbGljaz0icHJldlNsaWRlIj4KICAgICAgICAgIDxzdmcgdmlld0JveD0iMCAwIDI0IDI0IiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgIDxwYXRoCiAgICAgICAgICAgICAgZD0iTTE1IDE4bC02LTYgNi02IgogICAgICAgICAgICAgIGZpbGw9Im5vbmUiCiAgICAgICAgICAgICAgc3Ryb2tlPSJjdXJyZW50Q29sb3IiCiAgICAgICAgICAgICAgc3Ryb2tlLXdpZHRoPSIxLjgiCiAgICAgICAgICAgICAgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIgogICAgICAgICAgICAgIHN0cm9rZS1saW5lam9pbj0icm91bmQiCiAgICAgICAgICAgID48L3BhdGg+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICA8L2J1dHRvbj4KCiAgICAgICAgPHNwYW4gY2xhc3M9InB2LWNvdW50ZXIiIGFyaWEtbGl2ZT0icG9saXRlIj4KICAgICAgICAgIDxzdHJvbmc+e3sgcGFkKGN1cnJlbnRTbGlkZUluZGV4ICsgMSkgfX08L3N0cm9uZz4KICAgICAgICAgIDxzcGFuPi8ge3sgcGFkKHRvdGFsU2xpZGVzKSB9fTwvc3Bhbj4KICAgICAgICA8L3NwYW4+CgogICAgICAgIDxidXR0b24gY2xhc3M9InB2LXRvb2wtYnRuIiB0eXBlPSJidXR0b24iIGFyaWEtbGFiZWw9IuS4i+S4gOmhtSIgQGNsaWNrPSJuZXh0U2xpZGUiPgogICAgICAgICAgPHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGgKICAgICAgICAgICAgICBkPSJNOSA2bDYgNi02IDYiCiAgICAgICAgICAgICAgZmlsbD0ibm9uZSIKICAgICAgICAgICAgICBzdHJva2U9ImN1cnJlbnRDb2xvciIKICAgICAgICAgICAgICBzdHJva2Utd2lkdGg9IjEuOCIKICAgICAgICAgICAgICBzdHJva2UtbGluZWNhcD0icm91bmQiCiAgICAgICAgICAgICAgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIKICAgICAgICAgICAgPjwvcGF0aD4KICAgICAgICAgIDwvc3ZnPgogICAgICAgIDwvYnV0dG9uPgoKICAgICAgICA8YnV0dG9uCiAgICAgICAgICBjbGFzcz0icHYtdG9vbC1idG4gcHYtZXhwb3J0LWJ0biIKICAgICAgICAgIHR5cGU9ImJ1dHRvbiIKICAgICAgICAgIGFyaWEtbGFiZWw9IuWvvOWHuuW9k+WJjeW5u+eBr+eJh+S4uuWbvueJhyIKICAgICAgICAgIHRpdGxlPSLlr7zlh7rlvZPliY3pobXkuLogUE5HIOWbvueJhyIKICAgICAgICAgIDpkaXNhYmxlZD0iaXNFeHBvcnRpbmcgfHwgZW5naW5lU3RhdHVzICE9PSAncmVhZHknIgogICAgICAgICAgOmFyaWEtYnVzeT0iaXNFeHBvcnRpbmciCiAgICAgICAgICBAY2xpY2s9ImV4cG9ydEN1cnJlbnRTbGlkZSIKICAgICAgICA+CiAgICAgICAgICA8c3BhbiB2LWlmPSJpc0V4cG9ydGluZyIgY2xhc3M9InB2LWV4cG9ydC1zcGlubmVyIiBhcmlhLWhpZGRlbj0idHJ1ZSI+PC9zcGFuPgogICAgICAgICAgPHN2ZyB2LWVsc2Ugdmlld0JveD0iMCAwIDI0IDI0IiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgIDxwYXRoCiAgICAgICAgICAgICAgZD0iTTEyIDN2MTFtMCAwbC00LTRtNCA0bDQtNE01IDE1djRhMiAyIDAgMCAwIDIgMmgxMGEyIDIgMCAwIDAgMi0ydi00IgogICAgICAgICAgICAgIGZpbGw9Im5vbmUiCiAgICAgICAgICAgICAgc3Ryb2tlPSJjdXJyZW50Q29sb3IiCiAgICAgICAgICAgICAgc3Ryb2tlLXdpZHRoPSIxLjgiCiAgICAgICAgICAgICAgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIgogICAgICAgICAgICAgIHN0cm9rZS1saW5lam9pbj0icm91bmQiCiAgICAgICAgICAgID48L3BhdGg+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICAgIDxzcGFuIGNsYXNzPSJwdi1leHBvcnQtbGFiZWwiPnt7IGlzRXhwb3J0aW5nID8gIuWvvOWHuuS4rSIgOiAi5a+85Ye6IiB9fTwvc3Bhbj4KICAgICAgICA8L2J1dHRvbj4KCiAgICAgICAgPHNwYW4gY2xhc3M9InB2LXRvb2xiYXItZGl2aWRlciI+PC9zcGFuPgoKICAgICAgICA8YnV0dG9uIGNsYXNzPSJwdi1leGl0LWJ0biIgdHlwZT0iYnV0dG9uIiBAY2xpY2s9ImV4aXRQcmVzZW50YXRpb24iPgogICAgICAgICAgPHNwYW4+6YCA5Ye65pS+5pigPC9zcGFuPgogICAgICAgICAgPGtiZD5Fc2M8L2tiZD4KICAgICAgICA8L2J1dHRvbj4KICAgICAgPC9kaXY+CgogICAgICA8ZGl2IGNsYXNzPSJwdi1yb3RhdGUtaGludCIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgIOaoquWxj+ingueci+aViOaenOabtOS9swogICAgICA8L2Rpdj4KCiAgICAgIDxwIGNsYXNzPSJwdi1zci1vbmx5IiBhcmlhLWxpdmU9InBvbGl0ZSI+e3sgY3VycmVudFNsaWRlVGl0bGUgfX08L3A+CgogICAgICA8ZGl2CiAgICAgICAgdi1pZj0iaXNFeHBvcnRpbmcgfHwgZXhwb3J0Tm90aWNlIgogICAgICAgIGNsYXNzPSJwdi1leHBvcnQtdG9hc3QiCiAgICAgICAgOmNsYXNzPSJ7ICdwdi1leHBvcnQtdG9hc3QtLWVycm9yJzogZXhwb3J0Tm90aWNlVHlwZSA9PT0gJ2Vycm9yJyB9IgogICAgICAgIGFyaWEtbGl2ZT0icG9saXRlIgogICAgICA+CiAgICAgICAgPHNwYW4gdi1pZj0iaXNFeHBvcnRpbmciIGNsYXNzPSJwdi1leHBvcnQtc3Bpbm5lciIgYXJpYS1oaWRkZW49InRydWUiPjwvc3Bhbj4KICAgICAgICA8c3Bhbj57eyBpc0V4cG9ydGluZyA/ICLmraPlnKjlr7zlh7rlvZPliY3pobXigKYiIDogZXhwb3J0Tm90aWNlIH19PC9zcGFuPgogICAgICA8L2Rpdj4KCiAgICAgIDxkaXYgY2xhc3M9InB2LXN0YWdlIiByZWY9InN0YWdlIj4KICAgICAgICA8ZGl2CiAgICAgICAgICBjbGFzcz0icHYtc3RhZ2Utc2NlbmUiCiAgICAgICAgICByZWY9InN0YWdlU2NlbmUiCiAgICAgICAgICA6c3R5bGU9InsgJy0tcHYtc3RhZ2Utc2NhbGUnOiBzdGFnZVNjYWxlIH0iCiAgICAgICAgPgogICAgICAgICAgPGRpdiBjbGFzcz0icHYtc3RhZ2UtaG9zdCIgcmVmPSJzdGFnZUhvc3QiPjwvZGl2PgogICAgICAgIDwvZGl2PgoKICAgICAgICA8ZGl2CiAgICAgICAgICB2LWlmPSJlbmdpbmVTdGF0dXMgIT09ICdyZWFkeSciCiAgICAgICAgICBjbGFzcz0icHYtc3RhZ2Utc3RhdGUiCiAgICAgICAgICBhcmlhLWxpdmU9InBvbGl0ZSIKICAgICAgICA+CiAgICAgICAgICA8c3BhbiB2LWlmPSJlbmdpbmVTdGF0dXMgIT09ICdlcnJvciciIGNsYXNzPSJwdi1leHBvcnQtc3Bpbm5lciI+PC9zcGFuPgogICAgICAgICAgPHNwYW4+CiAgICAgICAgICAgIHt7IGVuZ2luZVN0YXR1cyA9PT0gImVycm9yIiA/ICLmuLLmn5PlvJXmk47liqDovb3lpLHotKUiIDogIuato+WcqOWKoOi9vea4suafk+W8leaTjuKApiIgfX0KICAgICAgICAgIDwvc3Bhbj4KICAgICAgICAgIDxidXR0b24KICAgICAgICAgICAgdi1pZj0iZW5naW5lU3RhdHVzID09PSAnZXJyb3InIgogICAgICAgICAgICBjbGFzcz0icHYtc3RhZ2UtcmV0cnkiCiAgICAgICAgICAgIHR5cGU9ImJ1dHRvbiIKICAgICAgICAgICAgQGNsaWNrPSJyZXRyeUVuZ2luZSIKICAgICAgICAgID4KICAgICAgICAgICAg6YeN6K+VCiAgICAgICAgICA8L2J1dHRvbj4KICAgICAgICA8L2Rpdj4KICAgICAgPC9kaXY+CgogICAgICA8ZGl2IGNsYXNzPSJwdi1wcm9ncmVzcyIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgIDxzcGFuIDpzdHlsZT0ieyB3aWR0aDogcHJvZ3Jlc3NQZXJjZW50ICsgJyUnIH0iPjwvc3Bhbj4KICAgICAgPC9kaXY+CiAgICA8L2Rpdj4KICA8L2Rpdj4KPC90ZW1wbGF0ZT4KCjxzY3JpcHQ+CiAgZXhwb3J0IGRlZmF1bHQgewogICAgbmFtZTogIlByZXNlbnRhdGlvblZpZXdlciIsCgogICAgZGF0YSgpIHsKICAgICAgcmV0dXJuIHsKICAgICAgICAvLyDmuLLmn5PluLjph4/nu5/kuIDmlLbmlZvlnKggZGF0YSDkuK3vvIzpgb/lhY0gc2NyaXB0IOe6p+WPmOmHj+OAggogICAgICAgIFBWX0xFQUZFUl9TUkM6ICJodHRwczovL3VucGtnLmNvbS9sZWFmZXItdWlAMi4zLjQvZGlzdC93ZWIubWluLmpzIiwKICAgICAgICBQVl9XOiAxOTIwLAogICAgICAgIFBWX0g6IDEwODAsCiAgICAgICAgUFZfRk9OVDoKICAgICAgICAgICciU0YgUHJvIERpc3BsYXkiLCAiU0YgUHJvIFRleHQiLCAtYXBwbGUtc3lzdGVtLCBCbGlua01hY1N5c3RlbUZvbnQsICJQaW5nRmFuZyBTQyIsICJIaXJhZ2lubyBTYW5zIEdCIiwgIk5vdG8gU2FucyBTQyIsICJNaWNyb3NvZnQgWWFIZWkiLCBzYW5zLXNlcmlmJywKICAgICAgICBQVl9NT05POgogICAgICAgICAgJyJTRiBNb25vIiwgIkpldEJyYWlucyBNb25vIiwgIlJvYm90byBNb25vIiwgdWktbW9ub3NwYWNlLCBTRk1vbm8tUmVndWxhciwgTWVubG8sIENvbnNvbGFzLCBtb25vc3BhY2UnLAogICAgICAgIFBWX0M6IHsKICAgICAgICAgIGJyYW5kOiAiIzBiNWNmZiIsCiAgICAgICAgICBicmFuZERlZXA6ICIjMDY0N2M5IiwKICAgICAgICAgIGJyYW5kU29mdDogIiNlZGY0ZmYiLAogICAgICAgICAgaW5rOiAiIzBjMTExYSIsCiAgICAgICAgICB0ZXh0MTogIiMxNzFkMjciLAogICAgICAgICAgdGV4dDI6ICIjNWI2NDcyIiwKICAgICAgICAgIHRleHQzOiAiIzhiOTRhMyIsCiAgICAgICAgICBwYXBlcjogIiNmN2Y4ZmEiLAogICAgICAgICAgc3VyZmFjZTogIiNmZmZmZmYiLAogICAgICAgICAgbGluZTogIiNlMmU2ZWMiLAogICAgICAgICAgbGluZVN0cm9uZzogIiNjZmQ2ZTAiLAogICAgICAgICAgZGFyazogIiMwYTBkMTMiLAogICAgICAgIH0sCiAgICAgICAgUFZfUEFMRVRURTogWyIjMGI1Y2ZmIiwgIiMxMmE1OTQiLCAiI2Y1OWUwYiIsICIjN2MzYWVkIiwgIiM5NGEzYjgiXSwKICAgICAgICAvLyBMZWFmZXIg5a6e5L6L44CBRE9N44CBT2JzZXJ2ZXIg562J6L+Q6KGM5pe25Y+l5p+E55SxIGNyZWF0ZVJ1bnRpbWUoKSDnlJ/miJDjgIIKICAgICAgICBydW50aW1lOiBudWxsLAoKICAgICAgICBlbmdpbmVTdGF0dXM6ICJpZGxlIiwKICAgICAgICBlbmdpbmVFcnJvcjogIiIsCgogICAgICAgIGlzUHJlc2VudGF0aW9uOiBmYWxzZSwKICAgICAgICB0b29sYmFySWRsZTogZmFsc2UsCiAgICAgICAgdG9vbGJhclRpbWVyOiBudWxsLAogICAgICAgIHN0YWdlU2NhbGU6IDEsCgogICAgICAgIGlzRXhwb3J0aW5nOiBmYWxzZSwKICAgICAgICBleHBvcnROb3RpY2U6ICIiLAogICAgICAgIGV4cG9ydE5vdGljZVR5cGU6ICJzdWNjZXNzIiwKICAgICAgICBleHBvcnROb3RpY2VUaW1lcjogbnVsbCwKCiAgICAgICAgY2hhcnRab29tOiB7fSwKICAgICAgICBjaGFydFBhbjoge30sCiAgICAgICAgY2hhcnRQYW5TdGF0ZTogbnVsbCwKCiAgICAgICAgY3VycmVudFNsaWRlSW5kZXg6IDAsCiAgICAgICAgYm9keU92ZXJmbG93OiAiIiwKICAgICAgICBldmVudHNCb3VuZDogZmFsc2UsCiAgICAgICAgdG91Y2hTdGFydFg6IDAsCiAgICAgICAgdG91Y2hTdGFydFk6IDAsCiAgICAgICAgdG91Y2hTdGFydFRpbWU6IDAsCiAgICAgICAgdG91Y2hTdGFydEluWm9vbWVkQ2hhcnQ6IGZhbHNlLAoKICAgICAgICBhY2NvdW50RGF0YTogewogICAgICAgICAgdGl0bGU6ICJBSSDmnrbmnoTlpJrnq6/ljY/lkIzmvJTov5vmiqXlkYoiLAogICAgICAgICAgcHJlc2VudGVyOiAi5oqA5pyv5p625p6E6YOoIiwKICAgICAgICAgIHN1YnRpdGxlOiAiQUkg6amx5Yqo55qE5aSa56uv5riy5p+T5p625p6EIiwKICAgICAgICAgIG9jY2FzaW9uOiAi5YaF6YOo5oqA5pyv5YiG5LqrIiwKICAgICAgICAgIHNsaWRlczogWwogICAgICAgICAgICB7CiAgICAgICAgICAgICAgaWQ6ICJjb3ZlciIsCiAgICAgICAgICAgICAgdHlwZTogInRpdGxlIiwKICAgICAgICAgICAgICB0aXRsZTogIuWkmuerr+WkmuaAgea4suafk+aetuaehOa8lOi/myIsCiAgICAgICAgICAgICAgZWxlbWVudHM6IFsKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgdHlwZTogInRleHQiLAogICAgICAgICAgICAgICAgICBjb250ZW50OgogICAgICAgICAgICAgICAgICAgICLku6Xnu5/kuIDlupXluqfljY/orq7ov57mjqXliqjmgIHlm77ooajkuI7lpJrmqKHmgIHmlbDmja7vvIzmnoTlu7rpnaLlkJHmnKrmnaXnmoTlpJrnq6/muLLmn5PlvJXmk47jgIIiLAogICAgICAgICAgICAgICAgfSwKICAgICAgICAgICAgICBdLAogICAgICAgICAgICB9LAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgaWQ6ICJjaGFydGpzLWRlbW8iLAogICAgICAgICAgICAgIHR5cGU6ICJzcGxpdCIsCiAgICAgICAgICAgICAgdGl0bGU6ICLlkJ7lkJDph4/kv53mjIHpq5jpgJ/lop7plb8iLAogICAgICAgICAgICAgIGVsZW1lbnRzOiBbCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgIHR5cGU6ICJ0ZXh0IiwKICAgICAgICAgICAgICAgICAgY29udGVudDoKICAgICAgICAgICAgICAgICAgICAiQ2FudmFzIOi9u+mHj+a4suafk+WcqOenu+WKqOerr+S4juWkp+Wxj+WcuuaZr+S4i+WFvOmhvuaAp+iDveS4juS6pOS6kuS9k+mqjO+8jOaguOW/g+WQnuWQkOmHj+i/nue7reWbm+S4quWto+W6puS/neaMgeWinumVv+OAgiIsCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICB0eXBlOiAiY2hhcnQiLAogICAgICAgICAgICAgICAgICBjaGFydFR5cGU6ICJjaGFydGpzIiwKICAgICAgICAgICAgICAgICAgY29udGVudDogIuWto+W6puWQnuWQkOmHj++8iFRQU++8iSIsCiAgICAgICAgICAgICAgICAgIHNvdXJjZTogIuW5s+WPsOebkeaOp+aVsOaNriIsCiAgICAgICAgICAgICAgICAgIGNoYXJ0RGF0YTogewogICAgICAgICAgICAgICAgICAgIHR5cGU6ICJsaW5lIiwKICAgICAgICAgICAgICAgICAgICBkYXRhOiB7CiAgICAgICAgICAgICAgICAgICAgICBsYWJlbHM6IFsiUTEiLCAiUTIiLCAiUTMiLCAiUTQiXSwKICAgICAgICAgICAgICAgICAgICAgIGRhdGFzZXRzOiBbCiAgICAgICAgICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICAgICAgICBsYWJlbDogIuWQnuWQkOmHj++8iFRQU++8iSIsCiAgICAgICAgICAgICAgICAgICAgICAgICAgZGF0YTogWzEyMDAsIDE5MDAsIDMyMDAsIDQ1MDBdLAogICAgICAgICAgICAgICAgICAgICAgICAgIGZpbGw6IHRydWUsCiAgICAgICAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICAgICAgICBdLAogICAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgIF0sCiAgICAgICAgICAgIH0sCiAgICAgICAgICAgIHsKICAgICAgICAgICAgICBpZDogIm1lcm1haWQtZGVtbyIsCiAgICAgICAgICAgICAgdHlwZTogImNvbnRlbnQiLAogICAgICAgICAgICAgIHRpdGxlOiAi5aSa5byV5pOO5Y2P5ZCM55qE5riy5p+T6ZO+6LevIiwKICAgICAgICAgICAgICBlbGVtZW50czogWwogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICB0eXBlOiAiY2hhcnQiLAogICAgICAgICAgICAgICAgICBjaGFydFR5cGU6ICJtZXJtYWlkIiwKICAgICAgICAgICAgICAgICAgY29udGVudDogIuS7juaVsOaNruWIsOinhuWbvueahOa1gei9rOmTvui3ryIsCiAgICAgICAgICAgICAgICAgIGNoYXJ0RGF0YTogewogICAgICAgICAgICAgICAgICAgIGNvZGU6CiAgICAgICAgICAgICAgICAgICAgICAiZmxvd2NoYXJ0IExSXG4gIEFbUHJvbXB0IOaVsOaNrl0gLS0+IEJb5aSa5oCB57yW6K+R5ZmoXVxuICBCIC0tPiBDW0NoYXJ0LmpzIOW8leaTjl1cbiAgQiAtLT4gRFtNZXJtYWlkIOW8leaTjl1cbiAgQyAtLT4gRVvop4blm77muLLmn5NdXG4gIEQgLS0+IEUiLAogICAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgfSwKICAgICAgICAgICAgICBdLAogICAgICAgICAgICB9LAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgaWQ6ICJkdWFsLWRlbW8iLAogICAgICAgICAgICAgIHR5cGU6ICJjb250ZW50IiwKICAgICAgICAgICAgICB0aXRsZTogIuabtOi9u+eahOa4suafk++8jOabtOW/q+eahOWTjeW6lCIsCiAgICAgICAgICAgICAgZWxlbWVudHM6IFsKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgdHlwZTogInRleHQiLAogICAgICAgICAgICAgICAgICBjb250ZW50OgogICAgICAgICAgICAgICAgICAgICLku44gUHJvbXB0IOino+aekOWIsOWbvuihqOi+k+WHuu+8jOe7n+S4gOa4suafk+mTvui3r+WcqOS/neivgeaVsOaNruWHhuehruaAp+eahOWQjOaXtu+8jOaMgee7remZjeS9juerr+S+p+W8gOmUgOOAgiIsCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICB0eXBlOiAibGlzdCIsCiAgICAgICAgICAgICAgICAgIGNvbnRlbnQ6IFsKICAgICAgICAgICAgICAgICAgICAiQ2FudmFzIOWOn+eUn+a4suafk++8jOenu+WKqOerr+S/neaMgea1geeVhSIsCiAgICAgICAgICAgICAgICAgICAgIuWbvuihqOS4jua1geeoi+WbvumbtuS+nei1luWOn+eUn+e7mOWItiIsCiAgICAgICAgICAgICAgICAgICAgIuWkmuerr+WwuuWvuOiHqumAguW6lO+8jOeJiOW8j+iHquWKqOmHjeaOkiIsCiAgICAgICAgICAgICAgICAgICAgIuaVsOaNruS4juinhuWbvuino+iApu+8jOaUr+aMgeWunuaXtuabtOaWsCIsCiAgICAgICAgICAgICAgICAgIF0sCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICB0eXBlOiAiY2hhcnQiLAogICAgICAgICAgICAgICAgICBjaGFydFR5cGU6ICJjaGFydGpzIiwKICAgICAgICAgICAgICAgICAgY29udGVudDogIua4suafk+iAl+aXtuWvueavlO+8iOavq+enku+8iSIsCiAgICAgICAgICAgICAgICAgIHNvdXJjZTogIuWfuuWHhua1i+ivlSDCtyDmlbDlgLzotorkvY7otorlpb0iLAogICAgICAgICAgICAgICAgICBoaWdobGlnaHRJbmRleDogMSwKICAgICAgICAgICAgICAgICAgY2hhcnREYXRhOiB7CiAgICAgICAgICAgICAgICAgICAgdHlwZTogImJhciIsCiAgICAgICAgICAgICAgICAgICAgZGF0YTogewogICAgICAgICAgICAgICAgICAgICAgbGFiZWxzOiBbIuWOn+eUnyBDYW52YXMiLCAiQ2hhcnQuanMiLCAiRDMuanMiLCAiRUNoYXJ0cyJdLAogICAgICAgICAgICAgICAgICAgICAgZGF0YXNldHM6IFsKICAgICAgICAgICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgICAgICAgIGxhYmVsOiAi5riy5p+T6ICX5pe277yIbXPvvIkiLAogICAgICAgICAgICAgICAgICAgICAgICAgIGRhdGE6IFsxMiwgMjgsIDQ1LCAzOF0sCiAgICAgICAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICAgICAgICBdLAogICAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgIF0sCiAgICAgICAgICAgIH0sCiAgICAgICAgICBdLAogICAgICAgIH0sCiAgICAgIH07CiAgICB9LAoKICAgIGNvbXB1dGVkOiB7CiAgICAgIHRvdGFsU2xpZGVzKCkgewogICAgICAgIHJldHVybiAodGhpcy5hY2NvdW50RGF0YS5zbGlkZXMgfHwgW10pLmxlbmd0aDsKICAgICAgfSwKCiAgICAgIHByb2dyZXNzUGVyY2VudCgpIHsKICAgICAgICBpZiAoIXRoaXMudG90YWxTbGlkZXMpIHJldHVybiAwOwogICAgICAgIGNvbnN0IGN1cnJlbnQgPSBNYXRoLm1pbih0aGlzLmN1cnJlbnRTbGlkZUluZGV4ICsgMSwgdGhpcy50b3RhbFNsaWRlcyk7CiAgICAgICAgcmV0dXJuIChjdXJyZW50IC8gdGhpcy50b3RhbFNsaWRlcykgKiAxMDA7CiAgICAgIH0sCgogICAgICBjdXJyZW50U2xpZGVUaXRsZSgpIHsKICAgICAgICBjb25zdCBzbGlkZXMgPSB0aGlzLmFjY291bnREYXRhLnNsaWRlcyB8fCBbXTsKICAgICAgICBjb25zdCBzbGlkZSA9IHNsaWRlc1t0aGlzLmN1cnJlbnRTbGlkZUluZGV4XTsKICAgICAgICByZXR1cm4gc2xpZGUgJiYgc2xpZGUudGl0bGUgPyBzbGlkZS50aXRsZSA6ICIiOwogICAgICB9LAogICAgfSwKCiAgICB3YXRjaDogewogICAgICBhY2NvdW50RGF0YTogewogICAgICAgIGRlZXA6IHRydWUsCiAgICAgICAgaGFuZGxlcigpIHsKICAgICAgICAgIHRoaXMuJG5leHRUaWNrKCgpID0+IHsKICAgICAgICAgICAgaWYgKHRoaXMuZW5naW5lU3RhdHVzID09PSAicmVhZHkiKSB7CiAgICAgICAgICAgICAgdGhpcy5yZW5kZXJBbGxUaHVtYm5haWxzKHRydWUpOwogICAgICAgICAgICAgIGlmICh0aGlzLmlzUHJlc2VudGF0aW9uKSB7CiAgICAgICAgICAgICAgICB0aGlzLnJlbmRlclN0YWdlU2xpZGUodGhpcy5jdXJyZW50U2xpZGVJbmRleCwgZmFsc2UpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQogICAgICAgICAgfSk7CiAgICAgICAgfSwKICAgICAgfSwKICAgIH0sCgogICAgY3JlYXRlZCgpIHsKICAgICAgdGhpcy5ydW50aW1lID0gdGhpcy5jcmVhdGVSdW50aW1lKCk7CiAgICB9LAoKICAgIG1vdW50ZWQoKSB7CiAgICAgIHRoaXMuJG5leHRUaWNrKCgpID0+IHsKICAgICAgICB0aGlzLnNldHVwVGh1bWJPYnNlcnZlcigpOwogICAgICB9KTsKICAgICAgdGhpcy5sb2FkRW5naW5lKCk7CiAgICB9LAoKICAgIGJlZm9yZVVubW91bnQoKSB7CiAgICAgIHRoaXMuY2xlYW51cFZpZXdlcigpOwogICAgfSwKCiAgICBiZWZvcmVEZXN0cm95KCkgewogICAgICB0aGlzLmNsZWFudXBWaWV3ZXIoKTsKICAgIH0sCgogICAgbWV0aG9kczogewoKICAgICAgLy8g6L+Q6KGM5pe25Y+l5p+E57uf5LiA5pS+5ZyoIGRhdGEg5Lit77yb6YCa6L+HIFN5bWJvbC50b1N0cmluZ1RhZyDmoIforrDkuLrpnZ7mma7pgJrlr7nosaHvvIwKICAgICAgLy8g6YG/5YWNIFZ1ZSAyLzMg5a+5IExlYWZlciDlrp7kvovlgZrmt7Hluqblk43lupTlvI/ku6PnkIbjgIIKICAgICAgY3JlYXRlUnVudGltZSgpIHsKICAgICAgICByZXR1cm4gewogICAgICAgICAgbGVhZmVyTGliOiBudWxsLAogICAgICAgICAgbGVhZmVyUHJvbWlzZTogbnVsbCwKICAgICAgICAgIG1lYXN1cmVDdHg6IG51bGwsCiAgICAgICAgICB0aHVtYkFwcHM6IHt9LAogICAgICAgICAgdGh1bWJUaW1lcjogbnVsbCwKICAgICAgICAgIHRodW1iUmVzaXplT2JzZXJ2ZXI6IG51bGwsCiAgICAgICAgICBzdGFnZUFwcDogbnVsbCwKICAgICAgICAgIHN0YWdlUm9vdDogbnVsbCwKICAgICAgICAgIHN0YWdlQ2FudmFzOiBudWxsLAogICAgICAgICAgc3RhZ2VIYW5kbGVyczogbnVsbCwKICAgICAgICAgIHN0YWdlUmVzaXplT2JzZXJ2ZXI6IG51bGwsCiAgICAgICAgICBjaGFydEFyZWFzOiBbXSwKICAgICAgICAgIHN0YWdlU2xpZGVLZXk6ICIiLAogICAgICAgICAgYW5pbVJhZjogbnVsbCwKICAgICAgICAgIGFuaW1Ub2tlbjogMCwKICAgICAgICAgIFtTeW1ib2wudG9TdHJpbmdUYWddOiAiUHJlc2VudGF0aW9uVmlld2VyUnVudGltZSIsCiAgICAgICAgfTsKICAgICAgfSwKCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAgTGVhZmVyIOW8leaTju+8muaMiemcgOWKqOaAgeazqOWFpSBzY3JpcHTvvIzlubblnKjlrp7kvovpl7TlpI3nlKgKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCgogICAgICBwdkxvYWRMZWFmZXIoKSB7CiAgICAgICAgaWYgKAogICAgICAgICAgdHlwZW9mIHdpbmRvdyAhPT0gInVuZGVmaW5lZCIgJiYKICAgICAgICAgIHdpbmRvdy5MZWFmZXJVSSAmJgogICAgICAgICAgd2luZG93LkxlYWZlclVJLkxlYWZlcgogICAgICAgICkgewogICAgICAgICAgcmV0dXJuIFByb21pc2UucmVzb2x2ZSh3aW5kb3cuTGVhZmVyVUkpOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5ydW50aW1lLmxlYWZlclByb21pc2UpIHJldHVybiB0aGlzLnJ1bnRpbWUubGVhZmVyUHJvbWlzZTsKCiAgICAgICAgdGhpcy5ydW50aW1lLmxlYWZlclByb21pc2UgPSBuZXcgUHJvbWlzZSgocmVzb2x2ZSwgcmVqZWN0KSA9PiB7CiAgICAgICAgICBpZiAodHlwZW9mIGRvY3VtZW50ID09PSAidW5kZWZpbmVkIikgewogICAgICAgICAgICB0aGlzLnJ1bnRpbWUubGVhZmVyUHJvbWlzZSA9IG51bGw7CiAgICAgICAgICAgIHJlamVjdChuZXcgRXJyb3IoIuW9k+WJjeeOr+Wig+S4jeaUr+aMgSBET00iKSk7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KCiAgICAgICAgICBjb25zdCBleGlzdGluZyA9IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3IoJ3NjcmlwdFtkYXRhLXB2LWxlYWZlcj0iMSJdJyk7CiAgICAgICAgICBpZiAoZXhpc3RpbmcgJiYgZXhpc3RpbmcuZ2V0QXR0cmlidXRlKCJkYXRhLWZhaWxlZCIpICE9PSAidHJ1ZSIpIHsKICAgICAgICAgICAgLy8g5Y+m5LiA5Liq57uE5Lu25a6e5L6L5Y+v6IO95q2j5Zyo5Yqg6L295ZCM5LiA5Liq6ISa5pys77yM562J5b6F5YW257uT5p6c77yM6YG/5YWN6YeN5aSN5rOo5YWl44CCCiAgICAgICAgICAgIGNvbnN0IHN0YXJ0ZWRBdCA9IERhdGUubm93KCk7CiAgICAgICAgICAgIGNvbnN0IHBvbGwgPSAoKSA9PiB7CiAgICAgICAgICAgICAgaWYgKHdpbmRvdy5MZWFmZXJVSSAmJiB3aW5kb3cuTGVhZmVyVUkuTGVhZmVyKSB7CiAgICAgICAgICAgICAgICB0aGlzLnJ1bnRpbWUubGVhZmVyUHJvbWlzZSA9IG51bGw7CiAgICAgICAgICAgICAgICByZXNvbHZlKHdpbmRvdy5MZWFmZXJVSSk7CiAgICAgICAgICAgICAgICByZXR1cm47CiAgICAgICAgICAgICAgfQogICAgICAgICAgICAgIGlmIChleGlzdGluZy5nZXRBdHRyaWJ1dGUoImRhdGEtZmFpbGVkIikgPT09ICJ0cnVlIikgewogICAgICAgICAgICAgICAgdGhpcy5ydW50aW1lLmxlYWZlclByb21pc2UgPSBudWxsOwogICAgICAgICAgICAgICAgcmVqZWN0KG5ldyBFcnJvcigibGVhZmVyLXVpIOi1hOa6kOWKoOi9veWksei0pSIpKTsKICAgICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgaWYgKERhdGUubm93KCkgLSBzdGFydGVkQXQgPiAyMDAwMCkgewogICAgICAgICAgICAgICAgdGhpcy5ydW50aW1lLmxlYWZlclByb21pc2UgPSBudWxsOwogICAgICAgICAgICAgICAgcmVqZWN0KG5ldyBFcnJvcigi5riy5p+T5byV5pOO5Yqg6L296LaF5pe2IikpOwogICAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICBzZXRUaW1lb3V0KHBvbGwsIDUwKTsKICAgICAgICAgICAgfTsKICAgICAgICAgICAgcG9sbCgpOwogICAgICAgICAgICByZXR1cm47CiAgICAgICAgICB9CgogICAgICAgICAgaWYgKGV4aXN0aW5nICYmIGV4aXN0aW5nLnBhcmVudE5vZGUpIHsKICAgICAgICAgICAgZXhpc3RpbmcucGFyZW50Tm9kZS5yZW1vdmVDaGlsZChleGlzdGluZyk7CiAgICAgICAgICB9CgogICAgICAgICAgY29uc3Qgc2NyaXB0ID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgic2NyaXB0Iik7CiAgICAgICAgICBzY3JpcHQuc3JjID0gdGhpcy5QVl9MRUFGRVJfU1JDOwogICAgICAgICAgc2NyaXB0LmFzeW5jID0gdHJ1ZTsKICAgICAgICAgIHNjcmlwdC5zZXRBdHRyaWJ1dGUoImRhdGEtcHYtbGVhZmVyIiwgIjEiKTsKCiAgICAgICAgICBsZXQgc2V0dGxlZCA9IGZhbHNlOwogICAgICAgICAgbGV0IHRpbWVyID0gbnVsbDsKCiAgICAgICAgICBjb25zdCBmYWlsID0gKGVycm9yKSA9PiB7CiAgICAgICAgICAgIGlmIChzZXR0bGVkKSByZXR1cm47CiAgICAgICAgICAgIHNldHRsZWQgPSB0cnVlOwogICAgICAgICAgICBpZiAodGltZXIpIGNsZWFyVGltZW91dCh0aW1lcik7CiAgICAgICAgICAgIHNjcmlwdC5vbmxvYWQgPSBudWxsOwogICAgICAgICAgICBzY3JpcHQub25lcnJvciA9IG51bGw7CiAgICAgICAgICAgIHNjcmlwdC5zZXRBdHRyaWJ1dGUoImRhdGEtZmFpbGVkIiwgInRydWUiKTsKICAgICAgICAgICAgaWYgKHNjcmlwdC5wYXJlbnROb2RlKSBzY3JpcHQucGFyZW50Tm9kZS5yZW1vdmVDaGlsZChzY3JpcHQpOwogICAgICAgICAgICB0aGlzLnJ1bnRpbWUubGVhZmVyUHJvbWlzZSA9IG51bGw7CiAgICAgICAgICAgIHJlamVjdChlcnJvcik7CiAgICAgICAgICB9OwoKICAgICAgICAgIGNvbnN0IHN1Y2Nlc3MgPSAoKSA9PiB7CiAgICAgICAgICAgIGlmIChzZXR0bGVkKSByZXR1cm47CiAgICAgICAgICAgIGNvbnN0IGxpYiA9IHdpbmRvdy5MZWFmZXJVSTsKICAgICAgICAgICAgaWYgKCFsaWIgfHwgIWxpYi5MZWFmZXIpIHsKICAgICAgICAgICAgICBmYWlsKG5ldyBFcnJvcigi6LWE5rqQ5bey5Yqg6L2977yM5L2G5pyq5om+5YiwIHdpbmRvdy5MZWFmZXJVSSIpKTsKICAgICAgICAgICAgICByZXR1cm47CiAgICAgICAgICAgIH0KICAgICAgICAgICAgc2V0dGxlZCA9IHRydWU7CiAgICAgICAgICAgIGlmICh0aW1lcikgY2xlYXJUaW1lb3V0KHRpbWVyKTsKICAgICAgICAgICAgc2NyaXB0LnNldEF0dHJpYnV0ZSgiZGF0YS1sb2FkZWQiLCAidHJ1ZSIpOwogICAgICAgICAgICBzY3JpcHQucmVtb3ZlQXR0cmlidXRlKCJkYXRhLWZhaWxlZCIpOwogICAgICAgICAgICAvLyDlkI7nu63osIPnlKjnm7TmjqXku44gd2luZG93LkxlYWZlclVJIOWRveS4re+8jOS4jeW/heWGjeWkjeeUqOivpSBQcm9taXNl44CCCiAgICAgICAgICAgIHRoaXMucnVudGltZS5sZWFmZXJQcm9taXNlID0gbnVsbDsKICAgICAgICAgICAgcmVzb2x2ZShsaWIpOwogICAgICAgICAgfTsKCiAgICAgICAgICBzY3JpcHQub25sb2FkID0gc3VjY2VzczsKICAgICAgICAgIHNjcmlwdC5vbmVycm9yID0gKCkgPT4gZmFpbChuZXcgRXJyb3IoImxlYWZlci11aSDotYTmupDliqDovb3lpLHotKUiKSk7CiAgICAgICAgICB0aW1lciA9IHNldFRpbWVvdXQoKCkgPT4gZmFpbChuZXcgRXJyb3IoIua4suafk+W8leaTjuWKoOi9vei2heaXtiIpKSwgMjAwMDApOwoKICAgICAgICAgIChkb2N1bWVudC5oZWFkIHx8IGRvY3VtZW50LmJvZHkpLmFwcGVuZENoaWxkKHNjcmlwdCk7CiAgICAgICAgfSk7CgogICAgICAgIHJldHVybiB0aGlzLnJ1bnRpbWUubGVhZmVyUHJvbWlzZTsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDmlofmnKzmtYvph48gJiDoh6rliqjmjaLooYwKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCgogICAgICBwdk1lYXN1cmVDdHgoKSB7CiAgICAgICAgaWYgKCF0aGlzLnJ1bnRpbWUubWVhc3VyZUN0eCkgewogICAgICAgICAgaWYgKHR5cGVvZiBkb2N1bWVudCA9PT0gInVuZGVmaW5lZCIpIHJldHVybiBudWxsOwogICAgICAgICAgY29uc3QgY2FudmFzID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgiY2FudmFzIik7CiAgICAgICAgICB0aGlzLnJ1bnRpbWUubWVhc3VyZUN0eCA9IGNhbnZhcy5nZXRDb250ZXh0KCIyZCIpOwogICAgICAgIH0KICAgICAgICByZXR1cm4gdGhpcy5ydW50aW1lLm1lYXN1cmVDdHg7CiAgICAgIH0sCgogICAgICBwdkZvbnQod2VpZ2h0LCBzaXplLCBtb25vKSB7CiAgICAgICAgcmV0dXJuIFN0cmluZyh3ZWlnaHQgfHwgNDAwKSArICIgIiArIHNpemUgKyAicHggIiArIChtb25vID8gdGhpcy5QVl9NT05PIDogdGhpcy5QVl9GT05UKTsKICAgICAgfSwKCiAgICAgIHB2VG9rZW5pemUodGV4dCkgewogICAgICAgIGNvbnN0IHRva2VucyA9IFtdOwogICAgICAgIGxldCBidWZmZXIgPSAiIjsKICAgICAgICBjb25zdCBjaGFycyA9IEFycmF5LmZyb20oU3RyaW5nKHRleHQgPT0gbnVsbCA/ICIiIDogdGV4dCkpOwoKICAgICAgICBjaGFycy5mb3JFYWNoKChjaGFyKSA9PiB7CiAgICAgICAgICBpZiAoY2hhciA9PT0gIiAiIHx8IGNoYXIgPT09ICJcdCIpIHsKICAgICAgICAgICAgaWYgKGJ1ZmZlcikgewogICAgICAgICAgICAgIHRva2Vucy5wdXNoKGJ1ZmZlcik7CiAgICAgICAgICAgICAgYnVmZmVyID0gIiI7CiAgICAgICAgICAgIH0KICAgICAgICAgICAgdG9rZW5zLnB1c2goIiAiKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQogICAgICAgICAgaWYgKAogICAgICAgICAgICAvW1x1MmU4MC1cdTlmZmZcdWFjMDAtXHVkN2FmXHVmOTAwLVx1ZmFmZlx1MzAwMC1cdTMwM2ZcdWZmMDAtXHVmZmVmXS8udGVzdCgKICAgICAgICAgICAgICBjaGFyCiAgICAgICAgICAgICkKICAgICAgICAgICkgewogICAgICAgICAgICBpZiAoYnVmZmVyKSB7CiAgICAgICAgICAgICAgdG9rZW5zLnB1c2goYnVmZmVyKTsKICAgICAgICAgICAgICBidWZmZXIgPSAiIjsKICAgICAgICAgICAgfQogICAgICAgICAgICB0b2tlbnMucHVzaChjaGFyKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQogICAgICAgICAgYnVmZmVyICs9IGNoYXI7CiAgICAgICAgfSk7CgogICAgICAgIGlmIChidWZmZXIpIHRva2Vucy5wdXNoKGJ1ZmZlcik7CiAgICAgICAgcmV0dXJuIHRva2VuczsKICAgICAgfSwKCiAgICAgIHB2V3JhcFRleHQodGV4dCwgbWF4V2lkdGgsIGZvbnQsIGxldHRlclNwYWNpbmcsIG1heExpbmVzKSB7CiAgICAgICAgY29uc3QgY3R4ID0gdGhpcy5wdk1lYXN1cmVDdHgoKTsKICAgICAgICBjb25zdCBzcGFjaW5nID0gTnVtYmVyKGxldHRlclNwYWNpbmcpIHx8IDA7CiAgICAgICAgY29uc3QgbGltaXQgPSBNYXRoLm1heCgxLCBOdW1iZXIobWF4V2lkdGgpIHx8IDEpOwogICAgICAgIGNvbnN0IGxpbmVzID0gW107CgogICAgICAgIGlmICghY3R4KSB7CiAgICAgICAgICBjb25zdCBmYWxsYmFjayA9IFN0cmluZyh0ZXh0ID09IG51bGwgPyAiIiA6IHRleHQpOwogICAgICAgICAgcmV0dXJuIG1heExpbmVzID8gW2ZhbGxiYWNrLnNsaWNlKDAsIE1hdGgubWF4KDEsIG1heExpbmVzICogMTIpKV0gOiBbZmFsbGJhY2tdOwogICAgICAgIH0KCiAgICAgICAgY3R4LmZvbnQgPSBmb250OwoKICAgICAgICBjb25zdCB3aWR0aE9mID0gKHN0cikgPT4gewogICAgICAgICAgaWYgKCFzdHIpIHJldHVybiAwOwogICAgICAgICAgcmV0dXJuICgKICAgICAgICAgICAgY3R4Lm1lYXN1cmVUZXh0KHN0cikud2lkdGggKwogICAgICAgICAgICBNYXRoLm1heCgwLCBBcnJheS5mcm9tKHN0cikubGVuZ3RoIC0gMSkgKiBzcGFjaW5nCiAgICAgICAgICApOwogICAgICAgIH07CgogICAgICAgIGNvbnN0IHB1c2hMaW5lID0gKGxpbmUpID0+IHsKICAgICAgICAgIGxpbmVzLnB1c2goU3RyaW5nKGxpbmUgPT0gbnVsbCA/ICIiIDogbGluZSkucmVwbGFjZSgvXHMrJC9nLCAiIikpOwogICAgICAgIH07CgogICAgICAgIGNvbnN0IHNwbGl0TG9uZ1Rva2VuID0gKHRva2VuKSA9PiB7CiAgICAgICAgICBsZXQgcGllY2UgPSAiIjsKICAgICAgICAgIEFycmF5LmZyb20odG9rZW4pLmZvckVhY2goKGNoYXIpID0+IHsKICAgICAgICAgICAgaWYgKHBpZWNlICYmIHdpZHRoT2YocGllY2UgKyBjaGFyKSA+IGxpbWl0KSB7CiAgICAgICAgICAgICAgcHVzaExpbmUocGllY2UpOwogICAgICAgICAgICAgIHBpZWNlID0gY2hhcjsKICAgICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgICBwaWVjZSArPSBjaGFyOwogICAgICAgICAgICB9CiAgICAgICAgICB9KTsKICAgICAgICAgIHJldHVybiBwaWVjZTsKICAgICAgICB9OwoKICAgICAgICBjb25zdCBwYXJhZ3JhcGhzID0gU3RyaW5nKHRleHQgPT0gbnVsbCA/ICIiIDogdGV4dCkuc3BsaXQoL1xyP1xuLyk7CgogICAgICAgIGZvciAobGV0IHBJbmRleCA9IDA7IHBJbmRleCA8IHBhcmFncmFwaHMubGVuZ3RoOyBwSW5kZXggKz0gMSkgewogICAgICAgICAgY29uc3QgdG9rZW5zID0gdGhpcy5wdlRva2VuaXplKHBhcmFncmFwaHNbcEluZGV4XSk7CiAgICAgICAgICBsZXQgbGluZSA9ICIiOwogICAgICAgICAgbGV0IHBlbmRpbmdTcGFjZSA9IGZhbHNlOwoKICAgICAgICAgIHRva2Vucy5mb3JFYWNoKCh0b2tlbikgPT4gewogICAgICAgICAgICBpZiAodG9rZW4gPT09ICIgIikgewogICAgICAgICAgICAgIGlmIChsaW5lKSBwZW5kaW5nU3BhY2UgPSB0cnVlOwogICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgY29uc3Qgc2VwYXJhdG9yID0gcGVuZGluZ1NwYWNlID8gIiAiIDogIiI7CiAgICAgICAgICAgIGNvbnN0IGNhbmRpZGF0ZSA9IGxpbmUgKyBzZXBhcmF0b3IgKyB0b2tlbjsKCiAgICAgICAgICAgIGlmICghbGluZSB8fCB3aWR0aE9mKGNhbmRpZGF0ZSkgPD0gbGltaXQpIHsKICAgICAgICAgICAgICBpZiAoIWxpbmUgJiYgd2lkdGhPZih0b2tlbikgPiBsaW1pdCkgewogICAgICAgICAgICAgICAgbGluZSA9IHNwbGl0TG9uZ1Rva2VuKHRva2VuKTsKICAgICAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICAgICAgbGluZSA9IGNhbmRpZGF0ZTsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgcGVuZGluZ1NwYWNlID0gZmFsc2U7CiAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICB9CgogICAgICAgICAgICBwdXNoTGluZShsaW5lKTsKICAgICAgICAgICAgbGluZSA9IHRva2VuOwogICAgICAgICAgICBwZW5kaW5nU3BhY2UgPSBmYWxzZTsKCiAgICAgICAgICAgIGlmICh3aWR0aE9mKHRva2VuKSA+IGxpbWl0KSB7CiAgICAgICAgICAgICAgbGluZSA9IHNwbGl0TG9uZ1Rva2VuKHRva2VuKTsKICAgICAgICAgICAgfQogICAgICAgICAgfSk7CgogICAgICAgICAgcHVzaExpbmUobGluZSk7CiAgICAgICAgICBpZiAobWF4TGluZXMgJiYgbGluZXMubGVuZ3RoID49IG1heExpbmVzKSBicmVhazsKICAgICAgICB9CgogICAgICAgIGlmICghbGluZXMubGVuZ3RoKSBsaW5lcy5wdXNoKCIiKTsKCiAgICAgICAgaWYgKG1heExpbmVzICYmIGxpbmVzLmxlbmd0aCA+IG1heExpbmVzKSB7CiAgICAgICAgICBjb25zdCBrZXB0ID0gbGluZXMuc2xpY2UoMCwgbWF4TGluZXMpOwogICAgICAgICAgbGV0IGxhc3QgPSBrZXB0W21heExpbmVzIC0gMV0gfHwgIiI7CiAgICAgICAgICB3aGlsZSAobGFzdCAmJiB3aWR0aE9mKGxhc3QgKyAi4oCmIikgPiBsaW1pdCkgewogICAgICAgICAgICBsYXN0ID0gQXJyYXkuZnJvbShsYXN0KS5zbGljZSgwLCAtMSkuam9pbigiIik7CiAgICAgICAgICB9CiAgICAgICAgICBrZXB0W21heExpbmVzIC0gMV0gPSBsYXN0ICsgIuKApiI7CiAgICAgICAgICByZXR1cm4ga2VwdDsKICAgICAgICB9CgogICAgICAgIHJldHVybiBsaW5lczsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDpopzoibIgJiDlsLrlr7jlt6XlhbcKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2TnVtKHZhbHVlKSB7CiAgICAgICAgcmV0dXJuIHR5cGVvZiB2YWx1ZSA9PT0gIm51bWJlciIgJiYgaXNGaW5pdGUodmFsdWUpID8gdmFsdWUgOiAwOwogICAgICB9LAoKICAgICAgLy8gTGVhZmVyIOWcqOWQjOaXtuWMheWQq+aWh+acrOS4juWNiumAj+aYjuWhq+WFheeahOWbvuWxgumHjOWPr+iDveWHuueOsOWhq+WFheWPoOiJsu+8jAogICAgICAvLyDlm6DmraTlm77ooajnmoTlpKfpnaLnp6/loavlhYXnu5/kuIDkvb/nlKjigJzkuI7nmb3lupXmt7flkIjlkI7nmoTkuI3pgI/mmI7mtYXoibLigJ3jgIIKICAgICAgcHZUaW50KGNvbG9yLCByYXRpbykgewogICAgICAgIGNvbnN0IGFscGhhID0gTWF0aC5tYXgoMCwgTWF0aC5taW4oMSwgdGhpcy5wdk51bShyYXRpbykpKTsKICAgICAgICBpZiAodHlwZW9mIGNvbG9yICE9PSAic3RyaW5nIiB8fCBjb2xvci5jaGFyQXQoMCkgIT09ICIjIikgcmV0dXJuIGNvbG9yOwoKICAgICAgICBsZXQgaGV4ID0gY29sb3Iuc2xpY2UoMSk7CiAgICAgICAgaWYgKGhleC5sZW5ndGggPT09IDMpIHsKICAgICAgICAgIGhleCA9IGhleAogICAgICAgICAgICAuc3BsaXQoIiIpCiAgICAgICAgICAgIC5tYXAoKGNoYXIpID0+IGNoYXIgKyBjaGFyKQogICAgICAgICAgICAuam9pbigiIik7CiAgICAgICAgfQogICAgICAgIGlmIChoZXgubGVuZ3RoICE9PSA2KSByZXR1cm4gY29sb3I7CgogICAgICAgIGNvbnN0IHJlZCA9IHBhcnNlSW50KGhleC5zbGljZSgwLCAyKSwgMTYpOwogICAgICAgIGNvbnN0IGdyZWVuID0gcGFyc2VJbnQoaGV4LnNsaWNlKDIsIDQpLCAxNik7CiAgICAgICAgY29uc3QgYmx1ZSA9IHBhcnNlSW50KGhleC5zbGljZSg0LCA2KSwgMTYpOwoKICAgICAgICBjb25zdCBtaXggPSAodmFsdWUpID0+CiAgICAgICAgICBNYXRoLnJvdW5kKDI1NSAtICgyNTUgLSB2YWx1ZSkgKiBhbHBoYSkKICAgICAgICAgICAgLnRvU3RyaW5nKDE2KQogICAgICAgICAgICAucGFkU3RhcnQoMiwgIjAiKTsKCiAgICAgICAgcmV0dXJuICIjIiArIG1peChyZWQpICsgbWl4KGdyZWVuKSArIG1peChibHVlKTsKICAgICAgfSwKCiAgICAgIHB2RXh0cmEoY3R4LCBleHRyYSkgewogICAgICAgIGlmICghZXh0cmEpIHJldHVybiB7fTsKICAgICAgICBjb25zdCBvdXQgPSBPYmplY3QuYXNzaWduKHt9LCBleHRyYSk7CgogICAgICAgIFsiY29ybmVyUmFkaXVzIiwgInN0cm9rZVdpZHRoIiwgImxldHRlclNwYWNpbmciXS5mb3JFYWNoKChrZXkpID0+IHsKICAgICAgICAgIGlmICh0eXBlb2Ygb3V0W2tleV0gPT09ICJudW1iZXIiKSB7CiAgICAgICAgICAgIG91dFtrZXldID0gb3V0W2tleV0gKiBjdHguazsKICAgICAgICAgIH0gZWxzZSBpZiAoQXJyYXkuaXNBcnJheShvdXRba2V5XSkpIHsKICAgICAgICAgICAgb3V0W2tleV0gPSBvdXRba2V5XS5tYXAoKHZhbHVlKSA9PiB2YWx1ZSAqIGN0eC5rKTsKICAgICAgICAgIH0KICAgICAgICB9KTsKCiAgICAgICAgaWYgKG91dC5zaGFkb3cpIHsKICAgICAgICAgIGNvbnN0IHNoYWRvdyA9IE9iamVjdC5hc3NpZ24oe30sIG91dC5zaGFkb3cpOwogICAgICAgICAgc2hhZG93LnggPSB0aGlzLnB2TnVtKHNoYWRvdy54KSAqIGN0eC5rOwogICAgICAgICAgc2hhZG93LnkgPSB0aGlzLnB2TnVtKHNoYWRvdy55KSAqIGN0eC5rOwogICAgICAgICAgc2hhZG93LmJsdXIgPSB0aGlzLnB2TnVtKHNoYWRvdy5ibHVyKSAqIGN0eC5rOwogICAgICAgICAgb3V0LnNoYWRvdyA9IHNoYWRvdzsKICAgICAgICB9CgogICAgICAgIGlmIChvdXQuYm9yZGVyICYmIHR5cGVvZiBvdXQuYm9yZGVyID09PSAib2JqZWN0IikgewogICAgICAgICAgY29uc3QgYm9yZGVyID0gT2JqZWN0LmFzc2lnbih7fSwgb3V0LmJvcmRlcik7CiAgICAgICAgICBpZiAodHlwZW9mIGJvcmRlci53aWR0aCA9PT0gIm51bWJlciIpIGJvcmRlci53aWR0aCAqPSBjdHguazsKICAgICAgICAgIG91dC5ib3JkZXIgPSBib3JkZXI7CiAgICAgICAgfQoKICAgICAgICBpZiAoQXJyYXkuaXNBcnJheShvdXQuZGFzaFBhdHRlcm4pKSB7CiAgICAgICAgICBvdXQuZGFzaFBhdHRlcm4gPSBvdXQuZGFzaFBhdHRlcm4ubWFwKCh2YWx1ZSkgPT4gdmFsdWUgKiBjdHguayk7CiAgICAgICAgfQoKICAgICAgICByZXR1cm4gb3V0OwogICAgICB9LAoKICAgICAgcHZSZWN0KGN0eCwgcGFyZW50LCB4LCB5LCB3aWR0aCwgaGVpZ2h0LCBmaWxsLCBleHRyYSkgewogICAgICAgIGNvbnN0IG5vZGUgPSBuZXcgY3R4LmxpYi5SZWN0KAogICAgICAgICAgT2JqZWN0LmFzc2lnbigKICAgICAgICAgICAgewogICAgICAgICAgICAgIHg6IHggKiBjdHguaywKICAgICAgICAgICAgICB5OiB5ICogY3R4LmssCiAgICAgICAgICAgICAgd2lkdGg6IE1hdGgubWF4KDAuMDEsIHdpZHRoICogY3R4LmspLAogICAgICAgICAgICAgIGhlaWdodDogTWF0aC5tYXgoMC4wMSwgaGVpZ2h0ICogY3R4LmspLAogICAgICAgICAgICAgIGZpbGwsCiAgICAgICAgICAgIH0sCiAgICAgICAgICAgIHRoaXMucHZFeHRyYShjdHgsIGV4dHJhKQogICAgICAgICAgKQogICAgICAgICk7CiAgICAgICAgcGFyZW50LmFkZChub2RlKTsKICAgICAgICByZXR1cm4gbm9kZTsKICAgICAgfSwKCiAgICAgIHB2TGluZShjdHgsIHBhcmVudCwgeDEsIHkxLCB4MiwgeTIsIHN0cm9rZSwgc3Ryb2tlV2lkdGgsIGV4dHJhKSB7CiAgICAgICAgY29uc3Qgbm9kZSA9IG5ldyBjdHgubGliLkxpbmUoCiAgICAgICAgICBPYmplY3QuYXNzaWduKAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgeDogeDEgKiBjdHguaywKICAgICAgICAgICAgICB5OiB5MSAqIGN0eC5rLAogICAgICAgICAgICAgIHBvaW50czogWzAsIDAsICh4MiAtIHgxKSAqIGN0eC5rLCAoeTIgLSB5MSkgKiBjdHgua10sCiAgICAgICAgICAgICAgc3Ryb2tlLAogICAgICAgICAgICAgIHN0cm9rZVdpZHRoOiBNYXRoLm1heCgwLjYsIChzdHJva2VXaWR0aCA9PSBudWxsID8gMSA6IHN0cm9rZVdpZHRoKSAqIGN0eC5rKSwKICAgICAgICAgICAgfSwKICAgICAgICAgICAgdGhpcy5wdkV4dHJhKGN0eCwgZXh0cmEpCiAgICAgICAgICApCiAgICAgICAgKTsKICAgICAgICBwYXJlbnQuYWRkKG5vZGUpOwogICAgICAgIHJldHVybiBub2RlOwogICAgICB9LAoKICAgICAgcHZQYXRoKGN0eCwgcGFyZW50LCBwYXRoLCBmaWxsLCBleHRyYSkgewogICAgICAgIGNvbnN0IG5vZGUgPSBuZXcgY3R4LmxpYi5QYXRoKAogICAgICAgICAgT2JqZWN0LmFzc2lnbigKICAgICAgICAgICAgewogICAgICAgICAgICAgIHBhdGgsCiAgICAgICAgICAgICAgLy8gTGVhZmVyIOWvuSBQYXRoIOeahCAibm9uZSIg5aGr5YWF5Zyo6YOo5YiG57yp5pS+L+aWh+acrOWbvuWxguS4reS8mumAgOWMluS4ugogICAgICAgICAgICAgIC8vIOeUqOaPj+i+ueiJsuWhq+WFhe+8jOi/memHjOe7n+S4gOS9v+eUqOecn+ato+eahOmAj+aYjuiJsuOAggogICAgICAgICAgICAgIGZpbGw6IGZpbGwgPT0gbnVsbCB8fCBmaWxsID09PSAibm9uZSIgPyAicmdiYSgwLCAwLCAwLCAwKSIgOiBmaWxsLAogICAgICAgICAgICB9LAogICAgICAgICAgICB0aGlzLnB2RXh0cmEoY3R4LCBleHRyYSkKICAgICAgICAgICkKICAgICAgICApOwogICAgICAgIHBhcmVudC5hZGQobm9kZSk7CiAgICAgICAgcmV0dXJuIG5vZGU7CiAgICAgIH0sCgogICAgICBwdkVsbGlwc2UoY3R4LCBwYXJlbnQsIHgsIHksIHdpZHRoLCBoZWlnaHQsIGZpbGwsIGV4dHJhKSB7CiAgICAgICAgY29uc3Qgbm9kZSA9IG5ldyBjdHgubGliLkVsbGlwc2UoCiAgICAgICAgICBPYmplY3QuYXNzaWduKAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgeDogeCAqIGN0eC5rLAogICAgICAgICAgICAgIHk6IHkgKiBjdHguaywKICAgICAgICAgICAgICB3aWR0aDogTWF0aC5tYXgoMC4wMSwgd2lkdGggKiBjdHguayksCiAgICAgICAgICAgICAgaGVpZ2h0OiBNYXRoLm1heCgwLjAxLCBoZWlnaHQgKiBjdHguayksCiAgICAgICAgICAgICAgZmlsbCwKICAgICAgICAgICAgfSwKICAgICAgICAgICAgdGhpcy5wdkV4dHJhKGN0eCwgZXh0cmEpCiAgICAgICAgICApCiAgICAgICAgKTsKICAgICAgICBwYXJlbnQuYWRkKG5vZGUpOwogICAgICAgIHJldHVybiBub2RlOwogICAgICB9LAoKICAgICAgcHZGcmFtZShjdHgsIHBhcmVudCwgeCwgeSwgd2lkdGgsIGhlaWdodCwgZmlsbCwgZXh0cmEpIHsKICAgICAgICBjb25zdCBub2RlID0gbmV3IGN0eC5saWIuRnJhbWUoCiAgICAgICAgICBPYmplY3QuYXNzaWduKAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgeDogeCAqIGN0eC5rLAogICAgICAgICAgICAgIHk6IHkgKiBjdHguaywKICAgICAgICAgICAgICB3aWR0aDogTWF0aC5tYXgoMC4wMSwgd2lkdGggKiBjdHguayksCiAgICAgICAgICAgICAgaGVpZ2h0OiBNYXRoLm1heCgwLjAxLCBoZWlnaHQgKiBjdHguayksCiAgICAgICAgICAgICAgZmlsbCwKICAgICAgICAgICAgfSwKICAgICAgICAgICAgdGhpcy5wdkV4dHJhKGN0eCwgZXh0cmEpCiAgICAgICAgICApCiAgICAgICAgKTsKICAgICAgICBwYXJlbnQuYWRkKG5vZGUpOwogICAgICAgIHJldHVybiBub2RlOwogICAgICB9LAoKICAgICAgcHZHcm91cChjdHgsIHBhcmVudCwgeCwgeSwgd2lkdGgsIGhlaWdodCwgZXh0cmEpIHsKICAgICAgICBjb25zdCBwcm9wcyA9IE9iamVjdC5hc3NpZ24oCiAgICAgICAgICB7CiAgICAgICAgICAgIHg6IHggKiBjdHguaywKICAgICAgICAgICAgeTogeSAqIGN0eC5rLAogICAgICAgICAgfSwKICAgICAgICAgIHRoaXMucHZFeHRyYShjdHgsIGV4dHJhKQogICAgICAgICk7CiAgICAgICAgaWYgKHR5cGVvZiB3aWR0aCA9PT0gIm51bWJlciIpIHByb3BzLndpZHRoID0gTWF0aC5tYXgoMC4wMSwgd2lkdGggKiBjdHguayk7CiAgICAgICAgaWYgKHR5cGVvZiBoZWlnaHQgPT09ICJudW1iZXIiKSBwcm9wcy5oZWlnaHQgPSBNYXRoLm1heCgwLjAxLCBoZWlnaHQgKiBjdHguayk7CgogICAgICAgIGNvbnN0IG5vZGUgPSBuZXcgY3R4LmxpYi5Hcm91cChwcm9wcyk7CiAgICAgICAgcGFyZW50LmFkZChub2RlKTsKICAgICAgICByZXR1cm4gbm9kZTsKICAgICAgfSwKCiAgICAgIHB2VGV4dChjdHgsIHBhcmVudCwgb3B0aW9ucykgewogICAgICAgIGNvbnN0IG9wdHMgPSBvcHRpb25zIHx8IHt9OwogICAgICAgIGNvbnN0IGZvbnRTaXplID0gb3B0cy5mb250U2l6ZSB8fCAzMDsKICAgICAgICBjb25zdCBsaW5lSGVpZ2h0ID0gb3B0cy5saW5lSGVpZ2h0IHx8IGZvbnRTaXplICogMS40OwogICAgICAgIGNvbnN0IGxpbmVzID0gb3B0cy5saW5lczsKICAgICAgICBjb25zdCBjb250ZW50ID0gbGluZXMgPyBsaW5lcy5qb2luKCJcbiIpIDogb3B0cy50ZXh0IHx8ICIiOwogICAgICAgIGNvbnN0IGhlaWdodCA9CiAgICAgICAgICBvcHRzLmhlaWdodCAhPSBudWxsCiAgICAgICAgICAgID8gb3B0cy5oZWlnaHQKICAgICAgICAgICAgOiBsaW5lcwogICAgICAgICAgICA/IGxpbmVzLmxlbmd0aCAqIGxpbmVIZWlnaHQKICAgICAgICAgICAgOiB1bmRlZmluZWQ7CgogICAgICAgIGNvbnN0IHByb3BzID0gewogICAgICAgICAgeDogdGhpcy5wdk51bShvcHRzLngpICogY3R4LmssCiAgICAgICAgICB5OiB0aGlzLnB2TnVtKG9wdHMueSkgKiBjdHguaywKICAgICAgICAgIHdpZHRoOgogICAgICAgICAgICBvcHRzLndpZHRoICE9IG51bGwKICAgICAgICAgICAgICA/IE1hdGgubWF4KDAuMDEsIG9wdHMud2lkdGggKiBjdHguaykKICAgICAgICAgICAgICA6IHVuZGVmaW5lZCwKICAgICAgICAgIGhlaWdodDoKICAgICAgICAgICAgaGVpZ2h0ICE9IG51bGwgPyBNYXRoLm1heCgwLjAxLCBoZWlnaHQgKiBjdHguaykgOiB1bmRlZmluZWQsCiAgICAgICAgICB0ZXh0OiBjb250ZW50LAogICAgICAgICAgZm9udFNpemU6IGZvbnRTaXplICogY3R4LmssCiAgICAgICAgICBsaW5lSGVpZ2h0OiBsaW5lSGVpZ2h0ICogY3R4LmssCiAgICAgICAgICBmb250RmFtaWx5OgogICAgICAgICAgICBvcHRzLmZvbnRGYW1pbHkgfHwgKG9wdHMubW9ubyA/IHRoaXMuUFZfTU9OTyA6IHRoaXMuUFZfRk9OVCksCiAgICAgICAgICBmb250V2VpZ2h0OiBvcHRzLndlaWdodCA9PSBudWxsID8gIm5vcm1hbCIgOiBvcHRzLndlaWdodCwKICAgICAgICAgIGZpbGw6IG9wdHMuZmlsbCA9PSBudWxsID8gdGhpcy5QVl9DLnRleHQxIDogb3B0cy5maWxsLAogICAgICAgICAgdGV4dEFsaWduOiBvcHRzLmFsaWduIHx8ICJsZWZ0IiwKICAgICAgICAgIHZlcnRpY2FsQWxpZ246IG9wdHMudmFsaWduIHx8ICJ0b3AiLAogICAgICAgICAgdGV4dFdyYXA6ICJub25lIiwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IChvcHRzLmxldHRlclNwYWNpbmcgfHwgMCkgKiBjdHguaywKICAgICAgICB9OwoKICAgICAgICBpZiAob3B0cy5vcGFjaXR5ICE9IG51bGwpIHByb3BzLm9wYWNpdHkgPSBvcHRzLm9wYWNpdHk7CgogICAgICAgIGNvbnN0IG5vZGUgPSBuZXcgY3R4LmxpYi5UZXh0KHByb3BzKTsKICAgICAgICBwYXJlbnQuYWRkKG5vZGUpOwogICAgICAgIHJldHVybiBub2RlOwogICAgICB9LAoKICAgICAgcHZBbmltYXRlKGN0eCwgbm9kZSwgZGVsYXksIGtpbmQpIHsKICAgICAgICBpZiAoIWN0eC5hbmltYXRlZCB8fCBjdHguYW5pbWF0ZSA9PT0gZmFsc2UpIHJldHVybjsKICAgICAgICBjdHguYW5pbWF0ZWQucHVzaCh7CiAgICAgICAgICBub2RlLAogICAgICAgICAgZGVsYXk6IHRoaXMucHZOdW0oZGVsYXkpLAogICAgICAgICAga2luZDoga2luZCB8fCAidXAiLAogICAgICAgIH0pOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOivreS5ieivhuWIq++8mueJiOW8j+OAgeWvhuW6puOAgeexu+Wei+agh+etvgogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcHZDb3ZlclRleHRzKHNsaWRlKSB7CiAgICAgICAgaWYgKCFzbGlkZSB8fCAhQXJyYXkuaXNBcnJheShzbGlkZS5lbGVtZW50cykpIHJldHVybiBbXTsKICAgICAgICByZXR1cm4gc2xpZGUuZWxlbWVudHMuZmlsdGVyKChlbGVtZW50KSA9PiBlbGVtZW50LnR5cGUgPT09ICJ0ZXh0Iik7CiAgICAgIH0sCgogICAgICBwdlR5cGVMYWJlbChzbGlkZSkgewogICAgICAgIGlmICghc2xpZGUpIHJldHVybiAi5bm754Gv54mHIjsKICAgICAgICBpZiAoc2xpZGUudHlwZSA9PT0gInRpdGxlIikgcmV0dXJuICLlsIHpnaIiOwogICAgICAgIGlmIChzbGlkZS50eXBlID09PSAic3VtbWFyeSIpIHJldHVybiAi5oC757uTIjsKCiAgICAgICAgY29uc3QgZWxlbWVudHMgPSBzbGlkZS5lbGVtZW50cyB8fCBbXTsKICAgICAgICBjb25zdCBjaGFydEVsZW1lbnQgPSBlbGVtZW50cy5maWx0ZXIoKGVsZW1lbnQpID0+IGVsZW1lbnQudHlwZSA9PT0gImNoYXJ0IilbMF07CgogICAgICAgIGlmIChjaGFydEVsZW1lbnQpIHsKICAgICAgICAgIGlmIChjaGFydEVsZW1lbnQuY2hhcnRUeXBlID09PSAibWVybWFpZCIpIHJldHVybiAi5p625p6E5rWB56iLIjsKICAgICAgICAgIGNvbnN0IGNoYXJ0VHlwZSA9CiAgICAgICAgICAgIGNoYXJ0RWxlbWVudC5jaGFydERhdGEgJiYgY2hhcnRFbGVtZW50LmNoYXJ0RGF0YS50eXBlCiAgICAgICAgICAgICAgPyBjaGFydEVsZW1lbnQuY2hhcnREYXRhLnR5cGUKICAgICAgICAgICAgICA6ICIiOwogICAgICAgICAgaWYgKGNoYXJ0VHlwZSA9PT0gImxpbmUiIHx8IGNoYXJ0VHlwZSA9PT0gImFyZWEiKSByZXR1cm4gIui2i+WKv+a0nuWvnyI7CiAgICAgICAgICBpZiAoY2hhcnRUeXBlID09PSAiYmFyIiB8fCBjaGFydFR5cGUgPT09ICJob3Jpem9udGFsQmFyIikgcmV0dXJuICLmlbDmja7lr7nmr5QiOwogICAgICAgICAgaWYgKAogICAgICAgICAgICBjaGFydFR5cGUgPT09ICJwaWUiIHx8CiAgICAgICAgICAgIGNoYXJ0VHlwZSA9PT0gImRvdWdobnV0IiB8fAogICAgICAgICAgICBjaGFydFR5cGUgPT09ICJwb2xhckFyZWEiCiAgICAgICAgICApIHsKICAgICAgICAgICAgcmV0dXJuICLnu5PmnoTljaDmr5QiOwogICAgICAgICAgfQogICAgICAgICAgaWYgKGNoYXJ0VHlwZSA9PT0gInJhZGFyIikgcmV0dXJuICLlpJrnu7Tor4TkvLAiOwogICAgICAgICAgaWYgKGNoYXJ0VHlwZSA9PT0gInNjYXR0ZXIiKSByZXR1cm4gIuebuOWFs+WIhuaekCI7CiAgICAgICAgICByZXR1cm4gIuaVsOaNrua0nuWvnyI7CiAgICAgICAgfQoKICAgICAgICBpZiAoc2xpZGUudHlwZSA9PT0gInNwbGl0IikgcmV0dXJuICLlm77mlofmt7fmjpIiOwogICAgICAgIHJldHVybiAi5qC45b+D5YaF5a65IjsKICAgICAgfSwKCiAgICAgIHB2Qm9keUxheW91dChzbGlkZSkgewogICAgICAgIGNvbnN0IGVsZW1lbnRzID0gc2xpZGUgJiYgc2xpZGUuZWxlbWVudHMgPyBzbGlkZS5lbGVtZW50cyA6IFtdOwogICAgICAgIGNvbnN0IHR5cGVzID0gZWxlbWVudHMubWFwKChlbGVtZW50KSA9PiBlbGVtZW50LnR5cGUpOwogICAgICAgIGNvbnN0IGhhc0NoYXJ0ID0gdHlwZXMuaW5kZXhPZigiY2hhcnQiKSAhPT0gLTE7CiAgICAgICAgY29uc3QgaGFzTGlzdCA9IHR5cGVzLmluZGV4T2YoImxpc3QiKSAhPT0gLTE7CiAgICAgICAgY29uc3QgaGFzVGV4dCA9IHR5cGVzLmluZGV4T2YoInRleHQiKSAhPT0gLTE7CiAgICAgICAgY29uc3QgaGFzSW1hZ2UgPSB0eXBlcy5pbmRleE9mKCJpbWFnZSIpICE9PSAtMTsKICAgICAgICBjb25zdCBoYXNUYWJsZSA9IHR5cGVzLmluZGV4T2YoInRhYmxlIikgIT09IC0xOwoKICAgICAgICBpZiAoZWxlbWVudHMubGVuZ3RoID09PSAxKSB7CiAgICAgICAgICBpZiAoaGFzQ2hhcnQpIHJldHVybiAic29sby1jaGFydCI7CiAgICAgICAgICBpZiAoaGFzSW1hZ2UpIHJldHVybiAic29sby1pbWFnZSI7CiAgICAgICAgICBpZiAoaGFzVGFibGUpIHJldHVybiAic29sby10YWJsZSI7CiAgICAgICAgICBpZiAoaGFzTGlzdCkgcmV0dXJuICJzb2xvLWxpc3QiOwogICAgICAgICAgcmV0dXJuICJzb2xvIjsKICAgICAgICB9CgogICAgICAgIGlmIChoYXNDaGFydCkgewogICAgICAgICAgaWYgKGhhc0xpc3QpIHJldHVybiBoYXNUZXh0ID8gImxlYWQtbGlzdC1jaGFydCIgOiAibGlzdC1jaGFydCI7CiAgICAgICAgICBpZiAoaGFzSW1hZ2UpIHJldHVybiAiaW1hZ2UtY2hhcnQiOwogICAgICAgICAgaWYgKGhhc1RhYmxlKSByZXR1cm4gInRhYmxlLWNoYXJ0IjsKICAgICAgICAgIGlmIChoYXNUZXh0KSByZXR1cm4gInRleHQtY2hhcnQiOwogICAgICAgICAgcmV0dXJuICJzdGFjayI7CiAgICAgICAgfQoKICAgICAgICBpZiAoaGFzSW1hZ2UgJiYgaGFzVGV4dCkgcmV0dXJuICJpbWFnZS10ZXh0IjsKICAgICAgICByZXR1cm4gInN0YWNrIjsKICAgICAgfSwKCiAgICAgIHB2RGVuc2l0eShzbGlkZSkgewogICAgICAgIGNvbnN0IGVsZW1lbnRzID0gc2xpZGUgJiYgc2xpZGUuZWxlbWVudHMgPyBzbGlkZS5lbGVtZW50cyA6IFtdOwogICAgICAgIGxldCB0b3RhbENoYXJzID0gMDsKICAgICAgICBsZXQgbGlzdEl0ZW1Db3VudCA9IDA7CiAgICAgICAgbGV0IGNoYXJ0Q291bnQgPSAwOwoKICAgICAgICBlbGVtZW50cy5mb3JFYWNoKChlbGVtZW50KSA9PiB7CiAgICAgICAgICBpZiAoZWxlbWVudC50eXBlID09PSAidGV4dCIgfHwgZWxlbWVudC50eXBlID09PSAicXVvdGUiKSB7CiAgICAgICAgICAgIHRvdGFsQ2hhcnMgKz0gKGVsZW1lbnQuY29udGVudCB8fCAiIikubGVuZ3RoOwogICAgICAgICAgfQogICAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gImxpc3QiKSB7CiAgICAgICAgICAgIGNvbnN0IGl0ZW1zID0gZWxlbWVudC5jb250ZW50IHx8IFtdOwogICAgICAgICAgICBsaXN0SXRlbUNvdW50ICs9IGl0ZW1zLmxlbmd0aDsKICAgICAgICAgICAgdG90YWxDaGFycyArPSBpdGVtcy5qb2luKCIiKS5sZW5ndGg7CiAgICAgICAgICB9CiAgICAgICAgICBpZiAoZWxlbWVudC50eXBlID09PSAiY2hhcnQiKSBjaGFydENvdW50ICs9IDE7CiAgICAgICAgfSk7CgogICAgICAgIGlmICgKICAgICAgICAgIHRvdGFsQ2hhcnMgPiAzNDAgfHwKICAgICAgICAgIGxpc3RJdGVtQ291bnQgPiA3IHx8CiAgICAgICAgICAoY2hhcnRDb3VudCA+PSAyICYmIHRvdGFsQ2hhcnMgPiAxODApCiAgICAgICAgKSB7CiAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICBrZXk6ICJleHRyZW1lIiwKICAgICAgICAgICAgdGl0bGU6IDQxLAogICAgICAgICAgICBsZWFkOiAyOCwKICAgICAgICAgICAgYm9keTogMjUsCiAgICAgICAgICAgIGxpc3Q6IDI1LAogICAgICAgICAgICBxdW90ZTogMzAsCiAgICAgICAgICAgIHRhYmxlOiAyMCwKICAgICAgICAgICAgY2hhcnRNaW46IDMwMCwKICAgICAgICAgICAgZ2FwOiAzMiwKICAgICAgICAgICAgcm93UGFkOiAxMywKICAgICAgICAgIH07CiAgICAgICAgfQogICAgICAgIGlmICh0b3RhbENoYXJzID4gMjMwIHx8IGxpc3RJdGVtQ291bnQgPiA1IHx8IGVsZW1lbnRzLmxlbmd0aCA+PSA0KSB7CiAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICBrZXk6ICJoZWF2eSIsCiAgICAgICAgICAgIHRpdGxlOiA0NiwKICAgICAgICAgICAgbGVhZDogMzEsCiAgICAgICAgICAgIGJvZHk6IDI3LAogICAgICAgICAgICBsaXN0OiAyNywKICAgICAgICAgICAgcXVvdGU6IDMyLAogICAgICAgICAgICB0YWJsZTogMjEsCiAgICAgICAgICAgIGNoYXJ0TWluOiAzMjAsCiAgICAgICAgICAgIGdhcDogMzgsCiAgICAgICAgICAgIHJvd1BhZDogMTYsCiAgICAgICAgICB9OwogICAgICAgIH0KICAgICAgICBpZiAodG90YWxDaGFycyA+IDEzMCB8fCBsaXN0SXRlbUNvdW50ID4gMyB8fCBlbGVtZW50cy5sZW5ndGggPj0gMykgewogICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAga2V5OiAibWVkaXVtIiwKICAgICAgICAgICAgdGl0bGU6IDUwLAogICAgICAgICAgICBsZWFkOiAzNCwKICAgICAgICAgICAgYm9keTogMjksCiAgICAgICAgICAgIGxpc3Q6IDI5LAogICAgICAgICAgICBxdW90ZTogMzYsCiAgICAgICAgICAgIHRhYmxlOiAyMiwKICAgICAgICAgICAgY2hhcnRNaW46IDM2MCwKICAgICAgICAgICAgZ2FwOiA0NCwKICAgICAgICAgICAgcm93UGFkOiAyMCwKICAgICAgICAgIH07CiAgICAgICAgfQogICAgICAgIHJldHVybiB7CiAgICAgICAgICBrZXk6ICJsaWdodCIsCiAgICAgICAgICB0aXRsZTogNTYsCiAgICAgICAgICBsZWFkOiAzNiwKICAgICAgICAgIGJvZHk6IDMwLAogICAgICAgICAgbGlzdDogMzAsCiAgICAgICAgICBxdW90ZTogNDIsCiAgICAgICAgICB0YWJsZTogMjQsCiAgICAgICAgICBjaGFydE1pbjogNDIwLAogICAgICAgICAgZ2FwOiA1MiwKICAgICAgICAgIHJvd1BhZDogMjQsCiAgICAgICAgfTsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDlubvnga/niYfmnoTlu7rlhaXlj6MKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2QnVpbGRTbGlkZShsaWIsIHNsaWRlLCBvcHRpb25zKSB7CiAgICAgICAgY29uc3Qgb3B0cyA9IG9wdGlvbnMgfHwge307CiAgICAgICAgY29uc3Qgd2lkdGggPSBvcHRzLndpZHRoIHx8IHRoaXMuUFZfVzsKICAgICAgICBjb25zdCBoZWlnaHQgPSBvcHRzLmhlaWdodCB8fCB0aGlzLlBWX0g7CiAgICAgICAgY29uc3QgY3R4ID0gewogICAgICAgICAgbGliLAogICAgICAgICAgc2xpZGUsCiAgICAgICAgICBtb2RlOiBvcHRzLm1vZGUgPT09ICJ0aHVtYiIgPyAidGh1bWIiIDogInN0YWdlIiwKICAgICAgICAgIGFuaW1hdGU6IG9wdHMuYW5pbWF0ZSAhPT0gZmFsc2UgJiYgb3B0cy5tb2RlICE9PSAidGh1bWIiLAogICAgICAgICAgazogMSwKICAgICAgICAgIHdpZHRoLAogICAgICAgICAgaGVpZ2h0LAogICAgICAgICAgaW5kZXg6IHRoaXMucHZOdW0ob3B0cy5pbmRleCksCiAgICAgICAgICB0b3RhbDogTWF0aC5tYXgoMSwgdGhpcy5wdk51bShvcHRzLnRvdGFsKSB8fCAxKSwKICAgICAgICAgIGRlY2tUaXRsZTogb3B0cy5kZWNrVGl0bGUgfHwgIiIsCiAgICAgICAgICBwcmVzZW50ZXI6IG9wdHMucHJlc2VudGVyIHx8ICIiLAogICAgICAgICAgZGVuc2l0eTogdGhpcy5wdkRlbnNpdHkoc2xpZGUpLAogICAgICAgICAgYW5pbWF0ZWQ6IFtdLAogICAgICAgICAgY2hhcnRBcmVhczogW10sCiAgICAgICAgfTsKCiAgICAgICAgY29uc3Qgcm9vdCA9IG5ldyBsaWIuR3JvdXAoewogICAgICAgICAgeDogMCwKICAgICAgICAgIHk6IDAsCiAgICAgICAgICB3aWR0aDogdGhpcy5QVl9XLAogICAgICAgICAgaGVpZ2h0OiB0aGlzLlBWX0gsCiAgICAgICAgICBzY2FsZVg6IHdpZHRoIC8gdGhpcy5QVl9XLAogICAgICAgICAgc2NhbGVZOiBoZWlnaHQgLyB0aGlzLlBWX0gsCiAgICAgICAgICBjbGlwOiB0cnVlLAogICAgICAgIH0pOwoKICAgICAgICBpZiAoIXNsaWRlKSB7CiAgICAgICAgICByZXR1cm4geyBub2RlOiByb290LCBhbmltYXRlZDogW10sIGNoYXJ0QXJlYXM6IFtdIH07CiAgICAgICAgfQoKICAgICAgICBpZiAoc2xpZGUudHlwZSA9PT0gInRpdGxlIikgewogICAgICAgICAgdGhpcy5wdkRyYXdDb3ZlcihjdHgsIHJvb3QpOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICB0aGlzLnB2RHJhd0NvbnRlbnQoY3R4LCByb290KTsKICAgICAgICB9CgogICAgICAgIHJldHVybiB7CiAgICAgICAgICBub2RlOiByb290LAogICAgICAgICAgYW5pbWF0ZWQ6IGN0eC5hbmltYXRlZCwKICAgICAgICAgIGNoYXJ0QXJlYXM6IGN0eC5jaGFydEFyZWFzLAogICAgICAgIH07CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5bCB6Z2iCiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdkRyYXdDb3ZlcihjdHgsIHJvb3QpIHsKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIHJvb3QsIDAsIDAsIHRoaXMuUFZfVywgdGhpcy5QVl9ILCB7CiAgICAgICAgICB0eXBlOiAibGluZWFyIiwKICAgICAgICAgIGZyb206IHsgeDogMCwgeTogMCwgdHlwZTogInBlcmNlbnQiIH0sCiAgICAgICAgICB0bzogeyB4OiAxLCB5OiAxLCB0eXBlOiAicGVyY2VudCIgfSwKICAgICAgICAgIHN0b3BzOiBbCiAgICAgICAgICAgIHsgb2Zmc2V0OiAwLCBjb2xvcjogIiMwZDEyMWIiIH0sCiAgICAgICAgICAgIHsgb2Zmc2V0OiAwLjU4LCBjb2xvcjogdGhpcy5QVl9DLmRhcmsgfSwKICAgICAgICAgICAgeyBvZmZzZXQ6IDEsIGNvbG9yOiAiIzA4MGIxMCIgfSwKICAgICAgICAgIF0sCiAgICAgICAgfSk7CgogICAgICAgIC8vIOWPs+S+p+i9tOe6v+a4kOmakAogICAgICAgIHRoaXMucHZSZWN0KGN0eCwgcm9vdCwgMTE5MCwgMCwgNzMwLCB0aGlzLlBWX0gsIHsKICAgICAgICAgIHR5cGU6ICJsaW5lYXIiLAogICAgICAgICAgZnJvbTogImxlZnQiLAogICAgICAgICAgdG86ICJyaWdodCIsCiAgICAgICAgICBzdG9wczogWwogICAgICAgICAgICB7IG9mZnNldDogMCwgY29sb3I6ICJyZ2JhKDExLCA5MiwgMjU1LCAwKSIgfSwKICAgICAgICAgICAgeyBvZmZzZXQ6IDEsIGNvbG9yOiAicmdiYSgxMSwgOTIsIDI1NSwgMC4wMzUpIiB9LAogICAgICAgICAgXSwKICAgICAgICB9KTsKCiAgICAgICAgLy8g5Y+z5LiK6JOd6Imy6L6J5YWJCiAgICAgICAgdGhpcy5wdkVsbGlwc2UoY3R4LCByb290LCAxMTUwLCAtMzYwLCA5MDAsIDkwMCwgewogICAgICAgICAgdHlwZTogInJhZGlhbCIsCiAgICAgICAgICBmcm9tOiAiY2VudGVyIiwKICAgICAgICAgIHRvOiAiYm90dG9tIiwKICAgICAgICAgIHN0b3BzOiBbCiAgICAgICAgICAgIHsgb2Zmc2V0OiAwLCBjb2xvcjogInJnYmEoMTEsIDkyLCAyNTUsIDAuMzQpIiB9LAogICAgICAgICAgICB7IG9mZnNldDogMC4zOCwgY29sb3I6ICJyZ2JhKDExLCA5MiwgMjU1LCAwLjA4KSIgfSwKICAgICAgICAgICAgeyBvZmZzZXQ6IDEsIGNvbG9yOiAicmdiYSgxMSwgOTIsIDI1NSwgMCkiIH0sCiAgICAgICAgICBdLAogICAgICAgIH0pOwoKICAgICAgICAvLyDmnoHnu4bnvZHmoLwKICAgICAgICBsZXQgZ3JpZCA9ICIiOwogICAgICAgIGZvciAobGV0IHggPSAwOyB4IDw9IHRoaXMuUFZfVzsgeCArPSA4OCkgewogICAgICAgICAgZ3JpZCArPSAiTSAiICsgeCAqIGN0eC5rICsgIiAwIEwgIiArIHggKiBjdHguayArICIgIiArIHRoaXMuUFZfSCAqIGN0eC5rICsgIiAiOwogICAgICAgIH0KICAgICAgICBmb3IgKGxldCB5ID0gMDsgeSA8PSB0aGlzLlBWX0g7IHkgKz0gODgpIHsKICAgICAgICAgIGdyaWQgKz0gIk0gMCAiICsgeSAqIGN0eC5rICsgIiBMICIgKyB0aGlzLlBWX1cgKiBjdHguayArICIgIiArIHkgKiBjdHguayArICIgIjsKICAgICAgICB9CiAgICAgICAgdGhpcy5wdlBhdGgoY3R4LCByb290LCBncmlkLCAibm9uZSIsIHsKICAgICAgICAgIHN0cm9rZTogInJnYmEoMjU1LCAyNTUsIDI1NSwgMC4wMzUpIiwKICAgICAgICAgIHN0cm9rZVdpZHRoOiAxLAogICAgICAgIH0pOwoKICAgICAgICAvLyDoo4XppbDlnIbnjq8KICAgICAgICB0aGlzLnB2RWxsaXBzZShjdHgsIHJvb3QsIDE0NzAsIDE4MCwgNzIwLCA3MjAsICJub25lIiwgewogICAgICAgICAgc3Ryb2tlOiAicmdiYSgxMzMsIDE2OCwgMjU1LCAwLjE0KSIsCiAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICB9KTsKICAgICAgICB0aGlzLnB2RWxsaXBzZShjdHgsIHJvb3QsIDEzNDAsIDUwLCA5ODAsIDk4MCwgIm5vbmUiLCB7CiAgICAgICAgICBzdHJva2U6ICJyZ2JhKDEzMywgMTY4LCAyNTUsIDAuMDc1KSIsCiAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICB9KTsKCiAgICAgICAgY29uc3QgcGFkWCA9IDk2OwogICAgICAgIGNvbnN0IGlubmVyVyA9IHRoaXMuUFZfVyAtIHBhZFggKiAyOwoKICAgICAgICAvLyDpobbpg6jmoIfor4YKICAgICAgICBjb25zdCB0b3BHcm91cCA9IHRoaXMucHZHcm91cChjdHgsIHJvb3QsIHBhZFgsIDYyLCBpbm5lclcsIDMwKTsKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIHRvcEdyb3VwLCAwLCA0LCA4LCA4LCB0aGlzLlBWX0MuYnJhbmQsIHsgY29ybmVyUmFkaXVzOiAxIH0pOwogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgdG9wR3JvdXAsIHsKICAgICAgICAgIHg6IDE4LAogICAgICAgICAgeTogLTEsCiAgICAgICAgICB3aWR0aDogMjQwLAogICAgICAgICAgdGV4dDogIlBSRVNFTlRBVElPTiIsCiAgICAgICAgICBmb250U2l6ZTogMTEsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxMywKICAgICAgICAgIG1vbm86IHRydWUsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAxLjc2LAogICAgICAgICAgZmlsbDogIiM3ZDg5YTAiLAogICAgICAgIH0pOwogICAgICAgIGNvbnN0IGRlY2tMaW5lcyA9IHRoaXMucHZXcmFwVGV4dCgKICAgICAgICAgIFN0cmluZyhjdHguZGVja1RpdGxlIHx8ICIiKS50b1VwcGVyQ2FzZSgpLAogICAgICAgICAgOTAwLAogICAgICAgICAgdGhpcy5wdkZvbnQoNTAwLCAxMSwgdHJ1ZSksCiAgICAgICAgICAxLjc2LAogICAgICAgICAgMQogICAgICAgICk7CiAgICAgICAgdGhpcy5wdlRleHQoY3R4LCB0b3BHcm91cCwgewogICAgICAgICAgeDogaW5uZXJXIC0gOTAwLAogICAgICAgICAgeTogLTEsCiAgICAgICAgICB3aWR0aDogOTAwLAogICAgICAgICAgbGluZXM6IGRlY2tMaW5lcywKICAgICAgICAgIGZvbnRTaXplOiAxMSwKICAgICAgICAgIGxpbmVIZWlnaHQ6IDEzLAogICAgICAgICAgbW9ubzogdHJ1ZSwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IDEuNzYsCiAgICAgICAgICBmaWxsOiAiIzZmN2M5MSIsCiAgICAgICAgICBhbGlnbjogInJpZ2h0IiwKICAgICAgICB9KTsKICAgICAgICB0aGlzLnB2QW5pbWF0ZShjdHgsIHRvcEdyb3VwLCAwLCAiZmFkZSIpOwoKICAgICAgICAvLyDkuLvmoIfpopjljLrvvIjlnoLnm7TlsYXkuK3vvIkKICAgICAgICBjb25zdCB0ZXh0cyA9IHRoaXMucHZDb3ZlclRleHRzKGN0eC5zbGlkZSk7CiAgICAgICAgY29uc3Qgc3VidGl0bGVCbG9ja3MgPSB0ZXh0cy5tYXAoKGVsZW1lbnQpID0+IHsKICAgICAgICAgIGNvbnN0IGxpbmVzID0gdGhpcy5wdldyYXBUZXh0KAogICAgICAgICAgICBlbGVtZW50LmNvbnRlbnQgfHwgIiIsCiAgICAgICAgICAgIDc4MCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNDAwLCAyOSksCiAgICAgICAgICAgIC0wLjM1LAogICAgICAgICAgICA0CiAgICAgICAgICApOwogICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgbGluZXMsCiAgICAgICAgICAgIGhlaWdodDogTWF0aC5tYXgoMSwgbGluZXMubGVuZ3RoKSAqIDQ1LjgyLAogICAgICAgICAgfTsKICAgICAgICB9KTsKICAgICAgICBjb25zdCBzdWJ0aXRsZUhlaWdodCA9IHN1YnRpdGxlQmxvY2tzLnJlZHVjZSgKICAgICAgICAgIChzdW0sIGJsb2NrKSA9PiBzdW0gKyBibG9jay5oZWlnaHQsCiAgICAgICAgICAwCiAgICAgICAgKTsKICAgICAgICBjb25zdCBzdWJ0aXRsZUdhcHMgPSBNYXRoLm1heCgwLCBzdWJ0aXRsZUJsb2Nrcy5sZW5ndGggLSAxKSAqIDEwOwoKICAgICAgICBsZXQgdGl0bGVTaXplID0gOTY7CiAgICAgICAgbGV0IHRpdGxlTGluZSA9IDk5Ljg0OwogICAgICAgIGxldCB0aXRsZUxpbmVzID0gW107CiAgICAgICAgZm9yIChsZXQgYXR0ZW1wdCA9IDA7IGF0dGVtcHQgPCA0OyBhdHRlbXB0ICs9IDEpIHsKICAgICAgICAgIHRpdGxlTGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICAgIGN0eC5zbGlkZS50aXRsZSB8fCAiIiwKICAgICAgICAgICAgMTIwMCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNjUwLCB0aXRsZVNpemUpLAogICAgICAgICAgICAtMC4wNTIgKiB0aXRsZVNpemUsCiAgICAgICAgICAgIDMKICAgICAgICAgICk7CiAgICAgICAgICB0aXRsZUxpbmUgPSB0aXRsZVNpemUgKiAxLjA0OwogICAgICAgICAgaWYgKHRpdGxlTGluZXMubGVuZ3RoIDw9IDIpIGJyZWFrOwogICAgICAgICAgdGl0bGVTaXplIC09IDg7CiAgICAgICAgfQogICAgICAgIGlmICh0aXRsZUxpbmVzLmxlbmd0aCA+IDMpIHRpdGxlTGluZXMgPSB0aXRsZUxpbmVzLnNsaWNlKDAsIDMpOwogICAgICAgIGNvbnN0IHRpdGxlSGVpZ2h0ID0gdGl0bGVMaW5lcy5sZW5ndGggKiB0aXRsZUxpbmU7CgogICAgICAgIGNvbnN0IGtpY2tlckhlaWdodCA9IDEzOwogICAgICAgIGNvbnN0IG1haW5IZWlnaHQgPQogICAgICAgICAga2lja2VySGVpZ2h0ICsKICAgICAgICAgIDMwICsKICAgICAgICAgIHRpdGxlSGVpZ2h0ICsKICAgICAgICAgIChzdWJ0aXRsZUJsb2Nrcy5sZW5ndGggPyAzMiArIHN1YnRpdGxlSGVpZ2h0ICsgc3VidGl0bGVHYXBzIDogMCk7CiAgICAgICAgbGV0IG1haW5Ub3AgPSAxMTIgKyAoOTcyIC0gMTEyIC0gbWFpbkhlaWdodCkgLyAyOwogICAgICAgIGlmIChtYWluVG9wIDwgMTEyKSBtYWluVG9wID0gMTEyOwoKICAgICAgICAvLyDnnInpopgKICAgICAgICBjb25zdCBraWNrZXJHcm91cCA9IHRoaXMucHZHcm91cChjdHgsIHJvb3QsIHBhZFgsIG1haW5Ub3AsIDkwMCwga2lja2VySGVpZ2h0KTsKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIGtpY2tlckdyb3VwLCAwLCA2LCA1NCwgMSwgdGhpcy5QVl9DLmJyYW5kKTsKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGtpY2tlckdyb3VwLCB7CiAgICAgICAgICB4OiA2OSwKICAgICAgICAgIHk6IC0xLAogICAgICAgICAgd2lkdGg6IDI2MCwKICAgICAgICAgIHRleHQ6ICJFWEVDVVRJVkUgQlJJRUYiLAogICAgICAgICAgZm9udFNpemU6IDEzLAogICAgICAgICAgbGluZUhlaWdodDogMTUsCiAgICAgICAgICBtb25vOiB0cnVlLAogICAgICAgICAgbGV0dGVyU3BhY2luZzogMi4yMSwKICAgICAgICAgIGZpbGw6ICIjOGQ5Y2I0IiwKICAgICAgICB9KTsKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGtpY2tlckdyb3VwLCB7CiAgICAgICAgICB4OiAyNzgsCiAgICAgICAgICB5OiAtMSwKICAgICAgICAgIHdpZHRoOiAxMjAsCiAgICAgICAgICB0ZXh0OiAiLyAiICsgU3RyaW5nKGN0eC5pbmRleCArIDEpLnBhZFN0YXJ0KDIsICIwIiksCiAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxNSwKICAgICAgICAgIG1vbm86IHRydWUsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAyLjIxLAogICAgICAgICAgZmlsbDogIiM1ZjZkODIiLAogICAgICAgIH0pOwogICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwga2lja2VyR3JvdXAsIDcwLCAiZG93biIpOwoKICAgICAgICAvLyDmoIfpopgKICAgICAgICBjb25zdCB0aXRsZUdyb3VwID0gdGhpcy5wdkdyb3VwKAogICAgICAgICAgY3R4LAogICAgICAgICAgcm9vdCwKICAgICAgICAgIHBhZFgsCiAgICAgICAgICBtYWluVG9wICsga2lja2VySGVpZ2h0ICsgMzAsCiAgICAgICAgICAxMjAwLAogICAgICAgICAgdGl0bGVIZWlnaHQKICAgICAgICApOwogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgdGl0bGVHcm91cCwgewogICAgICAgICAgeDogMCwKICAgICAgICAgIHk6IDAsCiAgICAgICAgICB3aWR0aDogMTIwMCwKICAgICAgICAgIGxpbmVzOiB0aXRsZUxpbmVzLAogICAgICAgICAgZm9udFNpemU6IHRpdGxlU2l6ZSwKICAgICAgICAgIGxpbmVIZWlnaHQ6IHRpdGxlTGluZSwKICAgICAgICAgIHdlaWdodDogNjUwLAogICAgICAgICAgbGV0dGVyU3BhY2luZzogLTAuMDUyICogdGl0bGVTaXplLAogICAgICAgICAgZmlsbDogIiNmOGZhZmMiLAogICAgICAgIH0pOwogICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwgdGl0bGVHcm91cCwgMTUwLCAidXAiKTsKCiAgICAgICAgLy8g5Ymv5qCH6aKYCiAgICAgICAgbGV0IHN1YnRpdGxlWSA9IG1haW5Ub3AgKyBraWNrZXJIZWlnaHQgKyAzMCArIHRpdGxlSGVpZ2h0ICsgMzI7CiAgICAgICAgc3VidGl0bGVCbG9ja3MuZm9yRWFjaCgoYmxvY2ssIGluZGV4KSA9PiB7CiAgICAgICAgICBjb25zdCBncm91cCA9IHRoaXMucHZHcm91cChjdHgsIHJvb3QsIHBhZFgsIHN1YnRpdGxlWSwgNzgwLCBibG9jay5oZWlnaHQpOwogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBncm91cCwgewogICAgICAgICAgICB4OiAwLAogICAgICAgICAgICB5OiAwLAogICAgICAgICAgICB3aWR0aDogNzgwLAogICAgICAgICAgICBsaW5lczogYmxvY2subGluZXMsCiAgICAgICAgICAgIGZvbnRTaXplOiAyOSwKICAgICAgICAgICAgbGluZUhlaWdodDogNDUuODIsCiAgICAgICAgICAgIGxldHRlclNwYWNpbmc6IC0wLjM1LAogICAgICAgICAgICBmaWxsOiAiI2E2YjJjNCIsCiAgICAgICAgICB9KTsKICAgICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwgZ3JvdXAsIDI2MCArIGluZGV4ICogMTAwLCAidXAiKTsKICAgICAgICAgIHN1YnRpdGxlWSArPSBibG9jay5oZWlnaHQgKyAxMDsKICAgICAgICB9KTsKCiAgICAgICAgLy8g5bqV6YOoCiAgICAgICAgY29uc3QgZm9vdFkgPSA5NzI7CiAgICAgICAgdGhpcy5wdkxpbmUoY3R4LCByb290LCBwYWRYLCBmb290WSwgcGFkWCArIGlubmVyVywgZm9vdFksICJyZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMTIpIiwgMSk7CiAgICAgICAgY29uc3QgZm9vdEdyb3VwID0gdGhpcy5wdkdyb3VwKGN0eCwgcm9vdCwgcGFkWCwgZm9vdFkgKyAyNCwgaW5uZXJXLCAzMCk7CiAgICAgICAgdGhpcy5wdkVsbGlwc2UoY3R4LCBmb290R3JvdXAsIDAsIDAsIDMwLCAzMCwgInJnYmEoMjU1LCAyNTUsIDI1NSwgMC4wNykiLCB7CiAgICAgICAgICBzdHJva2U6ICJyZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMTQpIiwKICAgICAgICAgIHN0cm9rZVdpZHRoOiAxLAogICAgICAgIH0pOwogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZm9vdEdyb3VwLCB7CiAgICAgICAgICB4OiAwLAogICAgICAgICAgeTogMCwKICAgICAgICAgIHdpZHRoOiAzMCwKICAgICAgICAgIGhlaWdodDogMzAsCiAgICAgICAgICB0ZXh0OiBTdHJpbmcoY3R4LnByZXNlbnRlciB8fCAiwrciKS50cmltKCkuY2hhckF0KDApIHx8ICLCtyIsCiAgICAgICAgICBmb250U2l6ZTogMTEsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxMSwKICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgIHZhbGlnbjogIm1pZGRsZSIsCiAgICAgICAgICBmaWxsOiAiI2RiZTRmMyIsCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBmb290R3JvdXAsIHsKICAgICAgICAgIHg6IDQyLAogICAgICAgICAgeTogMywKICAgICAgICAgIHdpZHRoOiA0MjAsCiAgICAgICAgICB0ZXh0OiBjdHgucHJlc2VudGVyIHx8ICIiLAogICAgICAgICAgZm9udFNpemU6IDEyLAogICAgICAgICAgbGluZUhlaWdodDogMTYsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAwLjYsCiAgICAgICAgICBmaWxsOiAiIzdkODlhMCIsCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBmb290R3JvdXAsIHsKICAgICAgICAgIHg6IGlubmVyVyAtIDI2MCwKICAgICAgICAgIHk6IDQsCiAgICAgICAgICB3aWR0aDogMjYwLAogICAgICAgICAgdGV4dDoKICAgICAgICAgICAgU3RyaW5nKGN0eC5pbmRleCArIDEpLnBhZFN0YXJ0KDIsICIwIikgKwogICAgICAgICAgICAiIC8gIiArCiAgICAgICAgICAgIFN0cmluZyhjdHgudG90YWwpLnBhZFN0YXJ0KDIsICIwIiksCiAgICAgICAgICBmb250U2l6ZTogMTEsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxNCwKICAgICAgICAgIG1vbm86IHRydWUsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAwLjg4LAogICAgICAgICAgYWxpZ246ICJyaWdodCIsCiAgICAgICAgICBmaWxsOiAiIzZmN2M5MSIsCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdkFuaW1hdGUoY3R4LCBmb290R3JvdXAsIDQ4MCwgImZhZGUiKTsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDlhoXlrrnpobXvvJrpobXnnInjgIHmraPmlofliIbmoI/jgIHpobXohJoKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2RHJhd0NvbnRlbnQoY3R4LCByb290KSB7CiAgICAgICAgY29uc3QgZCA9IGN0eC5kZW5zaXR5OwoKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIHJvb3QsIDAsIDAsIHRoaXMuUFZfVywgdGhpcy5QVl9ILCB0aGlzLlBWX0MucGFwZXIpOwoKICAgICAgICBjb25zdCBwYWRYID0gOTY7CiAgICAgICAgY29uc3QgaW5uZXJXID0gdGhpcy5QVl9XIC0gcGFkWCAqIDI7CgogICAgICAgIC8vIOmhteecieeciemimAogICAgICAgIGNvbnN0IGV5ZWJyb3dHcm91cCA9IHRoaXMucHZHcm91cChjdHgsIHJvb3QsIHBhZFgsIDU4LCBpbm5lclcsIDEzKTsKICAgICAgICBjb25zdCBpbmRleFRleHQgPSBTdHJpbmcoY3R4LmluZGV4ICsgMSkucGFkU3RhcnQoMiwgIjAiKTsKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGV5ZWJyb3dHcm91cCwgewogICAgICAgICAgeDogMCwKICAgICAgICAgIHk6IC0xLAogICAgICAgICAgd2lkdGg6IDQwLAogICAgICAgICAgdGV4dDogaW5kZXhUZXh0LAogICAgICAgICAgZm9udFNpemU6IDEzLAogICAgICAgICAgbGluZUhlaWdodDogMTUsCiAgICAgICAgICBtb25vOiB0cnVlLAogICAgICAgICAgd2VpZ2h0OiA2MDAsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAxLjgyLAogICAgICAgICAgZmlsbDogdGhpcy5QVl9DLmJyYW5kLAogICAgICAgIH0pOwogICAgICAgIGNvbnN0IGluZGV4V2lkdGggPSA0MDsKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIGV5ZWJyb3dHcm91cCwgaW5kZXhXaWR0aCArIDE0LCA2LCAyOCwgMSwgdGhpcy5QVl9DLmxpbmVTdHJvbmcpOwogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZXllYnJvd0dyb3VwLCB7CiAgICAgICAgICB4OiBpbmRleFdpZHRoICsgMTQgKyAyOCArIDE0LAogICAgICAgICAgeTogLTEsCiAgICAgICAgICB3aWR0aDogNDIwLAogICAgICAgICAgdGV4dDogU3RyaW5nKHRoaXMucHZUeXBlTGFiZWwoY3R4LnNsaWRlKSkudG9VcHBlckNhc2UoKSwKICAgICAgICAgIGZvbnRTaXplOiAxMywKICAgICAgICAgIGxpbmVIZWlnaHQ6IDE1LAogICAgICAgICAgbW9ubzogdHJ1ZSwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IDEuODIsCiAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdkFuaW1hdGUoY3R4LCBleWVicm93R3JvdXAsIDAsICJmYWRlIik7CgogICAgICAgIC8vIOagh+mimAogICAgICAgIGxldCB0aXRsZVNpemUgPSBkLnRpdGxlOwogICAgICAgIGxldCB0aXRsZUxpbmVzID0gW107CiAgICAgICAgbGV0IHRpdGxlTGluZUhlaWdodCA9IHRpdGxlU2l6ZSAqIDEuMTY7CiAgICAgICAgZm9yIChsZXQgYXR0ZW1wdCA9IDA7IGF0dGVtcHQgPCA0OyBhdHRlbXB0ICs9IDEpIHsKICAgICAgICAgIHRpdGxlTGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICAgIGN0eC5zbGlkZS50aXRsZSB8fCAiIiwKICAgICAgICAgICAgMTUwMCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNjUwLCB0aXRsZVNpemUpLAogICAgICAgICAgICAtMC4wMzggKiB0aXRsZVNpemUsCiAgICAgICAgICAgIDIKICAgICAgICAgICk7CiAgICAgICAgICB0aXRsZUxpbmVIZWlnaHQgPSB0aXRsZVNpemUgKiAxLjE2OwogICAgICAgICAgaWYgKHRpdGxlTGluZXMubGVuZ3RoIDw9IDIpIGJyZWFrOwogICAgICAgICAgdGl0bGVTaXplIC09IDM7CiAgICAgICAgfQogICAgICAgIGlmICh0aXRsZUxpbmVzLmxlbmd0aCA+IDIpIHRpdGxlTGluZXMgPSB0aXRsZUxpbmVzLnNsaWNlKDAsIDIpOwogICAgICAgIGNvbnN0IHRpdGxlSGVpZ2h0ID0gTWF0aC5tYXgoMSwgdGl0bGVMaW5lcy5sZW5ndGgpICogdGl0bGVMaW5lSGVpZ2h0OwoKICAgICAgICBjb25zdCB0aXRsZUdyb3VwID0gdGhpcy5wdkdyb3VwKGN0eCwgcm9vdCwgcGFkWCwgODksIDE1MDAsIHRpdGxlSGVpZ2h0KTsKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHRpdGxlR3JvdXAsIHsKICAgICAgICAgIHg6IDAsCiAgICAgICAgICB5OiAwLAogICAgICAgICAgd2lkdGg6IDE1MDAsCiAgICAgICAgICBsaW5lczogdGl0bGVMaW5lcywKICAgICAgICAgIGZvbnRTaXplOiB0aXRsZVNpemUsCiAgICAgICAgICBsaW5lSGVpZ2h0OiB0aXRsZUxpbmVIZWlnaHQsCiAgICAgICAgICB3ZWlnaHQ6IDY1MCwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IC0wLjAzOCAqIHRpdGxlU2l6ZSwKICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy5pbmssCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdkFuaW1hdGUoY3R4LCB0aXRsZUdyb3VwLCA4MCwgInVwIik7CgogICAgICAgIGNvbnN0IGJvZHlUb3AgPSA4OSArIHRpdGxlSGVpZ2h0ICsgMzY7CiAgICAgICAgY29uc3QgYm9keUJvdHRvbSA9IHRoaXMuUFZfSCAtIDY0IC0gMzg7CiAgICAgICAgY29uc3QgYm9keSA9IHsKICAgICAgICAgIHg6IHBhZFgsCiAgICAgICAgICB5OiBib2R5VG9wLAogICAgICAgICAgd2lkdGg6IGlubmVyVywKICAgICAgICAgIGhlaWdodDogTWF0aC5tYXgoMTIwLCBib2R5Qm90dG9tIC0gYm9keVRvcCksCiAgICAgICAgfTsKCiAgICAgICAgdGhpcy5wdkRyYXdCb2R5KGN0eCwgcm9vdCwgYm9keSk7CgogICAgICAgIC8vIOmhteiEmgogICAgICAgIHRoaXMucHZMaW5lKGN0eCwgcm9vdCwgcGFkWCwgMTAxNiwgcGFkWCArIGlubmVyVywgMTAxNiwgdGhpcy5QVl9DLmxpbmUsIDEpOwogICAgICAgIGNvbnN0IGZvb3RHcm91cCA9IHRoaXMucHZHcm91cChjdHgsIHJvb3QsIHBhZFgsIDEwMzksIGlubmVyVywgMTgpOwogICAgICAgIHRoaXMucHZSZWN0KGN0eCwgZm9vdEdyb3VwLCAwLCA3LCA1LCA1LCB0aGlzLlBWX0MuYnJhbmQsIHsgY29ybmVyUmFkaXVzOiAxIH0pOwogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZm9vdEdyb3VwLCB7CiAgICAgICAgICB4OiAxNCwKICAgICAgICAgIHk6IDAsCiAgICAgICAgICB3aWR0aDogMTAwMCwKICAgICAgICAgIHRleHQ6IGN0eC5kZWNrVGl0bGUgfHwgIiIsCiAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxOCwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IDAuMzksCiAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgfSk7CiAgICAgICAgY29uc3QgY3VycmVudCA9IFN0cmluZyhjdHguaW5kZXggKyAxKS5wYWRTdGFydCgyLCAiMCIpOwogICAgICAgIGNvbnN0IHRvdGFsID0gU3RyaW5nKGN0eC50b3RhbCkucGFkU3RhcnQoMiwgIjAiKTsKICAgICAgICBjb25zdCBwYWdlRm9udCA9IHRoaXMucHZGb250KDYwMCwgMTEsIHRydWUpOwogICAgICAgIGNvbnN0IHRvdGFsRm9udCA9IHRoaXMucHZGb250KDQwMCwgMTEsIHRydWUpOwogICAgICAgIGNvbnN0IHBhZ2VDdHggPSB0aGlzLnB2TWVhc3VyZUN0eCgpOwogICAgICAgIGxldCBjdXJyZW50V2lkdGggPSAxODsKICAgICAgICBsZXQgdG90YWxXaWR0aCA9IDMyOwogICAgICAgIGlmIChwYWdlQ3R4KSB7CiAgICAgICAgICBwYWdlQ3R4LmZvbnQgPSBwYWdlRm9udDsKICAgICAgICAgIGN1cnJlbnRXaWR0aCA9IHBhZ2VDdHgubWVhc3VyZVRleHQoY3VycmVudCkud2lkdGggKyAwLjg4OwogICAgICAgICAgcGFnZUN0eC5mb250ID0gdG90YWxGb250OwogICAgICAgICAgdG90YWxXaWR0aCA9IHBhZ2VDdHgubWVhc3VyZVRleHQoIi8gIiArIHRvdGFsKS53aWR0aCArIDAuODg7CiAgICAgICAgfQogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZm9vdEdyb3VwLCB7CiAgICAgICAgICB4OiBpbm5lclcgLSBjdXJyZW50V2lkdGggLSA0IC0gdG90YWxXaWR0aCwKICAgICAgICAgIHk6IDAsCiAgICAgICAgICB3aWR0aDogY3VycmVudFdpZHRoLAogICAgICAgICAgdGV4dDogY3VycmVudCwKICAgICAgICAgIGZvbnRTaXplOiAxMSwKICAgICAgICAgIGxpbmVIZWlnaHQ6IDE0LAogICAgICAgICAgbW9ubzogdHJ1ZSwKICAgICAgICAgIHdlaWdodDogNjAwLAogICAgICAgICAgbGV0dGVyU3BhY2luZzogMC44OCwKICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy5pbmssCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBmb290R3JvdXAsIHsKICAgICAgICAgIHg6IGlubmVyVyAtIHRvdGFsV2lkdGgsCiAgICAgICAgICB5OiAwLAogICAgICAgICAgd2lkdGg6IHRvdGFsV2lkdGgsCiAgICAgICAgICB0ZXh0OiAiLyAiICsgdG90YWwsCiAgICAgICAgICBmb250U2l6ZTogMTEsCiAgICAgICAgICBsaW5lSGVpZ2h0OiAxNCwKICAgICAgICAgIG1vbm86IHRydWUsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAwLjg4LAogICAgICAgICAgZmlsbDogdGhpcy5QVl9DLnRleHQzLAogICAgICAgIH0pOwogICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwgZm9vdEdyb3VwLCA2MjAsICJmYWRlIik7CiAgICAgIH0sCgogICAgICBwdkRyYXdCb2R5KGN0eCwgcm9vdCwgYm9keSkgewogICAgICAgIGNvbnN0IGQgPSBjdHguZGVuc2l0eTsKICAgICAgICBjb25zdCBnYXAgPSBkLmdhcDsKICAgICAgICBjb25zdCBsYXlvdXQgPSB0aGlzLnB2Qm9keUxheW91dChjdHguc2xpZGUpOwogICAgICAgIGNvbnN0IGVsZW1lbnRzID0gY3R4LnNsaWRlLmVsZW1lbnRzIHx8IFtdOwogICAgICAgIGNvbnN0IGZpbmQgPSAodHlwZSkgPT4gZWxlbWVudHMuZmlsdGVyKChlbCkgPT4gZWwudHlwZSA9PT0gdHlwZSlbMF07CgogICAgICAgIGlmICghZWxlbWVudHMubGVuZ3RoKSByZXR1cm47CgogICAgICAgIGNvbnN0IGNvbHVtbldpZHRoID0gKHNwYW4pID0+CiAgICAgICAgICBNYXRoLm1heCg4MCwgKChib2R5LndpZHRoIC0gZ2FwKSAqIHNwYW4pIC8gMTIpOwogICAgICAgIGNvbnN0IGNvbHVtblggPSAoc3BhbkJlZm9yZSkgPT4KICAgICAgICAgIGJvZHkueCArIGNvbHVtbldpZHRoKHNwYW5CZWZvcmUpICsgZ2FwOwoKICAgICAgICBpZiAobGF5b3V0ID09PSAic29sby1jaGFydCIpIHsKICAgICAgICAgIGNvbnN0IGNoYXJ0ID0gZmluZCgiY2hhcnQiKTsKICAgICAgICAgIGlmIChjaGFydCkgewogICAgICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0Q2FyZChjdHgsIHJvb3QsIGNoYXJ0LCBib2R5LCB7IGRlbGF5OiAxNDAgfSk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobGF5b3V0ID09PSAic29sbyIpIHsKICAgICAgICAgIGNvbnN0IGVsZW1lbnQgPSBlbGVtZW50c1swXTsKICAgICAgICAgIGlmICghZWxlbWVudCkgcmV0dXJuOwogICAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gInRleHQiKSB7CiAgICAgICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5taW4oYm9keS53aWR0aCwgMTE4MCk7CiAgICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2VGV4dE1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCwgInN0YXRlbWVudCIpOwogICAgICAgICAgICB0aGlzLnB2RHJhd1RleHRCbG9jaygKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICBlbGVtZW50LAogICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgIHg6IGJvZHkueCwKICAgICAgICAgICAgICAgIHk6IGJvZHkueSArIChib2R5LmhlaWdodCAtIG1ldHJpY3MuaGVpZ2h0KSAvIDIsCiAgICAgICAgICAgICAgICB3aWR0aCwKICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICJzdGF0ZW1lbnQiLAogICAgICAgICAgICAgIDE0MAogICAgICAgICAgICApOwogICAgICAgICAgfSBlbHNlIGlmIChlbGVtZW50LnR5cGUgPT09ICJxdW90ZSIpIHsKICAgICAgICAgICAgY29uc3Qgd2lkdGggPSBNYXRoLm1pbihib2R5LndpZHRoLCAxMzIwKTsKICAgICAgICAgICAgY29uc3QgbWV0cmljcyA9IHRoaXMucHZRdW90ZU1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCk7CiAgICAgICAgICAgIHRoaXMucHZEcmF3UXVvdGVCbG9jaygKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICBlbGVtZW50LAogICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgIHg6IGJvZHkueCwKICAgICAgICAgICAgICAgIHk6IGJvZHkueSArIChib2R5LmhlaWdodCAtIG1ldHJpY3MuaGVpZ2h0KSAvIDIsCiAgICAgICAgICAgICAgICB3aWR0aCwKICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgIDE0MAogICAgICAgICAgICApOwogICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgY29uc3QgbWV0cmljcyA9IHRoaXMucHZNZWFzdXJlRWxlbWVudChjdHgsIGVsZW1lbnQsIGJvZHkud2lkdGgsIGJvZHkuaGVpZ2h0KTsKICAgICAgICAgICAgdGhpcy5wdkRyYXdFbGVtZW50KAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICByb290LAogICAgICAgICAgICAgIGVsZW1lbnQsCiAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgeDogYm9keS54LAogICAgICAgICAgICAgICAgeTogYm9keS55ICsgKGJvZHkuaGVpZ2h0IC0gbWV0cmljcy5oZWlnaHQpIC8gMiwKICAgICAgICAgICAgICAgIHdpZHRoOiBib2R5LndpZHRoLAogICAgICAgICAgICAgICAgaGVpZ2h0OiBtZXRyaWNzLmhlaWdodCwKICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgIDAsCiAgICAgICAgICAgICAgMTQwCiAgICAgICAgICAgICk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobGF5b3V0ID09PSAic29sby1saXN0IikgewogICAgICAgICAgY29uc3QgbGlzdCA9IGZpbmQoImxpc3QiKTsKICAgICAgICAgIGlmIChsaXN0KSB7CiAgICAgICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5taW4oYm9keS53aWR0aCwgMTE4MCk7CiAgICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2TGlzdE1ldHJpY3MoY3R4LCBsaXN0LCB3aWR0aCwgYm9keS5oZWlnaHQpOwogICAgICAgICAgICB0aGlzLnB2RHJhd0xpc3RCbG9jaygKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICBsaXN0LAogICAgICAgICAgICAgIHsgeDogYm9keS54LCB5OiBib2R5LnkgKyAoYm9keS5oZWlnaHQgLSBtZXRyaWNzLmhlaWdodCkgLyAyLCB3aWR0aCB9LAogICAgICAgICAgICAgIG1ldHJpY3MsCiAgICAgICAgICAgICAgMTQwCiAgICAgICAgICAgICk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobGF5b3V0ID09PSAic29sby1pbWFnZSIgfHwgbGF5b3V0ID09PSAic29sby10YWJsZSIpIHsKICAgICAgICAgIGNvbnN0IGVsZW1lbnQgPSBmaW5kKGxheW91dCA9PT0gInNvbG8taW1hZ2UiID8gImltYWdlIiA6ICJ0YWJsZSIpOwogICAgICAgICAgaWYgKGVsZW1lbnQpIHsKICAgICAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gImltYWdlIikgewogICAgICAgICAgICAgIHRoaXMucHZEcmF3SW1hZ2VCbG9jayhjdHgsIHJvb3QsIGVsZW1lbnQsIGJvZHksIDE0MCk7CiAgICAgICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgICAgdGhpcy5wdkRyYXdUYWJsZUJsb2NrKAogICAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICAgIGVsZW1lbnQsCiAgICAgICAgICAgICAgICBPYmplY3QuYXNzaWduKHt9LCBib2R5LCB7IGZpbGxIZWlnaHQ6IHRydWUgfSksCiAgICAgICAgICAgICAgICAxNDAKICAgICAgICAgICAgICApOwogICAgICAgICAgICB9CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobGF5b3V0ID09PSAibGVhZC1saXN0LWNoYXJ0IikgewogICAgICAgICAgY29uc3QgdGV4dCA9IGZpbmQoInRleHQiKTsKICAgICAgICAgIGNvbnN0IGxpc3QgPSBmaW5kKCJsaXN0Iik7CiAgICAgICAgICBjb25zdCBjaGFydCA9IGZpbmQoImNoYXJ0Iik7CiAgICAgICAgICBjb25zdCB0ZXh0V2lkdGggPSBNYXRoLm1pbihib2R5LndpZHRoLCAxNjIwKTsKICAgICAgICAgIGNvbnN0IHRleHRNZXRyaWNzID0gdGV4dAogICAgICAgICAgICA/IHRoaXMucHZUZXh0TWV0cmljcyhjdHgsIHRleHQsIHRleHRXaWR0aCwgImxlYWQiKQogICAgICAgICAgICA6IHsgaGVpZ2h0OiAwIH07CiAgICAgICAgICBpZiAodGV4dCkgewogICAgICAgICAgICB0aGlzLnB2RHJhd1RleHRCbG9jaygKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICB0ZXh0LAogICAgICAgICAgICAgIHsgeDogYm9keS54LCB5OiBib2R5LnksIHdpZHRoOiB0ZXh0V2lkdGggfSwKICAgICAgICAgICAgICAibGVhZCIsCiAgICAgICAgICAgICAgMTQwCiAgICAgICAgICAgICk7CiAgICAgICAgICB9CiAgICAgICAgICBjb25zdCByb3dUb3AgPSBib2R5LnkgKyB0ZXh0TWV0cmljcy5oZWlnaHQgKyBnYXA7CiAgICAgICAgICBjb25zdCByb3dIZWlnaHQgPSBNYXRoLm1heCgxNDAsIGJvZHkuaGVpZ2h0IC0gdGV4dE1ldHJpY3MuaGVpZ2h0IC0gZ2FwKTsKICAgICAgICAgIGNvbnN0IGxlZnRXaWR0aCA9IGNvbHVtbldpZHRoKDUpOwogICAgICAgICAgY29uc3QgcmlnaHRYID0gY29sdW1uWCg1KTsKICAgICAgICAgIGNvbnN0IHJpZ2h0V2lkdGggPSBib2R5LnggKyBib2R5LndpZHRoIC0gcmlnaHRYOwoKICAgICAgICAgIGlmIChsaXN0KSB7CiAgICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2TGlzdE1ldHJpY3MoY3R4LCBsaXN0LCBsZWZ0V2lkdGgsIHJvd0hlaWdodCk7CiAgICAgICAgICAgIHRoaXMucHZEcmF3TGlzdEJsb2NrKAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICByb290LAogICAgICAgICAgICAgIGxpc3QsCiAgICAgICAgICAgICAgeyB4OiBib2R5LngsIHk6IHJvd1RvcCArIDQsIHdpZHRoOiBsZWZ0V2lkdGggfSwKICAgICAgICAgICAgICBtZXRyaWNzLAogICAgICAgICAgICAgIDI5MAogICAgICAgICAgICApOwogICAgICAgICAgfQogICAgICAgICAgaWYgKGNoYXJ0KSB7CiAgICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRDYXJkKAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICByb290LAogICAgICAgICAgICAgIGNoYXJ0LAogICAgICAgICAgICAgIHsgeDogcmlnaHRYLCB5OiByb3dUb3AsIHdpZHRoOiByaWdodFdpZHRoLCBoZWlnaHQ6IHJvd0hlaWdodCB9LAogICAgICAgICAgICAgIHsgZGVsYXk6IDQ0MCB9CiAgICAgICAgICAgICk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAoCiAgICAgICAgICBsYXlvdXQgPT09ICJsaXN0LWNoYXJ0IiB8fAogICAgICAgICAgbGF5b3V0ID09PSAidGV4dC1jaGFydCIgfHwKICAgICAgICAgIGxheW91dCA9PT0gImltYWdlLWNoYXJ0IiB8fAogICAgICAgICAgbGF5b3V0ID09PSAidGFibGUtY2hhcnQiIHx8CiAgICAgICAgICBsYXlvdXQgPT09ICJpbWFnZS10ZXh0IgogICAgICAgICkgewogICAgICAgICAgY29uc3QgcHJpbWFyeSA9CiAgICAgICAgICAgIGxheW91dCA9PT0gImxpc3QtY2hhcnQiCiAgICAgICAgICAgICAgPyBmaW5kKCJsaXN0IikKICAgICAgICAgICAgICA6IGxheW91dCA9PT0gImltYWdlLWNoYXJ0IiB8fCBsYXlvdXQgPT09ICJpbWFnZS10ZXh0IgogICAgICAgICAgICAgID8gZmluZCgiaW1hZ2UiKQogICAgICAgICAgICAgIDogbGF5b3V0ID09PSAidGFibGUtY2hhcnQiCiAgICAgICAgICAgICAgPyBmaW5kKCJ0YWJsZSIpCiAgICAgICAgICAgICAgOiBmaW5kKCJ0ZXh0Iik7CiAgICAgICAgICBjb25zdCBjaGFydCA9IGZpbmQoImNoYXJ0Iik7CiAgICAgICAgICBjb25zdCBwcmltYXJ5U3BhbiA9CiAgICAgICAgICAgIGxheW91dCA9PT0gImltYWdlLWNoYXJ0IiB8fCBsYXlvdXQgPT09ICJpbWFnZS10ZXh0IiA/IDYgOiA1OwogICAgICAgICAgY29uc3QgcHJpbWFyeVdpZHRoID0gY29sdW1uV2lkdGgocHJpbWFyeVNwYW4pOwogICAgICAgICAgY29uc3Qgc2Vjb25kYXJ5WCA9IGNvbHVtblgocHJpbWFyeVNwYW4pOwogICAgICAgICAgY29uc3Qgc2Vjb25kYXJ5V2lkdGggPSBib2R5LnggKyBib2R5LndpZHRoIC0gc2Vjb25kYXJ5WDsKICAgICAgICAgIGNvbnN0IHByaW1hcnlCb3ggPSB7CiAgICAgICAgICAgIHg6IGJvZHkueCwKICAgICAgICAgICAgeTogYm9keS55LAogICAgICAgICAgICB3aWR0aDogcHJpbWFyeVdpZHRoLAogICAgICAgICAgICBoZWlnaHQ6IGJvZHkuaGVpZ2h0LAogICAgICAgICAgfTsKICAgICAgICAgIGNvbnN0IHNlY29uZGFyeUJveCA9IHsKICAgICAgICAgICAgeDogc2Vjb25kYXJ5WCwKICAgICAgICAgICAgeTogYm9keS55LAogICAgICAgICAgICB3aWR0aDogc2Vjb25kYXJ5V2lkdGgsCiAgICAgICAgICAgIGhlaWdodDogYm9keS5oZWlnaHQsCiAgICAgICAgICB9OwoKICAgICAgICAgIGlmIChwcmltYXJ5KSB7CiAgICAgICAgICAgIGlmIChwcmltYXJ5LnR5cGUgPT09ICJpbWFnZSIpIHsKICAgICAgICAgICAgICB0aGlzLnB2RHJhd0ltYWdlQmxvY2soY3R4LCByb290LCBwcmltYXJ5LCBwcmltYXJ5Qm94LCAxNDApOwogICAgICAgICAgICB9IGVsc2UgaWYgKHByaW1hcnkudHlwZSA9PT0gInRhYmxlIikgewogICAgICAgICAgICAgIHRoaXMucHZEcmF3VGFibGVCbG9jaygKICAgICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICAgIHJvb3QsCiAgICAgICAgICAgICAgICBwcmltYXJ5LAogICAgICAgICAgICAgICAgT2JqZWN0LmFzc2lnbih7fSwgcHJpbWFyeUJveCwgeyBmaWxsSGVpZ2h0OiB0cnVlIH0pLAogICAgICAgICAgICAgICAgMTQwCiAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgfSBlbHNlIGlmIChwcmltYXJ5LnR5cGUgPT09ICJsaXN0IikgewogICAgICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2TGlzdE1ldHJpY3MoY3R4LCBwcmltYXJ5LCBwcmltYXJ5V2lkdGgsIGJvZHkuaGVpZ2h0KTsKICAgICAgICAgICAgICB0aGlzLnB2RHJhd0xpc3RCbG9jaygKICAgICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICAgIHJvb3QsCiAgICAgICAgICAgICAgICBwcmltYXJ5LAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICB4OiBwcmltYXJ5Qm94LngsCiAgICAgICAgICAgICAgICAgIHk6IGJvZHkueSArIChib2R5LmhlaWdodCAtIG1ldHJpY3MuaGVpZ2h0KSAvIDIsCiAgICAgICAgICAgICAgICAgIHdpZHRoOiBwcmltYXJ5V2lkdGgsCiAgICAgICAgICAgICAgICB9LAogICAgICAgICAgICAgICAgbWV0cmljcywKICAgICAgICAgICAgICAgIDE0MAogICAgICAgICAgICAgICk7CiAgICAgICAgICAgIH0gZWxzZSBpZiAocHJpbWFyeS50eXBlID09PSAidGV4dCIpIHsKICAgICAgICAgICAgICBjb25zdCB2YXJpYW50ID0gImxlYWQiOwogICAgICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2VGV4dE1ldHJpY3MoY3R4LCBwcmltYXJ5LCBwcmltYXJ5V2lkdGgsIHZhcmlhbnQpOwogICAgICAgICAgICAgIHRoaXMucHZEcmF3VGV4dEJsb2NrKAogICAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgICAgcm9vdCwKICAgICAgICAgICAgICAgIHByaW1hcnksCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgIHg6IHByaW1hcnlCb3gueCwKICAgICAgICAgICAgICAgICAgeTogYm9keS55ICsgKGJvZHkuaGVpZ2h0IC0gbWV0cmljcy5oZWlnaHQpIC8gMiwKICAgICAgICAgICAgICAgICAgd2lkdGg6IHByaW1hcnlXaWR0aCwKICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICB2YXJpYW50LAogICAgICAgICAgICAgICAgMTQwCiAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGlmIChjaGFydCkgewogICAgICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0Q2FyZChjdHgsIHJvb3QsIGNoYXJ0LCBzZWNvbmRhcnlCb3gsIHsgZGVsYXk6IDI5MCB9KTsKICAgICAgICAgIH0gZWxzZSBpZiAobGF5b3V0ID09PSAiaW1hZ2UtdGV4dCIpIHsKICAgICAgICAgICAgY29uc3QgdGV4dCA9IGZpbmQoInRleHQiKTsKICAgICAgICAgICAgaWYgKHRleHQpIHsKICAgICAgICAgICAgICBjb25zdCBtZXRyaWNzID0gdGhpcy5wdlRleHRNZXRyaWNzKGN0eCwgdGV4dCwgc2Vjb25kYXJ5V2lkdGgsICJsZWFkIik7CiAgICAgICAgICAgICAgdGhpcy5wdkRyYXdUZXh0QmxvY2soCiAgICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgICByb290LAogICAgICAgICAgICAgICAgdGV4dCwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgeDogc2Vjb25kYXJ5Qm94LngsCiAgICAgICAgICAgICAgICAgIHk6IGJvZHkueSArIChib2R5LmhlaWdodCAtIG1ldHJpY3MuaGVpZ2h0KSAvIDIsCiAgICAgICAgICAgICAgICAgIHdpZHRoOiBzZWNvbmRhcnlXaWR0aCwKICAgICAgICAgICAgICAgIH0sCiAgICAgICAgICAgICAgICAibGVhZCIsCiAgICAgICAgICAgICAgICAyOTAKICAgICAgICAgICAgICApOwogICAgICAgICAgICB9CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICAvLyDlhbbku5bnu4TlkIjvvJrnurXlkJHloIblj6DlubblsYXkuK0KICAgICAgICBjb25zdCBibG9ja3MgPSBbXTsKICAgICAgICBlbGVtZW50cy5mb3JFYWNoKChlbGVtZW50KSA9PiB7CiAgICAgICAgICBjb25zdCB3aWR0aCA9IGJvZHkud2lkdGg7CiAgICAgICAgICBjb25zdCBtZXRyaWNzID0gdGhpcy5wdk1lYXN1cmVFbGVtZW50KGN0eCwgZWxlbWVudCwgd2lkdGgsIGJvZHkuaGVpZ2h0KTsKICAgICAgICAgIGJsb2Nrcy5wdXNoKHsgZWxlbWVudCwgd2lkdGgsIGhlaWdodDogbWV0cmljcy5oZWlnaHQgfSk7CiAgICAgICAgfSk7CiAgICAgICAgY29uc3QgdG90YWxIZWlnaHQgPQogICAgICAgICAgYmxvY2tzLnJlZHVjZSgoc3VtLCBibG9jaykgPT4gc3VtICsgYmxvY2suaGVpZ2h0LCAwKSArCiAgICAgICAgICBNYXRoLm1heCgwLCBibG9ja3MubGVuZ3RoIC0gMSkgKiBnYXA7CiAgICAgICAgbGV0IGN1cnJlbnRZID0gYm9keS55ICsgTWF0aC5tYXgoMCwgKGJvZHkuaGVpZ2h0IC0gdG90YWxIZWlnaHQpIC8gMik7CgogICAgICAgIGJsb2Nrcy5mb3JFYWNoKChibG9jaywgaW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IHggPQogICAgICAgICAgICBibG9jay5lbGVtZW50LnR5cGUgPT09ICJsaXN0IgogICAgICAgICAgICAgID8gYm9keS54CiAgICAgICAgICAgICAgOiBib2R5LnggKyBNYXRoLm1heCgwLCAoYm9keS53aWR0aCAtIGJsb2NrLndpZHRoKSAvIDIpOwogICAgICAgICAgdGhpcy5wdkRyYXdFbGVtZW50KAogICAgICAgICAgICBjdHgsCiAgICAgICAgICAgIHJvb3QsCiAgICAgICAgICAgIGJsb2NrLmVsZW1lbnQsCiAgICAgICAgICAgIHsgeCwgeTogY3VycmVudFksIHdpZHRoOiBibG9jay53aWR0aCwgaGVpZ2h0OiBibG9jay5oZWlnaHQgfSwKICAgICAgICAgICAgaW5kZXgsCiAgICAgICAgICAgIDE0MCArIGluZGV4ICogMTUwCiAgICAgICAgICApOwogICAgICAgICAgY3VycmVudFkgKz0gYmxvY2suaGVpZ2h0ICsgZ2FwOwogICAgICAgIH0pOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOato+aWh+aWh+acrOWdlwogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcHZUZXh0TWV0cmljcyhjdHgsIGVsZW1lbnQsIHdpZHRoLCB2YXJpYW50KSB7CiAgICAgICAgY29uc3QgZCA9IGN0eC5kZW5zaXR5OwogICAgICAgIGNvbnN0IGlzU3RhdGVtZW50ID0gdmFyaWFudCA9PT0gInN0YXRlbWVudCI7CiAgICAgICAgY29uc3QgaXNMZWFkID0gdmFyaWFudCA9PT0gImxlYWQiOwogICAgICAgIGNvbnN0IGZvbnRTaXplID0gaXNTdGF0ZW1lbnQgPyBkLmxlYWQgKiAxLjEgOiBpc0xlYWQgPyBkLmJvZHkgOiBkLmxlYWQ7CiAgICAgICAgY29uc3QgbGluZUhlaWdodCA9IGlzU3RhdGVtZW50CiAgICAgICAgICA/IGZvbnRTaXplICogMS41CiAgICAgICAgICA6IGlzTGVhZAogICAgICAgICAgPyBmb250U2l6ZSAqIDEuNgogICAgICAgICAgOiBmb250U2l6ZSAqIDEuNjI7CiAgICAgICAgY29uc3Qgd2VpZ2h0ID0gaXNTdGF0ZW1lbnQgPyA1MDAgOiA0MDA7CiAgICAgICAgY29uc3QgbGV0dGVyU3BhY2luZyA9IGZvbnRTaXplICogLTAuMDE1OwogICAgICAgIGNvbnN0IHBhZGRpbmdMZWZ0ID0gaXNTdGF0ZW1lbnQgPyAwIDogaXNMZWFkID8gMjIgOiAyODsKICAgICAgICBjb25zdCBib3JkZXJXaWR0aCA9IGlzU3RhdGVtZW50ID8gMCA6IGlzTGVhZCA/IDIgOiAzOwogICAgICAgIGNvbnN0IGNvbnRlbnRXaWR0aCA9IE1hdGgubWF4KDYwLCB3aWR0aCAtIHBhZGRpbmdMZWZ0KTsKICAgICAgICBjb25zdCBtYXhMaW5lcyA9IGN0eC5tb2RlID09PSAidGh1bWIiID8gMyA6IGlzU3RhdGVtZW50ID8gNiA6IDE0OwogICAgICAgIGNvbnN0IGxpbmVzID0gdGhpcy5wdldyYXBUZXh0KAogICAgICAgICAgZWxlbWVudC5jb250ZW50IHx8ICIiLAogICAgICAgICAgY29udGVudFdpZHRoLAogICAgICAgICAgdGhpcy5wdkZvbnQod2VpZ2h0LCBmb250U2l6ZSksCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nLAogICAgICAgICAgbWF4TGluZXMKICAgICAgICApOwoKICAgICAgICByZXR1cm4gewogICAgICAgICAgbGluZXMsCiAgICAgICAgICBmb250U2l6ZSwKICAgICAgICAgIGxpbmVIZWlnaHQsCiAgICAgICAgICB3ZWlnaHQsCiAgICAgICAgICBsZXR0ZXJTcGFjaW5nLAogICAgICAgICAgcGFkZGluZ0xlZnQsCiAgICAgICAgICBib3JkZXJXaWR0aCwKICAgICAgICAgIGhlaWdodDogTWF0aC5tYXgobGluZUhlaWdodCwgbGluZXMubGVuZ3RoICogbGluZUhlaWdodCksCiAgICAgICAgICB3aWR0aCwKICAgICAgICB9OwogICAgICB9LAoKICAgICAgcHZEcmF3VGV4dEJsb2NrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIHZhcmlhbnQsIGRlbGF5KSB7CiAgICAgICAgY29uc3QgbWV0cmljcyA9IHRoaXMucHZUZXh0TWV0cmljcyhjdHgsIGVsZW1lbnQsIGJveC53aWR0aCwgdmFyaWFudCk7CiAgICAgICAgY29uc3QgZ3JvdXAgPSB0aGlzLnB2R3JvdXAoCiAgICAgICAgICBjdHgsCiAgICAgICAgICBwYXJlbnQsCiAgICAgICAgICBib3gueCwKICAgICAgICAgIGJveC55LAogICAgICAgICAgYm94LndpZHRoLAogICAgICAgICAgbWV0cmljcy5oZWlnaHQKICAgICAgICApOwoKICAgICAgICBpZiAobWV0cmljcy5ib3JkZXJXaWR0aCkgewogICAgICAgICAgdGhpcy5wdlJlY3QoCiAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIG1ldHJpY3MuYm9yZGVyV2lkdGgsCiAgICAgICAgICAgIG1ldHJpY3MuaGVpZ2h0LAogICAgICAgICAgICB0aGlzLlBWX0MuYnJhbmQKICAgICAgICAgICk7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGdyb3VwLCB7CiAgICAgICAgICB4OiBtZXRyaWNzLnBhZGRpbmdMZWZ0LAogICAgICAgICAgeTogMCwKICAgICAgICAgIHdpZHRoOiBNYXRoLm1heCgyMCwgYm94LndpZHRoIC0gbWV0cmljcy5wYWRkaW5nTGVmdCksCiAgICAgICAgICBsaW5lczogbWV0cmljcy5saW5lcywKICAgICAgICAgIGZvbnRTaXplOiBtZXRyaWNzLmZvbnRTaXplLAogICAgICAgICAgbGluZUhlaWdodDogbWV0cmljcy5saW5lSGVpZ2h0LAogICAgICAgICAgd2VpZ2h0OiBtZXRyaWNzLndlaWdodCwKICAgICAgICAgIGxldHRlclNwYWNpbmc6IG1ldHJpY3MubGV0dGVyU3BhY2luZywKICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy50ZXh0MSwKICAgICAgICB9KTsKCiAgICAgICAgdGhpcy5wdkFuaW1hdGUoY3R4LCBncm91cCwgZGVsYXksICJ1cCIpOwogICAgICAgIHJldHVybiBtZXRyaWNzLmhlaWdodDsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDliJfooajlnZcKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2TGlzdE1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCwgYXZhaWxhYmxlSGVpZ2h0KSB7CiAgICAgICAgY29uc3QgZCA9IGN0eC5kZW5zaXR5OwogICAgICAgIGNvbnN0IGl0ZW1zID0gQXJyYXkuaXNBcnJheShlbGVtZW50LmNvbnRlbnQpID8gZWxlbWVudC5jb250ZW50IDogW107CiAgICAgICAgY29uc3QgbWF4SXRlbXMgPSBjdHgubW9kZSA9PT0gInRodW1iIiA/IDMgOiBpdGVtcy5sZW5ndGg7CiAgICAgICAgY29uc3Qgc291cmNlID0gaXRlbXMuc2xpY2UoMCwgbWF4SXRlbXMpOwogICAgICAgIGNvbnN0IGluZGV4V2lkdGggPSA1MjsKICAgICAgICBjb25zdCBnYXBYID0gMTg7CiAgICAgICAgbGV0IGZvbnRTaXplID0gZC5saXN0OwogICAgICAgIGxldCByb3dQYWQgPSBkLnJvd1BhZDsKICAgICAgICBsZXQgcm93cyA9IFtdOwogICAgICAgIGxldCB0b3RhbCA9IDA7CgogICAgICAgIGZvciAobGV0IGF0dGVtcHQgPSAwOyBhdHRlbXB0IDwgNjsgYXR0ZW1wdCArPSAxKSB7CiAgICAgICAgICBjb25zdCBsaW5lSGVpZ2h0ID0gZm9udFNpemUgKiAxLjQ4OwogICAgICAgICAgcm93cyA9IHNvdXJjZS5tYXAoKGl0ZW0pID0+IHsKICAgICAgICAgICAgY29uc3QgbGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICAgICAgaXRlbSwKICAgICAgICAgICAgICBNYXRoLm1heCg2MCwgd2lkdGggLSBpbmRleFdpZHRoIC0gZ2FwWCksCiAgICAgICAgICAgICAgdGhpcy5wdkZvbnQoNDAwLCBmb250U2l6ZSksCiAgICAgICAgICAgICAgZm9udFNpemUgKiAtMC4wMSwKICAgICAgICAgICAgICBjdHgubW9kZSA9PT0gInRodW1iIiA/IDIgOiAzCiAgICAgICAgICAgICk7CiAgICAgICAgICAgIHJldHVybiB7CiAgICAgICAgICAgICAgbGluZXMsCiAgICAgICAgICAgICAgaGVpZ2h0OiBNYXRoLm1heChsaW5lSGVpZ2h0LCBsaW5lcy5sZW5ndGggKiBsaW5lSGVpZ2h0KSwKICAgICAgICAgICAgfTsKICAgICAgICAgIH0pOwogICAgICAgICAgdG90YWwgPSByb3dzLnJlZHVjZSgoc3VtLCByb3cpID0+IHN1bSArIHJvdy5oZWlnaHQgKyByb3dQYWQgKiAyLCAwKTsKICAgICAgICAgIGlmICghYXZhaWxhYmxlSGVpZ2h0IHx8IHRvdGFsIDw9IGF2YWlsYWJsZUhlaWdodCB8fCBhdHRlbXB0ID09PSA1KSB7CiAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgfQogICAgICAgICAgZm9udFNpemUgPSBNYXRoLm1heCgxNSwgZm9udFNpemUgKiAwLjkpOwogICAgICAgICAgcm93UGFkID0gTWF0aC5tYXgoNCwgcm93UGFkICogMC44Mik7CiAgICAgICAgfQoKICAgICAgICByZXR1cm4gewogICAgICAgICAgcm93cywKICAgICAgICAgIHRvdGFsLAogICAgICAgICAgaGVpZ2h0OiB0b3RhbCwKICAgICAgICAgIGluZGV4V2lkdGgsCiAgICAgICAgICBnYXBYLAogICAgICAgICAgZm9udFNpemUsCiAgICAgICAgICBsaW5lSGVpZ2h0OiBmb250U2l6ZSAqIDEuNDgsCiAgICAgICAgICByb3dQYWQsCiAgICAgICAgICB3aWR0aCwKICAgICAgICB9OwogICAgICB9LAoKICAgICAgcHZEcmF3TGlzdEJsb2NrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG1ldHJpY3MsIGRlbGF5KSB7CiAgICAgICAgY29uc3QgbSA9IG1ldHJpY3MgfHwgdGhpcy5wdkxpc3RNZXRyaWNzKGN0eCwgZWxlbWVudCwgYm94LndpZHRoKTsKICAgICAgICBjb25zdCBncm91cCA9IHRoaXMucHZHcm91cChjdHgsIHBhcmVudCwgYm94LngsIGJveC55LCBib3gud2lkdGgsIG0uaGVpZ2h0KTsKICAgICAgICBsZXQgeSA9IDA7CgogICAgICAgIG0ucm93cy5mb3JFYWNoKChyb3csIGluZGV4KSA9PiB7CiAgICAgICAgICBjb25zdCByb3dIZWlnaHQgPSByb3cuaGVpZ2h0ICsgbS5yb3dQYWQgKiAyOwogICAgICAgICAgdGhpcy5wdkxpbmUoCiAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIHksCiAgICAgICAgICAgIGJveC53aWR0aCwKICAgICAgICAgICAgeSwKICAgICAgICAgICAgaW5kZXggPT09IDAgPyB0aGlzLlBWX0MubGluZVN0cm9uZyA6IHRoaXMuUFZfQy5saW5lLAogICAgICAgICAgICAxCiAgICAgICAgICApOwoKICAgICAgICAgIGNvbnN0IGluZGV4U2l6ZSA9IE1hdGgubWF4KDgsIDE0KTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZ3JvdXAsIHsKICAgICAgICAgICAgeDogMCwKICAgICAgICAgICAgeTogeSArIG0ucm93UGFkICsgKHJvdy5oZWlnaHQgLSBpbmRleFNpemUpIC8gMiwKICAgICAgICAgICAgd2lkdGg6IG0uaW5kZXhXaWR0aCwKICAgICAgICAgICAgdGV4dDogU3RyaW5nKGluZGV4ICsgMSkucGFkU3RhcnQoMiwgIjAiKSwKICAgICAgICAgICAgZm9udFNpemU6IGluZGV4U2l6ZSwKICAgICAgICAgICAgbGluZUhlaWdodDogaW5kZXhTaXplLAogICAgICAgICAgICBtb25vOiB0cnVlLAogICAgICAgICAgICB3ZWlnaHQ6IDYwMCwKICAgICAgICAgICAgbGV0dGVyU3BhY2luZzogMC41NiwKICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLmJyYW5kLAogICAgICAgICAgfSk7CgogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBncm91cCwgewogICAgICAgICAgICB4OiBtLmluZGV4V2lkdGggKyBtLmdhcFgsCiAgICAgICAgICAgIHk6IHkgKyBtLnJvd1BhZCwKICAgICAgICAgICAgd2lkdGg6IE1hdGgubWF4KDIwLCBib3gud2lkdGggLSBtLmluZGV4V2lkdGggLSBtLmdhcFgpLAogICAgICAgICAgICBsaW5lczogcm93LmxpbmVzLAogICAgICAgICAgICBmb250U2l6ZTogbS5mb250U2l6ZSwKICAgICAgICAgICAgbGluZUhlaWdodDogbS5saW5lSGVpZ2h0LAogICAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiBtLmZvbnRTaXplICogLTAuMDEsCiAgICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy50ZXh0MSwKICAgICAgICAgIH0pOwoKICAgICAgICAgIHkgKz0gcm93SGVpZ2h0OwogICAgICAgIH0pOwoKICAgICAgICBpZiAobS5yb3dzLmxlbmd0aCkgewogICAgICAgICAgdGhpcy5wdkxpbmUoY3R4LCBncm91cCwgMCwgeSwgYm94LndpZHRoLCB5LCB0aGlzLlBWX0MubGluZVN0cm9uZywgMSk7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2QW5pbWF0ZShjdHgsIGdyb3VwLCBkZWxheSwgInVwIik7CiAgICAgICAgcmV0dXJuIG0uaGVpZ2h0OwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOW8leeUqOWdlwogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcHZRdW90ZU1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCkgewogICAgICAgIGNvbnN0IGQgPSBjdHguZGVuc2l0eTsKICAgICAgICBjb25zdCBmb250U2l6ZSA9IGQucXVvdGU7CiAgICAgICAgY29uc3QgbGluZUhlaWdodCA9IGZvbnRTaXplICogMS40NjsKICAgICAgICBjb25zdCBjb250ZW50V2lkdGggPSBNYXRoLm1heCgxMjAsIHdpZHRoIC0gOTIpOwogICAgICAgIGNvbnN0IGxpbmVzID0gdGhpcy5wdldyYXBUZXh0KAogICAgICAgICAgZWxlbWVudC5jb250ZW50IHx8ICIiLAogICAgICAgICAgY29udGVudFdpZHRoLAogICAgICAgICAgdGhpcy5wdkZvbnQoNTAwLCBmb250U2l6ZSksCiAgICAgICAgICBmb250U2l6ZSAqIC0wLjAyNSwKICAgICAgICAgIGN0eC5tb2RlID09PSAidGh1bWIiID8gMyA6IDUKICAgICAgICApOwogICAgICAgIGNvbnN0IHRleHRIZWlnaHQgPSBNYXRoLm1heChsaW5lSGVpZ2h0LCBsaW5lcy5sZW5ndGggKiBsaW5lSGVpZ2h0KTsKICAgICAgICBjb25zdCBjaXRlSGVpZ2h0ID0gZWxlbWVudC5jaXRlID8gMzggOiAwOwogICAgICAgIHJldHVybiB7CiAgICAgICAgICBsaW5lcywKICAgICAgICAgIGZvbnRTaXplLAogICAgICAgICAgbGluZUhlaWdodCwKICAgICAgICAgIGNvbnRlbnRXaWR0aCwKICAgICAgICAgIHRleHRIZWlnaHQsCiAgICAgICAgICBjaXRlSGVpZ2h0LAogICAgICAgICAgaGVpZ2h0OiA4NCArIHRleHRIZWlnaHQgKyBjaXRlSGVpZ2h0LAogICAgICAgICAgd2lkdGgsCiAgICAgICAgfTsKICAgICAgfSwKCiAgICAgIHB2RHJhd1F1b3RlQmxvY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgZGVsYXkpIHsKICAgICAgICBjb25zdCBtID0gdGhpcy5wdlF1b3RlTWV0cmljcyhjdHgsIGVsZW1lbnQsIGJveC53aWR0aCk7CiAgICAgICAgY29uc3QgZ3JvdXAgPSB0aGlzLnB2R3JvdXAoY3R4LCBwYXJlbnQsIGJveC54LCBib3gueSwgYm94LndpZHRoLCBtLmhlaWdodCk7CgogICAgICAgIHRoaXMucHZMaW5lKGN0eCwgZ3JvdXAsIDAsIDAsIGJveC53aWR0aCwgMCwgdGhpcy5QVl9DLmxpbmVTdHJvbmcsIDEpOwogICAgICAgIHRoaXMucHZMaW5lKGN0eCwgZ3JvdXAsIDAsIG0uaGVpZ2h0LCBib3gud2lkdGgsIG0uaGVpZ2h0LCB0aGlzLlBWX0MubGluZVN0cm9uZywgMSk7CgogICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZ3JvdXAsIHsKICAgICAgICAgIHg6IDgsCiAgICAgICAgICB5OiA4LAogICAgICAgICAgd2lkdGg6IDEyMCwKICAgICAgICAgIGhlaWdodDogMTMwLAogICAgICAgICAgdGV4dDogIuKAnCIsCiAgICAgICAgICBmb250U2l6ZTogMTE2LAogICAgICAgICAgbGluZUhlaWdodDogMTE2LAogICAgICAgICAgZm9udEZhbWlseTogJ0dlb3JnaWEsICJUaW1lcyBOZXcgUm9tYW4iLCBzZXJpZicsCiAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MuYnJhbmQsCiAgICAgICAgICBvcGFjaXR5OiAwLjE2LAogICAgICAgIH0pOwoKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGdyb3VwLCB7CiAgICAgICAgICB4OiA5MiwKICAgICAgICAgIHk6IDQyLAogICAgICAgICAgd2lkdGg6IG0uY29udGVudFdpZHRoLAogICAgICAgICAgbGluZXM6IG0ubGluZXMsCiAgICAgICAgICBmb250U2l6ZTogbS5mb250U2l6ZSwKICAgICAgICAgIGxpbmVIZWlnaHQ6IG0ubGluZUhlaWdodCwKICAgICAgICAgIHdlaWdodDogNTAwLAogICAgICAgICAgbGV0dGVyU3BhY2luZzogbS5mb250U2l6ZSAqIC0wLjAyNSwKICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy5pbmssCiAgICAgICAgfSk7CgogICAgICAgIGlmIChlbGVtZW50LmNpdGUpIHsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZ3JvdXAsIHsKICAgICAgICAgICAgeDogOTIsCiAgICAgICAgICAgIHk6IDQyICsgbS50ZXh0SGVpZ2h0ICsgMjAsCiAgICAgICAgICAgIHdpZHRoOiBtLmNvbnRlbnRXaWR0aCwKICAgICAgICAgICAgdGV4dDogZWxlbWVudC5jaXRlLAogICAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAwLjI2LAogICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgICB9KTsKICAgICAgICB9CgogICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwgZ3JvdXAsIGRlbGF5LCAidXAiKTsKICAgICAgICByZXR1cm4gbS5oZWlnaHQ7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg6KGo5qC85Z2XCiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdlRhYmxlTWV0cmljcyhjdHgsIGVsZW1lbnQsIGJveCkgewogICAgICAgIGNvbnN0IGQgPSBjdHguZGVuc2l0eTsKICAgICAgICBjb25zdCBoZWFkZXJzID0gQXJyYXkuaXNBcnJheShlbGVtZW50LmhlYWRlcnMpID8gZWxlbWVudC5oZWFkZXJzIDogW107CiAgICAgICAgY29uc3QgcmF3Um93cyA9IEFycmF5LmlzQXJyYXkoZWxlbWVudC5kYXRhKSA/IGVsZW1lbnQuZGF0YSA6IFtdOwogICAgICAgIGNvbnN0IGRhdGEgPSBjdHgubW9kZSA9PT0gInRodW1iIiA/IHJhd1Jvd3Muc2xpY2UoMCwgMykgOiByYXdSb3dzOwogICAgICAgIGNvbnN0IGNvbENvdW50ID0gTWF0aC5tYXgoCiAgICAgICAgICBoZWFkZXJzLmxlbmd0aCwKICAgICAgICAgIGRhdGEucmVkdWNlKChtYXgsIHJvdykgPT4gTWF0aC5tYXgobWF4LCAocm93IHx8IFtdKS5sZW5ndGgpLCAwKQogICAgICAgICk7CgogICAgICAgIGlmICghY29sQ291bnQpIHJldHVybiBudWxsOwoKICAgICAgICBjb25zdCBwYWRYID0gMjQ7CiAgICAgICAgY29uc3QgY2VsbFdpZHRoID0gTWF0aC5tYXgoNDAsIChib3gud2lkdGggLSBwYWRYICogMikgLyBjb2xDb3VudCk7CiAgICAgICAgY29uc3QgdGV4dFdpZHRoID0gTWF0aC5tYXgoMjAsIGNlbGxXaWR0aCAtIDMyKTsKICAgICAgICBjb25zdCBoZWFkZXJGb250ID0gdGhpcy5wdkZvbnQoNjAwLCAxMyk7CiAgICAgICAgY29uc3QgaGVhZGVyTGluZUhlaWdodCA9IDE3OwogICAgICAgIGNvbnN0IGJvZHlGb250ID0gdGhpcy5wdkZvbnQoNDAwLCBkLnRhYmxlKTsKICAgICAgICBjb25zdCBib2R5TGluZUhlaWdodCA9IGQudGFibGUgKiAxLjQ7CgogICAgICAgIGNvbnN0IGhlYWRlckxpbmVzID0gaGVhZGVycy5tYXAoKGhlYWRlcikgPT4KICAgICAgICAgIHRoaXMucHZXcmFwVGV4dCgKICAgICAgICAgICAgU3RyaW5nKGhlYWRlciA9PSBudWxsID8gIiIgOiBoZWFkZXIpLnRvVXBwZXJDYXNlKCksCiAgICAgICAgICAgIHRleHRXaWR0aCwKICAgICAgICAgICAgaGVhZGVyRm9udCwKICAgICAgICAgICAgMC43OCwKICAgICAgICAgICAgMQogICAgICAgICAgKQogICAgICAgICk7CiAgICAgICAgY29uc3QgaGVhZGVySGVpZ2h0ID0gaGVhZGVycy5sZW5ndGggPyAzNiArIGhlYWRlckxpbmVIZWlnaHQgOiAwOwoKICAgICAgICBjb25zdCByb3dzID0gZGF0YS5tYXAoKHJvdykgPT4gewogICAgICAgICAgbGV0IHJvd0hlaWdodCA9IGJvZHlMaW5lSGVpZ2h0OwogICAgICAgICAgY29uc3QgY2VsbHMgPSBbXTsKICAgICAgICAgIGZvciAobGV0IGNJbmRleCA9IDA7IGNJbmRleCA8IGNvbENvdW50OyBjSW5kZXggKz0gMSkgewogICAgICAgICAgICBjb25zdCBsaW5lcyA9IHRoaXMucHZXcmFwVGV4dCgKICAgICAgICAgICAgICBTdHJpbmcoKHJvdyB8fCBbXSlbY0luZGV4XSA9PSBudWxsID8gIiIgOiByb3dbY0luZGV4XSksCiAgICAgICAgICAgICAgdGV4dFdpZHRoLAogICAgICAgICAgICAgIGJvZHlGb250LAogICAgICAgICAgICAgIDAsCiAgICAgICAgICAgICAgY3R4Lm1vZGUgPT09ICJ0aHVtYiIgPyAxIDogMgogICAgICAgICAgICApOwogICAgICAgICAgICBjZWxscy5wdXNoKGxpbmVzKTsKICAgICAgICAgICAgcm93SGVpZ2h0ID0gTWF0aC5tYXgocm93SGVpZ2h0LCBsaW5lcy5sZW5ndGggKiBib2R5TGluZUhlaWdodCk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICBjZWxscywKICAgICAgICAgICAgaGVpZ2h0OiByb3dIZWlnaHQgKyAzNCwKICAgICAgICAgIH07CiAgICAgICAgfSk7CgogICAgICAgIGNvbnN0IHNvdXJjZUhlaWdodCA9IGVsZW1lbnQuc291cmNlID8gMzggOiAwOwogICAgICAgIGNvbnN0IG1heEhlaWdodCA9IE1hdGgubWF4KDgwLCBib3guaGVpZ2h0IC0gc291cmNlSGVpZ2h0KTsKICAgICAgICBjb25zdCB2aXNpYmxlUm93cyA9IFtdOwogICAgICAgIGxldCB1c2VkSGVpZ2h0ID0gaGVhZGVySGVpZ2h0OwoKICAgICAgICByb3dzLmZvckVhY2goKHJvdykgPT4gewogICAgICAgICAgaWYgKHVzZWRIZWlnaHQgKyByb3cuaGVpZ2h0ID4gbWF4SGVpZ2h0ICYmIHZpc2libGVSb3dzLmxlbmd0aCkgcmV0dXJuOwogICAgICAgICAgdmlzaWJsZVJvd3MucHVzaChyb3cpOwogICAgICAgICAgdXNlZEhlaWdodCArPSByb3cuaGVpZ2h0OwogICAgICAgIH0pOwoKICAgICAgICBpZiAoYm94LmZpbGxIZWlnaHQgJiYgdmlzaWJsZVJvd3MubGVuZ3RoKSB7CiAgICAgICAgICBjb25zdCBhdmFpbGFibGUgPSBNYXRoLm1heCgKICAgICAgICAgICAgdmlzaWJsZVJvd3MubGVuZ3RoICogNTQsCiAgICAgICAgICAgIGJveC5oZWlnaHQgLSBoZWFkZXJIZWlnaHQgLSBzb3VyY2VIZWlnaHQKICAgICAgICAgICk7CiAgICAgICAgICBjb25zdCByb3dIZWlnaHQgPSBNYXRoLm1heCgKICAgICAgICAgICAgNTQsCiAgICAgICAgICAgIE1hdGgubWluKDEzMiwgYXZhaWxhYmxlIC8gdmlzaWJsZVJvd3MubGVuZ3RoKQogICAgICAgICAgKTsKICAgICAgICAgIHZpc2libGVSb3dzLmZvckVhY2goKHJvdykgPT4gewogICAgICAgICAgICByb3cuaGVpZ2h0ID0gcm93SGVpZ2h0OwogICAgICAgICAgfSk7CiAgICAgICAgICB1c2VkSGVpZ2h0ID0gaGVhZGVySGVpZ2h0ICsgcm93SGVpZ2h0ICogdmlzaWJsZVJvd3MubGVuZ3RoOwogICAgICAgIH0KCiAgICAgICAgcmV0dXJuIHsKICAgICAgICAgIGhlYWRlcnMsCiAgICAgICAgICBoZWFkZXJMaW5lcywKICAgICAgICAgIGNvbENvdW50LAogICAgICAgICAgY29sV2lkdGg6IGNlbGxXaWR0aCwKICAgICAgICAgIHBhZFgsCiAgICAgICAgICBoZWFkZXJIZWlnaHQsCiAgICAgICAgICBoZWFkZXJMaW5lSGVpZ2h0LAogICAgICAgICAgYm9keUZvbnQsCiAgICAgICAgICBib2R5TGluZUhlaWdodCwKICAgICAgICAgIGhlYWRlckZvbnQsCiAgICAgICAgICByb3dzOiB2aXNpYmxlUm93cywKICAgICAgICAgIGhlaWdodDogTWF0aC5taW4oYm94LmhlaWdodCwgdXNlZEhlaWdodCArIHNvdXJjZUhlaWdodCksCiAgICAgICAgICBzb3VyY2VIZWlnaHQsCiAgICAgICAgICB3aWR0aDogYm94LndpZHRoLAogICAgICAgIH07CiAgICAgIH0sCgogICAgICBwdkRyYXdUYWJsZUJsb2NrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIGRlbGF5KSB7CiAgICAgICAgY29uc3QgbSA9IHRoaXMucHZUYWJsZU1ldHJpY3MoY3R4LCBlbGVtZW50LCBib3gpOwogICAgICAgIGlmICghbSkgcmV0dXJuIDA7CiAgICAgICAgY29uc3QgdG9wID0gYm94LmZpbGxIZWlnaHQKICAgICAgICAgID8gYm94LnkKICAgICAgICAgIDogYm94LnkgKyBNYXRoLm1heCgwLCAoYm94LmhlaWdodCAtIG0uaGVpZ2h0KSAvIDIpOwogICAgICAgIGNvbnN0IGZyYW1lID0gdGhpcy5wdkZyYW1lKAogICAgICAgICAgY3R4LAogICAgICAgICAgcGFyZW50LAogICAgICAgICAgYm94LngsCiAgICAgICAgICB0b3AsCiAgICAgICAgICBib3gud2lkdGgsCiAgICAgICAgICBtLmhlaWdodCwKICAgICAgICAgIHRoaXMuUFZfQy5zdXJmYWNlLAogICAgICAgICAgewogICAgICAgICAgICBjb3JuZXJSYWRpdXM6IDIwLAogICAgICAgICAgICBjbGlwOiB0cnVlLAogICAgICAgICAgICBzdHJva2U6IHRoaXMuUFZfQy5saW5lLAogICAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICAgIH0KICAgICAgICApOwoKICAgICAgICBsZXQgeSA9IDA7CgogICAgICAgIGlmIChtLmhlYWRlcnMubGVuZ3RoKSB7CiAgICAgICAgICBtLmhlYWRlckxpbmVzLmZvckVhY2goKGxpbmVzLCBjSW5kZXgpID0+IHsKICAgICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBmcmFtZSwgewogICAgICAgICAgICAgIHg6IG0ucGFkWCArIGNJbmRleCAqIG0uY29sV2lkdGgsCiAgICAgICAgICAgICAgeTogMTgsCiAgICAgICAgICAgICAgd2lkdGg6IE1hdGgubWF4KDIwLCBtLmNvbFdpZHRoIC0gMzIpLAogICAgICAgICAgICAgIGxpbmVzLAogICAgICAgICAgICAgIGZvbnRTaXplOiAxMywKICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiBtLmhlYWRlckxpbmVIZWlnaHQsCiAgICAgICAgICAgICAgd2VpZ2h0OiA2MDAsCiAgICAgICAgICAgICAgbGV0dGVyU3BhY2luZzogMC43OCwKICAgICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgICAgIH0pOwogICAgICAgICAgfSk7CiAgICAgICAgICB5ID0gbS5oZWFkZXJIZWlnaHQ7CiAgICAgICAgICB0aGlzLnB2TGluZShjdHgsIGZyYW1lLCAwLCB5LCBib3gud2lkdGgsIHksIHRoaXMuUFZfQy5pbmssIDIpOwogICAgICAgIH0KCiAgICAgICAgbS5yb3dzLmZvckVhY2goKHJvdykgPT4gewogICAgICAgICAgcm93LmNlbGxzLmZvckVhY2goKGxpbmVzLCBjSW5kZXgpID0+IHsKICAgICAgICAgICAgY29uc3QgdGV4dEhlaWdodCA9IGxpbmVzLmxlbmd0aCAqIG0uYm9keUxpbmVIZWlnaHQ7CiAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZnJhbWUsIHsKICAgICAgICAgICAgICB4OiBtLnBhZFggKyBjSW5kZXggKiBtLmNvbFdpZHRoLAogICAgICAgICAgICAgIHk6IHkgKyBNYXRoLm1heCgxNywgKHJvdy5oZWlnaHQgLSB0ZXh0SGVpZ2h0KSAvIDIpLAogICAgICAgICAgICAgIHdpZHRoOiBNYXRoLm1heCgyMCwgbS5jb2xXaWR0aCAtIDMyKSwKICAgICAgICAgICAgICBsaW5lcywKICAgICAgICAgICAgICBmb250U2l6ZTogY3R4LmRlbnNpdHkudGFibGUsCiAgICAgICAgICAgICAgbGluZUhlaWdodDogbS5ib2R5TGluZUhlaWdodCwKICAgICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDEsCiAgICAgICAgICAgIH0pOwogICAgICAgICAgfSk7CiAgICAgICAgICB5ICs9IHJvdy5oZWlnaHQ7CiAgICAgICAgICB0aGlzLnB2TGluZShjdHgsIGZyYW1lLCAwLCB5LCBib3gud2lkdGgsIHksIHRoaXMuUFZfQy5saW5lLCAxKTsKICAgICAgICB9KTsKCiAgICAgICAgaWYgKGVsZW1lbnQuc291cmNlKSB7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGZyYW1lLCB7CiAgICAgICAgICAgIHg6IG0ucGFkWCwKICAgICAgICAgICAgeTogbS5oZWlnaHQgLSBtLnNvdXJjZUhlaWdodCArIDEyLAogICAgICAgICAgICB3aWR0aDogYm94LndpZHRoIC0gbS5wYWRYICogMiwKICAgICAgICAgICAgdGV4dDogIuaVsOaNruadpea6kO+8miIgKyBlbGVtZW50LnNvdXJjZSwKICAgICAgICAgICAgZm9udFNpemU6IDExLAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAxNSwKICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLnRleHQzLAogICAgICAgICAgfSk7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2QW5pbWF0ZShjdHgsIGZyYW1lLCBkZWxheSwgInVwIik7CiAgICAgICAgcmV0dXJuIG0uaGVpZ2h0OwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOWbvueJh+WdlwogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcHZEcmF3SW1hZ2VCbG9jayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBkZWxheSkgewogICAgICAgIGNvbnN0IGhhc0NhcHRpb24gPSBCb29sZWFuKGVsZW1lbnQuYWx0KSAmJiBjdHgubW9kZSAhPT0gInRodW1iIjsKICAgICAgICBjb25zdCBmcmFtZUhlaWdodCA9IE1hdGgubWF4KDgwLCBib3guaGVpZ2h0IC0gKGhhc0NhcHRpb24gPyAzMiA6IDApKTsKICAgICAgICBjb25zdCBncm91cCA9IHRoaXMucHZHcm91cChjdHgsIHBhcmVudCwgYm94LngsIGJveC55LCBib3gud2lkdGgsIGJveC5oZWlnaHQpOwoKICAgICAgICBjb25zdCBmcmFtZSA9IHRoaXMucHZGcmFtZSgKICAgICAgICAgIGN0eCwKICAgICAgICAgIGdyb3VwLAogICAgICAgICAgMCwKICAgICAgICAgIDAsCiAgICAgICAgICBib3gud2lkdGgsCiAgICAgICAgICBmcmFtZUhlaWdodCwKICAgICAgICAgIHRoaXMuUFZfQy5zdXJmYWNlLAogICAgICAgICAgewogICAgICAgICAgICBjb3JuZXJSYWRpdXM6IDIwLAogICAgICAgICAgICBjbGlwOiB0cnVlLAogICAgICAgICAgICBzdHJva2U6IHRoaXMuUFZfQy5saW5lLAogICAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICAgIH0KICAgICAgICApOwoKICAgICAgICBpZiAoZWxlbWVudC51cmwpIHsKICAgICAgICAgIGNvbnN0IGltYWdlID0gbmV3IGN0eC5saWIuSW1hZ2UoewogICAgICAgICAgICB4OiAwLAogICAgICAgICAgICB5OiAwLAogICAgICAgICAgICB3aWR0aDogYm94LndpZHRoICogY3R4LmssCiAgICAgICAgICAgIGhlaWdodDogZnJhbWVIZWlnaHQgKiBjdHguaywKICAgICAgICAgICAgdXJsOiBlbGVtZW50LnVybCwKICAgICAgICAgICAgb2JqZWN0Rml0OiBlbGVtZW50LmZpdCB8fCAiY292ZXIiLAogICAgICAgICAgICBvYmplY3RQb3NpdGlvbjogZWxlbWVudC5wb3NpdGlvbiB8fCAiY2VudGVyIiwKICAgICAgICAgIH0pOwogICAgICAgICAgZnJhbWUuYWRkKGltYWdlKTsKICAgICAgICB9IGVsc2UgewogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBmcmFtZSwgewogICAgICAgICAgICB4OiAwLAogICAgICAgICAgICB5OiAwLAogICAgICAgICAgICB3aWR0aDogYm94LndpZHRoLAogICAgICAgICAgICBoZWlnaHQ6IGZyYW1lSGVpZ2h0LAogICAgICAgICAgICB0ZXh0OiBlbGVtZW50LmFsdCB8fCAi5Zu+54mHIiwKICAgICAgICAgICAgZm9udFNpemU6IDIwLAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAyOCwKICAgICAgICAgICAgYWxpZ246ICJjZW50ZXIiLAogICAgICAgICAgICB2YWxpZ246ICJtaWRkbGUiLAogICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgICB9KTsKICAgICAgICB9CgogICAgICAgIGlmIChoYXNDYXB0aW9uKSB7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGdyb3VwLCB7CiAgICAgICAgICAgIHg6IDAsCiAgICAgICAgICAgIHk6IGZyYW1lSGVpZ2h0ICsgMTIsCiAgICAgICAgICAgIHdpZHRoOiBib3gud2lkdGgsCiAgICAgICAgICAgIHRleHQ6IGVsZW1lbnQuYWx0LAogICAgICAgICAgICBmb250U2l6ZTogMTIsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgICB9KTsKICAgICAgICB9CgogICAgICAgIHRoaXMucHZBbmltYXRlKGN0eCwgZ3JvdXAsIGRlbGF5LCAidXAiKTsKICAgICAgICByZXR1cm4gYm94LmhlaWdodDsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDlm77ooajmlbDmja7op6PmnpAKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2Q2hhcnRLaW5kKGVsZW1lbnQpIHsKICAgICAgICBpZiAoIWVsZW1lbnQgfHwgZWxlbWVudC50eXBlICE9PSAiY2hhcnQiKSByZXR1cm4gIm90aGVyIjsKICAgICAgICBpZiAoZWxlbWVudC5jaGFydFR5cGUgPT09ICJtZXJtYWlkIikgcmV0dXJuICJmbG93IjsKICAgICAgICBjb25zdCB0eXBlID0KICAgICAgICAgIGVsZW1lbnQuY2hhcnREYXRhICYmIGVsZW1lbnQuY2hhcnREYXRhLnR5cGUKICAgICAgICAgICAgPyBlbGVtZW50LmNoYXJ0RGF0YS50eXBlCiAgICAgICAgICAgIDogIiI7CiAgICAgICAgaWYgKHR5cGUgPT09ICJsaW5lIiB8fCB0eXBlID09PSAiYXJlYSIpIHJldHVybiAibGluZSI7CiAgICAgICAgaWYgKHR5cGUgPT09ICJiYXIiIHx8IHR5cGUgPT09ICJob3Jpem9udGFsQmFyIikgcmV0dXJuICJiYXIiOwogICAgICAgIGlmICh0eXBlID09PSAicGllIiB8fCB0eXBlID09PSAiZG91Z2hudXQiIHx8IHR5cGUgPT09ICJwb2xhckFyZWEiKSB7CiAgICAgICAgICByZXR1cm4gInBpZSI7CiAgICAgICAgfQogICAgICAgIGlmICh0eXBlID09PSAicmFkYXIiKSByZXR1cm4gInJhZGFyIjsKICAgICAgICBpZiAodHlwZSA9PT0gInNjYXR0ZXIiKSByZXR1cm4gInNjYXR0ZXIiOwogICAgICAgIHJldHVybiAib3RoZXIiOwogICAgICB9LAoKICAgICAgcHZDaGFydERhdGEoZWxlbWVudCkgewogICAgICAgIGNvbnN0IHJhdyA9IGVsZW1lbnQgJiYgZWxlbWVudC5jaGFydERhdGEgPyBlbGVtZW50LmNoYXJ0RGF0YSA6IHt9OwogICAgICAgIGNvbnN0IGRhdGEgPSByYXcuZGF0YSB8fCB7fTsKICAgICAgICBjb25zdCBsYWJlbHMgPSBBcnJheS5pc0FycmF5KGRhdGEubGFiZWxzKSA/IGRhdGEubGFiZWxzLm1hcChTdHJpbmcpIDogW107CiAgICAgICAgY29uc3QgZGF0YXNldHMgPSAoQXJyYXkuaXNBcnJheShkYXRhLmRhdGFzZXRzKSA/IGRhdGEuZGF0YXNldHMgOiBbXSkubWFwKAogICAgICAgICAgKGRhdGFzZXQsIGluZGV4KSA9PiB7CiAgICAgICAgICAgIGNvbnN0IHZhbHVlcyA9IChBcnJheS5pc0FycmF5KGRhdGFzZXQuZGF0YSkgPyBkYXRhc2V0LmRhdGEgOiBbXSkubWFwKAogICAgICAgICAgICAgICh2YWx1ZSkgPT4gewogICAgICAgICAgICAgICAgaWYgKHZhbHVlICYmIHR5cGVvZiB2YWx1ZSA9PT0gIm9iamVjdCIpIHsKICAgICAgICAgICAgICAgICAgaWYgKHZhbHVlLnkgIT0gbnVsbCkgcmV0dXJuIE51bWJlcih2YWx1ZS55KTsKICAgICAgICAgICAgICAgICAgaWYgKHZhbHVlLnggIT0gbnVsbCkgcmV0dXJuIE51bWJlcih2YWx1ZS54KTsKICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgIHJldHVybiBOdW1iZXIodmFsdWUpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgKTsKICAgICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgICBsYWJlbDogZGF0YXNldC5sYWJlbCB8fCAi57O75YiXICIgKyAoaW5kZXggKyAxKSwKICAgICAgICAgICAgICB2YWx1ZXMsCiAgICAgICAgICAgICAgZmlsbDogZGF0YXNldC5maWxsLAogICAgICAgICAgICAgIGJhY2tncm91bmRDb2xvcjogZGF0YXNldC5iYWNrZ3JvdW5kQ29sb3IsCiAgICAgICAgICAgICAgYm9yZGVyQ29sb3I6IGRhdGFzZXQuYm9yZGVyQ29sb3IsCiAgICAgICAgICAgICAgY29sb3I6CiAgICAgICAgICAgICAgICBkYXRhc2V0LmJvcmRlckNvbG9yIHx8CiAgICAgICAgICAgICAgICAodHlwZW9mIGRhdGFzZXQuYmFja2dyb3VuZENvbG9yID09PSAic3RyaW5nIgogICAgICAgICAgICAgICAgICA/IGRhdGFzZXQuYmFja2dyb3VuZENvbG9yCiAgICAgICAgICAgICAgICAgIDogIiIpIHx8CiAgICAgICAgICAgICAgICB0aGlzLlBWX1BBTEVUVEVbaW5kZXggJSB0aGlzLlBWX1BBTEVUVEUubGVuZ3RoXSwKICAgICAgICAgICAgfTsKICAgICAgICAgIH0KICAgICAgICApOwoKICAgICAgICByZXR1cm4gewogICAgICAgICAgcmF3LAogICAgICAgICAgdHlwZTogcmF3LnR5cGUgfHwgImJhciIsCiAgICAgICAgICBsYWJlbHMsCiAgICAgICAgICBkYXRhc2V0cywKICAgICAgICB9OwogICAgICB9LAoKICAgICAgcHZOaWNlTWF4KHZhbHVlKSB7CiAgICAgICAgY29uc3QgbnVtYmVyID0gTnVtYmVyKHZhbHVlKTsKICAgICAgICBpZiAoIWlzRmluaXRlKG51bWJlcikgfHwgbnVtYmVyIDw9IDApIHJldHVybiAxMDsKICAgICAgICBjb25zdCBleHBvbmVudCA9IE1hdGguZmxvb3IoTWF0aC5sb2cxMChudW1iZXIpKTsKICAgICAgICBjb25zdCBiYXNlID0gTWF0aC5wb3coMTAsIGV4cG9uZW50KTsKICAgICAgICBjb25zdCByYXRpbyA9IG51bWJlciAvIGJhc2U7CiAgICAgICAgbGV0IHN0ZXA7CiAgICAgICAgaWYgKHJhdGlvIDw9IDEpIHN0ZXAgPSAxOwogICAgICAgIGVsc2UgaWYgKHJhdGlvIDw9IDIpIHN0ZXAgPSAyOwogICAgICAgIGVsc2UgaWYgKHJhdGlvIDw9IDIuNSkgc3RlcCA9IDIuNTsKICAgICAgICBlbHNlIGlmIChyYXRpbyA8PSA1KSBzdGVwID0gNTsKICAgICAgICBlbHNlIHN0ZXAgPSAxMDsKICAgICAgICByZXR1cm4gc3RlcCAqIGJhc2U7CiAgICAgIH0sCgogICAgICBwdkZvcm1hdE51bWJlcih2YWx1ZSkgewogICAgICAgIGNvbnN0IG51bWJlciA9IE51bWJlcih2YWx1ZSk7CiAgICAgICAgaWYgKCFpc0Zpbml0ZShudW1iZXIpKSByZXR1cm4gIjAiOwogICAgICAgIGlmIChNYXRoLmFicyhudW1iZXIpID49IDEwMDAwMDApIHsKICAgICAgICAgIHJldHVybiAobnVtYmVyIC8gMTAwMDAwMCkudG9GaXhlZCgxKS5yZXBsYWNlKC9cLjAkLywgIiIpICsgIk0iOwogICAgICAgIH0KICAgICAgICBpZiAoTWF0aC5hYnMobnVtYmVyKSA+PSAxMDAwMCkgewogICAgICAgICAgcmV0dXJuIChudW1iZXIgLyAxMDAwKS50b0ZpeGVkKDEpLnJlcGxhY2UoL1wuMCQvLCAiIikgKyAiayI7CiAgICAgICAgfQogICAgICAgIHJldHVybiBudW1iZXIudG9Mb2NhbGVTdHJpbmcoImVuLVVTIiwgewogICAgICAgICAgbWF4aW11bUZyYWN0aW9uRGlnaXRzOiBudW1iZXIgJSAxID09PSAwID8gMCA6IDEsCiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICBwdlNlcmllc0NvbG9yKGRhdGFzZXQsIGluZGV4KSB7CiAgICAgICAgcmV0dXJuIGRhdGFzZXQuY29sb3IgfHwgdGhpcy5QVl9QQUxFVFRFW2luZGV4ICUgdGhpcy5QVl9QQUxFVFRFLmxlbmd0aF07CiAgICAgIH0sCgogICAgICBwdkRyYXdTZXJpZXNMZWdlbmQoY3R4LCBwYXJlbnQsIGRhdGFzZXRzLCB4LCB5KSB7CiAgICAgICAgbGV0IGN1cnNvciA9IHg7CiAgICAgICAgZGF0YXNldHMuc2xpY2UoMCwgNSkuZm9yRWFjaCgoZGF0YXNldCwgaW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IGNvbG9yID0gdGhpcy5wdlNlcmllc0NvbG9yKGRhdGFzZXQsIGluZGV4KTsKICAgICAgICAgIGNvbnN0IGxhYmVsID0gU3RyaW5nKGRhdGFzZXQubGFiZWwgfHwgIuezu+WIlyAiICsgKGluZGV4ICsgMSkpOwogICAgICAgICAgdGhpcy5wdkVsbGlwc2UoY3R4LCBwYXJlbnQsIGN1cnNvciwgeSArIDMsIDgsIDgsIGNvbG9yKTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgIHg6IGN1cnNvciArIDEzLAogICAgICAgICAgICB5OiB5IC0gMiwKICAgICAgICAgICAgd2lkdGg6IDIyMCwKICAgICAgICAgICAgdGV4dDogbGFiZWwsCiAgICAgICAgICAgIGZvbnRTaXplOiAxMiwKICAgICAgICAgICAgbGluZUhlaWdodDogMTYsCiAgICAgICAgICAgIGZpbGw6ICIjNjY3MDg1IiwKICAgICAgICAgIH0pOwogICAgICAgICAgY3Vyc29yICs9IDEzICsgdGhpcy5wdlN0cmluZ1dpZHRoKGxhYmVsLCB0aGlzLnB2Rm9udCg0MDAsIDEyKSwgMCkgKyAyNDsKICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIHB2Q2hhcnRLZXkoY3R4LCBlbGVtZW50KSB7CiAgICAgICAgY29uc3QgaW5kZXggPSAoKGN0eC5zbGlkZSAmJiBjdHguc2xpZGUuZWxlbWVudHMpIHx8IFtdKS5pbmRleE9mKGVsZW1lbnQpOwogICAgICAgIHJldHVybiAoCiAgICAgICAgICAicmV2ZWFsLSIgKwogICAgICAgICAgKGN0eC5zbGlkZSAmJiBjdHguc2xpZGUuaWQgPyBjdHguc2xpZGUuaWQgOiAic2xpZGUiKSArCiAgICAgICAgICAiLSIgKwogICAgICAgICAgTWF0aC5tYXgoMCwgaW5kZXgpCiAgICAgICAgKTsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDlm77ooajljaHniYcKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHB2RHJhd0NoYXJ0Q2FyZChjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBvcHRpb25zKSB7CiAgICAgICAgY29uc3Qgb3B0cyA9IG9wdGlvbnMgfHwge307CiAgICAgICAgY29uc3QgdGh1bWIgPSBjdHgubW9kZSA9PT0gInRodW1iIjsKICAgICAgICBjb25zdCBjYXJkID0gdGhpcy5wdkZyYW1lKAogICAgICAgICAgY3R4LAogICAgICAgICAgcGFyZW50LAogICAgICAgICAgYm94LngsCiAgICAgICAgICBib3gueSwKICAgICAgICAgIGJveC53aWR0aCwKICAgICAgICAgIGJveC5oZWlnaHQsCiAgICAgICAgICB0aGlzLlBWX0Muc3VyZmFjZSwKICAgICAgICAgIHsKICAgICAgICAgICAgY29ybmVyUmFkaXVzOiAyMCwKICAgICAgICAgICAgY2xpcDogdHJ1ZSwKICAgICAgICAgICAgc3Ryb2tlOiB0aGlzLlBWX0MubGluZSwKICAgICAgICAgICAgc3Ryb2tlV2lkdGg6IDEsCiAgICAgICAgICAgIHNoYWRvdzogewogICAgICAgICAgICAgIHg6IDAsCiAgICAgICAgICAgICAgeTogMTgsCiAgICAgICAgICAgICAgYmx1cjogNDIsCiAgICAgICAgICAgICAgY29sb3I6ICJyZ2JhKDEyLCAxNywgMjYsIDAuMTApIiwKICAgICAgICAgICAgfSwKICAgICAgICAgIH0KICAgICAgICApOwoKICAgICAgICBsZXQgaW5uZXI7CgogICAgICAgIGlmICh0aHVtYikgewogICAgICAgICAgdGhpcy5wdlJlY3QoY3R4LCBjYXJkLCAxOCwgMTgsIDgsIDgsIHRoaXMuUFZfQy5icmFuZCwgeyBjb3JuZXJSYWRpdXM6IDIgfSk7CiAgICAgICAgICBpbm5lciA9IHsKICAgICAgICAgICAgeDogMTYsCiAgICAgICAgICAgIHk6IDE2LAogICAgICAgICAgICB3aWR0aDogTWF0aC5tYXgoNDAsIGJveC53aWR0aCAtIDMyKSwKICAgICAgICAgICAgaGVpZ2h0OiBNYXRoLm1heCg0MCwgYm94LmhlaWdodCAtIDMyKSwKICAgICAgICAgIH07CiAgICAgICAgfSBlbHNlIHsKICAgICAgICAgIHRoaXMucHZSZWN0KGN0eCwgY2FyZCwgMzQsIDM3LCA3LCA3LCB0aGlzLlBWX0MuYnJhbmQsIHsgY29ybmVyUmFkaXVzOiAyIH0pOwoKICAgICAgICAgIGNvbnN0IHRpdGxlV2lkdGggPSBNYXRoLm1heCg4MCwgYm94LndpZHRoIC0gMjcwKTsKICAgICAgICAgIGNvbnN0IHRpdGxlTGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICAgIGVsZW1lbnQuY29udGVudCB8fCAi5pWw5o2u5Zu+6KGoIiwKICAgICAgICAgICAgdGl0bGVXaWR0aCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNjAwLCAyMiksCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIDEKICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGNhcmQsIHsKICAgICAgICAgICAgeDogNTQsCiAgICAgICAgICAgIHk6IDI2LAogICAgICAgICAgICB3aWR0aDogdGl0bGVXaWR0aCwKICAgICAgICAgICAgbGluZXM6IHRpdGxlTGluZXMsCiAgICAgICAgICAgIGZvbnRTaXplOiAyMiwKICAgICAgICAgICAgbGluZUhlaWdodDogMzAsCiAgICAgICAgICAgIHdlaWdodDogNjAwLAogICAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAtMC40LAogICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MuaW5rLAogICAgICAgICAgfSk7CgogICAgICAgICAgY29uc3QgZmlndXJlSW5kZXggPSAoKGN0eC5zbGlkZSAmJiBjdHguc2xpZGUuZWxlbWVudHMpIHx8IFtdKS5pbmRleE9mKGVsZW1lbnQpICsgMTsKICAgICAgICAgIGNvbnN0IGZpZ1RleHQgPQogICAgICAgICAgICAiRklHLiAiICsKICAgICAgICAgICAgU3RyaW5nKE1hdGgubWF4KDEsIGZpZ3VyZUluZGV4KSkucGFkU3RhcnQoMiwgIjAiKSArCiAgICAgICAgICAgIChib3gud2lkdGggPiA1NjAgPyAiICDCtyAg5rua6L2u57yp5pS+IiA6ICIiKTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgY2FyZCwgewogICAgICAgICAgICB4OiBib3gud2lkdGggLSAyNTAsCiAgICAgICAgICAgIHk6IDMxLAogICAgICAgICAgICB3aWR0aDogMjE2LAogICAgICAgICAgICB0ZXh0OiBmaWdUZXh0LAogICAgICAgICAgICBmb250U2l6ZTogMTEsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE0LAogICAgICAgICAgICBtb25vOiB0cnVlLAogICAgICAgICAgICBsZXR0ZXJTcGFjaW5nOiAxLjMyLAogICAgICAgICAgICBhbGlnbjogInJpZ2h0IiwKICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLnRleHQzLAogICAgICAgICAgfSk7CgogICAgICAgICAgdGhpcy5wdkxpbmUoY3R4LCBjYXJkLCAzNCwgNzYsIGJveC53aWR0aCAtIDM0LCA3NiwgdGhpcy5QVl9DLmxpbmUsIDEpOwogICAgICAgICAgdGhpcy5wdkxpbmUoY3R4LCBjYXJkLCAzNCwgNzYsIDU0LCA3NiwgdGhpcy5QVl9DLmJyYW5kLCAyKTsKCiAgICAgICAgICBjb25zdCBzb3VyY2VIZWlnaHQgPSBlbGVtZW50LnNvdXJjZSA/IDI5IDogMDsKICAgICAgICAgIGlubmVyID0gewogICAgICAgICAgICB4OiAzNCwKICAgICAgICAgICAgeTogOTIsCiAgICAgICAgICAgIHdpZHRoOiBNYXRoLm1heCg0MCwgYm94LndpZHRoIC0gNjgpLAogICAgICAgICAgICBoZWlnaHQ6IE1hdGgubWF4KDQwLCBib3guaGVpZ2h0IC0gOTIgLSAyNCAtIHNvdXJjZUhlaWdodCksCiAgICAgICAgICB9OwoKICAgICAgICAgIGlmIChlbGVtZW50LnNvdXJjZSkgewogICAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGNhcmQsIHsKICAgICAgICAgICAgICB4OiAzNCwKICAgICAgICAgICAgICB5OiBib3guaGVpZ2h0IC0gMjQgLSAxNywKICAgICAgICAgICAgICB3aWR0aDogTWF0aC5tYXgoNDAsIGJveC53aWR0aCAtIDY4KSwKICAgICAgICAgICAgICB0ZXh0OiAi5pWw5o2u5p2l5rqQ77yaIiArIGVsZW1lbnQuc291cmNlLAogICAgICAgICAgICAgIGZvbnRTaXplOiAxMiwKICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiAxNywKICAgICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgICAgIH0pOwogICAgICAgICAgfQogICAgICAgIH0KCiAgICAgICAgY29uc3QgY2xpcEdyb3VwID0gdGhpcy5wdkdyb3VwKAogICAgICAgICAgY3R4LAogICAgICAgICAgY2FyZCwKICAgICAgICAgIGlubmVyLngsCiAgICAgICAgICBpbm5lci55LAogICAgICAgICAgaW5uZXIud2lkdGgsCiAgICAgICAgICBpbm5lci5oZWlnaHQsCiAgICAgICAgICB7IGNsaXA6IHRydWUgfQogICAgICAgICk7CiAgICAgICAgY29uc3QgY29udGVudCA9IHRoaXMucHZHcm91cCgKICAgICAgICAgIGN0eCwKICAgICAgICAgIGNsaXBHcm91cCwKICAgICAgICAgIDAsCiAgICAgICAgICAwLAogICAgICAgICAgaW5uZXIud2lkdGgsCiAgICAgICAgICBpbm5lci5oZWlnaHQKICAgICAgICApOwoKICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICBjdHguY2hhcnRBcmVhcy5wdXNoKHsKICAgICAgICAgICAga2V5OiB0aGlzLnB2Q2hhcnRLZXkoY3R4LCBlbGVtZW50KSwKICAgICAgICAgICAgeDogYm94LnggKyBpbm5lci54LAogICAgICAgICAgICB5OiBib3gueSArIGlubmVyLnksCiAgICAgICAgICAgIHdpZHRoOiBpbm5lci53aWR0aCwKICAgICAgICAgICAgaGVpZ2h0OiBpbm5lci5oZWlnaHQsCiAgICAgICAgICAgIGNvbnRlbnQsCiAgICAgICAgICAgIGs6IGN0eC5rLAogICAgICAgICAgfSk7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0Q29udGVudCgKICAgICAgICAgIGN0eCwKICAgICAgICAgIGNvbnRlbnQsCiAgICAgICAgICBlbGVtZW50LAogICAgICAgICAgeyB4OiAwLCB5OiAwLCB3aWR0aDogaW5uZXIud2lkdGgsIGhlaWdodDogaW5uZXIuaGVpZ2h0IH0sCiAgICAgICAgICB7IHRodW1iIH0KICAgICAgICApOwoKICAgICAgICB0aGlzLnB2QW5pbWF0ZShjdHgsIGNhcmQsIG9wdHMuZGVsYXksICJ1cCIpOwogICAgICAgIHJldHVybiBjYXJkOwogICAgICB9LAoKICAgICAgcHZEcmF3Q2hhcnRDb250ZW50KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCBvcHRzID0gb3B0aW9ucyB8fCB7fTsKICAgICAgICBjb25zdCB0aHVtYiA9IEJvb2xlYW4ob3B0cy50aHVtYikgfHwgY3R4Lm1vZGUgPT09ICJ0aHVtYiI7CiAgICAgICAgY29uc3Qga2luZCA9IHRoaXMucHZDaGFydEtpbmQoZWxlbWVudCk7CgogICAgICAgIGlmIChraW5kID09PSAiZmxvdyIpIHsKICAgICAgICAgIHRoaXMucHZEcmF3RGlhZ3JhbShjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCB7IHRodW1iIH0pOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgaWYgKGtpbmQgPT09ICJsaW5lIikgewogICAgICAgICAgdGhpcy5wdkRyYXdMaW5lQ2hhcnQoY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgeyB0aHVtYiB9KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CiAgICAgICAgaWYgKGtpbmQgPT09ICJiYXIiKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd0JhckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIHsgdGh1bWIgfSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmIChraW5kID09PSAicGllIikgewogICAgICAgICAgdGhpcy5wdkRyYXdQaWVDaGFydChjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCB7IHRodW1iIH0pOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBpZiAoa2luZCA9PT0gInJhZGFyIikgewogICAgICAgICAgdGhpcy5wdkRyYXdSYWRhckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIHsgdGh1bWIgfSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmIChraW5kID09PSAic2NhdHRlciIpIHsKICAgICAgICAgIHRoaXMucHZEcmF3U2NhdHRlckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIHsgdGh1bWIgfSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0RmFsbGJhY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCk7CiAgICAgIH0sCgogICAgICBwdkRyYXdDaGFydEZhbGxiYWNrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gpIHsKICAgICAgICBjb25zdCBsaW5lcyA9IHRoaXMucHZXcmFwVGV4dCgKICAgICAgICAgIGVsZW1lbnQuY29udGVudCB8fCAi5Zu+6KGo5pWw5o2u5pqC5LiN5Y+v55SoIiwKICAgICAgICAgIE1hdGgubWF4KDYwLCBib3gud2lkdGggLSA0MCksCiAgICAgICAgICB0aGlzLnB2Rm9udCg1MDAsIDIwKSwKICAgICAgICAgIDAsCiAgICAgICAgICAzCiAgICAgICAgKTsKICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHBhcmVudCwgewogICAgICAgICAgeDogMjAsCiAgICAgICAgICB5OiBNYXRoLm1heCgwLCAoYm94LmhlaWdodCAtIGxpbmVzLmxlbmd0aCAqIDMwKSAvIDIpLAogICAgICAgICAgd2lkdGg6IE1hdGgubWF4KDQwLCBib3gud2lkdGggLSA0MCksCiAgICAgICAgICBsaW5lcywKICAgICAgICAgIGZvbnRTaXplOiAyMCwKICAgICAgICAgIGxpbmVIZWlnaHQ6IDMwLAogICAgICAgICAgd2VpZ2h0OiA1MDAsCiAgICAgICAgICBhbGlnbjogImNlbnRlciIsCiAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MudGV4dDMsCiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICBwdkNhcnRlc2lhblBsb3QoYm94LCBwYWRkaW5nKSB7CiAgICAgICAgcmV0dXJuIHsKICAgICAgICAgIHg6IGJveC54ICsgcGFkZGluZy5sZWZ0LAogICAgICAgICAgeTogYm94LnkgKyBwYWRkaW5nLnRvcCwKICAgICAgICAgIHdpZHRoOiBNYXRoLm1heCgxMCwgYm94LndpZHRoIC0gcGFkZGluZy5sZWZ0IC0gcGFkZGluZy5yaWdodCksCiAgICAgICAgICBoZWlnaHQ6IE1hdGgubWF4KDEwLCBib3guaGVpZ2h0IC0gcGFkZGluZy50b3AgLSBwYWRkaW5nLmJvdHRvbSksCiAgICAgICAgfTsKICAgICAgfSwKCiAgICAgIHB2RHJhd0NhcnRlc2lhbkdyaWQoY3R4LCBwYXJlbnQsIHBsb3QsIG1heFZhbHVlLCB0aWNrcywgdGh1bWIpIHsKICAgICAgICBpZiAodGh1bWIpIHsKICAgICAgICAgIHRoaXMucHZMaW5lKAogICAgICAgICAgICBjdHgsCiAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgcGxvdC54LAogICAgICAgICAgICBwbG90LnkgKyBwbG90LmhlaWdodCwKICAgICAgICAgICAgcGxvdC54ICsgcGxvdC53aWR0aCwKICAgICAgICAgICAgcGxvdC55ICsgcGxvdC5oZWlnaHQsCiAgICAgICAgICAgICJyZ2JhKDE2LCAyNCwgNDAsIDAuMDgpIiwKICAgICAgICAgICAgMQogICAgICAgICAgKTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGZvciAobGV0IGluZGV4ID0gMDsgaW5kZXggPD0gdGlja3M7IGluZGV4ICs9IDEpIHsKICAgICAgICAgIGNvbnN0IHJhdGlvID0gaW5kZXggLyB0aWNrczsKICAgICAgICAgIGNvbnN0IHkgPSBwbG90LnkgKyBwbG90LmhlaWdodCAtIHBsb3QuaGVpZ2h0ICogcmF0aW87CiAgICAgICAgICB0aGlzLnB2TGluZSgKICAgICAgICAgICAgY3R4LAogICAgICAgICAgICBwYXJlbnQsCiAgICAgICAgICAgIHBsb3QueCwKICAgICAgICAgICAgeSwKICAgICAgICAgICAgcGxvdC54ICsgcGxvdC53aWR0aCwKICAgICAgICAgICAgeSwKICAgICAgICAgICAgaW5kZXggPT09IDAgPyAicmdiYSgxNiwgMjQsIDQwLCAwLjEyKSIgOiAicmdiYSgxNiwgMjQsIDQwLCAwLjA3KSIsCiAgICAgICAgICAgIDEKICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHBhcmVudCwgewogICAgICAgICAgICB4OiBwbG90LnggLSA1OCwKICAgICAgICAgICAgeTogeSAtIDksCiAgICAgICAgICAgIHdpZHRoOiA0OCwKICAgICAgICAgICAgdGV4dDogdGhpcy5wdkZvcm1hdE51bWJlcihtYXhWYWx1ZSAqIHJhdGlvKSwKICAgICAgICAgICAgZm9udFNpemU6IDEzLAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAxOCwKICAgICAgICAgICAgYWxpZ246ICJyaWdodCIsCiAgICAgICAgICAgIGZpbGw6ICIjOThhMmIzIiwKICAgICAgICAgIH0pOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIHB2RHJhd0NhdGVnb3J5TGFiZWxzKGN0eCwgcGFyZW50LCBwbG90LCBsYWJlbHMsIHRodW1iLCBjZW50ZXJlZCkgewogICAgICAgIGlmICh0aHVtYikgcmV0dXJuOwogICAgICAgIGNvbnN0IGNvdW50ID0gbGFiZWxzLmxlbmd0aDsKICAgICAgICBpZiAoIWNvdW50KSByZXR1cm47CiAgICAgICAgY29uc3Qgc3RlcCA9IE1hdGgubWF4KDEsIE1hdGguY2VpbChjb3VudCAvIDgpKTsKCiAgICAgICAgbGFiZWxzLmZvckVhY2goKGxhYmVsLCBpbmRleCkgPT4gewogICAgICAgICAgaWYgKGluZGV4ICUgc3RlcCAhPT0gMCAmJiBpbmRleCAhPT0gY291bnQgLSAxKSByZXR1cm47CiAgICAgICAgICBjb25zdCB4ID0gY2VudGVyZWQKICAgICAgICAgICAgPyBwbG90LnggKyAoKGluZGV4ICsgMC41KSAvIGNvdW50KSAqIHBsb3Qud2lkdGgKICAgICAgICAgICAgOiBjb3VudCA9PT0gMQogICAgICAgICAgICA/IHBsb3QueCArIHBsb3Qud2lkdGggLyAyCiAgICAgICAgICAgIDogcGxvdC54ICsgKGluZGV4IC8gKGNvdW50IC0gMSkpICogcGxvdC53aWR0aDsKICAgICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5taW4oMTIwLCBNYXRoLm1heCg0OCwgcGxvdC53aWR0aCAvIE1hdGgubWF4KDEsIGNvdW50KSkpOwogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBwYXJlbnQsIHsKICAgICAgICAgICAgeDogeCAtIHdpZHRoIC8gMiwKICAgICAgICAgICAgeTogcGxvdC55ICsgcGxvdC5oZWlnaHQgKyAxMiwKICAgICAgICAgICAgd2lkdGgsCiAgICAgICAgICAgIHRleHQ6IFN0cmluZyhsYWJlbCksCiAgICAgICAgICAgIGZvbnRTaXplOiAxMywKICAgICAgICAgICAgbGluZUhlaWdodDogMTgsCiAgICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgICAgZmlsbDogIiM2NjcwODUiLAogICAgICAgICAgfSk7CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5oqY57q/5Zu+IC8g6Z2i56ev5Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdlNtb290aFBhdGgocG9pbnRzKSB7CiAgICAgICAgaWYgKCFwb2ludHMubGVuZ3RoKSByZXR1cm4gIiI7CiAgICAgICAgaWYgKHBvaW50cy5sZW5ndGggPT09IDEpIHsKICAgICAgICAgIHJldHVybiAiTSAiICsgcG9pbnRzWzBdLnggKyAiICIgKyBwb2ludHNbMF0ueTsKICAgICAgICB9CgogICAgICAgIGxldCBwYXRoID0gIk0gIiArIHBvaW50c1swXS54ICsgIiAiICsgcG9pbnRzWzBdLnk7CgogICAgICAgIGZvciAobGV0IGluZGV4ID0gMDsgaW5kZXggPCBwb2ludHMubGVuZ3RoIC0gMTsgaW5kZXggKz0gMSkgewogICAgICAgICAgY29uc3QgcDAgPSBwb2ludHNbaW5kZXggLSAxXSB8fCBwb2ludHNbaW5kZXhdOwogICAgICAgICAgY29uc3QgcDEgPSBwb2ludHNbaW5kZXhdOwogICAgICAgICAgY29uc3QgcDIgPSBwb2ludHNbaW5kZXggKyAxXTsKICAgICAgICAgIGNvbnN0IHAzID0gcG9pbnRzW2luZGV4ICsgMl0gfHwgcDI7CiAgICAgICAgICBjb25zdCBjMXggPSBwMS54ICsgKHAyLnggLSBwMC54KSAvIDY7CiAgICAgICAgICBjb25zdCBjMXkgPSBwMS55ICsgKHAyLnkgLSBwMC55KSAvIDY7CiAgICAgICAgICBjb25zdCBjMnggPSBwMi54IC0gKHAzLnggLSBwMS54KSAvIDY7CiAgICAgICAgICBjb25zdCBjMnkgPSBwMi55IC0gKHAzLnkgLSBwMS55KSAvIDY7CiAgICAgICAgICBwYXRoICs9CiAgICAgICAgICAgICIgQyAiICsgYzF4ICsgIiAiICsgYzF5ICsgIiAiICsgYzJ4ICsgIiAiICsgYzJ5ICsgIiAiICsgcDIueCArICIgIiArIHAyLnk7CiAgICAgICAgfQoKICAgICAgICByZXR1cm4gcGF0aDsKICAgICAgfSwKCiAgICAgIHB2RHJhd0xpbmVDaGFydChjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBvcHRpb25zKSB7CiAgICAgICAgY29uc3QgdGh1bWIgPSBCb29sZWFuKG9wdGlvbnMgJiYgb3B0aW9ucy50aHVtYik7CiAgICAgICAgY29uc3QgY2hhcnQgPSB0aGlzLnB2Q2hhcnREYXRhKGVsZW1lbnQpOwogICAgICAgIGNvbnN0IGRhdGFzZXRzID0gY2hhcnQuZGF0YXNldHMuZmlsdGVyKAogICAgICAgICAgKGRhdGFzZXQpID0+CiAgICAgICAgICAgIGRhdGFzZXQudmFsdWVzLmxlbmd0aCAmJiBkYXRhc2V0LnZhbHVlcy5zb21lKCh2YWx1ZSkgPT4gaXNGaW5pdGUodmFsdWUpKQogICAgICAgICk7CiAgICAgICAgY29uc3QgcHJpbWFyeSA9IGRhdGFzZXRzWzBdOwoKICAgICAgICBpZiAoIXByaW1hcnkpIHsKICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRGYWxsYmFjayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IG11bHRpID0gZGF0YXNldHMubGVuZ3RoID4gMTsKICAgICAgICBjb25zdCBwYWRkaW5nID0gdGh1bWIKICAgICAgICAgID8geyBsZWZ0OiAxMCwgcmlnaHQ6IDEwLCB0b3A6IDEwLCBib3R0b206IDEwIH0KICAgICAgICAgIDogeyBsZWZ0OiA2NCwgcmlnaHQ6IDI0LCB0b3A6IG11bHRpID8gNDggOiAyMiwgYm90dG9tOiA0NCB9OwogICAgICAgIGNvbnN0IHBsb3QgPSB0aGlzLnB2Q2FydGVzaWFuUGxvdChib3gsIHBhZGRpbmcpOwogICAgICAgIGNvbnN0IG1heFZhbHVlID0gdGhpcy5wdk5pY2VNYXgoCiAgICAgICAgICBNYXRoLm1heC5hcHBseSgKICAgICAgICAgICAgbnVsbCwKICAgICAgICAgICAgZGF0YXNldHMucmVkdWNlKAogICAgICAgICAgICAgIChhbGwsIGRhdGFzZXQpID0+IGFsbC5jb25jYXQoZGF0YXNldC52YWx1ZXMpLAogICAgICAgICAgICAgIFswXQogICAgICAgICAgICApCiAgICAgICAgICApCiAgICAgICAgKTsKICAgICAgICBjb25zdCB0aWNrcyA9IDU7CiAgICAgICAgY29uc3QgY291bnQgPSBNYXRoLm1heC5hcHBseSgKICAgICAgICAgIG51bGwsCiAgICAgICAgICBkYXRhc2V0cy5tYXAoKGRhdGFzZXQpID0+IGRhdGFzZXQudmFsdWVzLmxlbmd0aCkKICAgICAgICApOwoKICAgICAgICB0aGlzLnB2RHJhd0NhcnRlc2lhbkdyaWQoY3R4LCBwYXJlbnQsIHBsb3QsIG1heFZhbHVlLCB0aWNrcywgdGh1bWIpOwogICAgICAgIHRoaXMucHZEcmF3Q2F0ZWdvcnlMYWJlbHMoY3R4LCBwYXJlbnQsIHBsb3QsIGNoYXJ0LmxhYmVscywgdGh1bWIpOwoKICAgICAgICBpZiAobXVsdGkgJiYgIXRodW1iKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd1Nlcmllc0xlZ2VuZChjdHgsIHBhcmVudCwgZGF0YXNldHMsIHBsb3QueCwgYm94LnkgKyAxMCk7CiAgICAgICAgfQoKICAgICAgICBjb25zdCBwb2ludHNGb3IgPSAoZGF0YXNldCkgPT4KICAgICAgICAgIGRhdGFzZXQudmFsdWVzLm1hcCgodmFsdWUsIGluZGV4KSA9PiAoewogICAgICAgICAgICB4OgogICAgICAgICAgICAgIHBsb3QueCArCiAgICAgICAgICAgICAgKGNvdW50ID09PSAxID8gcGxvdC53aWR0aCAvIDIgOiAoaW5kZXggLyAoY291bnQgLSAxKSkgKiBwbG90LndpZHRoKSwKICAgICAgICAgICAgeToKICAgICAgICAgICAgICBwbG90LnkgKwogICAgICAgICAgICAgIHBsb3QuaGVpZ2h0IC0KICAgICAgICAgICAgICAoTWF0aC5tYXgoMCwgdmFsdWUpIC8gbWF4VmFsdWUpICogcGxvdC5oZWlnaHQsCiAgICAgICAgICB9KSk7CgogICAgICAgIGNvbnN0IHByaW1hcnlQb2ludHMgPSBwb2ludHNGb3IocHJpbWFyeSk7CiAgICAgICAgY29uc3QgbGluZVBhdGggPSB0aGlzLnB2U21vb3RoUGF0aChwcmltYXJ5UG9pbnRzKTsKCiAgICAgICAgaWYgKHByaW1hcnkuZmlsbCAhPT0gZmFsc2UpIHsKICAgICAgICAgIGNvbnN0IGFyZWFQYXRoID0KICAgICAgICAgICAgbGluZVBhdGggKwogICAgICAgICAgICAiIEwgIiArCiAgICAgICAgICAgIHByaW1hcnlQb2ludHNbcHJpbWFyeVBvaW50cy5sZW5ndGggLSAxXS54ICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgKHBsb3QueSArIHBsb3QuaGVpZ2h0KSArCiAgICAgICAgICAgICIgTCAiICsKICAgICAgICAgICAgcHJpbWFyeVBvaW50c1swXS54ICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgKHBsb3QueSArIHBsb3QuaGVpZ2h0KSArCiAgICAgICAgICAgICIgWiI7CiAgICAgICAgICB0aGlzLnB2UGF0aChjdHgsIHBhcmVudCwgYXJlYVBhdGgsIHsKICAgICAgICAgICAgdHlwZTogImxpbmVhciIsCiAgICAgICAgICAgIGZyb206ICJ0b3AiLAogICAgICAgICAgICB0bzogImJvdHRvbSIsCiAgICAgICAgICAgIHN0b3BzOiBbCiAgICAgICAgICAgICAgeyBvZmZzZXQ6IDAsIGNvbG9yOiB0aGlzLnB2VGludCh0aGlzLnB2U2VyaWVzQ29sb3IocHJpbWFyeSwgMCksIDAuMTgpIH0sCiAgICAgICAgICAgICAgeyBvZmZzZXQ6IDEsIGNvbG9yOiAiI2ZmZmZmZiIgfSwKICAgICAgICAgICAgXSwKICAgICAgICAgIH0pOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5wdlBhdGgoY3R4LCBwYXJlbnQsIGxpbmVQYXRoLCAibm9uZSIsIHsKICAgICAgICAgIHN0cm9rZTogdGhpcy5wdlNlcmllc0NvbG9yKHByaW1hcnksIDApLAogICAgICAgICAgc3Ryb2tlV2lkdGg6IHRodW1iID8gNSA6IDQsCiAgICAgICAgICBzdHJva2VDYXA6ICJyb3VuZCIsCiAgICAgICAgICBzdHJva2VKb2luOiAicm91bmQiLAogICAgICAgIH0pOwoKICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICBwcmltYXJ5UG9pbnRzLmZvckVhY2goKHBvaW50KSA9PiB7CiAgICAgICAgICAgIHRoaXMucHZFbGxpcHNlKGN0eCwgcGFyZW50LCBwb2ludC54IC0gNCwgcG9pbnQueSAtIDQsIDgsIDgsICIjZmZmZmZmIiwgewogICAgICAgICAgICAgIHN0cm9rZTogdGhpcy5wdlNlcmllc0NvbG9yKHByaW1hcnksIDApLAogICAgICAgICAgICAgIHN0cm9rZVdpZHRoOiAyLAogICAgICAgICAgICB9KTsKICAgICAgICAgIH0pOwogICAgICAgIH0KCiAgICAgICAgZGF0YXNldHMuc2xpY2UoMSkuZm9yRWFjaCgoZGF0YXNldCwgZGF0YXNldEluZGV4KSA9PiB7CiAgICAgICAgICBjb25zdCBwb2ludHMgPSBwb2ludHNGb3IoZGF0YXNldCk7CiAgICAgICAgICB0aGlzLnB2UGF0aChjdHgsIHBhcmVudCwgdGhpcy5wdlNtb290aFBhdGgocG9pbnRzKSwgIm5vbmUiLCB7CiAgICAgICAgICAgIHN0cm9rZTogdGhpcy5wdlNlcmllc0NvbG9yKGRhdGFzZXQsIGRhdGFzZXRJbmRleCArIDEpLAogICAgICAgICAgICBzdHJva2VXaWR0aDogMywKICAgICAgICAgICAgc3Ryb2tlQ2FwOiAicm91bmQiLAogICAgICAgICAgICBzdHJva2VKb2luOiAicm91bmQiLAogICAgICAgICAgfSk7CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5p+x54q25Zu+IC8g5p2h5b2i5Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdkRyYXdNdWx0aUJhckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG9wdGlvbnMsIGNoYXJ0KSB7CiAgICAgICAgY29uc3QgdGh1bWIgPSBCb29sZWFuKG9wdGlvbnMgJiYgb3B0aW9ucy50aHVtYik7CiAgICAgICAgY29uc3QgZGF0YXNldHMgPSBjaGFydC5kYXRhc2V0cy5maWx0ZXIoCiAgICAgICAgICAoZGF0YXNldCkgPT4KICAgICAgICAgICAgZGF0YXNldC52YWx1ZXMubGVuZ3RoICYmIGRhdGFzZXQudmFsdWVzLnNvbWUoKHZhbHVlKSA9PiBpc0Zpbml0ZSh2YWx1ZSkpCiAgICAgICAgKTsKCiAgICAgICAgaWYgKCFkYXRhc2V0cy5sZW5ndGgpIHsKICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRGYWxsYmFjayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IGhvcml6b250YWwgPQogICAgICAgICAgY2hhcnQudHlwZSA9PT0gImhvcml6b250YWxCYXIiIHx8CiAgICAgICAgICBCb29sZWFuKGNoYXJ0LnJhdy5vcHRpb25zICYmIGNoYXJ0LnJhdy5vcHRpb25zLmluZGV4QXhpcyA9PT0gInkiKTsKICAgICAgICBjb25zdCBtYXhWYWx1ZSA9IHRoaXMucHZOaWNlTWF4KAogICAgICAgICAgTWF0aC5tYXguYXBwbHkoCiAgICAgICAgICAgIG51bGwsCiAgICAgICAgICAgIGRhdGFzZXRzLnJlZHVjZSgKICAgICAgICAgICAgICAoYWxsLCBkYXRhc2V0KSA9PiBhbGwuY29uY2F0KGRhdGFzZXQudmFsdWVzKSwKICAgICAgICAgICAgICBbMF0KICAgICAgICAgICAgKQogICAgICAgICAgKQogICAgICAgICk7CiAgICAgICAgY29uc3QgY291bnQgPSBNYXRoLm1heC5hcHBseSgKICAgICAgICAgIG51bGwsCiAgICAgICAgICBkYXRhc2V0cy5tYXAoKGRhdGFzZXQpID0+IGRhdGFzZXQudmFsdWVzLmxlbmd0aCkKICAgICAgICApOwoKICAgICAgICBpZiAoaG9yaXpvbnRhbCkgewogICAgICAgICAgY29uc3QgcGFkZGluZyA9IHRodW1iCiAgICAgICAgICAgID8geyBsZWZ0OiAxMCwgcmlnaHQ6IDEwLCB0b3A6IDEwLCBib3R0b206IDEwIH0KICAgICAgICAgICAgOiB7IGxlZnQ6IDE1MCwgcmlnaHQ6IDQyLCB0b3A6IDQ2LCBib3R0b206IDI2IH07CiAgICAgICAgICBjb25zdCBwbG90ID0gdGhpcy5wdkNhcnRlc2lhblBsb3QoYm94LCBwYWRkaW5nKTsKICAgICAgICAgIGNvbnN0IHNsb3QgPSBwbG90LmhlaWdodCAvIE1hdGgubWF4KDEsIGNvdW50KTsKICAgICAgICAgIGNvbnN0IGdyb3VwSGVpZ2h0ID0gTWF0aC5taW4oc2xvdCAqIDAuNjIsIDEyMCk7CiAgICAgICAgICBjb25zdCBiYXJIZWlnaHQgPSBNYXRoLm1heCgKICAgICAgICAgICAgNSwKICAgICAgICAgICAgTWF0aC5taW4oNTIsIChncm91cEhlaWdodCAvIGRhdGFzZXRzLmxlbmd0aCkgKiAwLjgyKQogICAgICAgICAgKTsKCiAgICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICAgIHRoaXMucHZEcmF3U2VyaWVzTGVnZW5kKGN0eCwgcGFyZW50LCBkYXRhc2V0cywgcGxvdC54LCBib3gueSArIDgpOwogICAgICAgICAgICBmb3IgKGxldCBpbmRleCA9IDA7IGluZGV4IDw9IDQ7IGluZGV4ICs9IDEpIHsKICAgICAgICAgICAgICBjb25zdCB4ID0gcGxvdC54ICsgKHBsb3Qud2lkdGggKiBpbmRleCkgLyA0OwogICAgICAgICAgICAgIHRoaXMucHZMaW5lKAogICAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgICAgcGFyZW50LAogICAgICAgICAgICAgICAgeCwKICAgICAgICAgICAgICAgIHBsb3QueSwKICAgICAgICAgICAgICAgIHgsCiAgICAgICAgICAgICAgICBwbG90LnkgKyBwbG90LmhlaWdodCwKICAgICAgICAgICAgICAgICJyZ2JhKDE2LCAyNCwgNDAsIDAuMDcpIiwKICAgICAgICAgICAgICAgIDEKICAgICAgICAgICAgICApOwogICAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgICAgICB4OiB4IC0gMzIsCiAgICAgICAgICAgICAgICB5OiBwbG90LnkgKyBwbG90LmhlaWdodCArIDEwLAogICAgICAgICAgICAgICAgd2lkdGg6IDY0LAogICAgICAgICAgICAgICAgdGV4dDogdGhpcy5wdkZvcm1hdE51bWJlcigobWF4VmFsdWUgKiBpbmRleCkgLyA0KSwKICAgICAgICAgICAgICAgIGZvbnRTaXplOiAxMiwKICAgICAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE2LAogICAgICAgICAgICAgICAgYWxpZ246ICJjZW50ZXIiLAogICAgICAgICAgICAgICAgZmlsbDogIiM5OGEyYjMiLAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9CgogICAgICAgICAgICBmb3IgKGxldCBpbmRleCA9IDA7IGluZGV4IDwgY291bnQ7IGluZGV4ICs9IDEpIHsKICAgICAgICAgICAgICBpZiAoY2hhcnQubGFiZWxzW2luZGV4XSA9PSBudWxsKSBjb250aW51ZTsKICAgICAgICAgICAgICBjb25zdCByb3dZID0gcGxvdC55ICsgaW5kZXggKiBzbG90ICsgKHNsb3QgLSBncm91cEhlaWdodCkgLyAyOwogICAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgICAgICB4OiBwbG90LnggLSAxMzgsCiAgICAgICAgICAgICAgICB5OiByb3dZICsgKGdyb3VwSGVpZ2h0IC0gMTgpIC8gMiwKICAgICAgICAgICAgICAgIHdpZHRoOiAxMjYsCiAgICAgICAgICAgICAgICB0ZXh0OiBTdHJpbmcoY2hhcnQubGFiZWxzW2luZGV4XSksCiAgICAgICAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiAxOCwKICAgICAgICAgICAgICAgIGFsaWduOiAicmlnaHQiLAogICAgICAgICAgICAgICAgZmlsbDogIiM2NjcwODUiLAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9CiAgICAgICAgICB9CgogICAgICAgICAgZGF0YXNldHMuZm9yRWFjaCgoZGF0YXNldCwgZGF0YXNldEluZGV4KSA9PiB7CiAgICAgICAgICAgIGNvbnN0IGNvbG9yID0gdGhpcy5wdlNlcmllc0NvbG9yKGRhdGFzZXQsIGRhdGFzZXRJbmRleCk7CiAgICAgICAgICAgIGRhdGFzZXQudmFsdWVzLmZvckVhY2goKHZhbHVlLCBpbmRleCkgPT4gewogICAgICAgICAgICAgIGNvbnN0IGdyb3VwWSA9IHBsb3QueSArIGluZGV4ICogc2xvdCArIChzbG90IC0gZ3JvdXBIZWlnaHQpIC8gMjsKICAgICAgICAgICAgICBjb25zdCB5ID0KICAgICAgICAgICAgICAgIGdyb3VwWSArCiAgICAgICAgICAgICAgICBkYXRhc2V0SW5kZXggKiAoZ3JvdXBIZWlnaHQgLyBkYXRhc2V0cy5sZW5ndGgpICsKICAgICAgICAgICAgICAgIChncm91cEhlaWdodCAvIGRhdGFzZXRzLmxlbmd0aCAtIGJhckhlaWdodCkgLyAyOwogICAgICAgICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5tYXgoCiAgICAgICAgICAgICAgICAyLAogICAgICAgICAgICAgICAgKE1hdGgubWF4KDAsIHZhbHVlKSAvIG1heFZhbHVlKSAqIHBsb3Qud2lkdGgKICAgICAgICAgICAgICApOwogICAgICAgICAgICAgIHRoaXMucHZSZWN0KGN0eCwgcGFyZW50LCBwbG90LngsIHksIHdpZHRoLCBiYXJIZWlnaHQsIGNvbG9yLCB7CiAgICAgICAgICAgICAgICBjb3JuZXJSYWRpdXM6IFswLCA2LCA2LCAwXSwKICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IHBhZGRpbmcgPSB0aHVtYgogICAgICAgICAgPyB7IGxlZnQ6IDEwLCByaWdodDogMTAsIHRvcDogMTAsIGJvdHRvbTogMTAgfQogICAgICAgICAgOiB7IGxlZnQ6IDY0LCByaWdodDogMjQsIHRvcDogNDYsIGJvdHRvbTogNDQgfTsKICAgICAgICBjb25zdCBwbG90ID0gdGhpcy5wdkNhcnRlc2lhblBsb3QoYm94LCBwYWRkaW5nKTsKICAgICAgICBjb25zdCBzbG90ID0gcGxvdC53aWR0aCAvIE1hdGgubWF4KDEsIGNvdW50KTsKICAgICAgICBjb25zdCBncm91cFdpZHRoID0gTWF0aC5taW4oc2xvdCAqIDAuNzIsIDE4MCk7CiAgICAgICAgY29uc3QgYmFyV2lkdGggPSBNYXRoLm1heCgKICAgICAgICAgIDQsCiAgICAgICAgICBNYXRoLm1pbig4MCwgKGdyb3VwV2lkdGggLyBkYXRhc2V0cy5sZW5ndGgpICogMC44NCkKICAgICAgICApOwoKICAgICAgICB0aGlzLnB2RHJhd0NhcnRlc2lhbkdyaWQoY3R4LCBwYXJlbnQsIHBsb3QsIG1heFZhbHVlLCA1LCB0aHVtYik7CiAgICAgICAgdGhpcy5wdkRyYXdDYXRlZ29yeUxhYmVscyhjdHgsIHBhcmVudCwgcGxvdCwgY2hhcnQubGFiZWxzLCB0aHVtYiwgdHJ1ZSk7CiAgICAgICAgaWYgKCF0aHVtYikgewogICAgICAgICAgdGhpcy5wdkRyYXdTZXJpZXNMZWdlbmQoY3R4LCBwYXJlbnQsIGRhdGFzZXRzLCBwbG90LngsIGJveC55ICsgOCk7CiAgICAgICAgfQoKICAgICAgICBkYXRhc2V0cy5mb3JFYWNoKChkYXRhc2V0LCBkYXRhc2V0SW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IGNvbG9yID0gdGhpcy5wdlNlcmllc0NvbG9yKGRhdGFzZXQsIGRhdGFzZXRJbmRleCk7CiAgICAgICAgICBkYXRhc2V0LnZhbHVlcy5mb3JFYWNoKCh2YWx1ZSwgaW5kZXgpID0+IHsKICAgICAgICAgICAgY29uc3QgaGVpZ2h0ID0gTWF0aC5tYXgoCiAgICAgICAgICAgICAgMCwKICAgICAgICAgICAgICAoTWF0aC5tYXgoMCwgdmFsdWUpIC8gbWF4VmFsdWUpICogcGxvdC5oZWlnaHQKICAgICAgICAgICAgKTsKICAgICAgICAgICAgY29uc3QgZ3JvdXBYID0gcGxvdC54ICsgaW5kZXggKiBzbG90ICsgKHNsb3QgLSBncm91cFdpZHRoKSAvIDI7CiAgICAgICAgICAgIGNvbnN0IHggPQogICAgICAgICAgICAgIGdyb3VwWCArCiAgICAgICAgICAgICAgZGF0YXNldEluZGV4ICogKGdyb3VwV2lkdGggLyBkYXRhc2V0cy5sZW5ndGgpICsKICAgICAgICAgICAgICAoZ3JvdXBXaWR0aCAvIGRhdGFzZXRzLmxlbmd0aCAtIGJhcldpZHRoKSAvIDI7CiAgICAgICAgICAgIGlmIChoZWlnaHQgPiAwLjUpIHsKICAgICAgICAgICAgICB0aGlzLnB2UmVjdCgKICAgICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgICAgIHgsCiAgICAgICAgICAgICAgICBwbG90LnkgKyBwbG90LmhlaWdodCAtIGhlaWdodCwKICAgICAgICAgICAgICAgIGJhcldpZHRoLAogICAgICAgICAgICAgICAgaGVpZ2h0LAogICAgICAgICAgICAgICAgY29sb3IsCiAgICAgICAgICAgICAgICB7IGNvcm5lclJhZGl1czogWzYsIDYsIDAsIDBdIH0KICAgICAgICAgICAgICApOwogICAgICAgICAgICB9CiAgICAgICAgICB9KTsKICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIHB2RHJhd0JhckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCB0aHVtYiA9IEJvb2xlYW4ob3B0aW9ucyAmJiBvcHRpb25zLnRodW1iKTsKICAgICAgICBjb25zdCBjaGFydCA9IHRoaXMucHZDaGFydERhdGEoZWxlbWVudCk7CiAgICAgICAgaWYgKGNoYXJ0LmRhdGFzZXRzLmxlbmd0aCA+IDEpIHsKICAgICAgICAgIHRoaXMucHZEcmF3TXVsdGlCYXJDaGFydChjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBvcHRpb25zLCBjaGFydCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGNvbnN0IGZpcnN0ID0gY2hhcnQuZGF0YXNldHNbMF07CgogICAgICAgIGlmICgKICAgICAgICAgICFmaXJzdCB8fAogICAgICAgICAgIWZpcnN0LnZhbHVlcy5sZW5ndGggfHwKICAgICAgICAgICFmaXJzdC52YWx1ZXMuc29tZSgodmFsdWUpID0+IGlzRmluaXRlKHZhbHVlKSkKICAgICAgICApIHsKICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRGYWxsYmFjayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IGhvcml6b250YWwgPQogICAgICAgICAgY2hhcnQudHlwZSA9PT0gImhvcml6b250YWxCYXIiIHx8CiAgICAgICAgICBCb29sZWFuKGNoYXJ0LnJhdy5vcHRpb25zICYmIGNoYXJ0LnJhdy5vcHRpb25zLmluZGV4QXhpcyA9PT0gInkiKTsKICAgICAgICBjb25zdCBtYXhWYWx1ZSA9IHRoaXMucHZOaWNlTWF4KAogICAgICAgICAgTWF0aC5tYXguYXBwbHkobnVsbCwgZmlyc3QudmFsdWVzLmNvbmNhdChbMF0pKQogICAgICAgICk7CiAgICAgICAgY29uc3QgaGlnaGxpZ2h0ID0KICAgICAgICAgIHR5cGVvZiBlbGVtZW50LmhpZ2hsaWdodEluZGV4ID09PSAibnVtYmVyIgogICAgICAgICAgICA/IGVsZW1lbnQuaGlnaGxpZ2h0SW5kZXgKICAgICAgICAgICAgOiAtMTsKCiAgICAgICAgaWYgKGhvcml6b250YWwpIHsKICAgICAgICAgIGNvbnN0IHBhZGRpbmcgPSB0aHVtYgogICAgICAgICAgICA/IHsgbGVmdDogMTAsIHJpZ2h0OiAxMCwgdG9wOiAxMCwgYm90dG9tOiAxMCB9CiAgICAgICAgICAgIDogeyBsZWZ0OiAxNTAsIHJpZ2h0OiA0MiwgdG9wOiAyMCwgYm90dG9tOiAyNiB9OwogICAgICAgICAgY29uc3QgcGxvdCA9IHRoaXMucHZDYXJ0ZXNpYW5QbG90KGJveCwgcGFkZGluZyk7CiAgICAgICAgICBjb25zdCBjb3VudCA9IGZpcnN0LnZhbHVlcy5sZW5ndGg7CiAgICAgICAgICBjb25zdCBzbG90ID0gcGxvdC5oZWlnaHQgLyBNYXRoLm1heCgxLCBjb3VudCk7CiAgICAgICAgICBjb25zdCBiYXJIZWlnaHQgPSBNYXRoLm1pbihzbG90ICogMC41NiwgNTgpOwoKICAgICAgICAgIGlmICghdGh1bWIpIHsKICAgICAgICAgICAgZm9yIChsZXQgaW5kZXggPSAwOyBpbmRleCA8PSA0OyBpbmRleCArPSAxKSB7CiAgICAgICAgICAgICAgY29uc3QgeCA9IHBsb3QueCArIChwbG90LndpZHRoICogaW5kZXgpIC8gNDsKICAgICAgICAgICAgICB0aGlzLnB2TGluZSgKICAgICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgICAgIHgsCiAgICAgICAgICAgICAgICBwbG90LnksCiAgICAgICAgICAgICAgICB4LAogICAgICAgICAgICAgICAgcGxvdC55ICsgcGxvdC5oZWlnaHQsCiAgICAgICAgICAgICAgICAicmdiYSgxNiwgMjQsIDQwLCAwLjA3KSIsCiAgICAgICAgICAgICAgICAxCiAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHBhcmVudCwgewogICAgICAgICAgICAgICAgeDogeCAtIDMyLAogICAgICAgICAgICAgICAgeTogcGxvdC55ICsgcGxvdC5oZWlnaHQgKyAxMCwKICAgICAgICAgICAgICAgIHdpZHRoOiA2NCwKICAgICAgICAgICAgICAgIHRleHQ6IHRoaXMucHZGb3JtYXROdW1iZXIoKG1heFZhbHVlICogaW5kZXgpIC8gNCksCiAgICAgICAgICAgICAgICBmb250U2l6ZTogMTIsCiAgICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiAxNiwKICAgICAgICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgICAgICAgIGZpbGw6ICIjOThhMmIzIiwKICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZpcnN0LnZhbHVlcy5mb3JFYWNoKCh2YWx1ZSwgaW5kZXgpID0+IHsKICAgICAgICAgICAgY29uc3QgeSA9IHBsb3QueSArIGluZGV4ICogc2xvdCArIChzbG90IC0gYmFySGVpZ2h0KSAvIDI7CiAgICAgICAgICAgIGNvbnN0IHdpZHRoID0gTWF0aC5tYXgoCiAgICAgICAgICAgICAgMiwKICAgICAgICAgICAgICAoTWF0aC5tYXgoMCwgdmFsdWUpIC8gbWF4VmFsdWUpICogcGxvdC53aWR0aAogICAgICAgICAgICApOwogICAgICAgICAgICBjb25zdCBjb2xvciA9CiAgICAgICAgICAgICAgaGlnaGxpZ2h0ID49IDAKICAgICAgICAgICAgICAgID8gaW5kZXggPT09IGhpZ2hsaWdodAogICAgICAgICAgICAgICAgICA/IHRoaXMuUFZfQy5icmFuZAogICAgICAgICAgICAgICAgICA6ICIjZGZlNmYxIgogICAgICAgICAgICAgICAgOiBmaXJzdC5jb2xvcjsKICAgICAgICAgICAgdGhpcy5wdlJlY3QoY3R4LCBwYXJlbnQsIHBsb3QueCwgeSwgd2lkdGgsIGJhckhlaWdodCwgY29sb3IsIHsKICAgICAgICAgICAgICBjb3JuZXJSYWRpdXM6IFswLCA4LCA4LCAwXSwKICAgICAgICAgICAgfSk7CgogICAgICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBwYXJlbnQsIHsKICAgICAgICAgICAgICAgIHg6IHBsb3QueCAtIDEzOCwKICAgICAgICAgICAgICAgIHk6IHkgKyAoYmFySGVpZ2h0IC0gMTgpIC8gMiwKICAgICAgICAgICAgICAgIHdpZHRoOiAxMjYsCiAgICAgICAgICAgICAgICB0ZXh0OiBTdHJpbmcoY2hhcnQubGFiZWxzW2luZGV4XSA9PSBudWxsID8gIiIgOiBjaGFydC5sYWJlbHNbaW5kZXhdKSwKICAgICAgICAgICAgICAgIGZvbnRTaXplOiAxMywKICAgICAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICAgICAgYWxpZ246ICJyaWdodCIsCiAgICAgICAgICAgICAgICBmaWxsOiAiIzY2NzA4NSIsCiAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBwYXJlbnQsIHsKICAgICAgICAgICAgICAgIHg6IHBsb3QueCArIHdpZHRoICsgMTAsCiAgICAgICAgICAgICAgICB5OiB5ICsgKGJhckhlaWdodCAtIDE4KSAvIDIsCiAgICAgICAgICAgICAgICB3aWR0aDogNzAsCiAgICAgICAgICAgICAgICB0ZXh0OiB0aGlzLnB2Rm9ybWF0TnVtYmVyKHZhbHVlKSwKICAgICAgICAgICAgICAgIGZvbnRTaXplOiAxMiwKICAgICAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICAgICAgZmlsbDogIiM2NjcwODUiLAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9CiAgICAgICAgICB9KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IHBhZGRpbmcgPSB0aHVtYgogICAgICAgICAgPyB7IGxlZnQ6IDEwLCByaWdodDogMTAsIHRvcDogMTAsIGJvdHRvbTogMTAgfQogICAgICAgICAgOiB7IGxlZnQ6IDY0LCByaWdodDogMjQsIHRvcDogMjIsIGJvdHRvbTogNDQgfTsKICAgICAgICBjb25zdCBwbG90ID0gdGhpcy5wdkNhcnRlc2lhblBsb3QoYm94LCBwYWRkaW5nKTsKCiAgICAgICAgdGhpcy5wdkRyYXdDYXJ0ZXNpYW5HcmlkKGN0eCwgcGFyZW50LCBwbG90LCBtYXhWYWx1ZSwgNSwgdGh1bWIpOwogICAgICAgIHRoaXMucHZEcmF3Q2F0ZWdvcnlMYWJlbHMoY3R4LCBwYXJlbnQsIHBsb3QsIGNoYXJ0LmxhYmVscywgdGh1bWIsIHRydWUpOwoKICAgICAgICBjb25zdCBjb3VudCA9IGZpcnN0LnZhbHVlcy5sZW5ndGg7CiAgICAgICAgY29uc3Qgc2xvdCA9IHBsb3Qud2lkdGggLyBNYXRoLm1heCgxLCBjb3VudCk7CiAgICAgICAgY29uc3QgYmFyV2lkdGggPSBNYXRoLm1pbigKICAgICAgICAgIHNsb3QgKiAwLjU4LAogICAgICAgICAgdGh1bWIgPyA2MCA6IDcyCiAgICAgICAgKTsKCiAgICAgICAgZmlyc3QudmFsdWVzLmZvckVhY2goKHZhbHVlLCBpbmRleCkgPT4gewogICAgICAgICAgY29uc3QgaGVpZ2h0ID0gTWF0aC5tYXgoCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIChNYXRoLm1heCgwLCB2YWx1ZSkgLyBtYXhWYWx1ZSkgKiBwbG90LmhlaWdodAogICAgICAgICAgKTsKICAgICAgICAgIGNvbnN0IHggPSBwbG90LnggKyBpbmRleCAqIHNsb3QgKyAoc2xvdCAtIGJhcldpZHRoKSAvIDI7CiAgICAgICAgICBjb25zdCB5ID0gcGxvdC55ICsgcGxvdC5oZWlnaHQgLSBoZWlnaHQ7CiAgICAgICAgICBjb25zdCBjb2xvciA9CiAgICAgICAgICAgIGhpZ2hsaWdodCA+PSAwCiAgICAgICAgICAgICAgPyBpbmRleCA9PT0gaGlnaGxpZ2h0CiAgICAgICAgICAgICAgICA/IHRoaXMuUFZfQy5icmFuZAogICAgICAgICAgICAgICAgOiAiI2RmZTZmMSIKICAgICAgICAgICAgICA6IGZpcnN0LmNvbG9yOwogICAgICAgICAgaWYgKGhlaWdodCA+IDAuNSkgewogICAgICAgICAgICB0aGlzLnB2UmVjdChjdHgsIHBhcmVudCwgeCwgeSwgYmFyV2lkdGgsIGhlaWdodCwgY29sb3IsIHsKICAgICAgICAgICAgICBjb3JuZXJSYWRpdXM6IFs4LCA4LCAwLCAwXSwKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg6aW85Zu+IC8g546v5b2i5Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdkFyY1NsaWNlUGF0aChjeCwgY3ksIG91dGVyLCBpbm5lciwgc3RhcnRBbmdsZSwgZW5kQW5nbGUpIHsKICAgICAgICBjb25zdCBkZWx0YSA9IGVuZEFuZ2xlIC0gc3RhcnRBbmdsZTsKICAgICAgICBpZiAoZGVsdGEgPj0gTWF0aC5QSSAqIDIgLSAwLjAwMSkgewogICAgICAgICAgY29uc3Qgb3V0ZXJDaXJjbGUgPQogICAgICAgICAgICAiTSAiICsKICAgICAgICAgICAgKGN4ICsgb3V0ZXIpICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgY3kgKwogICAgICAgICAgICAiIEEgIiArCiAgICAgICAgICAgIG91dGVyICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgb3V0ZXIgKwogICAgICAgICAgICAiIDAgMSAxICIgKwogICAgICAgICAgICAoY3ggLSBvdXRlcikgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBjeSArCiAgICAgICAgICAgICIgQSAiICsKICAgICAgICAgICAgb3V0ZXIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBvdXRlciArCiAgICAgICAgICAgICIgMCAxIDEgIiArCiAgICAgICAgICAgIChjeCArIG91dGVyKSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIGN5OwogICAgICAgICAgaWYgKGlubmVyIDw9IDApIHJldHVybiBvdXRlckNpcmNsZSArICIgWiI7CiAgICAgICAgICBjb25zdCBpbm5lckNpcmNsZSA9CiAgICAgICAgICAgICIgTSAiICsKICAgICAgICAgICAgKGN4ICsgaW5uZXIpICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgY3kgKwogICAgICAgICAgICAiIEEgIiArCiAgICAgICAgICAgIGlubmVyICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgaW5uZXIgKwogICAgICAgICAgICAiIDAgMSAwICIgKwogICAgICAgICAgICAoY3ggLSBpbm5lcikgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBjeSArCiAgICAgICAgICAgICIgQSAiICsKICAgICAgICAgICAgaW5uZXIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBpbm5lciArCiAgICAgICAgICAgICIgMCAxIDAgIiArCiAgICAgICAgICAgIChjeCArIGlubmVyKSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIGN5OwogICAgICAgICAgcmV0dXJuIG91dGVyQ2lyY2xlICsgaW5uZXJDaXJjbGUgKyAiIFoiOwogICAgICAgIH0KCiAgICAgICAgY29uc3QgbGFyZ2VBcmMgPSBkZWx0YSA+IE1hdGguUEkgPyAxIDogMDsKICAgICAgICBjb25zdCBvdXRlclN0YXJ0ID0gewogICAgICAgICAgeDogY3ggKyBNYXRoLmNvcyhzdGFydEFuZ2xlKSAqIG91dGVyLAogICAgICAgICAgeTogY3kgKyBNYXRoLnNpbihzdGFydEFuZ2xlKSAqIG91dGVyLAogICAgICAgIH07CiAgICAgICAgY29uc3Qgb3V0ZXJFbmQgPSB7CiAgICAgICAgICB4OiBjeCArIE1hdGguY29zKGVuZEFuZ2xlKSAqIG91dGVyLAogICAgICAgICAgeTogY3kgKyBNYXRoLnNpbihlbmRBbmdsZSkgKiBvdXRlciwKICAgICAgICB9OwoKICAgICAgICBpZiAoaW5uZXIgPD0gMCkgewogICAgICAgICAgcmV0dXJuICgKICAgICAgICAgICAgIk0gIiArCiAgICAgICAgICAgIGN4ICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgY3kgKwogICAgICAgICAgICAiIEwgIiArCiAgICAgICAgICAgIG91dGVyU3RhcnQueCArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIG91dGVyU3RhcnQueSArCiAgICAgICAgICAgICIgQSAiICsKICAgICAgICAgICAgb3V0ZXIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBvdXRlciArCiAgICAgICAgICAgICIgMCAiICsKICAgICAgICAgICAgbGFyZ2VBcmMgKwogICAgICAgICAgICAiIDEgIiArCiAgICAgICAgICAgIG91dGVyRW5kLnggKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICBvdXRlckVuZC55ICsKICAgICAgICAgICAgIiBaIgogICAgICAgICAgKTsKICAgICAgICB9CgogICAgICAgIGNvbnN0IGlubmVyU3RhcnQgPSB7CiAgICAgICAgICB4OiBjeCArIE1hdGguY29zKHN0YXJ0QW5nbGUpICogaW5uZXIsCiAgICAgICAgICB5OiBjeSArIE1hdGguc2luKHN0YXJ0QW5nbGUpICogaW5uZXIsCiAgICAgICAgfTsKICAgICAgICBjb25zdCBpbm5lckVuZCA9IHsKICAgICAgICAgIHg6IGN4ICsgTWF0aC5jb3MoZW5kQW5nbGUpICogaW5uZXIsCiAgICAgICAgICB5OiBjeSArIE1hdGguc2luKGVuZEFuZ2xlKSAqIGlubmVyLAogICAgICAgIH07CgogICAgICAgIHJldHVybiAoCiAgICAgICAgICAiTSAiICsKICAgICAgICAgIG91dGVyU3RhcnQueCArCiAgICAgICAgICAiICIgKwogICAgICAgICAgb3V0ZXJTdGFydC55ICsKICAgICAgICAgICIgQSAiICsKICAgICAgICAgIG91dGVyICsKICAgICAgICAgICIgIiArCiAgICAgICAgICBvdXRlciArCiAgICAgICAgICAiIDAgIiArCiAgICAgICAgICBsYXJnZUFyYyArCiAgICAgICAgICAiIDEgIiArCiAgICAgICAgICBvdXRlckVuZC54ICsKICAgICAgICAgICIgIiArCiAgICAgICAgICBvdXRlckVuZC55ICsKICAgICAgICAgICIgTCAiICsKICAgICAgICAgIGlubmVyRW5kLnggKwogICAgICAgICAgIiAiICsKICAgICAgICAgIGlubmVyRW5kLnkgKwogICAgICAgICAgIiBBICIgKwogICAgICAgICAgaW5uZXIgKwogICAgICAgICAgIiAiICsKICAgICAgICAgIGlubmVyICsKICAgICAgICAgICIgMCAiICsKICAgICAgICAgIGxhcmdlQXJjICsKICAgICAgICAgICIgMCAiICsKICAgICAgICAgIGlubmVyU3RhcnQueCArCiAgICAgICAgICAiICIgKwogICAgICAgICAgaW5uZXJTdGFydC55ICsKICAgICAgICAgICIgWiIKICAgICAgICApOwogICAgICB9LAoKICAgICAgcHZEcmF3UGllQ2hhcnQoY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgb3B0aW9ucykgewogICAgICAgIGNvbnN0IHRodW1iID0gQm9vbGVhbihvcHRpb25zICYmIG9wdGlvbnMudGh1bWIpOwogICAgICAgIGNvbnN0IGNoYXJ0ID0gdGhpcy5wdkNoYXJ0RGF0YShlbGVtZW50KTsKICAgICAgICBjb25zdCBmaXJzdCA9IGNoYXJ0LmRhdGFzZXRzWzBdOwogICAgICAgIGNvbnN0IHZhbHVlcyA9IGZpcnN0ID8gZmlyc3QudmFsdWVzLm1hcCgodmFsdWUpID0+IE1hdGgubWF4KDAsIHZhbHVlKSkgOiBbXTsKICAgICAgICBjb25zdCB0b3RhbCA9IHZhbHVlcy5yZWR1Y2UoKHN1bSwgdmFsdWUpID0+IHN1bSArIHZhbHVlLCAwKTsKCiAgICAgICAgaWYgKCF2YWx1ZXMubGVuZ3RoIHx8ICF0b3RhbCkgewogICAgICAgICAgdGhpcy5wdkRyYXdDaGFydEZhbGxiYWNrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgY29uc3QgcmF3VHlwZSA9IGNoYXJ0LnR5cGU7CiAgICAgICAgY29uc3QgaXNEb3VnaG51dCA9IHJhd1R5cGUgPT09ICJkb3VnaG51dCI7CiAgICAgICAgY29uc3Qgc2hvd0xlZ2VuZCA9ICF0aHVtYiAmJiBib3gud2lkdGggPj0gNDYwOwogICAgICAgIGNvbnN0IHBpZVdpZHRoID0gc2hvd0xlZ2VuZCA/IGJveC53aWR0aCAqIDAuNTggOiBib3gud2lkdGg7CiAgICAgICAgY29uc3QgY3ggPSBib3gueCArIHBpZVdpZHRoIC8gMjsKICAgICAgICBjb25zdCBjeSA9IGJveC55ICsgYm94LmhlaWdodCAvIDI7CiAgICAgICAgY29uc3QgcmFkaXVzID0KICAgICAgICAgIE1hdGgubWluKHBpZVdpZHRoLCBib3guaGVpZ2h0KSAqICh0aHVtYiA/IDAuNDQgOiAwLjQpOwogICAgICAgIGNvbnN0IGlubmVyUmFkaXVzID0gaXNEb3VnaG51dCA/IHJhZGl1cyAqIDAuNjIgOiAwOwoKICAgICAgICBsZXQgYW5nbGUgPSAtTWF0aC5QSSAvIDI7CgogICAgICAgIHZhbHVlcy5mb3JFYWNoKCh2YWx1ZSwgaW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IHN3ZWVwID0gKHZhbHVlIC8gdG90YWwpICogTWF0aC5QSSAqIDI7CiAgICAgICAgICBjb25zdCBuZXh0QW5nbGUgPSBhbmdsZSArIHN3ZWVwOwogICAgICAgICAgY29uc3QgcGF0aCA9IHRoaXMucHZBcmNTbGljZVBhdGgoCiAgICAgICAgICAgIGN4LAogICAgICAgICAgICBjeSwKICAgICAgICAgICAgcmFkaXVzLAogICAgICAgICAgICBpbm5lclJhZGl1cywKICAgICAgICAgICAgYW5nbGUsCiAgICAgICAgICAgIG5leHRBbmdsZQogICAgICAgICAgKTsKICAgICAgICAgIHRoaXMucHZQYXRoKAogICAgICAgICAgICBjdHgsCiAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgcGF0aCwKICAgICAgICAgICAgdGhpcy5QVl9QQUxFVFRFW2luZGV4ICUgdGhpcy5QVl9QQUxFVFRFLmxlbmd0aF0sCiAgICAgICAgICAgIHsKICAgICAgICAgICAgICBzdHJva2U6ICIjZmZmZmZmIiwKICAgICAgICAgICAgICBzdHJva2VXaWR0aDogdGh1bWIgPyAyLjUgOiAzLAogICAgICAgICAgICB9CiAgICAgICAgICApOwogICAgICAgICAgYW5nbGUgPSBuZXh0QW5nbGU7CiAgICAgICAgfSk7CgogICAgICAgIGlmIChpc0RvdWdobnV0KSB7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHBhcmVudCwgewogICAgICAgICAgICB4OiBjeCAtIHJhZGl1cyAqIDAuNiwKICAgICAgICAgICAgeTogY3kgLSAyNiwKICAgICAgICAgICAgd2lkdGg6IHJhZGl1cyAqIDEuMiwKICAgICAgICAgICAgdGV4dDogdGhpcy5wdkZvcm1hdE51bWJlcih0b3RhbCksCiAgICAgICAgICAgIGZvbnRTaXplOiB0aHVtYiA/IDI2IDogMzAsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDM0LAogICAgICAgICAgICB3ZWlnaHQ6IDY1MCwKICAgICAgICAgICAgYWxpZ246ICJjZW50ZXIiLAogICAgICAgICAgICBmaWxsOiB0aGlzLlBWX0MuaW5rLAogICAgICAgICAgfSk7CiAgICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgICAgeDogY3ggLSByYWRpdXMgKiAwLjYsCiAgICAgICAgICAgICAgeTogY3kgKyAxMiwKICAgICAgICAgICAgICB3aWR0aDogcmFkaXVzICogMS4yLAogICAgICAgICAgICAgIHRleHQ6ICLmgLvorqEiLAogICAgICAgICAgICAgIGZvbnRTaXplOiAxMiwKICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiAxNiwKICAgICAgICAgICAgICBhbGlnbjogImNlbnRlciIsCiAgICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLnRleHQzLAogICAgICAgICAgICB9KTsKICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIGlmICghc2hvd0xlZ2VuZCkgcmV0dXJuOwoKICAgICAgICBjb25zdCBsZWdlbmRYID0gYm94LnggKyBib3gud2lkdGggKiAwLjYyOwogICAgICAgIGNvbnN0IGl0ZW1HYXAgPSAzMjsKICAgICAgICBjb25zdCBpdGVtSGVpZ2h0ID0gMjA7CiAgICAgICAgY29uc3QgY291bnQgPSBNYXRoLm1pbih2YWx1ZXMubGVuZ3RoLCA3KTsKICAgICAgICBsZXQgbGVnZW5kWSA9CiAgICAgICAgICBib3gueSArIChib3guaGVpZ2h0IC0gY291bnQgKiBpdGVtR2FwICsgKGl0ZW1HYXAgLSBpdGVtSGVpZ2h0KSkgLyAyOwoKICAgICAgICBmb3IgKGxldCBpbmRleCA9IDA7IGluZGV4IDwgY291bnQ7IGluZGV4ICs9IDEpIHsKICAgICAgICAgIGNvbnN0IGxhYmVsID0gU3RyaW5nKGNoYXJ0LmxhYmVsc1tpbmRleF0gPT0gbnVsbCA/ICIiIDogY2hhcnQubGFiZWxzW2luZGV4XSk7CiAgICAgICAgICBjb25zdCBwZXJjZW50ID0gKCh2YWx1ZXNbaW5kZXhdIC8gdG90YWwpICogMTAwKS50b0ZpeGVkKDEpICsgIiUiOwogICAgICAgICAgY29uc3QgY29sb3IgPSB0aGlzLlBWX1BBTEVUVEVbaW5kZXggJSB0aGlzLlBWX1BBTEVUVEUubGVuZ3RoXTsKCiAgICAgICAgICB0aGlzLnB2RWxsaXBzZShjdHgsIHBhcmVudCwgbGVnZW5kWCwgbGVnZW5kWSArIDQsIDksIDksIGNvbG9yKTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgIHg6IGxlZ2VuZFggKyAxOCwKICAgICAgICAgICAgeTogbGVnZW5kWSwKICAgICAgICAgICAgd2lkdGg6IE1hdGgubWF4KDQwLCBib3gueCArIGJveC53aWR0aCAtIGxlZ2VuZFggLSA3NiksCiAgICAgICAgICAgIHRleHQ6IGxhYmVsLAogICAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICBmaWxsOiAiIzQ3NTQ2NyIsCiAgICAgICAgICB9KTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgIHg6IGJveC54ICsgYm94LndpZHRoIC0gNjIsCiAgICAgICAgICAgIHk6IGxlZ2VuZFksCiAgICAgICAgICAgIHdpZHRoOiA2MiwKICAgICAgICAgICAgdGV4dDogcGVyY2VudCwKICAgICAgICAgICAgZm9udFNpemU6IDEyLAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAxOCwKICAgICAgICAgICAgbW9ubzogdHJ1ZSwKICAgICAgICAgICAgYWxpZ246ICJyaWdodCIsCiAgICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy50ZXh0MywKICAgICAgICAgIH0pOwoKICAgICAgICAgIGxlZ2VuZFkgKz0gaXRlbUdhcDsKICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg6Zu36L6+5Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdkRyYXdSYWRhckNoYXJ0KGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCB0aHVtYiA9IEJvb2xlYW4ob3B0aW9ucyAmJiBvcHRpb25zLnRodW1iKTsKICAgICAgICBjb25zdCBjaGFydCA9IHRoaXMucHZDaGFydERhdGEoZWxlbWVudCk7CiAgICAgICAgY29uc3QgZmlyc3QgPSBjaGFydC5kYXRhc2V0c1swXTsKICAgICAgICBjb25zdCBjb3VudCA9IGZpcnN0ID8gZmlyc3QudmFsdWVzLmxlbmd0aCA6IDA7CgogICAgICAgIGlmICghZmlyc3QgfHwgY291bnQgPCAzKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0RmFsbGJhY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBjb25zdCBjeCA9IGJveC54ICsgYm94LndpZHRoIC8gMjsKICAgICAgICBjb25zdCBjeSA9IGJveC55ICsgYm94LmhlaWdodCAvIDI7CiAgICAgICAgY29uc3QgcmFkaXVzID0KICAgICAgICAgIE1hdGgubWluKGJveC53aWR0aCwgYm94LmhlaWdodCkgKiAodGh1bWIgPyAwLjQyIDogMC4zMyk7CiAgICAgICAgY29uc3QgbWF4VmFsdWUgPSB0aGlzLnB2TmljZU1heCgKICAgICAgICAgIE1hdGgubWF4LmFwcGx5KAogICAgICAgICAgICBudWxsLAogICAgICAgICAgICBjaGFydC5kYXRhc2V0cy5yZWR1Y2UoCiAgICAgICAgICAgICAgKGFsbCwgZGF0YXNldCkgPT4gYWxsLmNvbmNhdChkYXRhc2V0LnZhbHVlcyksCiAgICAgICAgICAgICAgWzBdCiAgICAgICAgICAgICkKICAgICAgICAgICkKICAgICAgICApOwogICAgICAgIGNvbnN0IGxldmVscyA9IHRodW1iID8gMyA6IDQ7CgogICAgICAgIGNvbnN0IHBvaW50QXQgPSAoaW5kZXgsIHJhdGlvKSA9PiB7CiAgICAgICAgICBjb25zdCBhbmdsZSA9IC1NYXRoLlBJIC8gMiArIChpbmRleCAvIGNvdW50KSAqIE1hdGguUEkgKiAyOwogICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgeDogY3ggKyBNYXRoLmNvcyhhbmdsZSkgKiByYWRpdXMgKiByYXRpbywKICAgICAgICAgICAgeTogY3kgKyBNYXRoLnNpbihhbmdsZSkgKiByYWRpdXMgKiByYXRpbywKICAgICAgICAgIH07CiAgICAgICAgfTsKCiAgICAgICAgLy8g572R5qC85LiO6L2057q/5YWI57uY5Yi277yM5pWw5o2u5aGr5YWF5ZCO57uY5Yi277yM6YG/5YWN5YWz6Zet6Lev5b6E6YeN5aSN6YCg5oiQ55qECiAgICAgICAgLy8gTGVhZmVyIOWhq+WFhee8k+WtmOeisOaSnuOAggogICAgICAgIGZvciAobGV0IGxldmVsID0gMTsgbGV2ZWwgPD0gbGV2ZWxzOyBsZXZlbCArPSAxKSB7CiAgICAgICAgICBjb25zdCByYXRpbyA9IGxldmVsIC8gbGV2ZWxzOwogICAgICAgICAgZm9yIChsZXQgaW5kZXggPSAwOyBpbmRleCA8IGNvdW50OyBpbmRleCArPSAxKSB7CiAgICAgICAgICAgIGNvbnN0IHN0YXJ0ID0gcG9pbnRBdChpbmRleCwgcmF0aW8pOwogICAgICAgICAgICBjb25zdCBlbmQgPSBwb2ludEF0KChpbmRleCArIDEpICUgY291bnQsIHJhdGlvKTsKICAgICAgICAgICAgdGhpcy5wdkxpbmUoCiAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgICBzdGFydC54LAogICAgICAgICAgICAgIHN0YXJ0LnksCiAgICAgICAgICAgICAgZW5kLngsCiAgICAgICAgICAgICAgZW5kLnksCiAgICAgICAgICAgICAgbGV2ZWwgPT09IGxldmVscwogICAgICAgICAgICAgICAgPyAicmdiYSgxNiwgMjQsIDQwLCAwLjE0KSIKICAgICAgICAgICAgICAgIDogInJnYmEoMTYsIDI0LCA0MCwgMC4wOCkiLAogICAgICAgICAgICAgIDEKICAgICAgICAgICAgKTsKICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIGZvciAobGV0IGluZGV4ID0gMDsgaW5kZXggPCBjb3VudDsgaW5kZXggKz0gMSkgewogICAgICAgICAgY29uc3Qgb3V0ZXJQb2ludCA9IHBvaW50QXQoaW5kZXgsIDEpOwogICAgICAgICAgdGhpcy5wdkxpbmUoCiAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgcGFyZW50LAogICAgICAgICAgICBjeCwKICAgICAgICAgICAgY3ksCiAgICAgICAgICAgIG91dGVyUG9pbnQueCwKICAgICAgICAgICAgb3V0ZXJQb2ludC55LAogICAgICAgICAgICAicmdiYSgxNiwgMjQsIDQwLCAwLjA4KSIsCiAgICAgICAgICAgIDEKICAgICAgICAgICk7CiAgICAgICAgfQoKICAgICAgICBjb25zdCBkYXRhc2V0TGF5b3V0ID0gY2hhcnQuZGF0YXNldHMubWFwKChkYXRhc2V0LCBkYXRhc2V0SW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IGNvbG9yID0KICAgICAgICAgICAgZGF0YXNldC5jb2xvciB8fCB0aGlzLlBWX1BBTEVUVEVbZGF0YXNldEluZGV4ICUgdGhpcy5QVl9QQUxFVFRFLmxlbmd0aF07CiAgICAgICAgICBjb25zdCBwb2ludHMgPSBkYXRhc2V0LnZhbHVlcy5tYXAoKHZhbHVlLCBpbmRleCkgPT4KICAgICAgICAgICAgcG9pbnRBdChpbmRleCwgTWF0aC5taW4oMSwgTWF0aC5tYXgoMCwgdmFsdWUgLyBtYXhWYWx1ZSkpKQogICAgICAgICAgKTsKICAgICAgICAgIHJldHVybiB7CiAgICAgICAgICAgIGNvbG9yLAogICAgICAgICAgICBwb2ludHMsCiAgICAgICAgICAgIHBhdGg6CiAgICAgICAgICAgICAgIk0gIiArCiAgICAgICAgICAgICAgcG9pbnRzLm1hcCgocG9pbnQpID0+IHBvaW50LnggKyAiICIgKyBwb2ludC55KS5qb2luKCIgTCAiKSArCiAgICAgICAgICAgICAgIiBaIE0gIiArCiAgICAgICAgICAgICAgZGF0YXNldEluZGV4ICogMC4wMSArCiAgICAgICAgICAgICAgIiAwIiwKICAgICAgICAgIH07CiAgICAgICAgfSk7CgogICAgICAgIGRhdGFzZXRMYXlvdXQuZm9yRWFjaCgoZGF0YXNldCwgZGF0YXNldEluZGV4KSA9PiB7CiAgICAgICAgICBjb25zdCBpc1ByaW1hcnkgPSBkYXRhc2V0SW5kZXggPT09IDA7CiAgICAgICAgICB0aGlzLnB2UGF0aCgKICAgICAgICAgICAgY3R4LAogICAgICAgICAgICBwYXJlbnQsCiAgICAgICAgICAgIGRhdGFzZXQucGF0aCwKICAgICAgICAgICAgaXNQcmltYXJ5ID8gdGhpcy5wdlRpbnQoZGF0YXNldC5jb2xvciwgMC4xNikgOiAibm9uZSIsCiAgICAgICAgICAgIHsKICAgICAgICAgICAgICBzdHJva2U6IGRhdGFzZXQuY29sb3IsCiAgICAgICAgICAgICAgc3Ryb2tlV2lkdGg6IGlzUHJpbWFyeSA/IDIuNSA6IDIsCiAgICAgICAgICAgIH0KICAgICAgICAgICk7CiAgICAgICAgfSk7CgogICAgICAgIGlmICghdGh1bWIpIHsKICAgICAgICAgIGRhdGFzZXRMYXlvdXQuZm9yRWFjaCgoZGF0YXNldCkgPT4gewogICAgICAgICAgICBkYXRhc2V0LnBvaW50cy5mb3JFYWNoKChwb2ludCkgPT4gewogICAgICAgICAgICAgIHRoaXMucHZFbGxpcHNlKAogICAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgICAgcGFyZW50LAogICAgICAgICAgICAgICAgcG9pbnQueCAtIDMuNSwKICAgICAgICAgICAgICAgIHBvaW50LnkgLSAzLjUsCiAgICAgICAgICAgICAgICA3LAogICAgICAgICAgICAgICAgNywKICAgICAgICAgICAgICAgIGRhdGFzZXQuY29sb3IsCiAgICAgICAgICAgICAgICB7IHN0cm9rZTogIiNmZmZmZmYiLCBzdHJva2VXaWR0aDogMS41IH0KICAgICAgICAgICAgICApOwogICAgICAgICAgICB9KTsKICAgICAgICAgIH0pOwogICAgICAgIH0KCiAgICAgICAgaWYgKCF0aHVtYikgewogICAgICAgICAgY2hhcnQubGFiZWxzLmZvckVhY2goKGxhYmVsLCBpbmRleCkgPT4gewogICAgICAgICAgICBjb25zdCBwb2ludCA9IHBvaW50QXQoaW5kZXgsIDEuMTgpOwogICAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIHBhcmVudCwgewogICAgICAgICAgICAgIHg6IHBvaW50LnggLSA4MCwKICAgICAgICAgICAgICB5OiBwb2ludC55IC0gOSwKICAgICAgICAgICAgICB3aWR0aDogMTYwLAogICAgICAgICAgICAgIHRleHQ6IFN0cmluZyhsYWJlbCksCiAgICAgICAgICAgICAgZm9udFNpemU6IDEzLAogICAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE4LAogICAgICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgICAgICBmaWxsOiAiIzQ3NTQ2NyIsCiAgICAgICAgICAgIH0pOwogICAgICAgICAgfSk7CgogICAgICAgICAgaWYgKGNoYXJ0LmRhdGFzZXRzLmxlbmd0aCA+IDEpIHsKICAgICAgICAgICAgdGhpcy5wdkRyYXdTZXJpZXNMZWdlbmQoCiAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgICBjaGFydC5kYXRhc2V0cywKICAgICAgICAgICAgICBib3gueCArIDE2LAogICAgICAgICAgICAgIGJveC55ICsgMTIKICAgICAgICAgICAgKTsKICAgICAgICAgIH0KICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5pWj54K55Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdkRyYXdTY2F0dGVyQ2hhcnQoY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgb3B0aW9ucykgewogICAgICAgIGNvbnN0IHRodW1iID0gQm9vbGVhbihvcHRpb25zICYmIG9wdGlvbnMudGh1bWIpOwogICAgICAgIGNvbnN0IHJhdyA9IGVsZW1lbnQuY2hhcnREYXRhICYmIGVsZW1lbnQuY2hhcnREYXRhLmRhdGE7CiAgICAgICAgY29uc3QgZGF0YXNldHMgPSAocmF3ICYmIEFycmF5LmlzQXJyYXkocmF3LmRhdGFzZXRzKSA/IHJhdy5kYXRhc2V0cyA6IFtdKS5tYXAoCiAgICAgICAgICAoZGF0YXNldCwgaW5kZXgpID0+IHsKICAgICAgICAgICAgY29uc3QgcG9pbnRzID0gKEFycmF5LmlzQXJyYXkoZGF0YXNldC5kYXRhKSA/IGRhdGFzZXQuZGF0YSA6IFtdKQogICAgICAgICAgICAgIC5tYXAoKHZhbHVlLCBwb2ludEluZGV4KSA9PiB7CiAgICAgICAgICAgICAgICBpZiAodmFsdWUgJiYgdHlwZW9mIHZhbHVlID09PSAib2JqZWN0IikgewogICAgICAgICAgICAgICAgICByZXR1cm4geyB4OiBOdW1iZXIodmFsdWUueCksIHk6IE51bWJlcih2YWx1ZS55KSB9OwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgcmV0dXJuIHsgeDogcG9pbnRJbmRleCwgeTogTnVtYmVyKHZhbHVlKSB9OwogICAgICAgICAgICAgIH0pCiAgICAgICAgICAgICAgLmZpbHRlcigKICAgICAgICAgICAgICAgIChwb2ludCkgPT4gaXNGaW5pdGUocG9pbnQueCkgJiYgaXNGaW5pdGUocG9pbnQueSkKICAgICAgICAgICAgICApOwogICAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICAgIHBvaW50cywKICAgICAgICAgICAgICBjb2xvcjoKICAgICAgICAgICAgICAgIGRhdGFzZXQuYm9yZGVyQ29sb3IgfHwKICAgICAgICAgICAgICAgICh0eXBlb2YgZGF0YXNldC5iYWNrZ3JvdW5kQ29sb3IgPT09ICJzdHJpbmciCiAgICAgICAgICAgICAgICAgID8gZGF0YXNldC5iYWNrZ3JvdW5kQ29sb3IKICAgICAgICAgICAgICAgICAgOiAiIikgfHwKICAgICAgICAgICAgICAgIHRoaXMuUFZfUEFMRVRURVtpbmRleCAlIHRoaXMuUFZfUEFMRVRURS5sZW5ndGhdLAogICAgICAgICAgICB9OwogICAgICAgICAgfQogICAgICAgICk7CgogICAgICAgIGNvbnN0IGFsbFBvaW50cyA9IGRhdGFzZXRzLnJlZHVjZSgKICAgICAgICAgIChhbGwsIGRhdGFzZXQpID0+IGFsbC5jb25jYXQoZGF0YXNldC5wb2ludHMpLAogICAgICAgICAgW10KICAgICAgICApOwoKICAgICAgICBpZiAoIWFsbFBvaW50cy5sZW5ndGgpIHsKICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRGYWxsYmFjayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IHBhZGRpbmcgPSB0aHVtYgogICAgICAgICAgPyB7IGxlZnQ6IDEwLCByaWdodDogMTAsIHRvcDogMTAsIGJvdHRvbTogMTAgfQogICAgICAgICAgOiB7IGxlZnQ6IDY0LCByaWdodDogMjQsIHRvcDogMjIsIGJvdHRvbTogNDAgfTsKICAgICAgICBjb25zdCBwbG90ID0gdGhpcy5wdkNhcnRlc2lhblBsb3QoYm94LCBwYWRkaW5nKTsKICAgICAgICBjb25zdCB4TWluID0gTWF0aC5taW4uYXBwbHkobnVsbCwgYWxsUG9pbnRzLm1hcCgocG9pbnQpID0+IHBvaW50LngpKTsKICAgICAgICBjb25zdCB4TWF4ID0gTWF0aC5tYXguYXBwbHkobnVsbCwgYWxsUG9pbnRzLm1hcCgocG9pbnQpID0+IHBvaW50LngpKTsKICAgICAgICBjb25zdCB5TWluID0gTWF0aC5taW4oMCwgTWF0aC5taW4uYXBwbHkobnVsbCwgYWxsUG9pbnRzLm1hcCgocG9pbnQpID0+IHBvaW50LnkpKSk7CiAgICAgICAgY29uc3QgeU1heCA9IHRoaXMucHZOaWNlTWF4KAogICAgICAgICAgTWF0aC5tYXguYXBwbHkobnVsbCwgYWxsUG9pbnRzLm1hcCgocG9pbnQpID0+IHBvaW50LnkpKQogICAgICAgICk7CiAgICAgICAgY29uc3QgeFNwYW4gPSB4TWF4IC0geE1pbiB8fCAxOwoKICAgICAgICBpZiAoIXRodW1iKSB7CiAgICAgICAgICBmb3IgKGxldCBpbmRleCA9IDA7IGluZGV4IDw9IDQ7IGluZGV4ICs9IDEpIHsKICAgICAgICAgICAgY29uc3QgcmF0aW8gPSBpbmRleCAvIDQ7CiAgICAgICAgICAgIGNvbnN0IHkgPSBwbG90LnkgKyBwbG90LmhlaWdodCAtIHBsb3QuaGVpZ2h0ICogcmF0aW87CiAgICAgICAgICAgIHRoaXMucHZMaW5lKAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICBwYXJlbnQsCiAgICAgICAgICAgICAgcGxvdC54LAogICAgICAgICAgICAgIHksCiAgICAgICAgICAgICAgcGxvdC54ICsgcGxvdC53aWR0aCwKICAgICAgICAgICAgICB5LAogICAgICAgICAgICAgICJyZ2JhKDE2LCAyNCwgNDAsIDAuMDcpIiwKICAgICAgICAgICAgICAxCiAgICAgICAgICAgICk7CiAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgcGFyZW50LCB7CiAgICAgICAgICAgICAgeDogcGxvdC54IC0gNTgsCiAgICAgICAgICAgICAgeTogeSAtIDksCiAgICAgICAgICAgICAgd2lkdGg6IDQ4LAogICAgICAgICAgICAgIHRleHQ6IHRoaXMucHZGb3JtYXROdW1iZXIoeU1heCAqIHJhdGlvKSwKICAgICAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICAgICAgbGluZUhlaWdodDogMTgsCiAgICAgICAgICAgICAgYWxpZ246ICJyaWdodCIsCiAgICAgICAgICAgICAgZmlsbDogIiM5OGEyYjMiLAogICAgICAgICAgICB9KTsKICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIGRhdGFzZXRzLmZvckVhY2goKGRhdGFzZXQpID0+IHsKICAgICAgICAgIGRhdGFzZXQucG9pbnRzLmZvckVhY2goKHBvaW50KSA9PiB7CiAgICAgICAgICAgIGNvbnN0IHggPQogICAgICAgICAgICAgIHBsb3QueCArICgocG9pbnQueCAtIHhNaW4pIC8geFNwYW4pICogcGxvdC53aWR0aDsKICAgICAgICAgICAgY29uc3QgeSA9CiAgICAgICAgICAgICAgcGxvdC55ICsKICAgICAgICAgICAgICBwbG90LmhlaWdodCAtCiAgICAgICAgICAgICAgKE1hdGgubWF4KDAsIHBvaW50LnkpIC8gKHlNYXggfHwgMSkpICogcGxvdC5oZWlnaHQ7CiAgICAgICAgICAgIGNvbnN0IHNpemUgPSB0aHVtYiA/IDggOiAxMDsKICAgICAgICAgICAgdGhpcy5wdkVsbGlwc2UoCiAgICAgICAgICAgICAgY3R4LAogICAgICAgICAgICAgIHBhcmVudCwKICAgICAgICAgICAgICB4IC0gc2l6ZSAvIDIsCiAgICAgICAgICAgICAgeSAtIHNpemUgLyAyLAogICAgICAgICAgICAgIHNpemUsCiAgICAgICAgICAgICAgc2l6ZSwKICAgICAgICAgICAgICBkYXRhc2V0LmNvbG9yLAogICAgICAgICAgICAgIHsgc3Ryb2tlOiAiI2ZmZmZmZiIsIHN0cm9rZVdpZHRoOiAxLjUgfQogICAgICAgICAgICApOwogICAgICAgICAgfSk7CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5YWD57Sg5rWL6YePIC8g5YWc5bqV57uY5Yi2CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBwdk1lYXN1cmVFbGVtZW50KGN0eCwgZWxlbWVudCwgd2lkdGgsIGF2YWlsYWJsZUhlaWdodCkgewogICAgICAgIGlmICghZWxlbWVudCkgcmV0dXJuIHsgaGVpZ2h0OiAwIH07CgogICAgICAgIGlmIChlbGVtZW50LnR5cGUgPT09ICJ0ZXh0IikgewogICAgICAgICAgcmV0dXJuIHRoaXMucHZUZXh0TWV0cmljcyhjdHgsIGVsZW1lbnQsIHdpZHRoLCAibGVhZCIpOwogICAgICAgIH0KICAgICAgICBpZiAoZWxlbWVudC50eXBlID09PSAibGlzdCIpIHsKICAgICAgICAgIHJldHVybiB0aGlzLnB2TGlzdE1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCwgYXZhaWxhYmxlSGVpZ2h0KTsKICAgICAgICB9CiAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gInF1b3RlIikgewogICAgICAgICAgcmV0dXJuIHRoaXMucHZRdW90ZU1ldHJpY3MoY3R4LCBlbGVtZW50LCB3aWR0aCk7CiAgICAgICAgfQogICAgICAgIGlmIChlbGVtZW50LnR5cGUgPT09ICJ0YWJsZSIpIHsKICAgICAgICAgIGNvbnN0IG1ldHJpY3MgPSB0aGlzLnB2VGFibGVNZXRyaWNzKGN0eCwgZWxlbWVudCwgewogICAgICAgICAgICB4OiAwLAogICAgICAgICAgICB5OiAwLAogICAgICAgICAgICB3aWR0aCwKICAgICAgICAgICAgaGVpZ2h0OiBhdmFpbGFibGVIZWlnaHQgfHwgNDgwLAogICAgICAgICAgfSk7CiAgICAgICAgICByZXR1cm4geyBoZWlnaHQ6IG1ldHJpY3MgPyBtZXRyaWNzLmhlaWdodCA6IDAgfTsKICAgICAgICB9CiAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gImNoYXJ0IikgewogICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgaGVpZ2h0OiBNYXRoLm1pbigKICAgICAgICAgICAgICBNYXRoLm1heChjdHguZGVuc2l0eS5jaGFydE1pbiwgMzAwKSwKICAgICAgICAgICAgICBNYXRoLm1heCgyNDAsIGF2YWlsYWJsZUhlaWdodCAqIDAuNTYpCiAgICAgICAgICAgICksCiAgICAgICAgICB9OwogICAgICAgIH0KICAgICAgICBpZiAoZWxlbWVudC50eXBlID09PSAiaW1hZ2UiKSB7CiAgICAgICAgICByZXR1cm4geyBoZWlnaHQ6IE1hdGgubWluKDUyMCwgTWF0aC5tYXgoMjQwLCBhdmFpbGFibGVIZWlnaHQgKiAwLjYpKSB9OwogICAgICAgIH0KCiAgICAgICAgcmV0dXJuIHsgaGVpZ2h0OiAxMjAgfTsKICAgICAgfSwKCiAgICAgIHB2RHJhd0VsZW1lbnQoY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgaW5kZXgsIGRlbGF5KSB7CiAgICAgICAgaWYgKCFlbGVtZW50KSByZXR1cm4gMDsKCiAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gInRleHQiKSB7CiAgICAgICAgICByZXR1cm4gdGhpcy5wdkRyYXdUZXh0QmxvY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgImxlYWQiLCBkZWxheSk7CiAgICAgICAgfQogICAgICAgIGlmIChlbGVtZW50LnR5cGUgPT09ICJsaXN0IikgewogICAgICAgICAgY29uc3QgbWV0cmljcyA9IHRoaXMucHZMaXN0TWV0cmljcyhjdHgsIGVsZW1lbnQsIGJveC53aWR0aCwgYm94LmhlaWdodCk7CiAgICAgICAgICByZXR1cm4gdGhpcy5wdkRyYXdMaXN0QmxvY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCwgbWV0cmljcywgZGVsYXkpOwogICAgICAgIH0KICAgICAgICBpZiAoZWxlbWVudC50eXBlID09PSAicXVvdGUiKSB7CiAgICAgICAgICByZXR1cm4gdGhpcy5wdkRyYXdRdW90ZUJsb2NrKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIGRlbGF5KTsKICAgICAgICB9CiAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gInRhYmxlIikgewogICAgICAgICAgcmV0dXJuIHRoaXMucHZEcmF3VGFibGVCbG9jayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBkZWxheSk7CiAgICAgICAgfQogICAgICAgIGlmIChlbGVtZW50LnR5cGUgPT09ICJjaGFydCIpIHsKICAgICAgICAgIHRoaXMucHZEcmF3Q2hhcnRDYXJkKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIHsgZGVsYXkgfSk7CiAgICAgICAgICByZXR1cm4gYm94LmhlaWdodDsKICAgICAgICB9CiAgICAgICAgaWYgKGVsZW1lbnQudHlwZSA9PT0gImltYWdlIikgewogICAgICAgICAgcmV0dXJuIHRoaXMucHZEcmF3SW1hZ2VCbG9jayhjdHgsIHBhcmVudCwgZWxlbWVudCwgYm94LCBkZWxheSk7CiAgICAgICAgfQoKICAgICAgICByZXR1cm4gMDsKICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICBNZXJtYWlkIOWbvuihqO+8mkxlYWZlciDljp/nlJ/nu5jliLbvvIjkuI3kvp3otZYgbWVybWFpZC5qc++8iQogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcHZTdHJpbmdXaWR0aCh0ZXh0LCBmb250LCBsZXR0ZXJTcGFjaW5nKSB7CiAgICAgICAgY29uc3QgY3R4ID0gdGhpcy5wdk1lYXN1cmVDdHgoKTsKICAgICAgICBjb25zdCB2YWx1ZSA9IFN0cmluZyh0ZXh0ID09IG51bGwgPyAiIiA6IHRleHQpOwogICAgICAgIGlmICghY3R4KSByZXR1cm4gQXJyYXkuZnJvbSh2YWx1ZSkubGVuZ3RoICogMTI7CiAgICAgICAgY3R4LmZvbnQgPSBmb250OwogICAgICAgIHJldHVybiAoCiAgICAgICAgICBjdHgubWVhc3VyZVRleHQodmFsdWUpLndpZHRoICsKICAgICAgICAgIE1hdGgubWF4KDAsIEFycmF5LmZyb20odmFsdWUpLmxlbmd0aCAtIDEpICogKGxldHRlclNwYWNpbmcgfHwgMCkKICAgICAgICApOwogICAgICB9LAoKICAgICAgcHZEaWFncmFtQ29kZShlbGVtZW50KSB7CiAgICAgICAgcmV0dXJuIGVsZW1lbnQgJiYgZWxlbWVudC5jaGFydERhdGEgJiYgZWxlbWVudC5jaGFydERhdGEuY29kZQogICAgICAgICAgPyBTdHJpbmcoZWxlbWVudC5jaGFydERhdGEuY29kZSkKICAgICAgICAgIDogIiI7CiAgICAgIH0sCgogICAgICBwdkRyYXdEaWFncmFtKGN0eCwgcGFyZW50LCBlbGVtZW50LCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCBjb2RlID0gdGhpcy5wdkRpYWdyYW1Db2RlKGVsZW1lbnQpOwogICAgICAgIGNvbnN0IHRodW1iID0gQm9vbGVhbihvcHRpb25zICYmIG9wdGlvbnMudGh1bWIpOwoKICAgICAgICBpZiAoIWNvZGUudHJpbSgpKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd0NoYXJ0RmFsbGJhY2soY3R4LCBwYXJlbnQsIGVsZW1lbnQsIGJveCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAoL15ccyooZmxvd2NoYXJ0fGdyYXBoKVxiL2kudGVzdChjb2RlKSkgewogICAgICAgICAgdGhpcy5wdkRyYXdGbG93Y2hhcnQoY3R4LCBwYXJlbnQsIGNvZGUsIGJveCwgeyB0aHVtYiB9KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CiAgICAgICAgaWYgKC9eXHMqc2VxdWVuY2VEaWFncmFtXGIvaS50ZXN0KGNvZGUpKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd1NlcXVlbmNlKGN0eCwgcGFyZW50LCBjb2RlLCBib3gsIHsgdGh1bWIgfSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmICgvXlxzKnRpbWVsaW5lXGIvaS50ZXN0KGNvZGUpKSB7CiAgICAgICAgICB0aGlzLnB2RHJhd1RpbWVsaW5lKGN0eCwgcGFyZW50LCBjb2RlLCBib3gsIHsgdGh1bWIgfSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmICgvXlxzKm1pbmRtYXBcYi9pLnRlc3QoY29kZSkpIHsKICAgICAgICAgIHRoaXMucHZEcmF3TWluZG1hcChjdHgsIHBhcmVudCwgY29kZSwgYm94LCB7IHRodW1iIH0pOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5wdkRyYXdEaWFncmFtRmFsbGJhY2soY3R4LCBwYXJlbnQsIGNvZGUsIGJveCk7CiAgICAgIH0sCgogICAgICBwdkZpdERpYWdyYW1Hcm91cChjdHgsIGdyb3VwLCBib3gsIG5hdHVyYWxXaWR0aCwgbmF0dXJhbEhlaWdodCwgcGFkZGluZykgewogICAgICAgIGNvbnN0IGluc2V0ID0gcGFkZGluZyA9PSBudWxsID8gMjAgOiBwYWRkaW5nOwogICAgICAgIGNvbnN0IGF2YWlsYWJsZVdpZHRoID0gTWF0aC5tYXgoMjAsIGJveC53aWR0aCAtIGluc2V0ICogMik7CiAgICAgICAgY29uc3QgYXZhaWxhYmxlSGVpZ2h0ID0gTWF0aC5tYXgoMjAsIGJveC5oZWlnaHQgLSBpbnNldCAqIDIpOwogICAgICAgIGNvbnN0IGZpdCA9IE1hdGgubWluKAogICAgICAgICAgMi40LAogICAgICAgICAgYXZhaWxhYmxlV2lkdGggLyBNYXRoLm1heCgxLCBuYXR1cmFsV2lkdGgpLAogICAgICAgICAgYXZhaWxhYmxlSGVpZ2h0IC8gTWF0aC5tYXgoMSwgbmF0dXJhbEhlaWdodCkKICAgICAgICApOwogICAgICAgIGdyb3VwLnNjYWxlWCA9IGZpdDsKICAgICAgICBncm91cC5zY2FsZVkgPSBmaXQ7CiAgICAgICAgZ3JvdXAueCA9CiAgICAgICAgICAoYm94LnggKyAoYm94LndpZHRoIC0gbmF0dXJhbFdpZHRoICogZml0KSAvIDIpICogY3R4Lms7CiAgICAgICAgZ3JvdXAueSA9CiAgICAgICAgICAoYm94LnkgKyAoYm94LmhlaWdodCAtIG5hdHVyYWxIZWlnaHQgKiBmaXQpIC8gMikgKiBjdHguazsKICAgICAgICByZXR1cm4gZml0OwogICAgICB9LAoKICAgICAgcHZTdHJpcE1lcm1haWRMYWJlbChsYWJlbCkgewogICAgICAgIHJldHVybiBTdHJpbmcobGFiZWwgPT0gbnVsbCA/ICIiIDogbGFiZWwpCiAgICAgICAgICAucmVwbGFjZSgvPGJyXHMqXC8/Pi9naSwgIlxuIikKICAgICAgICAgIC5yZXBsYWNlKC9eW1xzIiddK3xbXHMiJ10rJC9nLCAiIikKICAgICAgICAgIC50cmltKCk7CiAgICAgIH0sCgogICAgICAvKiAtLS0tLS0tLS0tIEZsb3djaGFydCAtLS0tLS0tLS0tICovCiAgICAgIHB2UGFyc2VGbG93Y2hhcnQoY29kZSkgewogICAgICAgIGNvbnN0IG5vZGVzID0gbmV3IE1hcCgpOwogICAgICAgIGNvbnN0IGVkZ2VzID0gW107CiAgICAgICAgbGV0IGRpcmVjdGlvbiA9ICJURCI7CgogICAgICAgIGNvbnN0IGVuc3VyZU5vZGUgPSAoaWQsIGxhYmVsKSA9PiB7CiAgICAgICAgICBpZiAoIW5vZGVzLmhhcyhpZCkpIHsKICAgICAgICAgICAgbm9kZXMuc2V0KGlkLCB7CiAgICAgICAgICAgICAgaWQsCiAgICAgICAgICAgICAgbGFiZWw6IGxhYmVsIHx8IGlkLAogICAgICAgICAgICAgIHJhbms6IDAsCiAgICAgICAgICAgICAgd2lkdGg6IDAsCiAgICAgICAgICAgICAgaGVpZ2h0OiAwLAogICAgICAgICAgICAgIGxpbmVzOiBbXSwKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9IGVsc2UgaWYgKGxhYmVsKSB7CiAgICAgICAgICAgIG5vZGVzLmdldChpZCkubGFiZWwgPSBsYWJlbDsKICAgICAgICAgIH0KICAgICAgICB9OwoKICAgICAgICBjb25zdCBwYXJzZU5vZGUgPSAocmF3KSA9PiB7CiAgICAgICAgICBsZXQgdGV4dCA9IFN0cmluZyhyYXcgfHwgIiIpLnRyaW0oKTsKICAgICAgICAgIGlmICghdGV4dCkgcmV0dXJuIG51bGw7CiAgICAgICAgICB0ZXh0ID0gdGV4dC5yZXBsYWNlKC86OjpbXHctXSskLywgIiIpLnRyaW0oKTsKICAgICAgICAgIGNvbnN0IG1hdGNoID0gdGV4dC5tYXRjaCgKICAgICAgICAgICAgL14oW0EtWmEtejAtOV9dW0EtWmEtejAtOV8uLV0qKVxzKig/OlwoXCgoW1xzXFNdKj8pXClcKXxcWyhbXHNcU10qPylcXXxcKChbXHNcU10qPylcKXxceyhbXHNcU10qPylcfSk/XHMqJC8KICAgICAgICAgICk7CiAgICAgICAgICBpZiAoIW1hdGNoKSByZXR1cm4gbnVsbDsKICAgICAgICAgIGNvbnN0IGlkID0gbWF0Y2hbMV07CiAgICAgICAgICBjb25zdCBsYWJlbCA9IHRoaXMucHZTdHJpcE1lcm1haWRMYWJlbCgKICAgICAgICAgICAgbWF0Y2hbMl0gfHwgbWF0Y2hbM10gfHwgbWF0Y2hbNF0gfHwgbWF0Y2hbNV0gfHwgIiIKICAgICAgICAgICk7CiAgICAgICAgICBlbnN1cmVOb2RlKGlkLCBsYWJlbCk7CiAgICAgICAgICByZXR1cm4gaWQ7CiAgICAgICAgfTsKCiAgICAgICAgY29uc3QgbGluZXMgPSBjb2RlLnNwbGl0KC9ccj9cbi8pOwoKICAgICAgICBsaW5lcy5mb3JFYWNoKChyYXdMaW5lKSA9PiB7CiAgICAgICAgICBjb25zdCBsaW5lID0gcmF3TGluZS50cmltKCk7CiAgICAgICAgICBpZiAoIWxpbmUpIHJldHVybjsKICAgICAgICAgIGlmICgvXiUlLy50ZXN0KGxpbmUpKSByZXR1cm47CgogICAgICAgICAgaWYgKC9eKGZsb3djaGFydHxncmFwaClcYi9pLnRlc3QobGluZSkpIHsKICAgICAgICAgICAgY29uc3QgbWF0Y2ggPSBsaW5lLm1hdGNoKC9cYihMUnxSTHxURHxUQnxCVClcYi8pOwogICAgICAgICAgICBpZiAobWF0Y2gpIGRpcmVjdGlvbiA9IG1hdGNoWzFdID09PSAiVEIiID8gIlREIiA6IG1hdGNoWzFdOwogICAgICAgICAgICByZXR1cm47CiAgICAgICAgICB9CgogICAgICAgICAgaWYgKAogICAgICAgICAgICAvXihzdWJncmFwaHxlbmR8Y2xhc3NEZWZ8Y2xhc3N8c3R5bGV8bGlua1N0eWxlfGNsaWNrfGRpcmVjdGlvbilcYi9pLnRlc3QoCiAgICAgICAgICAgICAgbGluZQogICAgICAgICAgICApCiAgICAgICAgICApIHsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQoKICAgICAgICAgIGNvbnN0IGVkZ2VMYWJlbHMgPSBbXTsKICAgICAgICAgIGxldCBub3JtYWxpemVkID0gbGluZS5yZXBsYWNlKC9cfChbXnxdKilcfC9nLCAoYWxsLCBsYWJlbCkgPT4gewogICAgICAgICAgICBlZGdlTGFiZWxzLnB1c2godGhpcy5wdlN0cmlwTWVybWFpZExhYmVsKGxhYmVsKSk7CiAgICAgICAgICAgIHJldHVybiAiICI7CiAgICAgICAgICB9KTsKICAgICAgICAgIG5vcm1hbGl6ZWQgPSBub3JtYWxpemVkLnJlcGxhY2UoCiAgICAgICAgICAgIC9ccy0tXHMqKFteLT58XSs/KVxzKi0tPi9nLAogICAgICAgICAgICAoYWxsLCBsYWJlbCkgPT4gewogICAgICAgICAgICAgIGVkZ2VMYWJlbHMucHVzaCh0aGlzLnB2U3RyaXBNZXJtYWlkTGFiZWwobGFiZWwpKTsKICAgICAgICAgICAgICByZXR1cm4gIiAtLT4gIjsKICAgICAgICAgICAgfQogICAgICAgICAgKTsKCiAgICAgICAgICBjb25zdCBwYXJ0cyA9IG5vcm1hbGl6ZWQKICAgICAgICAgICAgLnNwbGl0KC9ccyooPzotLSs+fD09Kz58LVwuKy0+fC0tK1tveF18LS0tK3wtLSt8PT0rKVxzKi8pCiAgICAgICAgICAgIC5tYXAoKHBhcnQpID0+IHBhcnQudHJpbSgpKQogICAgICAgICAgICAuZmlsdGVyKEJvb2xlYW4pOwoKICAgICAgICAgIGlmIChwYXJ0cy5sZW5ndGggPCAyKSB7CiAgICAgICAgICAgIHBhcnNlTm9kZShwYXJ0c1swXSB8fCAiIik7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KCiAgICAgICAgICBmb3IgKGxldCBpbmRleCA9IDA7IGluZGV4IDwgcGFydHMubGVuZ3RoIC0gMTsgaW5kZXggKz0gMSkgewogICAgICAgICAgICBjb25zdCBmcm9tID0gcGFyc2VOb2RlKHBhcnRzW2luZGV4XSk7CiAgICAgICAgICAgIGNvbnN0IHRvID0gcGFyc2VOb2RlKHBhcnRzW2luZGV4ICsgMV0pOwogICAgICAgICAgICBpZiAoZnJvbSAmJiB0bykgewogICAgICAgICAgICAgIGVkZ2VzLnB1c2goewogICAgICAgICAgICAgICAgZnJvbSwKICAgICAgICAgICAgICAgIHRvLAogICAgICAgICAgICAgICAgbGFiZWw6IGVkZ2VMYWJlbHNbaW5kZXhdIHx8IGVkZ2VMYWJlbHNbZWRnZXMubGVuZ3RoXSB8fCAiIiwKICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgfQogICAgICAgICAgfQogICAgICAgIH0pOwoKICAgICAgICByZXR1cm4gewogICAgICAgICAgZGlyZWN0aW9uLAogICAgICAgICAgbm9kZXM6IEFycmF5LmZyb20obm9kZXMudmFsdWVzKCkpLAogICAgICAgICAgZWRnZXMsCiAgICAgICAgfTsKICAgICAgfSwKCiAgICAgIHB2Rmxvd2NoYXJ0UmFua3MocGFyc2VkKSB7CiAgICAgICAgY29uc3Qgbm9kZXMgPSBwYXJzZWQubm9kZXM7CiAgICAgICAgaWYgKCFub2Rlcy5sZW5ndGgpIHJldHVybiBbXTsKCiAgICAgICAgbm9kZXMuZm9yRWFjaCgobm9kZSkgPT4gewogICAgICAgICAgbm9kZS5yYW5rID0gMDsKICAgICAgICB9KTsKCiAgICAgICAgZm9yIChsZXQgcGFzcyA9IDA7IHBhc3MgPCBub2Rlcy5sZW5ndGg7IHBhc3MgKz0gMSkgewogICAgICAgICAgbGV0IGNoYW5nZWQgPSBmYWxzZTsKICAgICAgICAgIHBhcnNlZC5lZGdlcy5mb3JFYWNoKChlZGdlKSA9PiB7CiAgICAgICAgICAgIGNvbnN0IGZyb20gPSBub2Rlcy5maWx0ZXIoKG5vZGUpID0+IG5vZGUuaWQgPT09IGVkZ2UuZnJvbSlbMF07CiAgICAgICAgICAgIGNvbnN0IHRvID0gbm9kZXMuZmlsdGVyKChub2RlKSA9PiBub2RlLmlkID09PSBlZGdlLnRvKVswXTsKICAgICAgICAgICAgaWYgKCFmcm9tIHx8ICF0bykgcmV0dXJuOwogICAgICAgICAgICBpZiAodG8ucmFuayA8IGZyb20ucmFuayArIDEgJiYgdG8ucmFuayA8IG5vZGVzLmxlbmd0aCkgewogICAgICAgICAgICAgIHRvLnJhbmsgPSBNYXRoLm1pbihub2Rlcy5sZW5ndGggLSAxLCBmcm9tLnJhbmsgKyAxKTsKICAgICAgICAgICAgICBjaGFuZ2VkID0gdHJ1ZTsKICAgICAgICAgICAgfQogICAgICAgICAgfSk7CiAgICAgICAgICBpZiAoIWNoYW5nZWQpIGJyZWFrOwogICAgICAgIH0KCiAgICAgICAgY29uc3QgbWF4UmFuayA9IG5vZGVzLnJlZHVjZSgKICAgICAgICAgIChtYXgsIG5vZGUpID0+IE1hdGgubWF4KG1heCwgbm9kZS5yYW5rKSwKICAgICAgICAgIDAKICAgICAgICApOwogICAgICAgIGNvbnN0IHJhbmtzID0gW107CiAgICAgICAgZm9yIChsZXQgaW5kZXggPSAwOyBpbmRleCA8PSBtYXhSYW5rOyBpbmRleCArPSAxKSB7CiAgICAgICAgICByYW5rcy5wdXNoKFtdKTsKICAgICAgICB9CiAgICAgICAgbm9kZXMuZm9yRWFjaCgobm9kZSkgPT4gewogICAgICAgICAgcmFua3Nbbm9kZS5yYW5rXS5wdXNoKG5vZGUpOwogICAgICAgIH0pOwogICAgICAgIHJldHVybiByYW5rczsKICAgICAgfSwKCiAgICAgIHB2Rmxvd2NoYXJ0Tm9kZU1ldHJpY3MoY3R4LCBub2RlKSB7CiAgICAgICAgY29uc3QgZm9udFNpemUgPSAxODsKICAgICAgICBjb25zdCBsaW5lSGVpZ2h0ID0gMjQ7CiAgICAgICAgY29uc3QgbWF4VGV4dFdpZHRoID0gMjIwOwogICAgICAgIG5vZGUubGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICBub2RlLmxhYmVsIHx8IG5vZGUuaWQsCiAgICAgICAgICBtYXhUZXh0V2lkdGgsCiAgICAgICAgICB0aGlzLnB2Rm9udCg1MDAsIGZvbnRTaXplKSwKICAgICAgICAgIDAsCiAgICAgICAgICAyCiAgICAgICAgKTsKCiAgICAgICAgbGV0IG1heFdpZHRoID0gMDsKICAgICAgICBub2RlLmxpbmVzLmZvckVhY2goKGxpbmUpID0+IHsKICAgICAgICAgIG1heFdpZHRoID0gTWF0aC5tYXgoCiAgICAgICAgICAgIG1heFdpZHRoLAogICAgICAgICAgICB0aGlzLnB2U3RyaW5nV2lkdGgobGluZSwgdGhpcy5wdkZvbnQoNTAwLCBmb250U2l6ZSksIDApCiAgICAgICAgICApOwogICAgICAgIH0pOwoKICAgICAgICBub2RlLndpZHRoID0gTWF0aC5tYXgoMTIwLCBNYXRoLm1pbigyODAsIG1heFdpZHRoICsgNDgpKTsKICAgICAgICBub2RlLmhlaWdodCA9IE1hdGgubWF4KDY0LCBub2RlLmxpbmVzLmxlbmd0aCAqIGxpbmVIZWlnaHQgKyAyOCk7CiAgICAgICAgbm9kZS5mb250U2l6ZSA9IGZvbnRTaXplOwogICAgICAgIG5vZGUubGluZUhlaWdodCA9IGxpbmVIZWlnaHQ7CiAgICAgIH0sCgogICAgICBwdkRyYXdGbG93RWRnZShjdHgsIHBhcmVudCwgZWRnZSwgZnJvbU5vZGUsIHRvTm9kZSwgZGlyZWN0aW9uKSB7CiAgICAgICAgbGV0IHgxOwogICAgICAgIGxldCB5MTsKICAgICAgICBsZXQgeDI7CiAgICAgICAgbGV0IHkyOwogICAgICAgIGxldCBwYXRoOwogICAgICAgIGxldCBhcnJvd0RpcmVjdGlvbjsKCiAgICAgICAgaWYgKGRpcmVjdGlvbiA9PT0gIkxSIiB8fCBkaXJlY3Rpb24gPT09ICJSTCIpIHsKICAgICAgICAgIGNvbnN0IGZvcndhcmQgPSBkaXJlY3Rpb24gPT09ICJMUiI7CiAgICAgICAgICB4MSA9IGZvcndhcmQgPyBmcm9tTm9kZS54ICsgZnJvbU5vZGUud2lkdGggOiBmcm9tTm9kZS54OwogICAgICAgICAgeTEgPSBmcm9tTm9kZS55ICsgZnJvbU5vZGUuaGVpZ2h0IC8gMjsKICAgICAgICAgIHgyID0gZm9yd2FyZCA/IHRvTm9kZS54IDogdG9Ob2RlLnggKyB0b05vZGUud2lkdGg7CiAgICAgICAgICB5MiA9IHRvTm9kZS55ICsgdG9Ob2RlLmhlaWdodCAvIDI7CiAgICAgICAgICBjb25zdCBtaWRkbGUgPSAoeDEgKyB4MikgLyAyOwogICAgICAgICAgcGF0aCA9CiAgICAgICAgICAgICJNICIgKwogICAgICAgICAgICB4MSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIHkxICsKICAgICAgICAgICAgIiBDICIgKwogICAgICAgICAgICBtaWRkbGUgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICB5MSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIG1pZGRsZSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIHkyICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgeDIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICB5MjsKICAgICAgICAgIGFycm93RGlyZWN0aW9uID0gZm9yd2FyZCA/ICJyaWdodCIgOiAibGVmdCI7CiAgICAgICAgfSBlbHNlIHsKICAgICAgICAgIGNvbnN0IGZvcndhcmQgPSBkaXJlY3Rpb24gIT09ICJCVCI7CiAgICAgICAgICB4MSA9IGZyb21Ob2RlLnggKyBmcm9tTm9kZS53aWR0aCAvIDI7CiAgICAgICAgICB5MSA9IGZvcndhcmQgPyBmcm9tTm9kZS55ICsgZnJvbU5vZGUuaGVpZ2h0IDogZnJvbU5vZGUueTsKICAgICAgICAgIHgyID0gdG9Ob2RlLnggKyB0b05vZGUud2lkdGggLyAyOwogICAgICAgICAgeTIgPSBmb3J3YXJkID8gdG9Ob2RlLnkgOiB0b05vZGUueSArIHRvTm9kZS5oZWlnaHQ7CiAgICAgICAgICBjb25zdCBtaWRkbGUgPSAoeTEgKyB5MikgLyAyOwogICAgICAgICAgcGF0aCA9CiAgICAgICAgICAgICJNICIgKwogICAgICAgICAgICB4MSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIHkxICsKICAgICAgICAgICAgIiBDICIgKwogICAgICAgICAgICB4MSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIG1pZGRsZSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIHgyICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgbWlkZGxlICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgeDIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICB5MjsKICAgICAgICAgIGFycm93RGlyZWN0aW9uID0gZm9yd2FyZCA/ICJkb3duIiA6ICJ1cCI7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnB2UGF0aChjdHgsIHBhcmVudCwgcGF0aCwgIm5vbmUiLCB7CiAgICAgICAgICBzdHJva2U6ICIjYTZiMmM0IiwKICAgICAgICAgIHN0cm9rZVdpZHRoOiAxLjYsCiAgICAgICAgICBzdHJva2VDYXA6ICJyb3VuZCIsCiAgICAgICAgfSk7CgogICAgICAgIGNvbnN0IGFycm93U2l6ZSA9IDk7CiAgICAgICAgbGV0IGFycm93UGF0aCA9ICIiOwogICAgICAgIGlmIChhcnJvd0RpcmVjdGlvbiA9PT0gInJpZ2h0IikgewogICAgICAgICAgYXJyb3dQYXRoID0KICAgICAgICAgICAgIk0gIiArCiAgICAgICAgICAgIHgyICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgeTIgKwogICAgICAgICAgICAiIEwgIiArCiAgICAgICAgICAgICh4MiAtIGFycm93U2l6ZSkgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICAoeTIgLSBhcnJvd1NpemUgLyAyKSArCiAgICAgICAgICAgICIgTCAiICsKICAgICAgICAgICAgKHgyIC0gYXJyb3dTaXplKSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgICh5MiArIGFycm93U2l6ZSAvIDIpICsKICAgICAgICAgICAgIiBaIjsKICAgICAgICB9IGVsc2UgaWYgKGFycm93RGlyZWN0aW9uID09PSAibGVmdCIpIHsKICAgICAgICAgIGFycm93UGF0aCA9CiAgICAgICAgICAgICJNICIgKwogICAgICAgICAgICB4MiArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgIHkyICsKICAgICAgICAgICAgIiBMICIgKwogICAgICAgICAgICAoeDIgKyBhcnJvd1NpemUpICsKICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgKHkyIC0gYXJyb3dTaXplIC8gMikgKwogICAgICAgICAgICAiIEwgIiArCiAgICAgICAgICAgICh4MiArIGFycm93U2l6ZSkgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICAoeTIgKyBhcnJvd1NpemUgLyAyKSArCiAgICAgICAgICAgICIgWiI7CiAgICAgICAgfSBlbHNlIGlmIChhcnJvd0RpcmVjdGlvbiA9PT0gImRvd24iKSB7CiAgICAgICAgICBhcnJvd1BhdGggPQogICAgICAgICAgICAiTSAiICsKICAgICAgICAgICAgeDIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICB5MiArCiAgICAgICAgICAgICIgTCAiICsKICAgICAgICAgICAgKHgyIC0gYXJyb3dTaXplIC8gMikgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICAoeTIgLSBhcnJvd1NpemUpICsKICAgICAgICAgICAgIiBMICIgKwogICAgICAgICAgICAoeDIgKyBhcnJvd1NpemUgLyAyKSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgICh5MiAtIGFycm93U2l6ZSkgKwogICAgICAgICAgICAiIFoiOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICBhcnJvd1BhdGggPQogICAgICAgICAgICAiTSAiICsKICAgICAgICAgICAgeDIgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICB5MiArCiAgICAgICAgICAgICIgTCAiICsKICAgICAgICAgICAgKHgyIC0gYXJyb3dTaXplIC8gMikgKwogICAgICAgICAgICAiICIgKwogICAgICAgICAgICAoeTIgKyBhcnJvd1NpemUpICsKICAgICAgICAgICAgIiBMICIgKwogICAgICAgICAgICAoeDIgKyBhcnJvd1NpemUgLyAyKSArCiAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgICh5MiArIGFycm93U2l6ZSkgKwogICAgICAgICAgICAiIFoiOwogICAgICAgIH0KICAgICAgICB0aGlzLnB2UGF0aChjdHgsIHBhcmVudCwgYXJyb3dQYXRoLCAiI2E2YjJjNCIpOwoKICAgICAgICBpZiAoZWRnZS5sYWJlbCkgewogICAgICAgICAgY29uc3QgbWlkZGxlWCA9ICh4MSArIHgyKSAvIDI7CiAgICAgICAgICBjb25zdCBtaWRkbGVZID0gKHkxICsgeTIpIC8gMjsKICAgICAgICAgIGNvbnN0IGxhYmVsTGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICAgIGVkZ2UubGFiZWwsCiAgICAgICAgICAgIDE0MCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNDAwLCAxMiksCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIDEKICAgICAgICAgICk7CiAgICAgICAgICBjb25zdCBsYWJlbFdpZHRoID0gTWF0aC5tYXgoCiAgICAgICAgICAgIDQwLAogICAgICAgICAgICB0aGlzLnB2U3RyaW5nV2lkdGgobGFiZWxMaW5lc1swXSB8fCAiIiwgdGhpcy5wdkZvbnQoNDAwLCAxMiksIDApICsgMTgKICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnB2UmVjdCgKICAgICAgICAgICAgY3R4LAogICAgICAgICAgICBwYXJlbnQsCiAgICAgICAgICAgIG1pZGRsZVggLSBsYWJlbFdpZHRoIC8gMiwKICAgICAgICAgICAgbWlkZGxlWSAtIDExLAogICAgICAgICAgICBsYWJlbFdpZHRoLAogICAgICAgICAgICAyMiwKICAgICAgICAgICAgIiNmZmZmZmYiLAogICAgICAgICAgICB7IGNvcm5lclJhZGl1czogNCB9CiAgICAgICAgICApOwogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBwYXJlbnQsIHsKICAgICAgICAgICAgeDogbWlkZGxlWCAtIGxhYmVsV2lkdGggLyAyLAogICAgICAgICAgICB5OiBtaWRkbGVZIC0gOSwKICAgICAgICAgICAgd2lkdGg6IGxhYmVsV2lkdGgsCiAgICAgICAgICAgIGxpbmVzOiBsYWJlbExpbmVzLAogICAgICAgICAgICBmb250U2l6ZTogMTIsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE2LAogICAgICAgICAgICBhbGlnbjogImNlbnRlciIsCiAgICAgICAgICAgIGZpbGw6ICIjNjY3MDg1IiwKICAgICAgICAgIH0pOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIHB2RHJhd0Zsb3djaGFydChjdHgsIHBhcmVudCwgY29kZSwgYm94LCBvcHRpb25zKSB7CiAgICAgICAgY29uc3QgcGFyc2VkID0gdGhpcy5wdlBhcnNlRmxvd2NoYXJ0KGNvZGUpOwogICAgICAgIGlmICghcGFyc2VkLm5vZGVzLmxlbmd0aCkgewogICAgICAgICAgdGhpcy5wdkRyYXdEaWFncmFtRmFsbGJhY2soY3R4LCBwYXJlbnQsIGNvZGUsIGJveCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBwYXJzZWQubm9kZXMuZm9yRWFjaCgobm9kZSkgPT4gdGhpcy5wdkZsb3djaGFydE5vZGVNZXRyaWNzKGN0eCwgbm9kZSkpOwogICAgICAgIGNvbnN0IHJhbmtzID0gdGhpcy5wdkZsb3djaGFydFJhbmtzKHBhcnNlZCk7CiAgICAgICAgY29uc3QgZGlyZWN0aW9uID0gcGFyc2VkLmRpcmVjdGlvbjsKICAgICAgICBjb25zdCBob3Jpem9udGFsID0gZGlyZWN0aW9uID09PSAiTFIiIHx8IGRpcmVjdGlvbiA9PT0gIlJMIjsKICAgICAgICBjb25zdCByYW5rR2FwID0gaG9yaXpvbnRhbCA/IDk2IDogODQ7CiAgICAgICAgY29uc3Qgbm9kZUdhcCA9IGhvcml6b250YWwgPyAyMiA6IDI4OwogICAgICAgIGNvbnN0IHJhbmtXaWR0aHMgPSByYW5rcy5tYXAoKHJhbmspID0+CiAgICAgICAgICByYW5rLnJlZHVjZSgKICAgICAgICAgICAgKG1heCwgbm9kZSkgPT4KICAgICAgICAgICAgICBNYXRoLm1heChtYXgsIGhvcml6b250YWwgPyBub2RlLndpZHRoIDogbm9kZS53aWR0aCksCiAgICAgICAgICAgIDAKICAgICAgICAgICkKICAgICAgICApOwogICAgICAgIGNvbnN0IHJhbmtIZWlnaHRzID0gcmFua3MubWFwKChyYW5rKSA9PgogICAgICAgICAgcmFuay5yZWR1Y2UoCiAgICAgICAgICAgIChzdW0sIG5vZGUsIGluZGV4KSA9PiBzdW0gKyBub2RlLmhlaWdodCArIChpbmRleCA/IG5vZGVHYXAgOiAwKSwKICAgICAgICAgICAgMAogICAgICAgICAgKQogICAgICAgICk7CgogICAgICAgIGxldCBuYXR1cmFsV2lkdGg7CiAgICAgICAgbGV0IG5hdHVyYWxIZWlnaHQ7CgogICAgICAgIGlmIChob3Jpem9udGFsKSB7CiAgICAgICAgICBuYXR1cmFsV2lkdGggPQogICAgICAgICAgICByYW5rV2lkdGhzLnJlZHVjZSgoc3VtLCB2YWx1ZSkgPT4gc3VtICsgdmFsdWUsIDApICsKICAgICAgICAgICAgTWF0aC5tYXgoMCwgcmFua3MubGVuZ3RoIC0gMSkgKiByYW5rR2FwOwogICAgICAgICAgbmF0dXJhbEhlaWdodCA9IE1hdGgubWF4LmFwcGx5KG51bGwsIHJhbmtIZWlnaHRzLmNvbmNhdChbMF0pKTsKICAgICAgICB9IGVsc2UgewogICAgICAgICAgbmF0dXJhbEhlaWdodCA9CiAgICAgICAgICAgIHJhbmtIZWlnaHRzLnJlZHVjZSgoc3VtLCB2YWx1ZSkgPT4gc3VtICsgdmFsdWUsIDApICsKICAgICAgICAgICAgTWF0aC5tYXgoMCwgcmFua3MubGVuZ3RoIC0gMSkgKiByYW5rR2FwOwogICAgICAgICAgbmF0dXJhbFdpZHRoID0gTWF0aC5tYXguYXBwbHkobnVsbCwgcmFua1dpZHRocy5jb25jYXQoWzBdKSk7CiAgICAgICAgfQoKICAgICAgICBuYXR1cmFsV2lkdGggPSBNYXRoLm1heCgxMjAsIG5hdHVyYWxXaWR0aCk7CiAgICAgICAgbmF0dXJhbEhlaWdodCA9IE1hdGgubWF4KDgwLCBuYXR1cmFsSGVpZ2h0KTsKCiAgICAgICAgY29uc3QgZ3JvdXAgPSB0aGlzLnB2R3JvdXAoY3R4LCBwYXJlbnQsIDAsIDAsIG5hdHVyYWxXaWR0aCwgbmF0dXJhbEhlaWdodCk7CiAgICAgICAgbGV0IGN1cnNvciA9IDA7CgogICAgICAgIHJhbmtzLmZvckVhY2goKHJhbmssIHJhbmtJbmRleCkgPT4gewogICAgICAgICAgY29uc3QgcmFua0hlaWdodCA9IHJhbmtIZWlnaHRzW3JhbmtJbmRleF07CiAgICAgICAgICBjb25zdCByYW5rV2lkdGggPSByYW5rV2lkdGhzW3JhbmtJbmRleF07CiAgICAgICAgICBsZXQgY3Jvc3MgPSAwOwoKICAgICAgICAgIGlmIChob3Jpem9udGFsKSB7CiAgICAgICAgICAgIGNvbnN0IHN0YXJ0WSA9IChuYXR1cmFsSGVpZ2h0IC0gcmFua0hlaWdodCkgLyAyOwogICAgICAgICAgICByYW5rLmZvckVhY2goKG5vZGUpID0+IHsKICAgICAgICAgICAgICBub2RlLnggPSBjdXJzb3I7CiAgICAgICAgICAgICAgbm9kZS55ID0gc3RhcnRZICsgY3Jvc3M7CiAgICAgICAgICAgICAgY3Jvc3MgKz0gbm9kZS5oZWlnaHQgKyBub2RlR2FwOwogICAgICAgICAgICB9KTsKICAgICAgICAgICAgY3Vyc29yICs9IHJhbmtXaWR0aCArIHJhbmtHYXA7CiAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICBjb25zdCBzdGFydFggPSAobmF0dXJhbFdpZHRoIC0gcmFua1dpZHRoKSAvIDI7CiAgICAgICAgICAgIHJhbmsuZm9yRWFjaCgobm9kZSkgPT4gewogICAgICAgICAgICAgIG5vZGUueCA9IHN0YXJ0WCArIGNyb3NzOwogICAgICAgICAgICAgIG5vZGUueSA9IGN1cnNvcjsKICAgICAgICAgICAgICBjcm9zcyArPSBub2RlLndpZHRoICsgbm9kZUdhcDsKICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIGN1cnNvciArPSByYW5rSGVpZ2h0ICsgcmFua0dhcDsKICAgICAgICAgIH0KICAgICAgICB9KTsKCiAgICAgICAgcGFyc2VkLmVkZ2VzLmZvckVhY2goKGVkZ2UpID0+IHsKICAgICAgICAgIGNvbnN0IGZyb21Ob2RlID0gcGFyc2VkLm5vZGVzLmZpbHRlcigobm9kZSkgPT4gbm9kZS5pZCA9PT0gZWRnZS5mcm9tKVswXTsKICAgICAgICAgIGNvbnN0IHRvTm9kZSA9IHBhcnNlZC5ub2Rlcy5maWx0ZXIoKG5vZGUpID0+IG5vZGUuaWQgPT09IGVkZ2UudG8pWzBdOwogICAgICAgICAgaWYgKGZyb21Ob2RlICYmIHRvTm9kZSkgewogICAgICAgICAgICB0aGlzLnB2RHJhd0Zsb3dFZGdlKGN0eCwgZ3JvdXAsIGVkZ2UsIGZyb21Ob2RlLCB0b05vZGUsIGRpcmVjdGlvbik7CiAgICAgICAgICB9CiAgICAgICAgfSk7CgogICAgICAgIHBhcnNlZC5ub2Rlcy5mb3JFYWNoKChub2RlKSA9PiB7CiAgICAgICAgICB0aGlzLnB2UmVjdCgKICAgICAgICAgICAgY3R4LAogICAgICAgICAgICBncm91cCwKICAgICAgICAgICAgbm9kZS54LAogICAgICAgICAgICBub2RlLnksCiAgICAgICAgICAgIG5vZGUud2lkdGgsCiAgICAgICAgICAgIG5vZGUuaGVpZ2h0LAogICAgICAgICAgICAiI2ZmZmZmZiIsCiAgICAgICAgICAgIHsKICAgICAgICAgICAgICBjb3JuZXJSYWRpdXM6IDEwLAogICAgICAgICAgICAgIHN0cm9rZTogIiNjM2QwZTMiLAogICAgICAgICAgICAgIHN0cm9rZVdpZHRoOiAxLjQsCiAgICAgICAgICAgICAgc2hhZG93OiB7CiAgICAgICAgICAgICAgICB4OiAwLAogICAgICAgICAgICAgICAgeTogNCwKICAgICAgICAgICAgICAgIGJsdXI6IDEyLAogICAgICAgICAgICAgICAgY29sb3I6ICJyZ2JhKDE2LCAyNCwgNDAsIDAuMDUpIiwKICAgICAgICAgICAgICB9LAogICAgICAgICAgICB9CiAgICAgICAgICApOwogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBncm91cCwgewogICAgICAgICAgICB4OiBub2RlLnggKyAxMiwKICAgICAgICAgICAgeTogbm9kZS55LAogICAgICAgICAgICB3aWR0aDogbm9kZS53aWR0aCAtIDI0LAogICAgICAgICAgICBoZWlnaHQ6IG5vZGUuaGVpZ2h0LAogICAgICAgICAgICBsaW5lczogbm9kZS5saW5lcywKICAgICAgICAgICAgZm9udFNpemU6IG5vZGUuZm9udFNpemUsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IG5vZGUubGluZUhlaWdodCwKICAgICAgICAgICAgd2VpZ2h0OiA1MDAsCiAgICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgICAgdmFsaWduOiAibWlkZGxlIiwKICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLmluaywKICAgICAgICAgIH0pOwogICAgICAgIH0pOwoKICAgICAgICB0aGlzLnB2Rml0RGlhZ3JhbUdyb3VwKGN0eCwgZ3JvdXAsIGJveCwgbmF0dXJhbFdpZHRoLCBuYXR1cmFsSGVpZ2h0LCAxNik7CiAgICAgIH0sCgogICAgICAvKiAtLS0tLS0tLS0tIFNlcXVlbmNlIC0tLS0tLS0tLS0gKi8KICAgICAgcHZQYXJzZVNlcXVlbmNlKGNvZGUpIHsKICAgICAgICBjb25zdCBwYXJ0aWNpcGFudHMgPSBbXTsKICAgICAgICBjb25zdCBtZXNzYWdlcyA9IFtdOwogICAgICAgIGNvbnN0IGVuc3VyZSA9IChuYW1lKSA9PiB7CiAgICAgICAgICBpZiAobmFtZSAmJiBwYXJ0aWNpcGFudHMuaW5kZXhPZihuYW1lKSA9PT0gLTEpIHBhcnRpY2lwYW50cy5wdXNoKG5hbWUpOwogICAgICAgICAgcmV0dXJuIG5hbWU7CiAgICAgICAgfTsKCiAgICAgICAgY29kZS5zcGxpdCgvXHI/XG4vKS5mb3JFYWNoKChyYXdMaW5lKSA9PiB7CiAgICAgICAgICBjb25zdCBsaW5lID0gcmF3TGluZS50cmltKCk7CiAgICAgICAgICBpZiAoIWxpbmUgfHwgL15zZXF1ZW5jZURpYWdyYW0vaS50ZXN0KGxpbmUpKSByZXR1cm47CiAgICAgICAgICBpZiAoL14lJS8udGVzdChsaW5lKSkgcmV0dXJuOwoKICAgICAgICAgIGxldCBtYXRjaCA9IGxpbmUubWF0Y2goL14oPzpwYXJ0aWNpcGFudHxhY3RvcilccysoW0EtWmEtejAtOV9dKykoPzpccythc1xzKyguKykpPyQvaSk7CiAgICAgICAgICBpZiAobWF0Y2gpIHsKICAgICAgICAgICAgZW5zdXJlKG1hdGNoWzFdKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQoKICAgICAgICAgIG1hdGNoID0gbGluZS5tYXRjaCgvXk5vdGVccysoPzpvdmVyfGxlZnQgb2Z8cmlnaHQgb2YpXHMrKFteOl0rKTpccyooLispJC9pKTsKICAgICAgICAgIGlmIChtYXRjaCkgewogICAgICAgICAgICBjb25zdCBuYW1lcyA9IG1hdGNoWzFdLnNwbGl0KCIsIikubWFwKChuYW1lKSA9PiBlbnN1cmUobmFtZS50cmltKCkpKTsKICAgICAgICAgICAgbWVzc2FnZXMucHVzaCh7IHR5cGU6ICJub3RlIiwgbmFtZXMsIHRleHQ6IG1hdGNoWzJdLnRyaW0oKSB9KTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQoKICAgICAgICAgIG1hdGNoID0gbGluZS5tYXRjaCgKICAgICAgICAgICAgL14oW0EtWmEtejAtOV9dKylccyooLS0+PnwtPj58LS0+fC0+fC0teHwteHwtLVwpfC1cKSlccyooW0EtWmEtejAtOV9dKylccyo6XHMqKC4rKSQvCiAgICAgICAgICApOwogICAgICAgICAgaWYgKG1hdGNoKSB7CiAgICAgICAgICAgIGVuc3VyZShtYXRjaFsxXSk7CiAgICAgICAgICAgIGVuc3VyZShtYXRjaFszXSk7CiAgICAgICAgICAgIG1lc3NhZ2VzLnB1c2goewogICAgICAgICAgICAgIHR5cGU6ICJtZXNzYWdlIiwKICAgICAgICAgICAgICBmcm9tOiBtYXRjaFsxXSwKICAgICAgICAgICAgICB0bzogbWF0Y2hbM10sCiAgICAgICAgICAgICAgZGFzaGVkOiBtYXRjaFsyXS5pbmRleE9mKCItLSIpID09PSAwLAogICAgICAgICAgICAgIHRleHQ6IG1hdGNoWzRdLnRyaW0oKSwKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9CiAgICAgICAgfSk7CgogICAgICAgIHJldHVybiB7IHBhcnRpY2lwYW50cywgbWVzc2FnZXMgfTsKICAgICAgfSwKCiAgICAgIHB2RHJhd1NlcXVlbmNlKGN0eCwgcGFyZW50LCBjb2RlLCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCBwYXJzZWQgPSB0aGlzLnB2UGFyc2VTZXF1ZW5jZShjb2RlKTsKICAgICAgICBjb25zdCBwYXJ0aWNpcGFudHMgPSBwYXJzZWQucGFydGljaXBhbnRzOwoKICAgICAgICBpZiAoIXBhcnRpY2lwYW50cy5sZW5ndGgpIHsKICAgICAgICAgIHRoaXMucHZEcmF3RGlhZ3JhbUZhbGxiYWNrKGN0eCwgcGFyZW50LCBjb2RlLCBib3gpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgY29uc3QgY29sdW1uV2lkdGggPSAyMTA7CiAgICAgICAgY29uc3QgYm94V2lkdGggPSAxNTY7CiAgICAgICAgY29uc3QgYm94SGVpZ2h0ID0gNTQ7CiAgICAgICAgY29uc3Qgc3RhcnRZID0gMTA7CiAgICAgICAgY29uc3QgbWVzc2FnZVN0YXJ0ID0gMTA0OwogICAgICAgIGNvbnN0IG1lc3NhZ2VHYXAgPSA1NjsKICAgICAgICBjb25zdCBuYXR1cmFsV2lkdGggPSBNYXRoLm1heCgzMjAsIHBhcnRpY2lwYW50cy5sZW5ndGggKiBjb2x1bW5XaWR0aCk7CiAgICAgICAgY29uc3QgbmF0dXJhbEhlaWdodCA9IE1hdGgubWF4KAogICAgICAgICAgMjQwLAogICAgICAgICAgbWVzc2FnZVN0YXJ0ICsgcGFyc2VkLm1lc3NhZ2VzLmxlbmd0aCAqIG1lc3NhZ2VHYXAgKyAzMAogICAgICAgICk7CiAgICAgICAgY29uc3QgcG9zaXRpb25zID0ge307CgogICAgICAgIHBhcnRpY2lwYW50cy5mb3JFYWNoKChuYW1lLCBpbmRleCkgPT4gewogICAgICAgICAgcG9zaXRpb25zW25hbWVdID0gewogICAgICAgICAgICB4OiAoaW5kZXggKyAwLjUpICogY29sdW1uV2lkdGgsCiAgICAgICAgICB9OwogICAgICAgIH0pOwoKICAgICAgICBjb25zdCBncm91cCA9IHRoaXMucHZHcm91cChjdHgsIHBhcmVudCwgMCwgMCwgbmF0dXJhbFdpZHRoLCBuYXR1cmFsSGVpZ2h0KTsKCiAgICAgICAgLy8g55Sf5ZG957q/CiAgICAgICAgcGFydGljaXBhbnRzLmZvckVhY2goKG5hbWUpID0+IHsKICAgICAgICAgIGNvbnN0IHggPSBwb3NpdGlvbnNbbmFtZV0ueDsKICAgICAgICAgIHRoaXMucHZMaW5lKAogICAgICAgICAgICBjdHgsCiAgICAgICAgICAgIGdyb3VwLAogICAgICAgICAgICB4LAogICAgICAgICAgICBzdGFydFkgKyBib3hIZWlnaHQsCiAgICAgICAgICAgIHgsCiAgICAgICAgICAgIG5hdHVyYWxIZWlnaHQgLSAxMCwKICAgICAgICAgICAgInJnYmEoMTYsIDI0LCA0MCwgMC4xMikiLAogICAgICAgICAgICAxLjIsCiAgICAgICAgICAgIHsgZGFzaFBhdHRlcm46IFs1LCA1XSB9CiAgICAgICAgICApOwogICAgICAgIH0pOwoKICAgICAgICAvLyDlj4LkuI7ogIUKICAgICAgICBwYXJ0aWNpcGFudHMuZm9yRWFjaCgobmFtZSkgPT4gewogICAgICAgICAgY29uc3QgeCA9IHBvc2l0aW9uc1tuYW1lXS54OwogICAgICAgICAgdGhpcy5wdlJlY3QoCiAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgIHggLSBib3hXaWR0aCAvIDIsCiAgICAgICAgICAgIHN0YXJ0WSwKICAgICAgICAgICAgYm94V2lkdGgsCiAgICAgICAgICAgIGJveEhlaWdodCwKICAgICAgICAgICAgIiNmZmZmZmYiLAogICAgICAgICAgICB7CiAgICAgICAgICAgICAgY29ybmVyUmFkaXVzOiA4LAogICAgICAgICAgICAgIHN0cm9rZTogIiNjM2QwZTMiLAogICAgICAgICAgICAgIHN0cm9rZVdpZHRoOiAxLjQsCiAgICAgICAgICAgIH0KICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGdyb3VwLCB7CiAgICAgICAgICAgIHg6IHggLSBib3hXaWR0aCAvIDIgKyA4LAogICAgICAgICAgICB5OiBzdGFydFksCiAgICAgICAgICAgIHdpZHRoOiBib3hXaWR0aCAtIDE2LAogICAgICAgICAgICBoZWlnaHQ6IGJveEhlaWdodCwKICAgICAgICAgICAgdGV4dDogbmFtZSwKICAgICAgICAgICAgZm9udFNpemU6IDE1LAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAyMCwKICAgICAgICAgICAgd2VpZ2h0OiA1MDAsCiAgICAgICAgICAgIGFsaWduOiAiY2VudGVyIiwKICAgICAgICAgICAgdmFsaWduOiAibWlkZGxlIiwKICAgICAgICAgICAgZmlsbDogdGhpcy5QVl9DLmluaywKICAgICAgICAgIH0pOwogICAgICAgIH0pOwoKICAgICAgICAvLyDmtojmga8KICAgICAgICBsZXQgeSA9IG1lc3NhZ2VTdGFydDsKICAgICAgICBwYXJzZWQubWVzc2FnZXMuZm9yRWFjaCgobWVzc2FnZSkgPT4gewogICAgICAgICAgaWYgKG1lc3NhZ2UudHlwZSA9PT0gIm5vdGUiKSB7CiAgICAgICAgICAgIGNvbnN0IHhzID0gbWVzc2FnZS5uYW1lcy5tYXAoKG5hbWUpID0+IHBvc2l0aW9uc1tuYW1lXS54KTsKICAgICAgICAgICAgY29uc3QgbWluWCA9IE1hdGgubWluLmFwcGx5KG51bGwsIHhzKSAtIDkwOwogICAgICAgICAgICBjb25zdCBtYXhYID0gTWF0aC5tYXguYXBwbHkobnVsbCwgeHMpICsgOTA7CiAgICAgICAgICAgIHRoaXMucHZSZWN0KAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICBncm91cCwKICAgICAgICAgICAgICBtaW5YLAogICAgICAgICAgICAgIHkgLSAyMiwKICAgICAgICAgICAgICBtYXhYIC0gbWluWCwKICAgICAgICAgICAgICA0NCwKICAgICAgICAgICAgICAiI2ZmZmFmMCIsCiAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgY29ybmVyUmFkaXVzOiA2LAogICAgICAgICAgICAgICAgc3Ryb2tlOiAiI2YyZDlhNCIsCiAgICAgICAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICAgICAgICB9CiAgICAgICAgICAgICk7CiAgICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZ3JvdXAsIHsKICAgICAgICAgICAgICB4OiBtaW5YICsgMTAsCiAgICAgICAgICAgICAgeTogeSAtIDIyLAogICAgICAgICAgICAgIHdpZHRoOiBtYXhYIC0gbWluWCAtIDIwLAogICAgICAgICAgICAgIGhlaWdodDogNDQsCiAgICAgICAgICAgICAgdGV4dDogbWVzc2FnZS50ZXh0LAogICAgICAgICAgICAgIGZvbnRTaXplOiAxMywKICAgICAgICAgICAgICBsaW5lSGVpZ2h0OiAxOCwKICAgICAgICAgICAgICBhbGlnbjogImNlbnRlciIsCiAgICAgICAgICAgICAgdmFsaWduOiAibWlkZGxlIiwKICAgICAgICAgICAgICBmaWxsOiAiIzdhNWIxNyIsCiAgICAgICAgICAgIH0pOwogICAgICAgICAgICB5ICs9IG1lc3NhZ2VHYXA7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KCiAgICAgICAgICBjb25zdCBmcm9tWCA9IHBvc2l0aW9uc1ttZXNzYWdlLmZyb21dLng7CiAgICAgICAgICBjb25zdCB0b1ggPSBwb3NpdGlvbnNbbWVzc2FnZS50b10ueDsKICAgICAgICAgIGNvbnN0IHNlbGYgPSBtZXNzYWdlLmZyb20gPT09IG1lc3NhZ2UudG87CiAgICAgICAgICBsZXQgbGluZVN0YXJ0ID0gZnJvbVg7CiAgICAgICAgICBsZXQgbGluZUVuZCA9IHRvWDsKCiAgICAgICAgICBpZiAoc2VsZikgewogICAgICAgICAgICBsaW5lU3RhcnQgPSBmcm9tWDsKICAgICAgICAgICAgbGluZUVuZCA9IGZyb21YICsgNDQ7CiAgICAgICAgICAgIHRoaXMucHZMaW5lKGN0eCwgZ3JvdXAsIGxpbmVTdGFydCwgeSwgbGluZUVuZCwgeSwgIiM4ZDliYjAiLCAxLjQpOwogICAgICAgICAgICB0aGlzLnB2TGluZShjdHgsIGdyb3VwLCBsaW5lRW5kLCB5LCBsaW5lRW5kLCB5ICsgMTgsICIjOGQ5YmIwIiwgMS40KTsKICAgICAgICAgICAgdGhpcy5wdkxpbmUoY3R4LCBncm91cCwgbGluZUVuZCwgeSArIDE4LCBsaW5lU3RhcnQsIHkgKyAxOCwgIiM4ZDliYjAiLCAxLjQpOwogICAgICAgICAgICB5ICs9IDE4OwogICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgY29uc3QgZGlyZWN0aW9uID0gdG9YID49IGZyb21YID8gMSA6IC0xOwogICAgICAgICAgICB0aGlzLnB2TGluZSgKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgICAgbGluZVN0YXJ0LAogICAgICAgICAgICAgIHksCiAgICAgICAgICAgICAgbGluZUVuZCAtIGRpcmVjdGlvbiAqIDgsCiAgICAgICAgICAgICAgeSwKICAgICAgICAgICAgICAiIzhkOWJiMCIsCiAgICAgICAgICAgICAgMS40LAogICAgICAgICAgICAgIG1lc3NhZ2UuZGFzaGVkID8geyBkYXNoUGF0dGVybjogWzYsIDVdIH0gOiB7fQogICAgICAgICAgICApOwogICAgICAgICAgICB0aGlzLnB2UGF0aCgKICAgICAgICAgICAgICBjdHgsCiAgICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgICAgIk0gIiArCiAgICAgICAgICAgICAgICBsaW5lRW5kICsKICAgICAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgICAgICB5ICsKICAgICAgICAgICAgICAgICIgTCAiICsKICAgICAgICAgICAgICAgIChsaW5lRW5kIC0gZGlyZWN0aW9uICogOSkgKwogICAgICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgICAgICh5IC0gNSkgKwogICAgICAgICAgICAgICAgIiBMICIgKwogICAgICAgICAgICAgICAgKGxpbmVFbmQgLSBkaXJlY3Rpb24gKiA5KSArCiAgICAgICAgICAgICAgICAiICIgKwogICAgICAgICAgICAgICAgKHkgKyA1KSArCiAgICAgICAgICAgICAgICAiIFoiLAogICAgICAgICAgICAgICIjOGQ5YmIwIgogICAgICAgICAgICApOwogICAgICAgICAgfQoKICAgICAgICAgIGNvbnN0IGxhYmVsWCA9IE1hdGgubWluKGZyb21YLCB0b1gpICsgKHNlbGYgPyA1MCA6IDApOwogICAgICAgICAgY29uc3QgbGFiZWxXaWR0aCA9IHNlbGYKICAgICAgICAgICAgPyAxNzAKICAgICAgICAgICAgOiBNYXRoLm1heCg5MCwgTWF0aC5hYnModG9YIC0gZnJvbVgpIC0gMzApOwogICAgICAgICAgY29uc3QgbGFiZWxGb250ID0gdGhpcy5wdkZvbnQoNDAwLCAxMyk7CiAgICAgICAgICBjb25zdCBsYWJlbExpbmVzID0gdGhpcy5wdldyYXBUZXh0KG1lc3NhZ2UudGV4dCwgbGFiZWxXaWR0aCwgbGFiZWxGb250LCAwLCAyKTsKICAgICAgICAgIHRoaXMucHZUZXh0KGN0eCwgZ3JvdXAsIHsKICAgICAgICAgICAgeDogc2VsZiA/IGxhYmVsWCA6IE1hdGgubWluKGZyb21YLCB0b1gpICsgMTAsCiAgICAgICAgICAgIHk6IHkgLSAyNCwKICAgICAgICAgICAgd2lkdGg6IGxhYmVsV2lkdGgsCiAgICAgICAgICAgIGxpbmVzOiBsYWJlbExpbmVzLAogICAgICAgICAgICBmb250U2l6ZTogMTMsCiAgICAgICAgICAgIGxpbmVIZWlnaHQ6IDE3LAogICAgICAgICAgICBhbGlnbjogc2VsZiA/ICJsZWZ0IiA6ICJjZW50ZXIiLAogICAgICAgICAgICBmaWxsOiAiIzQ3NTQ2NyIsCiAgICAgICAgICB9KTsKCiAgICAgICAgICB5ICs9IG1lc3NhZ2VHYXA7CiAgICAgICAgfSk7CgogICAgICAgIHRoaXMucHZGaXREaWFncmFtR3JvdXAoY3R4LCBncm91cCwgYm94LCBuYXR1cmFsV2lkdGgsIG5hdHVyYWxIZWlnaHQsIDE2KTsKICAgICAgfSwKCiAgICAgIC8qIC0tLS0tLS0tLS0gVGltZWxpbmUgLS0tLS0tLS0tLSAqLwogICAgICBwdlBhcnNlVGltZWxpbmUoY29kZSkgewogICAgICAgIGNvbnN0IGVudHJpZXMgPSBbXTsKICAgICAgICBsZXQgdGl0bGUgPSAiIjsKCiAgICAgICAgY29kZS5zcGxpdCgvXHI/XG4vKS5mb3JFYWNoKChyYXdMaW5lKSA9PiB7CiAgICAgICAgICBjb25zdCBsaW5lID0gcmF3TGluZS50cmltKCk7CiAgICAgICAgICBpZiAoIWxpbmUgfHwgL150aW1lbGluZVxiL2kudGVzdChsaW5lKSkgcmV0dXJuOwogICAgICAgICAgaWYgKC9edGl0bGVccysvaS50ZXN0KGxpbmUpKSB7CiAgICAgICAgICAgIHRpdGxlID0gbGluZS5yZXBsYWNlKC9edGl0bGVccysvaSwgIiIpLnRyaW0oKTsKICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgfQogICAgICAgICAgaWYgKC9ec2VjdGlvblxzKy9pLnRlc3QobGluZSkpIHJldHVybjsKCiAgICAgICAgICBjb25zdCBtYXRjaCA9IGxpbmUubWF0Y2goL14oW146XSs/KVxzKjpccyooLispJC8pOwogICAgICAgICAgaWYgKG1hdGNoKSB7CiAgICAgICAgICAgIGVudHJpZXMucHVzaCh7CiAgICAgICAgICAgICAgcGVyaW9kOiBtYXRjaFsxXS50cmltKCksCiAgICAgICAgICAgICAgdGV4dDogbWF0Y2hbMl0udHJpbSgpLAogICAgICAgICAgICB9KTsKICAgICAgICAgIH0KICAgICAgICB9KTsKCiAgICAgICAgcmV0dXJuIHsgdGl0bGUsIGVudHJpZXMgfTsKICAgICAgfSwKCiAgICAgIHB2RHJhd1RpbWVsaW5lKGN0eCwgcGFyZW50LCBjb2RlLCBib3gsIG9wdGlvbnMpIHsKICAgICAgICBjb25zdCBwYXJzZWQgPSB0aGlzLnB2UGFyc2VUaW1lbGluZShjb2RlKTsKICAgICAgICBjb25zdCBlbnRyaWVzID0gcGFyc2VkLmVudHJpZXM7CgogICAgICAgIGlmICghZW50cmllcy5sZW5ndGgpIHsKICAgICAgICAgIHRoaXMucHZEcmF3RGlhZ3JhbUZhbGxiYWNrKGN0eCwgcGFyZW50LCBjb2RlLCBib3gpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgY29uc3QgaXRlbVdpZHRoID0gMjUwOwogICAgICAgIGNvbnN0IG5hdHVyYWxXaWR0aCA9IGVudHJpZXMubGVuZ3RoICogaXRlbVdpZHRoICsgMTAwOwogICAgICAgIGNvbnN0IG5hdHVyYWxIZWlnaHQgPSAzODA7CiAgICAgICAgY29uc3QgYXhpc1kgPSBuYXR1cmFsSGVpZ2h0IC8gMjsKICAgICAgICBjb25zdCBncm91cCA9IHRoaXMucHZHcm91cChjdHgsIHBhcmVudCwgMCwgMCwgbmF0dXJhbFdpZHRoLCBuYXR1cmFsSGVpZ2h0KTsKCiAgICAgICAgdGhpcy5wdkxpbmUoCiAgICAgICAgICBjdHgsCiAgICAgICAgICBncm91cCwKICAgICAgICAgIDQwLAogICAgICAgICAgYXhpc1ksCiAgICAgICAgICBuYXR1cmFsV2lkdGggLSA0MCwKICAgICAgICAgIGF4aXNZLAogICAgICAgICAgdGhpcy5QVl9DLmxpbmVTdHJvbmcsCiAgICAgICAgICAyCiAgICAgICAgKTsKCiAgICAgICAgZW50cmllcy5mb3JFYWNoKChlbnRyeSwgaW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IHggPSA1MCArIGluZGV4ICogaXRlbVdpZHRoICsgaXRlbVdpZHRoIC8gMjsKICAgICAgICAgIGNvbnN0IGFib3ZlID0gaW5kZXggJSAyID09PSAwOwogICAgICAgICAgY29uc3QgY2FyZFdpZHRoID0gaXRlbVdpZHRoIC0gNDI7CiAgICAgICAgICBjb25zdCBjYXJkSGVpZ2h0ID0gMTEyOwogICAgICAgICAgY29uc3QgY2FyZFkgPSBhYm92ZSA/IGF4aXNZIC0gNDAgLSBjYXJkSGVpZ2h0IDogYXhpc1kgKyA0MDsKICAgICAgICAgIGNvbnN0IGxpbmVZID0gYWJvdmUgPyBheGlzWSAtIDQwIDogYXhpc1kgKyA0MDsKCiAgICAgICAgICB0aGlzLnB2TGluZShjdHgsIGdyb3VwLCB4LCBheGlzWSwgeCwgbGluZVksIHRoaXMuUFZfQy5saW5lU3Ryb25nLCAxLjIpOwogICAgICAgICAgdGhpcy5wdkVsbGlwc2UoY3R4LCBncm91cCwgeCAtIDYsIGF4aXNZIC0gNiwgMTIsIDEyLCB0aGlzLlBWX0MuYnJhbmQsIHsKICAgICAgICAgICAgc3Ryb2tlOiAiI2ZmZmZmZiIsCiAgICAgICAgICAgIHN0cm9rZVdpZHRoOiAyLAogICAgICAgICAgfSk7CgogICAgICAgICAgdGhpcy5wdlJlY3QoCiAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgZ3JvdXAsCiAgICAgICAgICAgIHggLSBjYXJkV2lkdGggLyAyLAogICAgICAgICAgICBjYXJkWSwKICAgICAgICAgICAgY2FyZFdpZHRoLAogICAgICAgICAgICBjYXJkSGVpZ2h0LAogICAgICAgICAgICB0aGlzLlBWX0Muc3VyZmFjZSwKICAgICAgICAgICAgewogICAgICAgICAgICAgIGNvcm5lclJhZGl1czogMTAsCiAgICAgICAgICAgICAgc3Ryb2tlOiB0aGlzLlBWX0MubGluZSwKICAgICAgICAgICAgICBzdHJva2VXaWR0aDogMSwKICAgICAgICAgICAgICBzaGFkb3c6IHsKICAgICAgICAgICAgICAgIHg6IDAsCiAgICAgICAgICAgICAgICB5OiA2LAogICAgICAgICAgICAgICAgYmx1cjogMTYsCiAgICAgICAgICAgICAgICBjb2xvcjogInJnYmEoMTYsIDI0LCA0MCwgMC4wNSkiLAogICAgICAgICAgICAgIH0sCiAgICAgICAgICAgIH0KICAgICAgICAgICk7CgogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBncm91cCwgewogICAgICAgICAgICB4OiB4IC0gY2FyZFdpZHRoIC8gMiArIDEyLAogICAgICAgICAgICB5OiBjYXJkWSArIDEyLAogICAgICAgICAgICB3aWR0aDogY2FyZFdpZHRoIC0gMjQsCiAgICAgICAgICAgIHRleHQ6IGVudHJ5LnBlcmlvZCwKICAgICAgICAgICAgZm9udFNpemU6IDE4LAogICAgICAgICAgICBsaW5lSGVpZ2h0OiAyMiwKICAgICAgICAgICAgd2VpZ2h0OiA2NTAsCiAgICAgICAgICAgIGZpbGw6IHRoaXMuUFZfQy5icmFuZCwKICAgICAgICAgIH0pOwoKICAgICAgICAgIGNvbnN0IGxpbmVzID0gdGhpcy5wdldyYXBUZXh0KAogICAgICAgICAgICBlbnRyeS50ZXh0LAogICAgICAgICAgICBjYXJkV2lkdGggLSAyNCwKICAgICAgICAgICAgdGhpcy5wdkZvbnQoNDAwLCAxNCksCiAgICAgICAgICAgIDAsCiAgICAgICAgICAgIDMKICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnB2VGV4dChjdHgsIGdyb3VwLCB7CiAgICAgICAgICAgIHg6IHggLSBjYXJkV2lkdGggLyAyICsgMTIsCiAgICAgICAgICAgIHk6IGNhcmRZICsgNDAsCiAgICAgICAgICAgIHdpZHRoOiBjYXJkV2lkdGggLSAyNCwKICAgICAgICAgICAgbGluZXMsCiAgICAgICAgICAgIGZvbnRTaXplOiAxNCwKICAgICAgICAgICAgbGluZUhlaWdodDogMjAsCiAgICAgICAgICAgIGZpbGw6ICIjNDc1NDY3IiwKICAgICAgICAgIH0pOwogICAgICAgIH0pOwoKICAgICAgICB0aGlzLnB2Rml0RGlhZ3JhbUdyb3VwKGN0eCwgZ3JvdXAsIGJveCwgbmF0dXJhbFdpZHRoLCBuYXR1cmFsSGVpZ2h0LCAxNCk7CiAgICAgIH0sCgogICAgICAvKiAtLS0tLS0tLS0tIE1pbmRtYXAgLS0tLS0tLS0tLSAqLwogICAgICBwdk1pbmROb2RlVGV4dChyYXcpIHsKICAgICAgICBsZXQgdGV4dCA9IFN0cmluZyhyYXcgfHwgIiIpLnRyaW0oKTsKICAgICAgICB0ZXh0ID0gdGV4dC5yZXBsYWNlKC9eW0EtWmEtejAtOV9dK1xzKig/PVwoXCh8XFt8XHt8XCgpLywgIiIpOwogICAgICAgIHRleHQgPSB0ZXh0CiAgICAgICAgICAucmVwbGFjZSgvXlwoXCh8XClcKSQvZywgIiIpCiAgICAgICAgICAucmVwbGFjZSgvXlxbfFxdJC9nLCAiIikKICAgICAgICAgIC5yZXBsYWNlKC9eXCh8XCkkL2csICIiKQogICAgICAgICAgLnJlcGxhY2UoL15ce3xcfSQvZywgIiIpOwogICAgICAgIHJldHVybiB0aGlzLnB2U3RyaXBNZXJtYWlkTGFiZWwodGV4dCk7CiAgICAgIH0sCgogICAgICBwdlBhcnNlTWluZG1hcChjb2RlKSB7CiAgICAgICAgY29uc3QgbGluZXMgPSBjb2RlLnNwbGl0KC9ccj9cbi8pOwogICAgICAgIGNvbnN0IGl0ZW1zID0gW107CgogICAgICAgIGxpbmVzLmZvckVhY2goKHJhd0xpbmUpID0+IHsKICAgICAgICAgIGlmICghcmF3TGluZS50cmltKCkpIHJldHVybjsKICAgICAgICAgIGlmICgvXm1pbmRtYXBcYi9pLnRlc3QocmF3TGluZS50cmltKCkpKSByZXR1cm47CiAgICAgICAgICBpZiAoL14lJS8udGVzdChyYXdMaW5lLnRyaW0oKSkpIHJldHVybjsKICAgICAgICAgIGNvbnN0IGluZGVudCA9IChyYXdMaW5lLm1hdGNoKC9eXHMqLykgfHwgWyIiXSlbMF0ubGVuZ3RoOwogICAgICAgICAgaXRlbXMucHVzaCh7IGluZGVudCwgdGV4dDogdGhpcy5wdk1pbmROb2RlVGV4dChyYXdMaW5lLnRyaW0oKSkgfSk7CiAgICAgICAgfSk7CgogICAgICAgIGlmICghaXRlbXMubGVuZ3RoKSByZXR1cm4gbnVsbDsKCiAgICAgICAgY29uc3Qgcm9vdCA9IHsKICAgICAgICAgIHRleHQ6IGl0ZW1zWzBdLnRleHQgfHwgIuS4u+mimCIsCiAgICAgICAgICBjaGlsZHJlbjogW10sCiAgICAgICAgICBpbmRlbnQ6IGl0ZW1zWzBdLmluZGVudCwKICAgICAgICAgIGxldmVsOiAwLAogICAgICAgIH07CiAgICAgICAgY29uc3Qgc3RhY2sgPSBbcm9vdF07CgogICAgICAgIGl0ZW1zLnNsaWNlKDEpLmZvckVhY2goKGl0ZW0pID0+IHsKICAgICAgICAgIGNvbnN0IG5vZGUgPSB7CiAgICAgICAgICAgIHRleHQ6IGl0ZW0udGV4dCwKICAgICAgICAgICAgY2hpbGRyZW46IFtdLAogICAgICAgICAgICBpbmRlbnQ6IGl0ZW0uaW5kZW50LAogICAgICAgICAgICBsZXZlbDogMSwKICAgICAgICAgIH07CiAgICAgICAgICB3aGlsZSAoc3RhY2subGVuZ3RoID4gMSAmJiBzdGFja1tzdGFjay5sZW5ndGggLSAxXS5pbmRlbnQgPj0gaXRlbS5pbmRlbnQpIHsKICAgICAgICAgICAgc3RhY2sucG9wKCk7CiAgICAgICAgICB9CiAgICAgICAgICBjb25zdCBwYXJlbnQgPSBzdGFja1tzdGFjay5sZW5ndGggLSAxXTsKICAgICAgICAgIG5vZGUubGV2ZWwgPSBwYXJlbnQubGV2ZWwgKyAxOwogICAgICAgICAgcGFyZW50LmNoaWxkcmVuLnB1c2gobm9kZSk7CiAgICAgICAgICBzdGFjay5wdXNoKG5vZGUpOwogICAgICAgIH0pOwoKICAgICAgICByZXR1cm4gcm9vdDsKICAgICAgfSwKCiAgICAgIHB2RHJhd01pbmRtYXAoY3R4LCBwYXJlbnQsIGNvZGUsIGJveCwgb3B0aW9ucykgewogICAgICAgIGNvbnN0IHJvb3QgPSB0aGlzLnB2UGFyc2VNaW5kbWFwKGNvZGUpOwogICAgICAgIGlmICghcm9vdCkgewogICAgICAgICAgdGhpcy5wdkRyYXdEaWFncmFtRmFsbGJhY2soY3R4LCBwYXJlbnQsIGNvZGUsIGJveCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBjb25zdCBsZXZlbEdhcCA9IDIzMDsKICAgICAgICBjb25zdCByb3dIZWlnaHQgPSA3NDsKICAgICAgICBsZXQgbGVhZkluZGV4ID0gMDsKICAgICAgICBsZXQgbWF4TGV2ZWwgPSAwOwoKICAgICAgICBjb25zdCB3YWxrID0gKG5vZGUpID0+IHsKICAgICAgICAgIG1heExldmVsID0gTWF0aC5tYXgobWF4TGV2ZWwsIG5vZGUubGV2ZWwpOwogICAgICAgICAgaWYgKCFub2RlLmNoaWxkcmVuLmxlbmd0aCkgewogICAgICAgICAgICBub2RlLnNsb3QgPSBsZWFmSW5kZXg7CiAgICAgICAgICAgIGxlYWZJbmRleCArPSAxOwogICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgbm9kZS5jaGlsZHJlbi5mb3JFYWNoKHdhbGspOwogICAgICAgICAgICBub2RlLnNsb3QgPQogICAgICAgICAgICAgIChub2RlLmNoaWxkcmVuWzBdLnNsb3QgKwogICAgICAgICAgICAgICAgbm9kZS5jaGlsZHJlbltub2RlLmNoaWxkcmVuLmxlbmd0aCAtIDFdLnNsb3QpIC8KICAgICAgICAgICAgICAyOwogICAgICAgICAgfQogICAgICAgIH07CiAgICAgICAgd2Fsayhyb290KTsKCiAgICAgICAgY29uc3QgbmF0dXJhbFdpZHRoID0gTWF0aC5tYXgoMzQwLCBtYXhMZXZlbCAqIGxldmVsR2FwICsgMjIwKTsKICAgICAgICBjb25zdCBuYXR1cmFsSGVpZ2h0ID0gTWF0aC5tYXgoMTYwLCBsZWFmSW5kZXggKiByb3dIZWlnaHQpOwogICAgICAgIGNvbnN0IGdyb3VwID0gdGhpcy5wdkdyb3VwKGN0eCwgcGFyZW50LCAwLCAwLCBuYXR1cmFsV2lkdGgsIG5hdHVyYWxIZWlnaHQpOwoKICAgICAgICBjb25zdCB2aXNpdCA9IChub2RlKSA9PiB7CiAgICAgICAgICBub2RlLnggPSA2MCArIG5vZGUubGV2ZWwgKiBsZXZlbEdhcDsKICAgICAgICAgIG5vZGUueSA9IChub2RlLnNsb3QgKyAwLjUpICogcm93SGVpZ2h0OwogICAgICAgICAgbm9kZS5jaGlsZHJlbi5mb3JFYWNoKHZpc2l0KTsKICAgICAgICB9OwogICAgICAgIHZpc2l0KHJvb3QpOwoKICAgICAgICBjb25zdCBjb25uZWN0ID0gKG5vZGUpID0+IHsKICAgICAgICAgIG5vZGUuY2hpbGRyZW4uZm9yRWFjaCgoY2hpbGQpID0+IHsKICAgICAgICAgICAgY29uc3Qgc3RhcnRYID0gbm9kZS54ICsgOTA7CiAgICAgICAgICAgIGNvbnN0IHN0YXJ0WSA9IG5vZGUueTsKICAgICAgICAgICAgY29uc3QgZW5kWCA9IGNoaWxkLnggLSAxMDsKICAgICAgICAgICAgY29uc3QgZW5kWSA9IGNoaWxkLnk7CiAgICAgICAgICAgIGNvbnN0IG1pZGRsZSA9IChzdGFydFggKyBlbmRYKSAvIDI7CiAgICAgICAgICAgIHRoaXMucHZQYXRoKAogICAgICAgICAgICAgIGN0eCwKICAgICAgICAgICAgICBncm91cCwKICAgICAgICAgICAgICAiTSAiICsKICAgICAgICAgICAgICAgIHN0YXJ0WCArCiAgICAgICAgICAgICAgICAiICIgKwogICAgICAgICAgICAgICAgc3RhcnRZICsKICAgICAgICAgICAgICAgICIgQyAiICsKICAgICAgICAgICAgICAgIG1pZGRsZSArCiAgICAgICAgICAgICAgICAiICIgKwogICAgICAgICAgICAgICAgc3RhcnRZICsKICAgICAgICAgICAgICAgICIgIiArCiAgICAgICAgICAgICAgICBtaWRkbGUgKwogICAgICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgICAgIGVuZFkgKwogICAgICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgICAgIGVuZFggKwogICAgICAgICAgICAgICAgIiAiICsKICAgICAgICAgICAgICAgIGVuZFksCiAgICAgICAgICAgICAgIm5vbmUiLAogICAgICAgICAgICAgIHsgc3Ryb2tlOiAiI2MzZDBlMyIsIHN0cm9rZVdpZHRoOiAxLjUgfQogICAgICAgICAgICApOwogICAgICAgICAgICBjb25uZWN0KGNoaWxkKTsKICAgICAgICAgIH0pOwogICAgICAgIH07CiAgICAgICAgY29ubmVjdChyb290KTsKCiAgICAgICAgY29uc3QgZHJhd05vZGUgPSAobm9kZSkgPT4gewogICAgICAgICAgY29uc3QgaXNSb290ID0gbm9kZS5sZXZlbCA9PT0gMDsKICAgICAgICAgIGNvbnN0IHdpZHRoID0gaXNSb290ID8gMTgwIDogMTcwOwogICAgICAgICAgY29uc3QgaGVpZ2h0ID0gaXNSb290ID8gNjAgOiA1MDsKICAgICAgICAgIGNvbnN0IHggPSBub2RlLnggLSB3aWR0aCAvIDI7CiAgICAgICAgICBjb25zdCB5ID0gbm9kZS55IC0gaGVpZ2h0IC8gMjsKICAgICAgICAgIGNvbnN0IGZpbGwgPSBpc1Jvb3QgPyB0aGlzLlBWX0MuYnJhbmQgOiAiI2ZmZmZmZiI7CiAgICAgICAgICBjb25zdCBjb2xvciA9IGlzUm9vdCA/ICIjZmZmZmZmIiA6IHRoaXMuUFZfQy5pbms7CgogICAgICAgICAgdGhpcy5wdlJlY3QoY3R4LCBncm91cCwgeCwgeSwgd2lkdGgsIGhlaWdodCwgZmlsbCwgewogICAgICAgICAgICBjb3JuZXJSYWRpdXM6IDEwLAogICAgICAgICAgICBzdHJva2U6IGlzUm9vdCA/IHRoaXMuUFZfQy5icmFuZCA6ICIjYzNkMGUzIiwKICAgICAgICAgICAgc3Ryb2tlV2lkdGg6IDEuNCwKICAgICAgICAgICAgc2hhZG93OiB7CiAgICAgICAgICAgICAgeDogMCwKICAgICAgICAgICAgICB5OiA0LAogICAgICAgICAgICAgIGJsdXI6IDEyLAogICAgICAgICAgICAgIGNvbG9yOiAicmdiYSgxNiwgMjQsIDQwLCAwLjA1KSIsCiAgICAgICAgICAgIH0sCiAgICAgICAgICB9KTsKCiAgICAgICAgICBjb25zdCBsaW5lcyA9IHRoaXMucHZXcmFwVGV4dCgKICAgICAgICAgICAgbm9kZS50ZXh0LAogICAgICAgICAgICB3aWR0aCAtIDI0LAogICAgICAgICAgICB0aGlzLnB2Rm9udCg1MDAsIGlzUm9vdCA/IDE3IDogMTUpLAogICAgICAgICAgICAwLAogICAgICAgICAgICAyCiAgICAgICAgICApOwogICAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBncm91cCwgewogICAgICAgICAgICB4OiB4ICsgMTIsCiAgICAgICAgICAgIHksCiAgICAgICAgICAgIHdpZHRoOiB3aWR0aCAtIDI0LAogICAgICAgICAgICBoZWlnaHQsCiAgICAgICAgICAgIGxpbmVzLAogICAgICAgICAgICBmb250U2l6ZTogaXNSb290ID8gMTcgOiAxNSwKICAgICAgICAgICAgbGluZUhlaWdodDogMjEsCiAgICAgICAgICAgIHdlaWdodDogNTAwLAogICAgICAgICAgICBhbGlnbjogImNlbnRlciIsCiAgICAgICAgICAgIHZhbGlnbjogIm1pZGRsZSIsCiAgICAgICAgICAgIGZpbGw6IGNvbG9yLAogICAgICAgICAgfSk7CgogICAgICAgICAgbm9kZS5jaGlsZHJlbi5mb3JFYWNoKGRyYXdOb2RlKTsKICAgICAgICB9OwogICAgICAgIGRyYXdOb2RlKHJvb3QpOwoKICAgICAgICB0aGlzLnB2Rml0RGlhZ3JhbUdyb3VwKGN0eCwgZ3JvdXAsIGJveCwgbmF0dXJhbFdpZHRoLCBuYXR1cmFsSGVpZ2h0LCAxNCk7CiAgICAgIH0sCgogICAgICAvKiAtLS0tLS0tLS0tIOe6r+aWh+acrOmZjee6pyAtLS0tLS0tLS0tICovCiAgICAgIHB2RHJhd0RpYWdyYW1GYWxsYmFjayhjdHgsIHBhcmVudCwgY29kZSwgYm94KSB7CiAgICAgICAgY29uc3QgbGluZXMgPSB0aGlzLnB2V3JhcFRleHQoCiAgICAgICAgICBjb2RlLAogICAgICAgICAgTWF0aC5tYXgoODAsIGJveC53aWR0aCAtIDQwKSwKICAgICAgICAgIHRoaXMucHZGb250KDQwMCwgMTMsIHRydWUpLAogICAgICAgICAgMCwKICAgICAgICAgIE1hdGgubWF4KDMsIE1hdGguZmxvb3IoYm94LmhlaWdodCAvIDIwKSkKICAgICAgICApOwoKICAgICAgICB0aGlzLnB2UmVjdChjdHgsIHBhcmVudCwgMCwgMCwgYm94LndpZHRoLCBib3guaGVpZ2h0LCAiI2Y4ZmFmYyIsIHsKICAgICAgICAgIGNvcm5lclJhZGl1czogMTAsCiAgICAgICAgICBzdHJva2U6IHRoaXMuUFZfQy5saW5lLAogICAgICAgICAgc3Ryb2tlV2lkdGg6IDEsCiAgICAgICAgfSk7CiAgICAgICAgdGhpcy5wdlRleHQoY3R4LCBwYXJlbnQsIHsKICAgICAgICAgIHg6IDIwLAogICAgICAgICAgeTogMjAsCiAgICAgICAgICB3aWR0aDogTWF0aC5tYXgoNDAsIGJveC53aWR0aCAtIDQwKSwKICAgICAgICAgIGxpbmVzLAogICAgICAgICAgZm9udFNpemU6IDEzLAogICAgICAgICAgbGluZUhlaWdodDogMjAsCiAgICAgICAgICBtb25vOiB0cnVlLAogICAgICAgICAgZmlsbDogIiM2NjcwODUiLAogICAgICAgIH0pOwogICAgICB9LAoKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDln7rnoYAKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIHBhZCh2YWx1ZSkgewogICAgICAgIHJldHVybiBTdHJpbmcodmFsdWUgPT0gbnVsbCA/IDAgOiB2YWx1ZSkucGFkU3RhcnQoMiwgIjAiKTsKICAgICAgfSwKCiAgICAgIGdldFNsaWRlVHlwZUxhYmVsKHNsaWRlKSB7CiAgICAgICAgcmV0dXJuIHRoaXMucHZUeXBlTGFiZWwoc2xpZGUpOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOW8leaTjuWKoOi9vQogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgYXN5bmMgbG9hZEVuZ2luZShmb3JjZSkgewogICAgICAgIGlmICh0aGlzLmVuZ2luZVN0YXR1cyA9PT0gImxvYWRpbmciKSByZXR1cm47CiAgICAgICAgaWYgKHRoaXMuZW5naW5lU3RhdHVzID09PSAicmVhZHkiICYmICFmb3JjZSkgcmV0dXJuOwoKICAgICAgICB0aGlzLmVuZ2luZVN0YXR1cyA9ICJsb2FkaW5nIjsKICAgICAgICB0aGlzLmVuZ2luZUVycm9yID0gIiI7CgogICAgICAgIHRyeSB7CiAgICAgICAgICBjb25zdCBsaWIgPSBhd2FpdCB0aGlzLnB2TG9hZExlYWZlcigpOwogICAgICAgICAgdGhpcy5ydW50aW1lLmxlYWZlckxpYiA9IGxpYjsKICAgICAgICAgIHRoaXMuZW5naW5lU3RhdHVzID0gInJlYWR5IjsKCiAgICAgICAgICB0aGlzLiRuZXh0VGljaygoKSA9PiB7CiAgICAgICAgICAgIHRoaXMucmVuZGVyQWxsVGh1bWJuYWlscyh0cnVlKTsKICAgICAgICAgICAgaWYgKHRoaXMuaXNQcmVzZW50YXRpb24pIHsKICAgICAgICAgICAgICBpZiAoIXRoaXMucnVudGltZS5zdGFnZUFwcCkgewogICAgICAgICAgICAgICAgdGhpcy5pbml0U3RhZ2UoKTsKICAgICAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICAgICAgdGhpcy5yZW5kZXJTdGFnZVNsaWRlKHRoaXMuY3VycmVudFNsaWRlSW5kZXgsIHRydWUpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQogICAgICAgICAgfSk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIGNvbnNvbGUuZXJyb3IoIltQcmVzZW50YXRpb25WaWV3ZXJdIExlYWZlciDliqDovb3lpLHotKUiLCBlcnJvcik7CiAgICAgICAgICB0aGlzLmVuZ2luZVN0YXR1cyA9ICJlcnJvciI7CiAgICAgICAgICB0aGlzLmVuZ2luZUVycm9yID0KICAgICAgICAgICAgZXJyb3IgJiYgZXJyb3IubWVzc2FnZSA/IGVycm9yLm1lc3NhZ2UgOiAi5riy5p+T5byV5pOO5Yqg6L295aSx6LSlIjsKICAgICAgICB9CiAgICAgIH0sCgogICAgICByZXRyeUVuZ2luZSgpIHsKICAgICAgICB0aGlzLmxvYWRFbmdpbmUodHJ1ZSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg57yp55Wl5Zu+CiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBzZXR1cFRodW1iT2JzZXJ2ZXIoKSB7CiAgICAgICAgaWYgKHR5cGVvZiBSZXNpemVPYnNlcnZlciA9PT0gInVuZGVmaW5lZCIpIHJldHVybjsKICAgICAgICBjb25zdCBncmlkID0gdGhpcy4kZWwgPyB0aGlzLiRlbC5xdWVyeVNlbGVjdG9yKCIucHYtZ3JpZCIpIDogbnVsbDsKICAgICAgICBpZiAoIWdyaWQpIHJldHVybjsKCiAgICAgICAgaWYgKHRoaXMucnVudGltZS50aHVtYlJlc2l6ZU9ic2VydmVyKSB7CiAgICAgICAgICB0aGlzLnJ1bnRpbWUudGh1bWJSZXNpemVPYnNlcnZlci5kaXNjb25uZWN0KCk7CiAgICAgICAgfQogICAgICAgIHRoaXMucnVudGltZS50aHVtYlJlc2l6ZU9ic2VydmVyID0gbmV3IFJlc2l6ZU9ic2VydmVyKCgpID0+IHsKICAgICAgICAgIHRoaXMuc2NoZWR1bGVUaHVtYkxheW91dCgpOwogICAgICAgIH0pOwogICAgICAgIHRoaXMucnVudGltZS50aHVtYlJlc2l6ZU9ic2VydmVyLm9ic2VydmUoZ3JpZCk7CiAgICAgIH0sCgogICAgICBnZXRUaHVtYkhvc3RzKCkgewogICAgICAgIGNvbnN0IHJlZnMgPSB0aGlzLiRyZWZzLnRodW1iSG9zdHM7CiAgICAgICAgaWYgKCFyZWZzKSByZXR1cm4gW107CiAgICAgICAgcmV0dXJuIEFycmF5LmlzQXJyYXkocmVmcykgPyByZWZzIDogW3JlZnNdOwogICAgICB9LAoKICAgICAgc2NoZWR1bGVUaHVtYkxheW91dCgpIHsKICAgICAgICBpZiAodGhpcy5ydW50aW1lLnRodW1iVGltZXIpIGNsZWFyVGltZW91dCh0aGlzLnJ1bnRpbWUudGh1bWJUaW1lcik7CiAgICAgICAgdGhpcy5ydW50aW1lLnRodW1iVGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgIHRoaXMucnVudGltZS50aHVtYlRpbWVyID0gbnVsbDsKICAgICAgICAgIHRoaXMucmVuZGVyQWxsVGh1bWJuYWlscyhmYWxzZSk7CiAgICAgICAgfSwgMTQwKTsKICAgICAgfSwKCiAgICAgIHJlbmRlckFsbFRodW1ibmFpbHMoZm9yY2UpIHsKICAgICAgICBpZiAodGhpcy5lbmdpbmVTdGF0dXMgIT09ICJyZWFkeSIgfHwgIXRoaXMucnVudGltZS5sZWFmZXJMaWIpIHJldHVybjsKCiAgICAgICAgY29uc3Qgc2xpZGVzID0gdGhpcy5hY2NvdW50RGF0YS5zbGlkZXMgfHwgW107CiAgICAgICAgY29uc3QgaG9zdHMgPSB0aGlzLmdldFRodW1iSG9zdHMoKTsKICAgICAgICBjb25zdCBkcHIgPSBNYXRoLm1pbigyLCBNYXRoLm1heCgxLCB3aW5kb3cuZGV2aWNlUGl4ZWxSYXRpbyB8fCAxKSk7CgogICAgICAgIHNsaWRlcy5mb3JFYWNoKChzbGlkZSwgaW5kZXgpID0+IHsKICAgICAgICAgIGNvbnN0IGhvc3QgPSBob3N0c1tpbmRleF07CiAgICAgICAgICBpZiAoIWhvc3QpIHJldHVybjsKCiAgICAgICAgICBjb25zdCByZWN0ID0gaG9zdC5nZXRCb3VuZGluZ0NsaWVudFJlY3QoKTsKICAgICAgICAgIGxldCBjc3NXaWR0aCA9IE1hdGgucm91bmQocmVjdC53aWR0aCk7CiAgICAgICAgICBpZiAoY3NzV2lkdGggPCAyKSBjc3NXaWR0aCA9IE1hdGgucm91bmQoaG9zdC5jbGllbnRXaWR0aCk7CiAgICAgICAgICBpZiAoY3NzV2lkdGggPCAyKSByZXR1cm47CgogICAgICAgICAgY29uc3QgcGl4ZWxXaWR0aCA9IE1hdGgubWF4KDE2MCwgTWF0aC5yb3VuZChjc3NXaWR0aCAqIGRwcikpOwogICAgICAgICAgY29uc3QgcGl4ZWxIZWlnaHQgPSBNYXRoLm1heCg5MCwgTWF0aC5yb3VuZCgocGl4ZWxXaWR0aCAqIDkpIC8gMTYpKTsKICAgICAgICAgIGNvbnN0IGN1cnJlbnQgPSB0aGlzLnJ1bnRpbWUudGh1bWJBcHBzW2luZGV4XTsKCiAgICAgICAgICBpZiAoCiAgICAgICAgICAgICFmb3JjZSAmJgogICAgICAgICAgICBjdXJyZW50ICYmCiAgICAgICAgICAgIGN1cnJlbnQuc2xpZGVJZCA9PT0gc2xpZGUuaWQgJiYKICAgICAgICAgICAgTWF0aC5hYnMoY3VycmVudC53aWR0aCAtIHBpeGVsV2lkdGgpIDwgMgogICAgICAgICAgKSB7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KCiAgICAgICAgICB0aGlzLm1vdW50VGh1bWIoaW5kZXgsIHNsaWRlLCBob3N0LCBwaXhlbFdpZHRoLCBwaXhlbEhlaWdodCk7CiAgICAgICAgfSk7CgogICAgICAgIE9iamVjdC5rZXlzKHRoaXMucnVudGltZS50aHVtYkFwcHMpLmZvckVhY2goKGtleSkgPT4gewogICAgICAgICAgaWYgKE51bWJlcihrZXkpID49IHNsaWRlcy5sZW5ndGgpIHRoaXMuZGVzdHJveVRodW1iKGtleSk7CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICBtb3VudFRodW1iKGluZGV4LCBzbGlkZSwgaG9zdCwgcGl4ZWxXaWR0aCwgcGl4ZWxIZWlnaHQpIHsKICAgICAgICBjb25zdCBsaWIgPSB0aGlzLnJ1bnRpbWUubGVhZmVyTGliOwogICAgICAgIGlmICghbGliIHx8ICFob3N0KSByZXR1cm47CgogICAgICAgIHRoaXMuZGVzdHJveVRodW1iKGluZGV4KTsKCiAgICAgICAgY29uc3QgY2FudmFzID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgiY2FudmFzIik7CiAgICAgICAgY2FudmFzLndpZHRoID0gcGl4ZWxXaWR0aDsKICAgICAgICBjYW52YXMuaGVpZ2h0ID0gcGl4ZWxIZWlnaHQ7CiAgICAgICAgY2FudmFzLnN0eWxlLmNzc1RleHQgPSAiZGlzcGxheTpibG9jazt3aWR0aDoxMDAlO2hlaWdodDoxMDAlOyI7CiAgICAgICAgY2FudmFzLnNldEF0dHJpYnV0ZSgicm9sZSIsICJpbWciKTsKICAgICAgICBjYW52YXMuc2V0QXR0cmlidXRlKAogICAgICAgICAgImFyaWEtbGFiZWwiLAogICAgICAgICAgKHNsaWRlLnRpdGxlIHx8ICLlubvnga/niYciKSArICIg6aKE6KeIIgogICAgICAgICk7CiAgICAgICAgaG9zdC5hcHBlbmRDaGlsZChjYW52YXMpOwoKICAgICAgICBsZXQgYXBwID0gbnVsbDsKICAgICAgICB0cnkgewogICAgICAgICAgYXBwID0gbmV3IGxpYi5MZWFmZXIoewogICAgICAgICAgICB2aWV3OiBjYW52YXMsCiAgICAgICAgICAgIHdpZHRoOiBwaXhlbFdpZHRoLAogICAgICAgICAgICBoZWlnaHQ6IHBpeGVsSGVpZ2h0LAogICAgICAgICAgICBwaXhlbFJhdGlvOiAxLAogICAgICAgICAgfSk7CgogICAgICAgICAgY29uc3QgYnVpbHQgPSB0aGlzLnB2QnVpbGRTbGlkZShsaWIsIHNsaWRlLCB7CiAgICAgICAgICAgIHdpZHRoOiBwaXhlbFdpZHRoLAogICAgICAgICAgICBoZWlnaHQ6IHBpeGVsSGVpZ2h0LAogICAgICAgICAgICBpbmRleCwKICAgICAgICAgICAgdG90YWw6IHRoaXMudG90YWxTbGlkZXMsCiAgICAgICAgICAgIGRlY2tUaXRsZTogdGhpcy5hY2NvdW50RGF0YS50aXRsZSwKICAgICAgICAgICAgcHJlc2VudGVyOiB0aGlzLmFjY291bnREYXRhLnByZXNlbnRlciwKICAgICAgICAgICAgbW9kZTogInRodW1iIiwKICAgICAgICAgICAgYW5pbWF0ZTogZmFsc2UsCiAgICAgICAgICB9KTsKICAgICAgICAgIGFwcC5hZGQoYnVpbHQubm9kZSk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIGNvbnNvbGUuZXJyb3IoIltQcmVzZW50YXRpb25WaWV3ZXJdIOe8qeeVpeWbvua4suafk+Wksei0pSIsIGVycm9yKTsKICAgICAgICAgIGlmIChhcHApIHsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICBhcHAuZGVzdHJveSgpOwogICAgICAgICAgICB9IGNhdGNoIChkZXN0cm95RXJyb3IpIHsKICAgICAgICAgICAgICAvKiBub29wICovCiAgICAgICAgICAgIH0KICAgICAgICAgIH0KICAgICAgICAgIGlmIChjYW52YXMucGFyZW50Tm9kZSkgY2FudmFzLnBhcmVudE5vZGUucmVtb3ZlQ2hpbGQoY2FudmFzKTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIHRoaXMucnVudGltZS50aHVtYkFwcHNbaW5kZXhdID0gewogICAgICAgICAgYXBwLAogICAgICAgICAgY2FudmFzLAogICAgICAgICAgaG9zdCwKICAgICAgICAgIHdpZHRoOiBwaXhlbFdpZHRoLAogICAgICAgICAgaGVpZ2h0OiBwaXhlbEhlaWdodCwKICAgICAgICAgIHNsaWRlSWQ6IHNsaWRlLmlkLAogICAgICAgIH07CiAgICAgIH0sCgogICAgICBkZXN0cm95VGh1bWIoaW5kZXgpIHsKICAgICAgICBjb25zdCBlbnRyeSA9IHRoaXMucnVudGltZS50aHVtYkFwcHNbaW5kZXhdOwogICAgICAgIGlmICghZW50cnkpIHJldHVybjsKICAgICAgICBkZWxldGUgdGhpcy5ydW50aW1lLnRodW1iQXBwc1tpbmRleF07CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGVudHJ5LmFwcC5kZXN0cm95KCk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIC8qIG5vb3AgKi8KICAgICAgICB9CiAgICAgICAgaWYgKGVudHJ5LmNhbnZhcyAmJiBlbnRyeS5jYW52YXMucGFyZW50Tm9kZSkgewogICAgICAgICAgZW50cnkuY2FudmFzLnBhcmVudE5vZGUucmVtb3ZlQ2hpbGQoZW50cnkuY2FudmFzKTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBkZXN0cm95QWxsVGh1bWJzKCkgewogICAgICAgIE9iamVjdC5rZXlzKHRoaXMucnVudGltZS50aHVtYkFwcHMpLmZvckVhY2goKGtleSkgPT4gewogICAgICAgICAgdGhpcy5kZXN0cm95VGh1bWIoa2V5KTsKICAgICAgICB9KTsKICAgICAgICB0aGlzLnJ1bnRpbWUudGh1bWJBcHBzID0ge307CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICAg5YWo5bGP6Iie5Y+wCiAgICAgICAgID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICB1cGRhdGVTdGFnZVNjYWxlKCkgewogICAgICAgIGNvbnN0IHN0YWdlID0gdGhpcy4kcmVmcy5zdGFnZTsKICAgICAgICBjb25zdCB2aWV3cG9ydFdpZHRoID0KICAgICAgICAgIChzdGFnZSAmJiBzdGFnZS5jbGllbnRXaWR0aCkgfHwKICAgICAgICAgIHdpbmRvdy5pbm5lcldpZHRoIHx8CiAgICAgICAgICBkb2N1bWVudC5kb2N1bWVudEVsZW1lbnQuY2xpZW50V2lkdGggfHwKICAgICAgICAgIDE5MjA7CiAgICAgICAgY29uc3Qgdmlld3BvcnRIZWlnaHQgPQogICAgICAgICAgKHN0YWdlICYmIHN0YWdlLmNsaWVudEhlaWdodCkgfHwKICAgICAgICAgIHdpbmRvdy5pbm5lckhlaWdodCB8fAogICAgICAgICAgZG9jdW1lbnQuZG9jdW1lbnRFbGVtZW50LmNsaWVudEhlaWdodCB8fAogICAgICAgICAgMTA4MDsKCiAgICAgICAgY29uc3Qgc2FmZUluc2V0ID0gMTI7CiAgICAgICAgY29uc3QgYXZhaWxhYmxlV2lkdGggPSBNYXRoLm1heCgxLCB2aWV3cG9ydFdpZHRoIC0gc2FmZUluc2V0ICogMik7CiAgICAgICAgY29uc3QgYXZhaWxhYmxlSGVpZ2h0ID0gTWF0aC5tYXgoMSwgdmlld3BvcnRIZWlnaHQgLSBzYWZlSW5zZXQgKiAyKTsKCiAgICAgICAgdGhpcy5zdGFnZVNjYWxlID0gTWF0aC5tYXgoCiAgICAgICAgICAwLjAxLAogICAgICAgICAgTWF0aC5taW4oYXZhaWxhYmxlV2lkdGggLyB0aGlzLlBWX1csIGF2YWlsYWJsZUhlaWdodCAvIHRoaXMuUFZfSCkKICAgICAgICApOwogICAgICB9LAoKICAgICAgc2V0dXBTdGFnZVJlc2l6ZU9ic2VydmVyKCkgewogICAgICAgIGlmICh0eXBlb2YgUmVzaXplT2JzZXJ2ZXIgPT09ICJ1bmRlZmluZWQiKSByZXR1cm47CiAgICAgICAgY29uc3Qgc3RhZ2UgPSB0aGlzLiRyZWZzLnN0YWdlOwogICAgICAgIGlmICghc3RhZ2UpIHJldHVybjsKCiAgICAgICAgaWYgKHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyKSB7CiAgICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VSZXNpemVPYnNlcnZlci5kaXNjb25uZWN0KCk7CiAgICAgICAgfQogICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyID0gbmV3IFJlc2l6ZU9ic2VydmVyKCgpID0+IHsKICAgICAgICAgIHRoaXMudXBkYXRlU3RhZ2VTY2FsZSgpOwogICAgICAgIH0pOwogICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyLm9ic2VydmUoc3RhZ2UpOwogICAgICB9LAoKICAgICAgaW5pdFN0YWdlKCkgewogICAgICAgIGlmICh0aGlzLnJ1bnRpbWUuc3RhZ2VBcHAgfHwgdGhpcy5lbmdpbmVTdGF0dXMgIT09ICJyZWFkeSIpIHJldHVybjsKCiAgICAgICAgY29uc3QgbGliID0gdGhpcy5ydW50aW1lLmxlYWZlckxpYjsKICAgICAgICBjb25zdCBob3N0ID0gdGhpcy4kcmVmcy5zdGFnZUhvc3Q7CiAgICAgICAgaWYgKCFsaWIgfHwgIWhvc3QpIHJldHVybjsKCiAgICAgICAgY29uc3QgY2FudmFzID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgiY2FudmFzIik7CiAgICAgICAgY2FudmFzLndpZHRoID0gdGhpcy5QVl9XOwogICAgICAgIGNhbnZhcy5oZWlnaHQgPSB0aGlzLlBWX0g7CiAgICAgICAgY2FudmFzLnN0eWxlLmNzc1RleHQgPQogICAgICAgICAgImRpc3BsYXk6YmxvY2s7d2lkdGg6MTkyMHB4O2hlaWdodDoxMDgwcHg7IjsKICAgICAgICBjYW52YXMuc2V0QXR0cmlidXRlKCJyb2xlIiwgImltZyIpOwogICAgICAgIGNhbnZhcy5zZXRBdHRyaWJ1dGUoImFyaWEtbGFiZWwiLCAi5bm754Gv54mH55S75biDIik7CiAgICAgICAgaG9zdC5hcHBlbmRDaGlsZChjYW52YXMpOwoKICAgICAgICBsZXQgYXBwID0gbnVsbDsKICAgICAgICB0cnkgewogICAgICAgICAgYXBwID0gbmV3IGxpYi5MZWFmZXIoewogICAgICAgICAgICB2aWV3OiBjYW52YXMsCiAgICAgICAgICAgIHdpZHRoOiB0aGlzLlBWX1csCiAgICAgICAgICAgIGhlaWdodDogdGhpcy5QVl9ILAogICAgICAgICAgICBwaXhlbFJhdGlvOiAxLAogICAgICAgICAgfSk7CiAgICAgICAgICBjb25zdCByb290ID0gbmV3IGxpYi5Hcm91cCh7CiAgICAgICAgICAgIHg6IDAsCiAgICAgICAgICAgIHk6IDAsCiAgICAgICAgICAgIHdpZHRoOiB0aGlzLlBWX1csCiAgICAgICAgICAgIGhlaWdodDogdGhpcy5QVl9ILAogICAgICAgICAgICBjbGlwOiB0cnVlLAogICAgICAgICAgfSk7CiAgICAgICAgICBhcHAuYWRkKHJvb3QpOwoKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZUFwcCA9IGFwcDsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJvb3QgPSByb290OwogICAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzID0gY2FudmFzOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICBjb25zb2xlLmVycm9yKCJbUHJlc2VudGF0aW9uVmlld2VyXSDoiJ7lj7DliJ3lp4vljJblpLHotKUiLCBlcnJvcik7CiAgICAgICAgICBpZiAoYXBwKSB7CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgYXBwLmRlc3Ryb3koKTsKICAgICAgICAgICAgfSBjYXRjaCAoZGVzdHJveUVycm9yKSB7CiAgICAgICAgICAgICAgLyogbm9vcCAqLwogICAgICAgICAgICB9CiAgICAgICAgICB9CiAgICAgICAgICBpZiAoY2FudmFzLnBhcmVudE5vZGUpIGNhbnZhcy5wYXJlbnROb2RlLnJlbW92ZUNoaWxkKGNhbnZhcyk7CiAgICAgICAgICB0aGlzLmVuZ2luZVN0YXR1cyA9ICJlcnJvciI7CiAgICAgICAgICB0aGlzLmVuZ2luZUVycm9yID0gZXJyb3IgJiYgZXJyb3IubWVzc2FnZSA/IGVycm9yLm1lc3NhZ2UgOiAi6Iie5Y+w5Yid5aeL5YyW5aSx6LSlIjsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIHRoaXMuYXR0YWNoU3RhZ2VFdmVudHMoY2FudmFzKTsKICAgICAgICB0aGlzLnJlbmRlclN0YWdlU2xpZGUodGhpcy5jdXJyZW50U2xpZGVJbmRleCwgdHJ1ZSk7CiAgICAgIH0sCgogICAgICBkZXN0cm95U3RhZ2UoKSB7CiAgICAgICAgdGhpcy5kZXRhY2hTdGFnZUV2ZW50cygpOwogICAgICAgIHRoaXMuc3RvcENoYXJ0UGFuKCk7CiAgICAgICAgdGhpcy5zdG9wQW5pbWF0aW9uKCk7CgogICAgICAgIGlmICh0aGlzLnJ1bnRpbWUuc3RhZ2VBcHApIHsKICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZUFwcC5kZXN0cm95KCk7CiAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAvKiBub29wICovCiAgICAgICAgICB9CiAgICAgICAgfQoKICAgICAgICBpZiAodGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzICYmIHRoaXMucnVudGltZS5zdGFnZUNhbnZhcy5wYXJlbnROb2RlKSB7CiAgICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMucGFyZW50Tm9kZS5yZW1vdmVDaGlsZCh0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMpOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlQXBwID0gbnVsbDsKICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VSb290ID0gbnVsbDsKICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMgPSBudWxsOwogICAgICAgIHRoaXMucnVudGltZS5jaGFydEFyZWFzID0gW107CiAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlU2xpZGVLZXkgPSAiIjsKICAgICAgfSwKCiAgICAgIHJlbmRlclN0YWdlU2xpZGUoaW5kZXgsIGFuaW1hdGUpIHsKICAgICAgICBpZiAoIXRoaXMucnVudGltZS5zdGFnZVJvb3QpIHJldHVybjsKCiAgICAgICAgY29uc3QgbGliID0gdGhpcy5ydW50aW1lLmxlYWZlckxpYjsKICAgICAgICBjb25zdCBzbGlkZXMgPSB0aGlzLmFjY291bnREYXRhLnNsaWRlcyB8fCBbXTsKICAgICAgICBjb25zdCBzbGlkZSA9IHNsaWRlc1tpbmRleF07CiAgICAgICAgaWYgKCFsaWIgfHwgIXNsaWRlKSByZXR1cm47CgogICAgICAgIHRoaXMuc3RvcEFuaW1hdGlvbigpOwogICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJvb3QuY2xlYXIoKTsKICAgICAgICB0aGlzLnJ1bnRpbWUuY2hhcnRBcmVhcyA9IFtdOwoKICAgICAgICBjb25zdCBidWlsdCA9IHRoaXMucHZCdWlsZFNsaWRlKGxpYiwgc2xpZGUsIHsKICAgICAgICAgIHdpZHRoOiB0aGlzLlBWX1csCiAgICAgICAgICBoZWlnaHQ6IHRoaXMuUFZfSCwKICAgICAgICAgIGluZGV4LAogICAgICAgICAgdG90YWw6IHRoaXMudG90YWxTbGlkZXMsCiAgICAgICAgICBkZWNrVGl0bGU6IHRoaXMuYWNjb3VudERhdGEudGl0bGUsCiAgICAgICAgICBwcmVzZW50ZXI6IHRoaXMuYWNjb3VudERhdGEucHJlc2VudGVyLAogICAgICAgICAgbW9kZTogInN0YWdlIiwKICAgICAgICAgIGFuaW1hdGU6IGFuaW1hdGUgIT09IGZhbHNlLAogICAgICAgIH0pOwoKICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VSb290LmFkZChidWlsdC5ub2RlKTsKICAgICAgICB0aGlzLnJ1bnRpbWUuY2hhcnRBcmVhcyA9IGJ1aWx0LmNoYXJ0QXJlYXMgfHwgW107CiAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlU2xpZGVLZXkgPSBzbGlkZS5pZDsKICAgICAgICB0aGlzLmFwcGx5Q2hhcnRUcmFuc2Zvcm1zKCk7CgogICAgICAgIGlmIChhbmltYXRlICE9PSBmYWxzZSkgewogICAgICAgICAgdGhpcy5wbGF5RW50cmFuY2UoYnVpbHQuYW5pbWF0ZWQpOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAoYnVpbHQuYW5pbWF0ZWQgfHwgW10pLmZvckVhY2goKGl0ZW0pID0+IHsKICAgICAgICAgICAgaXRlbS5ub2RlLm9wYWNpdHkgPSAxOwogICAgICAgICAgfSk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgcGxheUVudHJhbmNlKGFuaW1hdGVkKSB7CiAgICAgICAgaWYgKCFhbmltYXRlZCB8fCAhYW5pbWF0ZWQubGVuZ3RoKSByZXR1cm47CgogICAgICAgIGlmICgKICAgICAgICAgIHR5cGVvZiB3aW5kb3cgIT09ICJ1bmRlZmluZWQiICYmCiAgICAgICAgICB3aW5kb3cubWF0Y2hNZWRpYSAmJgogICAgICAgICAgd2luZG93Lm1hdGNoTWVkaWEoIihwcmVmZXJzLXJlZHVjZWQtbW90aW9uOiByZWR1Y2UpIikubWF0Y2hlcwogICAgICAgICkgewogICAgICAgICAgYW5pbWF0ZWQuZm9yRWFjaCgoaXRlbSkgPT4gewogICAgICAgICAgICBpdGVtLm5vZGUub3BhY2l0eSA9IDE7CiAgICAgICAgICB9KTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGNvbnN0IHRva2VuID0gKHRoaXMucnVudGltZS5hbmltVG9rZW4gKz0gMSk7CiAgICAgICAgaWYgKHRoaXMucnVudGltZS5hbmltUmFmKSBjYW5jZWxBbmltYXRpb25GcmFtZSh0aGlzLnJ1bnRpbWUuYW5pbVJhZik7CgogICAgICAgIGNvbnN0IGl0ZW1zID0gYW5pbWF0ZWQubWFwKChpdGVtKSA9PiAoewogICAgICAgICAgbm9kZTogaXRlbS5ub2RlLAogICAgICAgICAgZGVsYXk6IGl0ZW0uZGVsYXkgfHwgMCwKICAgICAgICAgIGtpbmQ6IGl0ZW0ua2luZCB8fCAidXAiLAogICAgICAgICAgZHVyYXRpb246IGl0ZW0ua2luZCA9PT0gImZhZGUiID8gNTAwIDogNjUwLAogICAgICAgICAgYmFzZVk6IGl0ZW0ubm9kZS55LAogICAgICAgIH0pKTsKCiAgICAgICAgY29uc3Qgb2Zmc2V0Rm9yID0gKGl0ZW0pID0+CiAgICAgICAgICBpdGVtLmtpbmQgPT09ICJ1cCIgPyAxNCA6IGl0ZW0ua2luZCA9PT0gImRvd24iID8gLTEwIDogMDsKCiAgICAgICAgaXRlbXMuZm9yRWFjaCgoaXRlbSkgPT4gewogICAgICAgICAgaXRlbS5ub2RlLm9wYWNpdHkgPSAwOwogICAgICAgICAgaXRlbS5ub2RlLnkgPSBpdGVtLmJhc2VZICsgb2Zmc2V0Rm9yKGl0ZW0pOwogICAgICAgIH0pOwoKICAgICAgICBjb25zdCB0b3RhbCA9IE1hdGgubWF4LmFwcGx5KAogICAgICAgICAgbnVsbCwKICAgICAgICAgIGl0ZW1zLm1hcCgoaXRlbSkgPT4gaXRlbS5kZWxheSArIGl0ZW0uZHVyYXRpb24pCiAgICAgICAgKTsKICAgICAgICBjb25zdCBzdGFydGVkQXQgPSB3aW5kb3cucGVyZm9ybWFuY2UKICAgICAgICAgID8gd2luZG93LnBlcmZvcm1hbmNlLm5vdygpCiAgICAgICAgICA6IERhdGUubm93KCk7CgogICAgICAgIGNvbnN0IHN0ZXAgPSAoKSA9PiB7CiAgICAgICAgICBpZiAodG9rZW4gIT09IHRoaXMucnVudGltZS5hbmltVG9rZW4pIHJldHVybjsKICAgICAgICAgIGNvbnN0IG5vdyA9IHdpbmRvdy5wZXJmb3JtYW5jZQogICAgICAgICAgICA/IHdpbmRvdy5wZXJmb3JtYW5jZS5ub3coKQogICAgICAgICAgICA6IERhdGUubm93KCk7CiAgICAgICAgICBjb25zdCBlbGFwc2VkID0gbm93IC0gc3RhcnRlZEF0OwoKICAgICAgICAgIGl0ZW1zLmZvckVhY2goKGl0ZW0pID0+IHsKICAgICAgICAgICAgY29uc3QgbG9jYWwgPSBlbGFwc2VkIC0gaXRlbS5kZWxheTsKICAgICAgICAgICAgY29uc3QgcHJvZ3Jlc3MgPSBNYXRoLm1heCgwLCBNYXRoLm1pbigxLCBsb2NhbCAvIGl0ZW0uZHVyYXRpb24pKTsKICAgICAgICAgICAgY29uc3QgZWFzZWQgPSAxIC0gTWF0aC5wb3coMSAtIHByb2dyZXNzLCAzKTsKICAgICAgICAgICAgaXRlbS5ub2RlLm9wYWNpdHkgPSBlYXNlZDsKICAgICAgICAgICAgaXRlbS5ub2RlLnkgPSBpdGVtLmJhc2VZICsgb2Zmc2V0Rm9yKGl0ZW0pICogKDEgLSBlYXNlZCk7CiAgICAgICAgICB9KTsKCiAgICAgICAgICBpZiAoZWxhcHNlZCA8IHRvdGFsKSB7CiAgICAgICAgICAgIHRoaXMucnVudGltZS5hbmltUmFmID0gcmVxdWVzdEFuaW1hdGlvbkZyYW1lKHN0ZXApOwogICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgaXRlbXMuZm9yRWFjaCgoaXRlbSkgPT4gewogICAgICAgICAgICAgIGl0ZW0ubm9kZS5vcGFjaXR5ID0gMTsKICAgICAgICAgICAgICBpdGVtLm5vZGUueSA9IGl0ZW0uYmFzZVk7CiAgICAgICAgICAgIH0pOwogICAgICAgICAgICB0aGlzLnJ1bnRpbWUuYW5pbVJhZiA9IG51bGw7CiAgICAgICAgICB9CiAgICAgICAgfTsKCiAgICAgICAgdGhpcy5ydW50aW1lLmFuaW1SYWYgPSByZXF1ZXN0QW5pbWF0aW9uRnJhbWUoc3RlcCk7CiAgICAgIH0sCgogICAgICBzdG9wQW5pbWF0aW9uKCkgewogICAgICAgIHRoaXMucnVudGltZS5hbmltVG9rZW4gKz0gMTsKICAgICAgICBpZiAodGhpcy5ydW50aW1lLmFuaW1SYWYpIHsKICAgICAgICAgIGNhbmNlbEFuaW1hdGlvbkZyYW1lKHRoaXMucnVudGltZS5hbmltUmFmKTsKICAgICAgICAgIHRoaXMucnVudGltZS5hbmltUmFmID0gbnVsbDsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBhcHBseUNoYXJ0VHJhbnNmb3JtcygpIHsKICAgICAgICAodGhpcy5ydW50aW1lLmNoYXJ0QXJlYXMgfHwgW10pLmZvckVhY2goKGFyZWEpID0+IHsKICAgICAgICAgIGNvbnN0IHpvb20gPSB0aGlzLmdldENoYXJ0Wm9vbUJ5S2V5KGFyZWEua2V5KTsKICAgICAgICAgIGNvbnN0IHBhbiA9IHRoaXMuY2hhcnRQYW5bYXJlYS5rZXldIHx8IHsgeDogMCwgeTogMCB9OwogICAgICAgICAgY29uc3QgayA9IGFyZWEuayB8fCAxOwoKICAgICAgICAgIGFyZWEuY29udGVudC5zY2FsZVggPSB6b29tOwogICAgICAgICAgYXJlYS5jb250ZW50LnNjYWxlWSA9IHpvb207CiAgICAgICAgICBhcmVhLmNvbnRlbnQueCA9ICgoYXJlYS53aWR0aCAvIDIpICogKDEgLSB6b29tKSArIHBhbi54KSAqIGs7CiAgICAgICAgICBhcmVhLmNvbnRlbnQueSA9ICgoYXJlYS5oZWlnaHQgLyAyKSAqICgxIC0gem9vbSkgKyBwYW4ueSkgKiBrOwogICAgICAgIH0pOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOWbvuihqOe8qeaUviAvIOW5s+enuwogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgZ2V0U3RhZ2VQb2ludChjbGllbnRYLCBjbGllbnRZKSB7CiAgICAgICAgaWYgKCF0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMpIHJldHVybiBudWxsOwogICAgICAgIGNvbnN0IHJlY3QgPSB0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMuZ2V0Qm91bmRpbmdDbGllbnRSZWN0KCk7CiAgICAgICAgaWYgKCFyZWN0LndpZHRoIHx8ICFyZWN0LmhlaWdodCkgcmV0dXJuIG51bGw7CiAgICAgICAgcmV0dXJuIHsKICAgICAgICAgIHg6ICgoY2xpZW50WCAtIHJlY3QubGVmdCkgLyByZWN0LndpZHRoKSAqIHRoaXMuUFZfVywKICAgICAgICAgIHk6ICgoY2xpZW50WSAtIHJlY3QudG9wKSAvIHJlY3QuaGVpZ2h0KSAqIHRoaXMuUFZfSCwKICAgICAgICB9OwogICAgICB9LAoKICAgICAgZmluZENoYXJ0QXJlYUF0KHBvaW50KSB7CiAgICAgICAgaWYgKCFwb2ludCkgcmV0dXJuIG51bGw7CiAgICAgICAgY29uc3QgYXJlYXMgPSB0aGlzLnJ1bnRpbWUuY2hhcnRBcmVhcyB8fCBbXTsKICAgICAgICBmb3IgKGxldCBpbmRleCA9IGFyZWFzLmxlbmd0aCAtIDE7IGluZGV4ID49IDA7IGluZGV4IC09IDEpIHsKICAgICAgICAgIGNvbnN0IGFyZWEgPSBhcmVhc1tpbmRleF07CiAgICAgICAgICBpZiAoCiAgICAgICAgICAgIHBvaW50LnggPj0gYXJlYS54ICYmCiAgICAgICAgICAgIHBvaW50LnggPD0gYXJlYS54ICsgYXJlYS53aWR0aCAmJgogICAgICAgICAgICBwb2ludC55ID49IGFyZWEueSAmJgogICAgICAgICAgICBwb2ludC55IDw9IGFyZWEueSArIGFyZWEuaGVpZ2h0CiAgICAgICAgICApIHsKICAgICAgICAgICAgcmV0dXJuIGFyZWE7CiAgICAgICAgICB9CiAgICAgICAgfQogICAgICAgIHJldHVybiBudWxsOwogICAgICB9LAoKICAgICAgZ2V0Q2hhcnRab29tQnlLZXkoa2V5KSB7CiAgICAgICAgY29uc3QgdmFsdWUgPSB0aGlzLmNoYXJ0Wm9vbVtrZXldOwogICAgICAgIHJldHVybiB0eXBlb2YgdmFsdWUgPT09ICJudW1iZXIiICYmIGlzRmluaXRlKHZhbHVlKSA/IHZhbHVlIDogMTsKICAgICAgfSwKCiAgICAgIHNldENoYXJ0Wm9vbUJ5S2V5KGtleSwgbmV4dFpvb20pIHsKICAgICAgICBjb25zdCB6b29tID0gTWF0aC5tYXgoCiAgICAgICAgICAwLjUsCiAgICAgICAgICBNYXRoLm1pbigzLCBNYXRoLnJvdW5kKG5leHRab29tICogMTAwKSAvIDEwMCkKICAgICAgICApOwoKICAgICAgICB0aGlzLmNoYXJ0Wm9vbSA9IE9iamVjdC5hc3NpZ24oe30sIHRoaXMuY2hhcnRab29tLCB7CiAgICAgICAgICBba2V5XTogem9vbSwKICAgICAgICB9KTsKCiAgICAgICAgaWYgKHpvb20gPD0gMSkgewogICAgICAgICAgdGhpcy5jaGFydFBhbiA9IE9iamVjdC5hc3NpZ24oe30sIHRoaXMuY2hhcnRQYW4sIHsKICAgICAgICAgICAgW2tleV06IHsgeDogMCwgeTogMCB9LAogICAgICAgICAgfSk7CiAgICAgICAgfQoKICAgICAgICB0aGlzLmFwcGx5Q2hhcnRUcmFuc2Zvcm1zKCk7CiAgICAgIH0sCgogICAgICByZXNldENoYXJ0Wm9vbUJ5S2V5KGtleSkgewogICAgICAgIHRoaXMuc3RvcENoYXJ0UGFuKCk7CiAgICAgICAgdGhpcy5jaGFydFpvb20gPSBPYmplY3QuYXNzaWduKHt9LCB0aGlzLmNoYXJ0Wm9vbSwgeyBba2V5XTogMSB9KTsKICAgICAgICB0aGlzLmNoYXJ0UGFuID0gT2JqZWN0LmFzc2lnbih7fSwgdGhpcy5jaGFydFBhbiwgewogICAgICAgICAgW2tleV06IHsgeDogMCwgeTogMCB9LAogICAgICAgIH0pOwogICAgICAgIHRoaXMuYXBwbHlDaGFydFRyYW5zZm9ybXMoKTsKICAgICAgfSwKCiAgICAgIGF0dGFjaFN0YWdlRXZlbnRzKGNhbnZhcykgewogICAgICAgIHRoaXMuZGV0YWNoU3RhZ2VFdmVudHMoKTsKICAgICAgICBpZiAoIWNhbnZhcykgcmV0dXJuOwoKICAgICAgICBjb25zdCBoYW5kbGVycyA9IHsKICAgICAgICAgIHdoZWVsOiB0aGlzLmhhbmRsZVN0YWdlV2hlZWwsCiAgICAgICAgICBwb2ludGVyZG93bjogdGhpcy5oYW5kbGVTdGFnZVBvaW50ZXJEb3duLAogICAgICAgICAgcG9pbnRlcm1vdmU6IHRoaXMuaGFuZGxlU3RhZ2VQb2ludGVySG92ZXIsCiAgICAgICAgICBkYmxjbGljazogdGhpcy5oYW5kbGVTdGFnZURvdWJsZUNsaWNrLAogICAgICAgIH07CgogICAgICAgIGNhbnZhcy5hZGRFdmVudExpc3RlbmVyKCJ3aGVlbCIsIGhhbmRsZXJzLndoZWVsLCB7IHBhc3NpdmU6IGZhbHNlIH0pOwogICAgICAgIGNhbnZhcy5hZGRFdmVudExpc3RlbmVyKCJwb2ludGVyZG93biIsIGhhbmRsZXJzLnBvaW50ZXJkb3duKTsKICAgICAgICBjYW52YXMuYWRkRXZlbnRMaXN0ZW5lcigicG9pbnRlcm1vdmUiLCBoYW5kbGVycy5wb2ludGVybW92ZSk7CiAgICAgICAgY2FudmFzLmFkZEV2ZW50TGlzdGVuZXIoImRibGNsaWNrIiwgaGFuZGxlcnMuZGJsY2xpY2spOwogICAgICAgIHRoaXMucnVudGltZS5zdGFnZUhhbmRsZXJzID0gaGFuZGxlcnM7CiAgICAgIH0sCgogICAgICBkZXRhY2hTdGFnZUV2ZW50cygpIHsKICAgICAgICBpZiAoIXRoaXMucnVudGltZS5zdGFnZUNhbnZhcyB8fCAhdGhpcy5ydW50aW1lLnN0YWdlSGFuZGxlcnMpIHJldHVybjsKICAgICAgICBjb25zdCBoYW5kbGVycyA9IHRoaXMucnVudGltZS5zdGFnZUhhbmRsZXJzOwogICAgICAgIHRoaXMucnVudGltZS5zdGFnZUNhbnZhcy5yZW1vdmVFdmVudExpc3RlbmVyKCJ3aGVlbCIsIGhhbmRsZXJzLndoZWVsKTsKICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMucmVtb3ZlRXZlbnRMaXN0ZW5lcigicG9pbnRlcmRvd24iLCBoYW5kbGVycy5wb2ludGVyZG93bik7CiAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzLnJlbW92ZUV2ZW50TGlzdGVuZXIoInBvaW50ZXJtb3ZlIiwgaGFuZGxlcnMucG9pbnRlcm1vdmUpOwogICAgICAgIHRoaXMucnVudGltZS5zdGFnZUNhbnZhcy5yZW1vdmVFdmVudExpc3RlbmVyKCJkYmxjbGljayIsIGhhbmRsZXJzLmRibGNsaWNrKTsKICAgICAgICB0aGlzLnJ1bnRpbWUuc3RhZ2VIYW5kbGVycyA9IG51bGw7CiAgICAgIH0sCgogICAgICBoYW5kbGVTdGFnZVdoZWVsKGV2ZW50KSB7CiAgICAgICAgY29uc3QgYXJlYSA9IHRoaXMuZmluZENoYXJ0QXJlYUF0KAogICAgICAgICAgdGhpcy5nZXRTdGFnZVBvaW50KGV2ZW50LmNsaWVudFgsIGV2ZW50LmNsaWVudFkpCiAgICAgICAgKTsKICAgICAgICBpZiAoIWFyZWEpIHJldHVybjsKCiAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICBldmVudC5zdG9wUHJvcGFnYXRpb24oKTsKCiAgICAgICAgY29uc3QgZGVsdGEgPSBldmVudC5kZWx0YVkgPCAwID8gMC4xMiA6IC0wLjEyOwogICAgICAgIHRoaXMuc2V0Q2hhcnRab29tQnlLZXkoCiAgICAgICAgICBhcmVhLmtleSwKICAgICAgICAgIHRoaXMuZ2V0Q2hhcnRab29tQnlLZXkoYXJlYS5rZXkpICsgZGVsdGEKICAgICAgICApOwogICAgICB9LAoKICAgICAgaGFuZGxlU3RhZ2VQb2ludGVyRG93bihldmVudCkgewogICAgICAgIGNvbnN0IGFyZWEgPSB0aGlzLmZpbmRDaGFydEFyZWFBdCgKICAgICAgICAgIHRoaXMuZ2V0U3RhZ2VQb2ludChldmVudC5jbGllbnRYLCBldmVudC5jbGllbnRZKQogICAgICAgICk7CiAgICAgICAgaWYgKCFhcmVhIHx8IGV2ZW50LmJ1dHRvbiAhPT0gMCkgcmV0dXJuOwogICAgICAgIGlmICh0aGlzLmdldENoYXJ0Wm9vbUJ5S2V5KGFyZWEua2V5KSA8PSAxKSByZXR1cm47CgogICAgICAgIGV2ZW50LnByZXZlbnREZWZhdWx0KCk7CiAgICAgICAgdGhpcy5zdG9wQ2hhcnRQYW4oKTsKCiAgICAgICAgY29uc3QgcGFuID0gdGhpcy5jaGFydFBhblthcmVhLmtleV0gfHwgeyB4OiAwLCB5OiAwIH07CiAgICAgICAgdGhpcy5jaGFydFBhblN0YXRlID0gewogICAgICAgICAga2V5OiBhcmVhLmtleSwKICAgICAgICAgIHN0YXJ0WDogZXZlbnQuY2xpZW50WCwKICAgICAgICAgIHN0YXJ0WTogZXZlbnQuY2xpZW50WSwKICAgICAgICAgIG9yaWdpblg6IHBhbi54LAogICAgICAgICAgb3JpZ2luWTogcGFuLnksCiAgICAgICAgfTsKCiAgICAgICAgaWYgKHRoaXMucnVudGltZS5zdGFnZUNhbnZhcykgewogICAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzLnN0eWxlLmN1cnNvciA9ICJncmFiYmluZyI7CiAgICAgICAgfQoKICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigicG9pbnRlcm1vdmUiLCB0aGlzLmhhbmRsZUNoYXJ0UGFuTW92ZSk7CiAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoInBvaW50ZXJ1cCIsIHRoaXMuc3RvcENoYXJ0UGFuKTsKICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigicG9pbnRlcmNhbmNlbCIsIHRoaXMuc3RvcENoYXJ0UGFuKTsKICAgICAgfSwKCiAgICAgIGhhbmRsZVN0YWdlUG9pbnRlckhvdmVyKGV2ZW50KSB7CiAgICAgICAgaWYgKHRoaXMuY2hhcnRQYW5TdGF0ZSB8fCAhdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzKSByZXR1cm47CiAgICAgICAgY29uc3QgYXJlYSA9IHRoaXMuZmluZENoYXJ0QXJlYUF0KAogICAgICAgICAgdGhpcy5nZXRTdGFnZVBvaW50KGV2ZW50LmNsaWVudFgsIGV2ZW50LmNsaWVudFkpCiAgICAgICAgKTsKCiAgICAgICAgdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzLnN0eWxlLmN1cnNvciA9ICFhcmVhCiAgICAgICAgICA/ICJkZWZhdWx0IgogICAgICAgICAgOiB0aGlzLmdldENoYXJ0Wm9vbUJ5S2V5KGFyZWEua2V5KSA+IDEKICAgICAgICAgID8gImdyYWIiCiAgICAgICAgICA6ICJ6b29tLWluIjsKICAgICAgfSwKCiAgICAgIGhhbmRsZVN0YWdlRG91YmxlQ2xpY2soZXZlbnQpIHsKICAgICAgICBjb25zdCBhcmVhID0gdGhpcy5maW5kQ2hhcnRBcmVhQXQoCiAgICAgICAgICB0aGlzLmdldFN0YWdlUG9pbnQoZXZlbnQuY2xpZW50WCwgZXZlbnQuY2xpZW50WSkKICAgICAgICApOwogICAgICAgIGlmIChhcmVhKSB0aGlzLnJlc2V0Q2hhcnRab29tQnlLZXkoYXJlYS5rZXkpOwogICAgICB9LAoKICAgICAgaGFuZGxlQ2hhcnRQYW5Nb3ZlKGV2ZW50KSB7CiAgICAgICAgY29uc3Qgc3RhdGUgPSB0aGlzLmNoYXJ0UGFuU3RhdGU7CiAgICAgICAgaWYgKCFzdGF0ZSkgcmV0dXJuOwoKICAgICAgICBjb25zdCByZWN0ID0gdGhpcy5ydW50aW1lLnN0YWdlQ2FudmFzCiAgICAgICAgICA/IHRoaXMucnVudGltZS5zdGFnZUNhbnZhcy5nZXRCb3VuZGluZ0NsaWVudFJlY3QoKQogICAgICAgICAgOiBudWxsOwogICAgICAgIGNvbnN0IHNjYWxlID0gcmVjdCAmJiByZWN0LndpZHRoID8gcmVjdC53aWR0aCAvIHRoaXMuUFZfVyA6IDE7CiAgICAgICAgY29uc3QgZGVsdGFYID0gKGV2ZW50LmNsaWVudFggLSBzdGF0ZS5zdGFydFgpIC8gKHNjYWxlIHx8IDEpOwogICAgICAgIGNvbnN0IGRlbHRhWSA9IChldmVudC5jbGllbnRZIC0gc3RhdGUuc3RhcnRZKSAvIChzY2FsZSB8fCAxKTsKCiAgICAgICAgdGhpcy5jaGFydFBhbiA9IE9iamVjdC5hc3NpZ24oe30sIHRoaXMuY2hhcnRQYW4sIHsKICAgICAgICAgIFtzdGF0ZS5rZXldOiB7CiAgICAgICAgICAgIHg6IHN0YXRlLm9yaWdpblggKyBkZWx0YVgsCiAgICAgICAgICAgIHk6IHN0YXRlLm9yaWdpblkgKyBkZWx0YVksCiAgICAgICAgICB9LAogICAgICAgIH0pOwoKICAgICAgICB0aGlzLmFwcGx5Q2hhcnRUcmFuc2Zvcm1zKCk7CiAgICAgIH0sCgogICAgICBzdG9wQ2hhcnRQYW4oKSB7CiAgICAgICAgY29uc3Qgd2FzUGFubmluZyA9IEJvb2xlYW4odGhpcy5jaGFydFBhblN0YXRlKTsKICAgICAgICB0aGlzLmNoYXJ0UGFuU3RhdGUgPSBudWxsOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCJwb2ludGVybW92ZSIsIHRoaXMuaGFuZGxlQ2hhcnRQYW5Nb3ZlKTsKICAgICAgICB3aW5kb3cucmVtb3ZlRXZlbnRMaXN0ZW5lcigicG9pbnRlcnVwIiwgdGhpcy5zdG9wQ2hhcnRQYW4pOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCJwb2ludGVyY2FuY2VsIiwgdGhpcy5zdG9wQ2hhcnRQYW4pOwoKICAgICAgICBpZiAod2FzUGFubmluZyAmJiB0aGlzLnJ1bnRpbWUuc3RhZ2VDYW52YXMpIHsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZUNhbnZhcy5zdHlsZS5jdXJzb3IgPSAiZGVmYXVsdCI7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOaUvuaYoOaOp+WItgogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgbG9ja0JvZHlTY3JvbGwoKSB7CiAgICAgICAgdGhpcy5ib2R5T3ZlcmZsb3cgPSBkb2N1bWVudC5ib2R5LnN0eWxlLm92ZXJmbG93IHx8ICIiOwogICAgICAgIGRvY3VtZW50LmJvZHkuc3R5bGUub3ZlcmZsb3cgPSAiaGlkZGVuIjsKICAgICAgfSwKCiAgICAgIHVubG9ja0JvZHlTY3JvbGwoKSB7CiAgICAgICAgZG9jdW1lbnQuYm9keS5zdHlsZS5vdmVyZmxvdyA9IHRoaXMuYm9keU92ZXJmbG93IHx8ICIiOwogICAgICAgIHRoaXMuYm9keU92ZXJmbG93ID0gIiI7CiAgICAgIH0sCgogICAgICBiaW5kRXZlbnRzKCkgewogICAgICAgIGlmICh0aGlzLmV2ZW50c0JvdW5kKSByZXR1cm47CiAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoImtleWRvd24iLCB0aGlzLmhhbmRsZUtleURvd24pOwogICAgICAgIHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCJyZXNpemUiLCB0aGlzLmhhbmRsZVdpbmRvd1Jlc2l6ZSk7CiAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoIm1vdXNlbW92ZSIsIHRoaXMuaGFuZGxlTW91c2VNb3ZlKTsKICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigibW91c2VvdXQiLCB0aGlzLmhhbmRsZVdpbmRvd01vdXNlT3V0KTsKICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigidG91Y2hzdGFydCIsIHRoaXMuaGFuZGxlVG91Y2hTdGFydCwgewogICAgICAgICAgcGFzc2l2ZTogdHJ1ZSwKICAgICAgICB9KTsKICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigidG91Y2hlbmQiLCB0aGlzLmhhbmRsZVRvdWNoRW5kLCB7CiAgICAgICAgICBwYXNzaXZlOiB0cnVlLAogICAgICAgIH0pOwogICAgICAgIHRoaXMuZXZlbnRzQm91bmQgPSB0cnVlOwogICAgICB9LAoKICAgICAgdW5iaW5kRXZlbnRzKCkgewogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCJrZXlkb3duIiwgdGhpcy5oYW5kbGVLZXlEb3duKTsKICAgICAgICB3aW5kb3cucmVtb3ZlRXZlbnRMaXN0ZW5lcigicmVzaXplIiwgdGhpcy5oYW5kbGVXaW5kb3dSZXNpemUpOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCJtb3VzZW1vdmUiLCB0aGlzLmhhbmRsZU1vdXNlTW92ZSk7CiAgICAgICAgd2luZG93LnJlbW92ZUV2ZW50TGlzdGVuZXIoIm1vdXNlb3V0IiwgdGhpcy5oYW5kbGVXaW5kb3dNb3VzZU91dCk7CiAgICAgICAgd2luZG93LnJlbW92ZUV2ZW50TGlzdGVuZXIoInRvdWNoc3RhcnQiLCB0aGlzLmhhbmRsZVRvdWNoU3RhcnQpOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCJ0b3VjaGVuZCIsIHRoaXMuaGFuZGxlVG91Y2hFbmQpOwogICAgICAgIHRoaXMuZXZlbnRzQm91bmQgPSBmYWxzZTsKICAgICAgfSwKCiAgICAgIG9wZW5TbGlkZUF0KGluZGV4KSB7CiAgICAgICAgdGhpcy5lbnRlclByZXNlbnRhdGlvbihpbmRleCk7CiAgICAgIH0sCgogICAgICBlbnRlclByZXNlbnRhdGlvbihzdGFydEluZGV4KSB7CiAgICAgICAgaWYgKHRoaXMuaXNQcmVzZW50YXRpb24gfHwgIXRoaXMudG90YWxTbGlkZXMpIHJldHVybjsKCiAgICAgICAgdGhpcy51cGRhdGVTdGFnZVNjYWxlKCk7CiAgICAgICAgdGhpcy5jaGFydFpvb20gPSB7fTsKICAgICAgICB0aGlzLmNoYXJ0UGFuID0ge307CiAgICAgICAgdGhpcy5zdG9wQ2hhcnRQYW4oKTsKICAgICAgICB0aGlzLnRvb2xiYXJJZGxlID0gZmFsc2U7CiAgICAgICAgdGhpcy5pc1ByZXNlbnRhdGlvbiA9IHRydWU7CiAgICAgICAgdGhpcy5jdXJyZW50U2xpZGVJbmRleCA9IE1hdGgubWF4KAogICAgICAgICAgMCwKICAgICAgICAgIE1hdGgubWluKAogICAgICAgICAgICB0aGlzLnRvdGFsU2xpZGVzIC0gMSwKICAgICAgICAgICAgdHlwZW9mIHN0YXJ0SW5kZXggPT09ICJudW1iZXIiID8gc3RhcnRJbmRleCA6IDAKICAgICAgICAgICkKICAgICAgICApOwoKICAgICAgICB0aGlzLmxvY2tCb2R5U2Nyb2xsKCk7CiAgICAgICAgdGhpcy5iaW5kRXZlbnRzKCk7CgogICAgICAgIHRoaXMuJG5leHRUaWNrKCgpID0+IHsKICAgICAgICAgIGlmICghdGhpcy5pc1ByZXNlbnRhdGlvbikgcmV0dXJuOwogICAgICAgICAgdGhpcy51cGRhdGVTdGFnZVNjYWxlKCk7CiAgICAgICAgICB0aGlzLnNldHVwU3RhZ2VSZXNpemVPYnNlcnZlcigpOwogICAgICAgICAgdGhpcy5zY2hlZHVsZVRvb2xiYXJIaWRlKCk7CgogICAgICAgICAgaWYgKHRoaXMuZW5naW5lU3RhdHVzID09PSAicmVhZHkiKSB7CiAgICAgICAgICAgIHRoaXMuaW5pdFN0YWdlKCk7CiAgICAgICAgICB9IGVsc2UgaWYgKHRoaXMuZW5naW5lU3RhdHVzID09PSAiZXJyb3IiKSB7CiAgICAgICAgICAgIHRoaXMubG9hZEVuZ2luZSh0cnVlKTsKICAgICAgICAgIH0KICAgICAgICB9KTsKICAgICAgfSwKCiAgICAgIGV4aXRQcmVzZW50YXRpb24oKSB7CiAgICAgICAgaWYgKCF0aGlzLmlzUHJlc2VudGF0aW9uKSByZXR1cm47CgogICAgICAgIHRoaXMuaXNQcmVzZW50YXRpb24gPSBmYWxzZTsKICAgICAgICB0aGlzLnRvb2xiYXJJZGxlID0gZmFsc2U7CiAgICAgICAgdGhpcy51bmJpbmRFdmVudHMoKTsKICAgICAgICB0aGlzLnVubG9ja0JvZHlTY3JvbGwoKTsKCiAgICAgICAgaWYgKHRoaXMudG9vbGJhclRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy50b29sYmFyVGltZXIpOwogICAgICAgICAgdGhpcy50b29sYmFyVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5ydW50aW1lLnN0YWdlUmVzaXplT2JzZXJ2ZXIpIHsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyLmRpc2Nvbm5lY3QoKTsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyID0gbnVsbDsKICAgICAgICB9CgogICAgICAgIHRoaXMuc3RvcENoYXJ0UGFuKCk7CiAgICAgICAgdGhpcy5kZXN0cm95U3RhZ2UoKTsKICAgICAgICB0aGlzLmNsZWFyRXhwb3J0Tm90aWNlKCk7CgogICAgICAgIHRoaXMuY2hhcnRab29tID0ge307CiAgICAgICAgdGhpcy5jaGFydFBhbiA9IHt9OwogICAgICAgIHRoaXMuY3VycmVudFNsaWRlSW5kZXggPSAwOwogICAgICB9LAoKICAgICAgZ29Ub1NsaWRlKGluZGV4KSB7CiAgICAgICAgaWYgKCF0aGlzLmlzUHJlc2VudGF0aW9uIHx8ICF0aGlzLnRvdGFsU2xpZGVzKSByZXR1cm47CiAgICAgICAgY29uc3QgbmV4dEluZGV4ID0gTWF0aC5tYXgoCiAgICAgICAgICAwLAogICAgICAgICAgTWF0aC5taW4odGhpcy50b3RhbFNsaWRlcyAtIDEsIGluZGV4KQogICAgICAgICk7CiAgICAgICAgaWYgKG5leHRJbmRleCA9PT0gdGhpcy5jdXJyZW50U2xpZGVJbmRleCkgcmV0dXJuOwoKICAgICAgICB0aGlzLmN1cnJlbnRTbGlkZUluZGV4ID0gbmV4dEluZGV4OwogICAgICAgIHRoaXMuc2NoZWR1bGVUb29sYmFySGlkZSgpOwogICAgICAgIHRoaXMuc3RvcENoYXJ0UGFuKCk7CiAgICAgICAgdGhpcy5yZW5kZXJTdGFnZVNsaWRlKG5leHRJbmRleCwgdHJ1ZSk7CiAgICAgIH0sCgogICAgICBwcmV2U2xpZGUoKSB7CiAgICAgICAgdGhpcy5zaG93VG9vbGJhcigpOwogICAgICAgIHRoaXMuZ29Ub1NsaWRlKHRoaXMuY3VycmVudFNsaWRlSW5kZXggLSAxKTsKICAgICAgfSwKCiAgICAgIG5leHRTbGlkZSgpIHsKICAgICAgICB0aGlzLnNob3dUb29sYmFyKCk7CiAgICAgICAgdGhpcy5nb1RvU2xpZGUodGhpcy5jdXJyZW50U2xpZGVJbmRleCArIDEpOwogICAgICB9LAoKICAgICAgaGFuZGxlV2luZG93UmVzaXplKCkgewogICAgICAgIHRoaXMudXBkYXRlU3RhZ2VTY2FsZSgpOwogICAgICAgIHRoaXMuc2NoZWR1bGVUaHVtYkxheW91dCgpOwogICAgICB9LAoKICAgICAgaGFuZGxlS2V5RG93bihldmVudCkgewogICAgICAgIHRoaXMuc2NoZWR1bGVUb29sYmFySGlkZSgpOwogICAgICAgIGlmICghdGhpcy5pc1ByZXNlbnRhdGlvbikgcmV0dXJuOwoKICAgICAgICBjb25zdCBrZXkgPSBldmVudC5rZXk7CgogICAgICAgIGlmIChrZXkgPT09ICJFc2NhcGUiKSB7CiAgICAgICAgICBldmVudC5wcmV2ZW50RGVmYXVsdCgpOwogICAgICAgICAgdGhpcy5leGl0UHJlc2VudGF0aW9uKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmICgKICAgICAgICAgIGtleSA9PT0gIkFycm93UmlnaHQiIHx8CiAgICAgICAgICBrZXkgPT09ICJBcnJvd0Rvd24iIHx8CiAgICAgICAgICBrZXkgPT09ICJQYWdlRG93biIgfHwKICAgICAgICAgIGtleSA9PT0gIiAiIHx8CiAgICAgICAgICBrZXkgPT09ICJTcGFjZWJhciIKICAgICAgICApIHsKICAgICAgICAgIGV2ZW50LnByZXZlbnREZWZhdWx0KCk7CiAgICAgICAgICB0aGlzLm5leHRTbGlkZSgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBpZiAoCiAgICAgICAgICBrZXkgPT09ICJBcnJvd0xlZnQiIHx8CiAgICAgICAgICBrZXkgPT09ICJBcnJvd1VwIiB8fAogICAgICAgICAga2V5ID09PSAiUGFnZVVwIgogICAgICAgICkgewogICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgIHRoaXMucHJldlNsaWRlKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmIChrZXkgPT09ICJIb21lIikgewogICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgIHRoaXMuZ29Ub1NsaWRlKDApOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBpZiAoa2V5ID09PSAiRW5kIikgewogICAgICAgICAgZXZlbnQucHJldmVudERlZmF1bHQoKTsKICAgICAgICAgIHRoaXMuZ29Ub1NsaWRlKHRoaXMudG90YWxTbGlkZXMgLSAxKTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBoYW5kbGVNb3VzZU1vdmUoZXZlbnQpIHsKICAgICAgICBpZiAoIXRoaXMuaXNQcmVzZW50YXRpb24pIHJldHVybjsKCiAgICAgICAgY29uc3QgdGFyZ2V0ID0gZXZlbnQudGFyZ2V0OwogICAgICAgIGNvbnN0IG92ZXJUb29sYmFyID0KICAgICAgICAgIHRhcmdldCAmJiB0eXBlb2YgdGFyZ2V0LmNsb3Nlc3QgPT09ICJmdW5jdGlvbiIKICAgICAgICAgICAgPyB0YXJnZXQuY2xvc2VzdCgiLnB2LXRvb2xiYXIiKQogICAgICAgICAgICA6IG51bGw7CiAgICAgICAgaWYgKG92ZXJUb29sYmFyKSB7CiAgICAgICAgICB0aGlzLnNob3dUb29sYmFyKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBjb25zdCBzY2VuZSA9IHRoaXMuJHJlZnMuc3RhZ2VTY2VuZTsKICAgICAgICBpZiAoIXNjZW5lKSB7CiAgICAgICAgICB0aGlzLmhpZGVUb29sYmFyKCk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBjb25zdCByZWN0ID0gc2NlbmUuZ2V0Qm91bmRpbmdDbGllbnRSZWN0KCk7CiAgICAgICAgY29uc3QgaW5zaWRlU2NlbmUgPQogICAgICAgICAgZXZlbnQuY2xpZW50WCA+PSByZWN0LmxlZnQgJiYKICAgICAgICAgIGV2ZW50LmNsaWVudFggPD0gcmVjdC5yaWdodCAmJgogICAgICAgICAgZXZlbnQuY2xpZW50WSA+PSByZWN0LnRvcCAmJgogICAgICAgICAgZXZlbnQuY2xpZW50WSA8PSByZWN0LmJvdHRvbTsKCiAgICAgICAgY29uc3QgdG9vbGJhciA9IHRoaXMuJGVsLnF1ZXJ5U2VsZWN0b3IoIi5wdi10b29sYmFyIik7CiAgICAgICAgY29uc3QgdG9vbGJhclJlY3QgPSB0b29sYmFyID8gdG9vbGJhci5nZXRCb3VuZGluZ0NsaWVudFJlY3QoKSA6IG51bGw7CiAgICAgICAgY29uc3QgdG9wWm9uZUJvdHRvbSA9IE1hdGgubWF4KAogICAgICAgICAgOTYsCiAgICAgICAgICB0b29sYmFyUmVjdCA/IHRvb2xiYXJSZWN0LmJvdHRvbSArIDE4IDogOTYKICAgICAgICApOwogICAgICAgIGNvbnN0IGluc2lkZVRvcFpvbmUgPQogICAgICAgICAgZXZlbnQuY2xpZW50WSA+PSAwICYmIGV2ZW50LmNsaWVudFkgPD0gdG9wWm9uZUJvdHRvbTsKCiAgICAgICAgaWYgKGluc2lkZVNjZW5lIHx8IGluc2lkZVRvcFpvbmUpIHsKICAgICAgICAgIHRoaXMuc2hvd1Rvb2xiYXIoKTsKICAgICAgICB9IGVsc2UgewogICAgICAgICAgdGhpcy5oaWRlVG9vbGJhcigpOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIGhhbmRsZVdpbmRvd01vdXNlT3V0KGV2ZW50KSB7CiAgICAgICAgaWYgKCFldmVudC5yZWxhdGVkVGFyZ2V0ICYmICFldmVudC50b0VsZW1lbnQpIHsKICAgICAgICAgIHRoaXMuaGlkZVRvb2xiYXIoKTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBoYW5kbGVUb3VjaFN0YXJ0KGV2ZW50KSB7CiAgICAgICAgdGhpcy5zY2hlZHVsZVRvb2xiYXJIaWRlKCk7CiAgICAgICAgaWYgKCF0aGlzLmlzUHJlc2VudGF0aW9uIHx8ICFldmVudC50b3VjaGVzIHx8ICFldmVudC50b3VjaGVzLmxlbmd0aCkgewogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBjb25zdCB0b3VjaCA9IGV2ZW50LnRvdWNoZXNbMF07CiAgICAgICAgdGhpcy50b3VjaFN0YXJ0WCA9IHRvdWNoLmNsaWVudFg7CiAgICAgICAgdGhpcy50b3VjaFN0YXJ0WSA9IHRvdWNoLmNsaWVudFk7CiAgICAgICAgdGhpcy50b3VjaFN0YXJ0VGltZSA9IERhdGUubm93KCk7CgogICAgICAgIGNvbnN0IGFyZWEgPSB0aGlzLmZpbmRDaGFydEFyZWFBdCgKICAgICAgICAgIHRoaXMuZ2V0U3RhZ2VQb2ludCh0b3VjaC5jbGllbnRYLCB0b3VjaC5jbGllbnRZKQogICAgICAgICk7CiAgICAgICAgdGhpcy50b3VjaFN0YXJ0SW5ab29tZWRDaGFydCA9IEJvb2xlYW4oCiAgICAgICAgICBhcmVhICYmIHRoaXMuZ2V0Q2hhcnRab29tQnlLZXkoYXJlYS5rZXkpID4gMQogICAgICAgICk7CiAgICAgIH0sCgogICAgICBoYW5kbGVUb3VjaEVuZChldmVudCkgewogICAgICAgIGlmICghdGhpcy5pc1ByZXNlbnRhdGlvbikgcmV0dXJuOwogICAgICAgIGNvbnN0IHdhc1Bhbm5pbmdab29tZWRDaGFydCA9IHRoaXMudG91Y2hTdGFydEluWm9vbWVkQ2hhcnQ7CiAgICAgICAgdGhpcy50b3VjaFN0YXJ0SW5ab29tZWRDaGFydCA9IGZhbHNlOwogICAgICAgIGlmICh3YXNQYW5uaW5nWm9vbWVkQ2hhcnQpIHsKICAgICAgICAgIHRoaXMuc2NoZWR1bGVUb29sYmFySGlkZSgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KICAgICAgICBjb25zdCB0b3VjaCA9CiAgICAgICAgICBldmVudC5jaGFuZ2VkVG91Y2hlcyAmJiBldmVudC5jaGFuZ2VkVG91Y2hlcy5sZW5ndGgKICAgICAgICAgICAgPyBldmVudC5jaGFuZ2VkVG91Y2hlc1swXQogICAgICAgICAgICA6IG51bGw7CiAgICAgICAgaWYgKCF0b3VjaCkgcmV0dXJuOwoKICAgICAgICBjb25zdCBkZWx0YVggPSB0b3VjaC5jbGllbnRYIC0gdGhpcy50b3VjaFN0YXJ0WDsKICAgICAgICBjb25zdCBkZWx0YVkgPSB0b3VjaC5jbGllbnRZIC0gdGhpcy50b3VjaFN0YXJ0WTsKICAgICAgICBjb25zdCBlbGFwc2VkID0gRGF0ZS5ub3coKSAtIHRoaXMudG91Y2hTdGFydFRpbWU7CiAgICAgICAgaWYgKAogICAgICAgICAgZWxhcHNlZCA8IDkwMCAmJgogICAgICAgICAgTWF0aC5hYnMoZGVsdGFYKSA+IDQyICYmCiAgICAgICAgICBNYXRoLmFicyhkZWx0YVgpID4gTWF0aC5hYnMoZGVsdGFZKSAqIDEuMgogICAgICAgICkgewogICAgICAgICAgaWYgKGRlbHRhWCA8IDApIHRoaXMubmV4dFNsaWRlKCk7CiAgICAgICAgICBlbHNlIHRoaXMucHJldlNsaWRlKCk7CiAgICAgICAgfQogICAgICAgIHRoaXMuc2NoZWR1bGVUb29sYmFySGlkZSgpOwogICAgICB9LAoKICAgICAgc2hvd1Rvb2xiYXIoKSB7CiAgICAgICAgaWYgKHRoaXMudG9vbGJhclRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy50b29sYmFyVGltZXIpOwogICAgICAgICAgdGhpcy50b29sYmFyVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICB0aGlzLnRvb2xiYXJJZGxlID0gZmFsc2U7CiAgICAgIH0sCgogICAgICBoaWRlVG9vbGJhcigpIHsKICAgICAgICBpZiAodGhpcy50b29sYmFyVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnRvb2xiYXJUaW1lcik7CiAgICAgICAgICB0aGlzLnRvb2xiYXJUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICAgIHRoaXMudG9vbGJhcklkbGUgPSB0cnVlOwogICAgICB9LAoKICAgICAgc2NoZWR1bGVUb29sYmFySGlkZSgpIHsKICAgICAgICB0aGlzLnNob3dUb29sYmFyKCk7CiAgICAgICAgdGhpcy50b29sYmFyVGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgIHRoaXMudG9vbGJhcklkbGUgPSB0cnVlOwogICAgICAgICAgdGhpcy50b29sYmFyVGltZXIgPSBudWxsOwogICAgICAgIH0sIDI2MDApOwogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAgIOWvvOWHugogICAgICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgZ2V0RXhwb3J0RmlsZU5hbWUoc2xpZGVJbmRleCkgewogICAgICAgIGNvbnN0IGluZGV4ID0KICAgICAgICAgIHR5cGVvZiBzbGlkZUluZGV4ID09PSAibnVtYmVyIgogICAgICAgICAgICA/IHNsaWRlSW5kZXgKICAgICAgICAgICAgOiB0aGlzLmN1cnJlbnRTbGlkZUluZGV4OwogICAgICAgIGNvbnN0IHNsaWRlID0gKHRoaXMuYWNjb3VudERhdGEuc2xpZGVzIHx8IFtdKVtpbmRleF0gfHwge307CiAgICAgICAgY29uc3QgdGl0bGUgPSBTdHJpbmcoc2xpZGUudGl0bGUgfHwgInNsaWRlIikKICAgICAgICAgIC5yZXBsYWNlKC9bXFwvOio/Ijw+fFxyXG5dKy9nLCAiLSIpCiAgICAgICAgICAucmVwbGFjZSgvXHMrL2csICIgIikKICAgICAgICAgIC50cmltKCkKICAgICAgICAgIC5zbGljZSgwLCA1Nik7CiAgICAgICAgcmV0dXJuICgKICAgICAgICAgICJzbGlkZS0iICsKICAgICAgICAgIHRoaXMucGFkKGluZGV4ICsgMSkgKwogICAgICAgICAgIi0iICsKICAgICAgICAgICh0aXRsZSB8fCAidW50aXRsZWQiKSArCiAgICAgICAgICAiLnBuZyIKICAgICAgICApOwogICAgICB9LAoKICAgICAgdHJpZ2dlckRvd25sb2FkKGRhdGFVcmwsIGZpbGVOYW1lKSB7CiAgICAgICAgY29uc3QgbGluayA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoImEiKTsKICAgICAgICBsaW5rLmhyZWYgPSBkYXRhVXJsOwogICAgICAgIGxpbmsuZG93bmxvYWQgPSBmaWxlTmFtZTsKICAgICAgICBsaW5rLnN0eWxlLmRpc3BsYXkgPSAibm9uZSI7CiAgICAgICAgZG9jdW1lbnQuYm9keS5hcHBlbmRDaGlsZChsaW5rKTsKICAgICAgICBsaW5rLmNsaWNrKCk7CiAgICAgICAgc2V0VGltZW91dCgoKSA9PiB7CiAgICAgICAgICBpZiAobGluay5wYXJlbnROb2RlKSBsaW5rLnBhcmVudE5vZGUucmVtb3ZlQ2hpbGQobGluayk7CiAgICAgICAgfSwgMCk7CiAgICAgIH0sCgogICAgICBhc3luYyByZW5kZXJTbGlkZVRvRGF0YVVybChpbmRleCkgewogICAgICAgIGNvbnN0IGxpYiA9IHRoaXMucnVudGltZS5sZWFmZXJMaWI7CiAgICAgICAgY29uc3Qgc2xpZGUgPSAodGhpcy5hY2NvdW50RGF0YS5zbGlkZXMgfHwgW10pW2luZGV4XTsKICAgICAgICBpZiAoIWxpYiB8fCAhc2xpZGUpIHRocm93IG5ldyBFcnJvcigi5b2T5YmN5bm754Gv54mH5LiN5a2Y5ZyoIik7CgogICAgICAgIGNvbnN0IGNhbnZhcyA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoImNhbnZhcyIpOwogICAgICAgIGNhbnZhcy53aWR0aCA9IHRoaXMuUFZfVzsKICAgICAgICBjYW52YXMuaGVpZ2h0ID0gdGhpcy5QVl9IOwoKICAgICAgICBjb25zdCBhcHAgPSBuZXcgbGliLkxlYWZlcih7CiAgICAgICAgICB2aWV3OiBjYW52YXMsCiAgICAgICAgICB3aWR0aDogdGhpcy5QVl9XLAogICAgICAgICAgaGVpZ2h0OiB0aGlzLlBWX0gsCiAgICAgICAgICBwaXhlbFJhdGlvOiAxLAogICAgICAgIH0pOwoKICAgICAgICB0cnkgewogICAgICAgICAgY29uc3QgYnVpbHQgPSB0aGlzLnB2QnVpbGRTbGlkZShsaWIsIHNsaWRlLCB7CiAgICAgICAgICAgIHdpZHRoOiB0aGlzLlBWX1csCiAgICAgICAgICAgIGhlaWdodDogdGhpcy5QVl9ILAogICAgICAgICAgICBpbmRleCwKICAgICAgICAgICAgdG90YWw6IHRoaXMudG90YWxTbGlkZXMsCiAgICAgICAgICAgIGRlY2tUaXRsZTogdGhpcy5hY2NvdW50RGF0YS50aXRsZSwKICAgICAgICAgICAgcHJlc2VudGVyOiB0aGlzLmFjY291bnREYXRhLnByZXNlbnRlciwKICAgICAgICAgICAgbW9kZTogInN0YWdlIiwKICAgICAgICAgICAgYW5pbWF0ZTogZmFsc2UsCiAgICAgICAgICB9KTsKICAgICAgICAgIGFwcC5hZGQoYnVpbHQubm9kZSk7CgogICAgICAgICAgY29uc3QgaGFzSW1hZ2UgPSAoc2xpZGUuZWxlbWVudHMgfHwgW10pLnNvbWUoCiAgICAgICAgICAgIChlbGVtZW50KSA9PiBlbGVtZW50LnR5cGUgPT09ICJpbWFnZSIgJiYgZWxlbWVudC51cmwKICAgICAgICAgICk7CiAgICAgICAgICBhd2FpdCBuZXcgUHJvbWlzZSgocmVzb2x2ZSkgPT4KICAgICAgICAgICAgc2V0VGltZW91dChyZXNvbHZlLCBoYXNJbWFnZSA/IDUwMCA6IDgwKQogICAgICAgICAgKTsKICAgICAgICAgIGF3YWl0IG5ldyBQcm9taXNlKChyZXNvbHZlKSA9PgogICAgICAgICAgICByZXF1ZXN0QW5pbWF0aW9uRnJhbWUoKCkgPT4gcmVxdWVzdEFuaW1hdGlvbkZyYW1lKHJlc29sdmUpKQogICAgICAgICAgKTsKCiAgICAgICAgICByZXR1cm4gY2FudmFzLnRvRGF0YVVSTCgiaW1hZ2UvcG5nIik7CiAgICAgICAgfSBmaW5hbGx5IHsKICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgIGFwcC5kZXN0cm95KCk7CiAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAvKiBub29wICovCiAgICAgICAgICB9CiAgICAgICAgfQogICAgICB9LAoKICAgICAgc2hvd0V4cG9ydE5vdGljZShtZXNzYWdlLCB0eXBlKSB7CiAgICAgICAgaWYgKHRoaXMuZXhwb3J0Tm90aWNlVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLmV4cG9ydE5vdGljZVRpbWVyKTsKICAgICAgICAgIHRoaXMuZXhwb3J0Tm90aWNlVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICB0aGlzLmV4cG9ydE5vdGljZSA9IG1lc3NhZ2U7CiAgICAgICAgdGhpcy5leHBvcnROb3RpY2VUeXBlID0gdHlwZSB8fCAic3VjY2VzcyI7CiAgICAgICAgdGhpcy5leHBvcnROb3RpY2VUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgdGhpcy5leHBvcnROb3RpY2UgPSAiIjsKICAgICAgICAgIHRoaXMuZXhwb3J0Tm90aWNlVGltZXIgPSBudWxsOwogICAgICAgIH0sIDI4MDApOwogICAgICB9LAoKICAgICAgY2xlYXJFeHBvcnROb3RpY2UoKSB7CiAgICAgICAgaWYgKHRoaXMuZXhwb3J0Tm90aWNlVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLmV4cG9ydE5vdGljZVRpbWVyKTsKICAgICAgICAgIHRoaXMuZXhwb3J0Tm90aWNlVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICB0aGlzLmV4cG9ydE5vdGljZSA9ICIiOwogICAgICB9LAoKICAgICAgYXN5bmMgZXhwb3J0Q3VycmVudFNsaWRlKCkgewogICAgICAgIGlmICh0aGlzLmlzRXhwb3J0aW5nIHx8IHRoaXMuZW5naW5lU3RhdHVzICE9PSAicmVhZHkiKSByZXR1cm47CgogICAgICAgIHRoaXMuc2hvd1Rvb2xiYXIoKTsKICAgICAgICB0aGlzLmlzRXhwb3J0aW5nID0gdHJ1ZTsKICAgICAgICB0aGlzLmNsZWFyRXhwb3J0Tm90aWNlKCk7CgogICAgICAgIGNvbnN0IGV4cG9ydEluZGV4ID0gdGhpcy5jdXJyZW50U2xpZGVJbmRleDsKCiAgICAgICAgdHJ5IHsKICAgICAgICAgIGlmIChkb2N1bWVudC5mb250cyAmJiBkb2N1bWVudC5mb250cy5yZWFkeSkgewogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIGF3YWl0IGRvY3VtZW50LmZvbnRzLnJlYWR5OwogICAgICAgICAgICB9IGNhdGNoIChmb250RXJyb3IpIHsKICAgICAgICAgICAgICAvKiBub29wICovCiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICBjb25zdCBkYXRhVXJsID0gYXdhaXQgdGhpcy5yZW5kZXJTbGlkZVRvRGF0YVVybChleHBvcnRJbmRleCk7CiAgICAgICAgICB0aGlzLnRyaWdnZXJEb3dubG9hZCgKICAgICAgICAgICAgZGF0YVVybCwKICAgICAgICAgICAgdGhpcy5nZXRFeHBvcnRGaWxlTmFtZShleHBvcnRJbmRleCkKICAgICAgICAgICk7CiAgICAgICAgICB0aGlzLnNob3dFeHBvcnROb3RpY2UoIuW9k+WJjemhteW3suWvvOWHuuS4uiBQTkcg5Zu+54mHIiwgInN1Y2Nlc3MiKTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgY29uc29sZS5lcnJvcigiW1ByZXNlbnRhdGlvblZpZXdlcl0g5a+85Ye65b2T5YmN5bm754Gv54mH5aSx6LSlIiwgZXJyb3IpOwogICAgICAgICAgdGhpcy5zaG93RXhwb3J0Tm90aWNlKCLlr7zlh7rlpLHotKXvvIzor7fph43or5UiLCAiZXJyb3IiKTsKICAgICAgICB9IGZpbmFsbHkgewogICAgICAgICAgdGhpcy5pc0V4cG9ydGluZyA9IGZhbHNlOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgICDmuIXnkIYKICAgICAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIGNsZWFudXBWaWV3ZXIoKSB7CiAgICAgICAgdGhpcy51bmJpbmRFdmVudHMoKTsKICAgICAgICB0aGlzLnVubG9ja0JvZHlTY3JvbGwoKTsKCiAgICAgICAgaWYgKHRoaXMudG9vbGJhclRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy50b29sYmFyVGltZXIpOwogICAgICAgICAgdGhpcy50b29sYmFyVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5ydW50aW1lLnRodW1iVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnJ1bnRpbWUudGh1bWJUaW1lcik7CiAgICAgICAgICB0aGlzLnJ1bnRpbWUudGh1bWJUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLmV4cG9ydE5vdGljZVRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5leHBvcnROb3RpY2VUaW1lcik7CiAgICAgICAgICB0aGlzLmV4cG9ydE5vdGljZVRpbWVyID0gbnVsbDsKICAgICAgICB9CgogICAgICAgIHRoaXMuc3RvcENoYXJ0UGFuKCk7CiAgICAgICAgdGhpcy5zdG9wQW5pbWF0aW9uKCk7CgogICAgICAgIGlmICh0aGlzLnJ1bnRpbWUudGh1bWJSZXNpemVPYnNlcnZlcikgewogICAgICAgICAgdGhpcy5ydW50aW1lLnRodW1iUmVzaXplT2JzZXJ2ZXIuZGlzY29ubmVjdCgpOwogICAgICAgICAgdGhpcy5ydW50aW1lLnRodW1iUmVzaXplT2JzZXJ2ZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5ydW50aW1lLnN0YWdlUmVzaXplT2JzZXJ2ZXIpIHsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyLmRpc2Nvbm5lY3QoKTsKICAgICAgICAgIHRoaXMucnVudGltZS5zdGFnZVJlc2l6ZU9ic2VydmVyID0gbnVsbDsKICAgICAgICB9CgogICAgICAgIHRoaXMuZGVzdHJveUFsbFRodW1icygpOwogICAgICAgIHRoaXMuZGVzdHJveVN0YWdlKCk7CgogICAgICAgIHRoaXMuaXNQcmVzZW50YXRpb24gPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyRXhwb3J0Tm90aWNlKCk7CiAgICAgICAgdGhpcy5jaGFydFpvb20gPSB7fTsKICAgICAgICB0aGlzLmNoYXJ0UGFuID0ge307CiAgICAgIH0sCiAgICB9LAogIH07Cjwvc2NyaXB0PgoKPHN0eWxlIHNjb3BlZD4KICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICDorr7orqHku6TniYwKICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAucHYtcm9vdCB7CiAgICAtLXB2LWZvbnQtc2FuczogIlNGIFBybyBEaXNwbGF5IiwgIlNGIFBybyBUZXh0IiwgLWFwcGxlLXN5c3RlbSwKICAgICAgQmxpbmtNYWNTeXN0ZW1Gb250LCAiUGluZ0ZhbmcgU0MiLCAiSGlyYWdpbm8gU2FucyBHQiIsICJOb3RvIFNhbnMgU0MiLAogICAgICAiTWljcm9zb2Z0IFlhSGVpIiwgc2Fucy1zZXJpZjsKICAgIC0tcHYtZm9udC1tb25vOiAiU0YgTW9ubyIsICJKZXRCcmFpbnMgTW9ubyIsICJSb2JvdG8gTW9ubyIsIHVpLW1vbm9zcGFjZSwKICAgICAgU0ZNb25vLVJlZ3VsYXIsIE1lbmxvLCBDb25zb2xhcywgbW9ub3NwYWNlOwoKICAgIC0tcHYtYnJhbmQ6ICMwYjVjZmY7CiAgICAtLXB2LWJyYW5kLWRlZXA6ICMwNjQ3Yzk7CiAgICAtLXB2LWJyYW5kLXNvZnQ6ICNlZGY0ZmY7CiAgICAtLXB2LWluazogIzBjMTExYTsKICAgIC0tcHYtdGV4dC0xOiAjMTcxZDI3OwogICAgLS1wdi10ZXh0LTI6ICM1YjY0NzI7CiAgICAtLXB2LXRleHQtMzogIzhiOTRhMzsKICAgIC0tcHYtcGFnZTogI2VlZjFmNTsKICAgIC0tcHYtcGFwZXI6ICNmN2Y4ZmE7CiAgICAtLXB2LXN1cmZhY2U6ICNmZmZmZmY7CiAgICAtLXB2LWxpbmU6ICNlMmU2ZWM7CiAgICAtLXB2LWxpbmUtc3Ryb25nOiAjY2ZkNmUwOwogICAgLS1wdi1kYXJrOiAjMGEwZDEzOwoKICAgIC0tcHYtcmFkaXVzLXNtOiA4cHg7CiAgICAtLXB2LXJhZGl1cy1tZDogMTRweDsKICAgIC0tcHYtcmFkaXVzLWxnOiAyMHB4OwogICAgLS1wdi1yYWRpdXMteGw6IDI2cHg7CiAgICAtLXB2LWVhc2U6IGN1YmljLWJlemllcigwLjIyLCAwLjYxLCAwLjM2LCAxKTsKCiAgICBtaW4taGVpZ2h0OiAxMDB2aDsKICAgIG1pbi1oZWlnaHQ6IDEwMGR2aDsKICAgIHBhZGRpbmc6IGNsYW1wKDIwcHgsIDMuNHZ3LCA0NHB4KSBjbGFtcCgxOHB4LCA0dncsIDU2cHgpIDE2cHg7CiAgICBib3gtc2l6aW5nOiBib3JkZXItYm94OwogICAgb3ZlcmZsb3cteDogaGlkZGVuOwogICAgY29sb3I6IHZhcigtLXB2LXRleHQtMSk7CiAgICBmb250LWZhbWlseTogdmFyKC0tcHYtZm9udC1zYW5zKTsKICAgIC13ZWJraXQtZm9udC1zbW9vdGhpbmc6IGFudGlhbGlhc2VkOwogICAgLW1vei1vc3gtZm9udC1zbW9vdGhpbmc6IGdyYXlzY2FsZTsKICAgIHRleHQtcmVuZGVyaW5nOiBvcHRpbWl6ZUxlZ2liaWxpdHk7CiAgICBiYWNrZ3JvdW5kOgogICAgICByYWRpYWwtZ3JhZGllbnQoCiAgICAgICAgMTIwMHB4IDYyMHB4IGF0IDkyJSAtMTIlLAogICAgICAgIHJnYmEoMTEsIDkyLCAyNTUsIDAuMDgpLAogICAgICAgIHRyYW5zcGFyZW50IDU4JQogICAgICApLAogICAgICBsaW5lYXItZ3JhZGllbnQoMTgwZGVnLCAjZjNmNWY5IDAlLCB2YXIoLS1wdi1wYWdlKSAxMDAlKTsKICB9CgogIC5wdi1yb290ICosCiAgLnB2LXJvb3QgKjo6YmVmb3JlLAogIC5wdi1yb290ICo6OmFmdGVyIHsKICAgIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgfQoKICAucHYtcm9vdCBidXR0b24gewogICAgZm9udDogaW5oZXJpdDsKICB9CgogIC5wdi1jb250YWluZXIgewogICAgd2lkdGg6IDEwMCU7CiAgICBtYXgtd2lkdGg6IDEyNDBweDsKICAgIG1hcmdpbjogMCBhdXRvOwogIH0KCiAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAg5ryU56S65paH56i/5aS06YOoCiAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgLnB2LWhlYWRlciB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGZsZXgtZW5kOwogICAganVzdGlmeS1jb250ZW50OiBzcGFjZS1iZXR3ZWVuOwogICAgZ2FwOiAzMnB4OwogICAgcGFkZGluZzogOHB4IDAgMzBweDsKICB9CgogIC5wdi1oZWFkZXItY29weSB7CiAgICBtaW4td2lkdGg6IDA7CiAgfQoKICAucHYta2lja2VyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiAxMnB4OwogICAgbWFyZ2luLWJvdHRvbTogMjBweDsKICAgIGNvbG9yOiB2YXIoLS1wdi10ZXh0LTMpOwogICAgZm9udC1mYW1pbHk6IHZhcigtLXB2LWZvbnQtbW9ubyk7CiAgICBmb250LXNpemU6IDEwcHg7CiAgICBmb250LXdlaWdodDogNTAwOwogICAgbGV0dGVyLXNwYWNpbmc6IDAuMTZlbTsKICAgIGxpbmUtaGVpZ2h0OiAxOwogICAgdGV4dC10cmFuc2Zvcm06IHVwcGVyY2FzZTsKICB9CgogIC5wdi1raWNrZXItbGluZSB7CiAgICB3aWR0aDogMzBweDsKICAgIGhlaWdodDogMXB4OwogICAgYmFja2dyb3VuZDogdmFyKC0tcHYtYnJhbmQpOwogIH0KCiAgLnB2LWtpY2tlci1jb3VudCB7CiAgICBwYWRkaW5nLWxlZnQ6IDEycHg7CiAgICBib3JkZXItbGVmdDogMXB4IHNvbGlkIHZhcigtLXB2LWxpbmUtc3Ryb25nKTsKICAgIGNvbG9yOiB2YXIoLS1wdi10ZXh0LTIpOwogICAgbGV0dGVyLXNwYWNpbmc6IDAuMWVtOwogIH0KCiAgLnB2LXRpdGxlIHsKICAgIG1heC13aWR0aDogOTAwcHg7CiAgICBtYXJnaW46IDA7CiAgICBjb2xvcjogdmFyKC0tcHYtaW5rKTsKICAgIGZvbnQtc2l6ZTogY2xhbXAoMzRweCwgNC40dncsIDU2cHgpOwogICAgZm9udC13ZWlnaHQ6IDY1MDsKICAgIGxldHRlci1zcGFjaW5nOiAtMC4wNGVtOwogICAgbGluZS1oZWlnaHQ6IDEuMDg7CiAgICB0ZXh0LXdyYXA6IGJhbGFuY2U7CiAgICBvdmVyZmxvdy13cmFwOiBicmVhay13b3JkOwogIH0KCiAgLnB2LXN1YnRpdGxlIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiAxMnB4OwogICAgbWFyZ2luOiAxOHB4IDAgMDsKICAgIGNvbG9yOiB2YXIoLS1wdi10ZXh0LTIpOwogICAgZm9udC1zaXplOiAxM3B4OwogICAgZm9udC13ZWlnaHQ6IDQ1MDsKICAgIGxldHRlci1zcGFjaW5nOiAwLjAxZW07CiAgfQoKICAucHYtbWV0YS1kb3QgewogICAgd2lkdGg6IDNweDsKICAgIGhlaWdodDogM3B4OwogICAgYm9yZGVyLXJhZGl1czogNTAlOwogICAgYmFja2dyb3VuZDogdmFyKC0tcHYtdGV4dC0zKTsKICB9CgogIC5wdi1wbGF5LWJ0biB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IGNlbnRlcjsKICAgIGdhcDogMTBweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgbWluLWhlaWdodDogNTBweDsKICAgIHBhZGRpbmc6IDAgMjJweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkIHZhcigtLXB2LWluayk7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6IHZhcigtLXB2LWluayk7CiAgICBjb2xvcjogI2ZmZmZmZjsKICAgIGZvbnQtc2l6ZTogMTNweDsKICAgIGZvbnQtd2VpZ2h0OiA1NTA7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wMWVtOwogICAgY3Vyc29yOiBwb2ludGVyOwogICAgYm94LXNoYWRvdzogMCAxMnB4IDMwcHggLTE4cHggcmdiYSgxMiwgMTcsIDI2LCAwLjY1KTsKICAgIHRyYW5zaXRpb246CiAgICAgIGJhY2tncm91bmQtY29sb3IgMC4yNXMgdmFyKC0tcHYtZWFzZSksCiAgICAgIGJvcmRlci1jb2xvciAwLjI1cyB2YXIoLS1wdi1lYXNlKSwKICAgICAgdHJhbnNmb3JtIDAuMjVzIHZhcigtLXB2LWVhc2UpLAogICAgICBib3gtc2hhZG93IDAuMjVzIHZhcigtLXB2LWVhc2UpOwogIH0KCiAgLnB2LXBsYXktYnRuOmhvdmVyIHsKICAgIGJvcmRlci1jb2xvcjogdmFyKC0tcHYtYnJhbmQpOwogICAgYmFja2dyb3VuZDogdmFyKC0tcHYtYnJhbmQpOwogICAgYm94LXNoYWRvdzogMCAxNHB4IDM0cHggLTE2cHggcmdiYSgxMSwgOTIsIDI1NSwgMC41NSk7CiAgICB0cmFuc2Zvcm06IHRyYW5zbGF0ZVkoLTFweCk7CiAgfQoKICAucHYtcGxheS1idG46Zm9jdXMtdmlzaWJsZSB7CiAgICBvdXRsaW5lOiAycHggc29saWQgdmFyKC0tcHYtYnJhbmQpOwogICAgb3V0bGluZS1vZmZzZXQ6IDNweDsKICB9CgogIC5wdi1wbGF5LWljb24gewogICAgd2lkdGg6IDE3cHg7CiAgICBoZWlnaHQ6IDE3cHg7CiAgfQoKICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICDlvJXmk47nirbmgIHmj5DnpLoKICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAucHYtZW5naW5lLWJhbm5lciB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogMTBweDsKICAgIG1hcmdpbi1ib3R0b206IDIwcHg7CiAgICBwYWRkaW5nOiAxMXB4IDE0cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE4MCwgNjMsIDYzLCAwLjIyKTsKICAgIGJvcmRlci1yYWRpdXM6IHZhcigtLXB2LXJhZGl1cy1tZCk7CiAgICBiYWNrZ3JvdW5kOiAjZmZmN2Y3OwogICAgY29sb3I6ICM4ZjNkM2Q7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBsaW5lLWhlaWdodDogMS41OwogIH0KCiAgLnB2LWVuZ2luZS1iYW5uZXItZG90IHsKICAgIHdpZHRoOiA3cHg7CiAgICBoZWlnaHQ6IDdweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgYm9yZGVyLXJhZGl1czogNTAlOwogICAgYmFja2dyb3VuZDogI2Q2NDU0NTsKICB9CgogIC5wdi1lbmdpbmUtYmFubmVyLXRleHQgewogICAgbWluLXdpZHRoOiAwOwogICAgb3ZlcmZsb3c6IGhpZGRlbjsKICAgIHRleHQtb3ZlcmZsb3c6IGVsbGlwc2lzOwogICAgd2hpdGUtc3BhY2U6IG5vd3JhcDsKICB9CgogIC5wdi1lbmdpbmUtcmV0cnkgewogICAgbWFyZ2luLWxlZnQ6IGF1dG87CiAgICBwYWRkaW5nOiA1cHggMTFweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkIHJnYmEoMTgwLCA2MywgNjMsIDAuMjgpOwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogICAgY29sb3I6ICM4ZjNkM2Q7CiAgICBmb250LXNpemU6IDExcHg7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgfQoKICAucHYtZW5naW5lLXJldHJ5OmhvdmVyIHsKICAgIGJhY2tncm91bmQ6ICNmZmVjZWM7CiAgfQoKICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICDlubvnga/niYfnvKnnlaXlm77nvZHmoLwKICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAucHYtZ3JpZCB7CiAgICBkaXNwbGF5OiBncmlkOwogICAgZ3JpZC10ZW1wbGF0ZS1jb2x1bW5zOiByZXBlYXQoMywgbWlubWF4KDAsIDFmcikpOwogICAgZ2FwOiAyNnB4IDI0cHg7CiAgfQoKICAucHYtY29udGFpbmVyLS1mb3VyIHsKICAgIG1heC13aWR0aDogMTAwMHB4OwogIH0KCiAgQG1lZGlhIChtaW4td2lkdGg6IDEwODFweCkgewogICAgLnB2LWdyaWQtLWZvdXIgewogICAgICBncmlkLXRlbXBsYXRlLWNvbHVtbnM6IHJlcGVhdCgyLCBtaW5tYXgoMCwgMWZyKSk7CiAgICB9CiAgfQoKICAucHYtY2FyZCB7CiAgICBtaW4td2lkdGg6IDA7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgICBvdXRsaW5lOiBub25lOwogIH0KCiAgLnB2LXRodW1iIHsKICAgIHBvc2l0aW9uOiByZWxhdGl2ZTsKICAgIHdpZHRoOiAxMDAlOwogICAgYXNwZWN0LXJhdGlvOiAxNiAvIDk7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYm9yZGVyOiAxcHggc29saWQgdmFyKC0tcHYtbGluZSk7CiAgICBib3JkZXItcmFkaXVzOiB2YXIoLS1wdi1yYWRpdXMtbWQpOwogICAgYmFja2dyb3VuZDogdmFyKC0tcHYtcGFwZXIpOwogICAgYm94LXNoYWRvdzoKICAgICAgMCAxcHggMnB4IHJnYmEoMTIsIDE3LCAyNiwgMC4wMyksCiAgICAgIDAgMThweCA0MHB4IC0zMnB4IHJnYmEoMTIsIDE3LCAyNiwgMC4yOCk7CiAgICB0cmFuc2l0aW9uOgogICAgICB0cmFuc2Zvcm0gMC4zNXMgdmFyKC0tcHYtZWFzZSksCiAgICAgIGJveC1zaGFkb3cgMC4zNXMgdmFyKC0tcHYtZWFzZSksCiAgICAgIGJvcmRlci1jb2xvciAwLjM1cyB2YXIoLS1wdi1lYXNlKTsKICB9CgogIC5wdi10aHVtYjo6YWZ0ZXIgewogICAgY29udGVudDogIiI7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICBpbnNldDogMDsKICAgIHotaW5kZXg6IDI7CiAgICBib3JkZXItcmFkaXVzOiBpbmhlcml0OwogICAgYm94LXNoYWRvdzogaW5zZXQgMCAwIDAgMXB4IHJnYmEoMTIsIDE3LCAyNiwgMC4wNDUpOwogICAgcG9pbnRlci1ldmVudHM6IG5vbmU7CiAgfQoKICAucHYtdGh1bWItLWNvdmVyIHsKICAgIGJvcmRlci1jb2xvcjogcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjA4KTsKICAgIGJhY2tncm91bmQ6ICMwZDEyMWI7CiAgICBib3gtc2hhZG93OgogICAgICAwIDFweCAycHggcmdiYSgxMiwgMTcsIDI2LCAwLjE2KSwKICAgICAgMCAyNHB4IDUwcHggLTMycHggcmdiYSgxMiwgMTcsIDI2LCAwLjcpOwogIH0KCiAgLnB2LXRodW1iLS1jb3Zlcjo6YWZ0ZXIgewogICAgYm94LXNoYWRvdzogaW5zZXQgMCAwIDAgMXB4IHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4wOCk7CiAgfQoKICAucHYtY2FyZDpob3ZlciAucHYtdGh1bWIgewogICAgYm9yZGVyLWNvbG9yOiB2YXIoLS1wdi1saW5lLXN0cm9uZyk7CiAgICBib3gtc2hhZG93OgogICAgICAwIDJweCA0cHggcmdiYSgxMiwgMTcsIDI2LCAwLjA0KSwKICAgICAgMCAyNnB4IDU0cHggLTMwcHggcmdiYSgxMiwgMTcsIDI2LCAwLjM2KTsKICAgIHRyYW5zZm9ybTogdHJhbnNsYXRlWSgtNHB4KTsKICB9CgogIC5wdi1jYXJkOmZvY3VzLXZpc2libGUgLnB2LXRodW1iIHsKICAgIG91dGxpbmU6IDJweCBzb2xpZCB2YXIoLS1wdi1icmFuZCk7CiAgICBvdXRsaW5lLW9mZnNldDogM3B4OwogIH0KCiAgLnB2LXRodW1iLWNhbnZhcywKICAucHYtdGh1bWItY2FudmFzIGNhbnZhcyB7CiAgICBkaXNwbGF5OiBibG9jazsKICAgIHdpZHRoOiAxMDAlOwogICAgaGVpZ2h0OiAxMDAlOwogIH0KCiAgLnB2LXRodW1iLWNhbnZhcyB7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICBpbnNldDogMDsKICB9CgogIC5wdi10aHVtYi1jYW52YXMgY2FudmFzIHsKICAgIHdpZHRoOiAxMDAlICFpbXBvcnRhbnQ7CiAgICBoZWlnaHQ6IDEwMCUgIWltcG9ydGFudDsKICB9CgogIC5wdi10aHVtYi1sb2FkaW5nIHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIGluc2V0OiAwOwogICAgei1pbmRleDogMTsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYmFja2dyb3VuZDogbGluZWFyLWdyYWRpZW50KDE4MGRlZywgI2ZiZmNmZSAwJSwgI2Y0ZjZmYSAxMDAlKTsKICB9CgogIC5wdi10aHVtYi0tY292ZXIgLnB2LXRodW1iLWxvYWRpbmcgewogICAgYmFja2dyb3VuZDogbGluZWFyLWdyYWRpZW50KDE1MGRlZywgIzBkMTIxYiAwJSwgIzBhMGQxMyAxMDAlKTsKICB9CgogIC5wdi10aHVtYi1zaGltbWVyIHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIGluc2V0OiAwOwogICAgYmFja2dyb3VuZDogbGluZWFyLWdyYWRpZW50KAogICAgICAxMDBkZWcsCiAgICAgIHRyYW5zcGFyZW50IDE4JSwKICAgICAgcmdiYSgxMSwgOTIsIDI1NSwgMC4wOCkgNDIlLAogICAgICByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMTQpIDUyJSwKICAgICAgdHJhbnNwYXJlbnQgNzglCiAgICApOwogICAgYmFja2dyb3VuZC1zaXplOiAyMjAlIDEwMCU7CiAgICBhbmltYXRpb246IHB2LXNoaW1tZXIgMS41cyBsaW5lYXIgaW5maW5pdGU7CiAgfQoKICBAa2V5ZnJhbWVzIHB2LXNoaW1tZXIgewogICAgZnJvbSB7CiAgICAgIGJhY2tncm91bmQtcG9zaXRpb246IDE4MCUgMDsKICAgIH0KICAgIHRvIHsKICAgICAgYmFja2dyb3VuZC1wb3NpdGlvbjogLTgwJSAwOwogICAgfQogIH0KCiAgLnB2LXRodW1iLWxvYWRpbmctdGV4dCB7CiAgICBwb3NpdGlvbjogcmVsYXRpdmU7CiAgICBjb2xvcjogdmFyKC0tcHYtdGV4dC0zKTsKICAgIGZvbnQtZmFtaWx5OiB2YXIoLS1wdi1mb250LW1vbm8pOwogICAgZm9udC1zaXplOiA5cHg7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wOGVtOwogIH0KCiAgLnB2LXRodW1iLS1jb3ZlciAucHYtdGh1bWItbG9hZGluZy10ZXh0IHsKICAgIGNvbG9yOiAjNzE4MDk3OwogIH0KCiAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAg5Y2h54mH5bqV6YOo5YWD5L+h5oGvIC8g6aG16Z2i6aG16ISaCiAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgLnB2LWNhcmQtbWV0YSB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogOXB4OwogICAgcGFkZGluZzogMTJweCAycHggMDsKICAgIGNvbG9yOiB2YXIoLS1wdi10ZXh0LTMpOwogICAgZm9udC1mYW1pbHk6IHZhcigtLXB2LWZvbnQtbW9ubyk7CiAgICBmb250LXNpemU6IDlweDsKICAgIGxldHRlci1zcGFjaW5nOiAwLjA4ZW07CiAgICBsaW5lLWhlaWdodDogMTsKICAgIHRleHQtdHJhbnNmb3JtOiB1cHBlcmNhc2U7CiAgfQoKICAucHYtY2FyZC1pbmRleCB7CiAgICBjb2xvcjogdmFyKC0tcHYtYnJhbmQpOwogICAgZm9udC13ZWlnaHQ6IDYwMDsKICB9CgogIC5wdi1jYXJkLWtpbmQgewogICAgY29sb3I6IHZhcigtLXB2LXRleHQtMik7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wNGVtOwogIH0KCiAgLnB2LWNhcmQtb3BlbiB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBnYXA6IDRweDsKICAgIG1hcmdpbi1sZWZ0OiBhdXRvOwogICAgb3BhY2l0eTogMDsKICAgIGNvbG9yOiB2YXIoLS1wdi1icmFuZCk7CiAgICBmb250LWZhbWlseTogdmFyKC0tcHYtZm9udC1zYW5zKTsKICAgIGZvbnQtc2l6ZTogMTBweDsKICAgIGxldHRlci1zcGFjaW5nOiAwLjAyZW07CiAgICB0ZXh0LXRyYW5zZm9ybTogbm9uZTsKICAgIHRyYW5zaXRpb246IG9wYWNpdHkgMC4yNXMgdmFyKC0tcHYtZWFzZSk7CiAgfQoKICAucHYtY2FyZC1vcGVuIHN2ZyB7CiAgICB3aWR0aDogMTJweDsKICAgIGhlaWdodDogMTJweDsKICB9CgogIC5wdi1jYXJkOmhvdmVyIC5wdi1jYXJkLW9wZW4sCiAgLnB2LWNhcmQ6Zm9jdXMtdmlzaWJsZSAucHYtY2FyZC1vcGVuIHsKICAgIG9wYWNpdHk6IDE7CiAgfQoKICAucHYtZm9vdGVyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IHNwYWNlLWJldHdlZW47CiAgICBnYXA6IDIwcHg7CiAgICBtYXJnaW4tdG9wOiAyNHB4OwogICAgcGFkZGluZy10b3A6IDE0cHg7CiAgICBib3JkZXItdG9wOiAxcHggc29saWQgdmFyKC0tcHYtbGluZSk7CiAgICBjb2xvcjogdmFyKC0tcHYtdGV4dC0zKTsKICAgIGZvbnQtZmFtaWx5OiB2YXIoLS1wdi1mb250LW1vbm8pOwogICAgZm9udC1zaXplOiA5cHg7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wOGVtOwogICAgbGluZS1oZWlnaHQ6IDEuNTsKICAgIHRleHQtdHJhbnNmb3JtOiB1cHBlcmNhc2U7CiAgfQoKICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICDmlL7mmKDopobnm5blsYIgJiDmjqfliLbmnaEKICAgICA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAucHYtb3ZlcmxheSB7CiAgICBwb3NpdGlvbjogZml4ZWQ7CiAgICBpbnNldDogMDsKICAgIHotaW5kZXg6IDk5OTk7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgb3ZlcmZsb3c6IGhpZGRlbjsKICAgIGJhY2tncm91bmQ6ICMwODBiMTA7CiAgfQoKICAucHYtdG9vbGJhciB7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICB0b3A6IDIycHg7CiAgICBsZWZ0OiA1MCU7CiAgICB6LWluZGV4OiAzMDsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiAycHg7CiAgICBwYWRkaW5nOiA1cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMSk7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMTIsIDE2LCAyMywgMC43Nik7CiAgICBjb2xvcjogI2ZmZmZmZjsKICAgIGJveC1zaGFkb3c6IDAgMThweCA0MHB4IC0yMnB4IHJnYmEoMCwgMCwgMCwgMC44KTsKICAgIGJhY2tkcm9wLWZpbHRlcjogc2F0dXJhdGUoMTYwJSkgYmx1cigxOHB4KTsKICAgIC13ZWJraXQtYmFja2Ryb3AtZmlsdGVyOiBzYXR1cmF0ZSgxNjAlKSBibHVyKDE4cHgpOwogICAgdHJhbnNmb3JtOiB0cmFuc2xhdGVYKC01MCUpOwogICAgdHJhbnNpdGlvbjoKICAgICAgb3BhY2l0eSAwLjM1cyB2YXIoLS1wdi1lYXNlKSwKICAgICAgdHJhbnNmb3JtIDAuMzVzIHZhcigtLXB2LWVhc2UpOwogIH0KCiAgLnB2LXRvb2xiYXItLWlkbGUgewogICAgdmlzaWJpbGl0eTogaGlkZGVuOwogICAgb3BhY2l0eTogMDsKICAgIHBvaW50ZXItZXZlbnRzOiBub25lOwogICAgdHJhbnNmb3JtOiB0cmFuc2xhdGUoLTUwJSwgLTEwcHgpOwogICAgdHJhbnNpdGlvbjoKICAgICAgb3BhY2l0eSAwLjM1cyB2YXIoLS1wdi1lYXNlKSwKICAgICAgdHJhbnNmb3JtIDAuMzVzIHZhcigtLXB2LWVhc2UpLAogICAgICB2aXNpYmlsaXR5IDBzIDAuMzVzOwogIH0KCiAgLnB2LXRvb2wtYnRuIHsKICAgIGRpc3BsYXk6IGlubGluZS1mbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGp1c3RpZnktY29udGVudDogY2VudGVyOwogICAgd2lkdGg6IDM0cHg7CiAgICBoZWlnaHQ6IDM0cHg7CiAgICBwYWRkaW5nOiAwOwogICAgYm9yZGVyOiAwOwogICAgYm9yZGVyLXJhZGl1czogNTAlOwogICAgYmFja2dyb3VuZDogdHJhbnNwYXJlbnQ7CiAgICBjb2xvcjogI2M3ZDBkZDsKICAgIGN1cnNvcjogcG9pbnRlcjsKICAgIHRyYW5zaXRpb246CiAgICAgIGJhY2tncm91bmQtY29sb3IgMC4ycyB2YXIoLS1wdi1lYXNlKSwKICAgICAgY29sb3IgMC4ycyB2YXIoLS1wdi1lYXNlKTsKICB9CgogIC5wdi10b29sLWJ0biBzdmcgewogICAgd2lkdGg6IDE3cHg7CiAgICBoZWlnaHQ6IDE3cHg7CiAgfQoKICAucHYtdG9vbC1idG46aG92ZXI6bm90KDpkaXNhYmxlZCkgewogICAgYmFja2dyb3VuZDogcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjEpOwogICAgY29sb3I6ICNmZmZmZmY7CiAgfQoKICAucHYtdG9vbC1idG46ZGlzYWJsZWQgewogICAgb3BhY2l0eTogMC40MjsKICAgIGN1cnNvcjogd2FpdDsKICB9CgogIC5wdi1leHBvcnQtYnRuIHsKICAgIHdpZHRoOiBhdXRvOwogICAgbWluLXdpZHRoOiAzNHB4OwogICAgcGFkZGluZzogMCAxMHB4OwogICAgZ2FwOiA2cHg7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICB9CgogIC5wdi1leHBvcnQtbGFiZWwgewogICAgY29sb3I6IGluaGVyaXQ7CiAgICBmb250LXNpemU6IDEwcHg7CiAgICBmb250LXdlaWdodDogNTAwOwogICAgbGV0dGVyLXNwYWNpbmc6IDAuMDJlbTsKICAgIGxpbmUtaGVpZ2h0OiAxOwogIH0KCiAgLnB2LWV4cG9ydC1zcGlubmVyIHsKICAgIGRpc3BsYXk6IGlubGluZS1ibG9jazsKICAgIHdpZHRoOiAxNHB4OwogICAgaGVpZ2h0OiAxNHB4OwogICAgYm9yZGVyOiAycHggc29saWQgcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjIyKTsKICAgIGJvcmRlci10b3AtY29sb3I6ICNmZmZmZmY7CiAgICBib3JkZXItcmFkaXVzOiA1MCU7CiAgICBhbmltYXRpb246IHB2LXNwaW4gMC43NXMgbGluZWFyIGluZmluaXRlOwogIH0KCiAgQGtleWZyYW1lcyBwdi1zcGluIHsKICAgIHRvIHsKICAgICAgdHJhbnNmb3JtOiByb3RhdGUoMzYwZGVnKTsKICAgIH0KICB9CgogIC5wdi10b29sLWJ0bjpmb2N1cy12aXNpYmxlLAogIC5wdi1leGl0LWJ0bjpmb2N1cy12aXNpYmxlLAogIC5wdi1zdGFnZS1yZXRyeTpmb2N1cy12aXNpYmxlIHsKICAgIG91dGxpbmU6IDJweCBzb2xpZCAjN2VhN2ZmOwogICAgb3V0bGluZS1vZmZzZXQ6IDFweDsKICB9CgogIC5wdi1jb3VudGVyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogYmFzZWxpbmU7CiAgICBnYXA6IDNweDsKICAgIG1pbi13aWR0aDogNTBweDsKICAgIHBhZGRpbmc6IDAgOHB4OwogICAgY29sb3I6ICM3NzgzOTY7CiAgICBmb250LWZhbWlseTogdmFyKC0tcHYtZm9udC1tb25vKTsKICAgIGZvbnQtc2l6ZTogMTBweDsKICAgIGxldHRlci1zcGFjaW5nOiAwLjA0ZW07CiAgICBsaW5lLWhlaWdodDogMTsKICAgIHdoaXRlLXNwYWNlOiBub3dyYXA7CiAgfQoKICAucHYtY291bnRlciBzdHJvbmcgewogICAgY29sb3I6ICNmZmZmZmY7CiAgICBmb250LXdlaWdodDogNjAwOwogIH0KCiAgLnB2LXRvb2xiYXItZGl2aWRlciB7CiAgICB3aWR0aDogMXB4OwogICAgaGVpZ2h0OiAxNnB4OwogICAgbWFyZ2luOiAwIDRweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4xNCk7CiAgfQoKICAucHYtZXhpdC1idG4gewogICAgZGlzcGxheTogaW5saW5lLWZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiA3cHg7CiAgICBoZWlnaHQ6IDM0cHg7CiAgICBwYWRkaW5nOiAwIDExcHggMCAxMnB4OwogICAgYm9yZGVyOiAwOwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiB0cmFuc3BhcmVudDsKICAgIGNvbG9yOiAjYzdkMGRkOwogICAgZm9udC1zaXplOiAxMHB4OwogICAgbGV0dGVyLXNwYWNpbmc6IDAuMDJlbTsKICAgIGN1cnNvcjogcG9pbnRlcjsKICAgIHRyYW5zaXRpb246CiAgICAgIGJhY2tncm91bmQtY29sb3IgMC4ycyB2YXIoLS1wdi1lYXNlKSwKICAgICAgY29sb3IgMC4ycyB2YXIoLS1wdi1lYXNlKTsKICB9CgogIC5wdi1leGl0LWJ0bjpob3ZlciB7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMSk7CiAgICBjb2xvcjogI2ZmZmZmZjsKICB9CgogIC5wdi1leGl0LWJ0biBrYmQgewogICAgcGFkZGluZzogMnB4IDVweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkIHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4xNik7CiAgICBib3JkZXItcmFkaXVzOiA0cHg7CiAgICBjb2xvcjogIzhlOWFhYjsKICAgIGZvbnQtZmFtaWx5OiB2YXIoLS1wdi1mb250LW1vbm8pOwogICAgZm9udC1zaXplOiA4cHg7CiAgICBsaW5lLWhlaWdodDogMTsKICB9CgogIC5wdi1yb3RhdGUtaGludCB7CiAgICBkaXNwbGF5OiBub25lOwogIH0KCiAgLnB2LXNyLW9ubHkgewogICAgcG9zaXRpb246IGFic29sdXRlOwogICAgd2lkdGg6IDFweDsKICAgIGhlaWdodDogMXB4OwogICAgcGFkZGluZzogMDsKICAgIG1hcmdpbjogLTFweDsKICAgIG92ZXJmbG93OiBoaWRkZW47CiAgICBjbGlwOiByZWN0KDAsIDAsIDAsIDApOwogICAgd2hpdGUtc3BhY2U6IG5vd3JhcDsKICAgIGJvcmRlcjogMDsKICB9CgogIC5wdi1leHBvcnQtdG9hc3QgewogICAgcG9zaXRpb246IGFic29sdXRlOwogICAgbGVmdDogNTAlOwogICAgYm90dG9tOiAyMnB4OwogICAgei1pbmRleDogMzM7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogOHB4OwogICAgcGFkZGluZzogOXB4IDE0cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMTIpOwogICAgYm9yZGVyLXJhZGl1czogOTk5cHg7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDEyLCAxNiwgMjMsIDAuODQpOwogICAgY29sb3I6ICNkZmU3ZjM7CiAgICBmb250LWZhbWlseTogdmFyKC0tcHYtZm9udC1tb25vKTsKICAgIGZvbnQtc2l6ZTogMTBweDsKICAgIGxldHRlci1zcGFjaW5nOiAwLjA0ZW07CiAgICBsaW5lLWhlaWdodDogMTsKICAgIHBvaW50ZXItZXZlbnRzOiBub25lOwogICAgdHJhbnNmb3JtOiB0cmFuc2xhdGVYKC01MCUpOwogICAgYm94LXNoYWRvdzogMCAxMnB4IDMwcHggLTE4cHggcmdiYSgwLCAwLCAwLCAwLjkpOwogICAgYmFja2Ryb3AtZmlsdGVyOiBzYXR1cmF0ZSgxNjAlKSBibHVyKDE2cHgpOwogICAgLXdlYmtpdC1iYWNrZHJvcC1maWx0ZXI6IHNhdHVyYXRlKDE2MCUpIGJsdXIoMTZweCk7CiAgfQoKICAucHYtZXhwb3J0LXRvYXN0LS1lcnJvciB7CiAgICBib3JkZXItY29sb3I6IHJnYmEoMjU1LCAxMjAsIDEyMCwgMC4yOCk7CiAgICBjb2xvcjogI2ZmYjRiNDsKICB9CgogIC5wdi1leHBvcnQtdG9hc3QgLnB2LWV4cG9ydC1zcGlubmVyIHsKICAgIGJvcmRlci1jb2xvcjogcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjE2KTsKICAgIGJvcmRlci10b3AtY29sb3I6ICNmZmZmZmY7CiAgfQoKICAucHYtcHJvZ3Jlc3MgewogICAgcG9zaXRpb246IGFic29sdXRlOwogICAgcmlnaHQ6IDA7CiAgICBib3R0b206IDA7CiAgICBsZWZ0OiAwOwogICAgei1pbmRleDogMzE7CiAgICBoZWlnaHQ6IDJweDsKICAgIG92ZXJmbG93OiBoaWRkZW47CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMDgpOwogIH0KCiAgLnB2LXByb2dyZXNzIHNwYW4gewogICAgZGlzcGxheTogYmxvY2s7CiAgICBoZWlnaHQ6IDEwMCU7CiAgICBiYWNrZ3JvdW5kOiBsaW5lYXItZ3JhZGllbnQoOTBkZWcsIHZhcigtLXB2LWJyYW5kLWRlZXApLCAjNWY5NWZmKTsKICAgIHRyYW5zaXRpb246IHdpZHRoIDAuNDVzIHZhcigtLXB2LWVhc2UpOwogIH0KCiAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgMTkyMCDDlyAxMDgwIExlYWZ0ZXIg6Iie5Y+wCiAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgLnB2LXN0YWdlIHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIGluc2V0OiAwOwogICAgZGlzcGxheTogZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IGNlbnRlcjsKICAgIHdpZHRoOiAxMDAlOwogICAgaGVpZ2h0OiAxMDAlOwogICAgb3ZlcmZsb3c6IGhpZGRlbjsKICAgIGJhY2tncm91bmQ6ICMwODBiMTA7CiAgICB0b3VjaC1hY3Rpb246IG5vbmU7CiAgfQoKICAucHYtc3RhZ2Utc2NlbmUgewogICAgcG9zaXRpb246IGFic29sdXRlOwogICAgdG9wOiA1MCU7CiAgICBsZWZ0OiA1MCU7CiAgICB3aWR0aDogMTkyMHB4OwogICAgaGVpZ2h0OiAxMDgwcHg7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYmFja2dyb3VuZDogdmFyKC0tcHYtcGFwZXIpOwogICAgYm94LXNoYWRvdzogMCA1MHB4IDE0MHB4IC03MHB4IHJnYmEoMCwgMCwgMCwgMC44NSk7CiAgICB0cmFuc2Zvcm06IHRyYW5zbGF0ZSgtNTAlLCAtNTAlKSBzY2FsZSh2YXIoLS1wdi1zdGFnZS1zY2FsZSwgMSkpOwogICAgdHJhbnNmb3JtLW9yaWdpbjogY2VudGVyIGNlbnRlcjsKICB9CgogIC5wdi1zdGFnZS1ob3N0IHsKICAgIHdpZHRoOiAxMDAlOwogICAgaGVpZ2h0OiAxMDAlOwogIH0KCiAgLnB2LXN0YWdlLWhvc3QgY2FudmFzIHsKICAgIGRpc3BsYXk6IGJsb2NrOwogICAgd2lkdGg6IDEwMCUgIWltcG9ydGFudDsKICAgIGhlaWdodDogMTAwJSAhaW1wb3J0YW50OwogIH0KCiAgLnB2LXN0YWdlLXN0YXRlIHsKICAgIHBvc2l0aW9uOiBhYnNvbHV0ZTsKICAgIHRvcDogNTAlOwogICAgbGVmdDogNTAlOwogICAgei1pbmRleDogNTsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAgZ2FwOiAxMHB4OwogICAgcGFkZGluZzogMTNweCAxOHB4OwogICAgYm9yZGVyOiAxcHggc29saWQgcmdiYSgyNTUsIDI1NSwgMjU1LCAwLjEyKTsKICAgIGJvcmRlci1yYWRpdXM6IDk5OXB4OwogICAgYmFja2dyb3VuZDogcmdiYSgxMiwgMTYsIDIzLCAwLjg0KTsKICAgIGNvbG9yOiAjZGJlNGYzOwogICAgZm9udC1mYW1pbHk6IHZhcigtLXB2LWZvbnQtbW9ubyk7CiAgICBmb250LXNpemU6IDExcHg7CiAgICBsZXR0ZXItc3BhY2luZzogMC4wM2VtOwogICAgdHJhbnNmb3JtOiB0cmFuc2xhdGUoLTUwJSwgLTUwJSk7CiAgICBib3gtc2hhZG93OiAwIDE4cHggNDRweCAtMjRweCByZ2JhKDAsIDAsIDAsIDAuOSk7CiAgICBiYWNrZHJvcC1maWx0ZXI6IHNhdHVyYXRlKDE2MCUpIGJsdXIoMTZweCk7CiAgICAtd2Via2l0LWJhY2tkcm9wLWZpbHRlcjogc2F0dXJhdGUoMTYwJSkgYmx1cigxNnB4KTsKICB9CgogIC5wdi1zdGFnZS1yZXRyeSB7CiAgICBwYWRkaW5nOiA0cHggMTBweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkIHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4xOCk7CiAgICBib3JkZXItcmFkaXVzOiA5OTlweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4wOCk7CiAgICBjb2xvcjogI2ZmZmZmZjsKICAgIGZvbnQtc2l6ZTogMTBweDsKICAgIGN1cnNvcjogcG9pbnRlcjsKICB9CgogIC5wdi1zdGFnZS1yZXRyeTpob3ZlciB7CiAgICBiYWNrZ3JvdW5kOiByZ2JhKDI1NSwgMjU1LCAyNTUsIDAuMTYpOwogIH0KCiAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAg5ZON5bqU5byPCiAgICAgPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgQG1lZGlhIChtYXgtd2lkdGg6IDEwODBweCkgewogICAgLnB2LWdyaWQgewogICAgICBncmlkLXRlbXBsYXRlLWNvbHVtbnM6IHJlcGVhdCgyLCBtaW5tYXgoMCwgMWZyKSk7CiAgICB9CiAgfQoKICBAbWVkaWEgKG1heC13aWR0aDogNzIwcHgpIHsKICAgIC5wdi1yb290IHsKICAgICAgcGFkZGluZzogMjBweCAxNnB4IDI2cHg7CiAgICB9CgogICAgLnB2LWhlYWRlciB7CiAgICAgIGZsZXgtZGlyZWN0aW9uOiBjb2x1bW47CiAgICAgIGFsaWduLWl0ZW1zOiBmbGV4LXN0YXJ0OwogICAgICBnYXA6IDI0cHg7CiAgICAgIHBhZGRpbmctYm90dG9tOiAyOHB4OwogICAgfQoKICAgIC5wdi10aXRsZSB7CiAgICAgIGZvbnQtc2l6ZTogY2xhbXAoMzBweCwgOXZ3LCA0MnB4KTsKICAgIH0KCiAgICAucHYtc3VidGl0bGUgewogICAgICBmbGV4LXdyYXA6IHdyYXA7CiAgICAgIGdhcDogOHB4OwogICAgICBmb250LXNpemU6IDEycHg7CiAgICB9CgogICAgLnB2LXBsYXktYnRuIHsKICAgICAgd2lkdGg6IDEwMCU7CiAgICB9CgogICAgLnB2LWdyaWQgewogICAgICBncmlkLXRlbXBsYXRlLWNvbHVtbnM6IG1pbm1heCgwLCAxZnIpOwogICAgICBnYXA6IDI0cHg7CiAgICB9CgogICAgLnB2LWZvb3RlciB7CiAgICAgIGZsZXgtZGlyZWN0aW9uOiBjb2x1bW47CiAgICAgIGdhcDogNXB4OwogICAgICBtYXJnaW4tdG9wOiAyNnB4OwogICAgfQoKICAgIC5wdi10b29sYmFyIHsKICAgICAgdG9wOiAxMnB4OwogICAgICBwYWRkaW5nOiA0cHg7CiAgICB9CgogICAgLnB2LXRvb2wtYnRuIHsKICAgICAgd2lkdGg6IDMycHg7CiAgICAgIGhlaWdodDogMzJweDsKICAgIH0KCiAgICAucHYtZXhpdC1idG4gewogICAgICBoZWlnaHQ6IDMycHg7CiAgICAgIHBhZGRpbmc6IDAgOXB4OwogICAgfQoKICAgIC5wdi1leGl0LWJ0biA+IHNwYW4gewogICAgICBkaXNwbGF5OiBub25lOwogICAgfQoKICAgIC5wdi1leHBvcnQtbGFiZWwgewogICAgICBkaXNwbGF5OiBub25lOwogICAgfQoKICAgIC5wdi1leHBvcnQtYnRuIHsKICAgICAgd2lkdGg6IDMycHg7CiAgICAgIHBhZGRpbmc6IDA7CiAgICB9CgogICAgLnB2LWNvdW50ZXIgewogICAgICBtaW4td2lkdGg6IDQ0cHg7CiAgICAgIHBhZGRpbmc6IDAgNXB4OwogICAgfQogIH0KCiAgQG1lZGlhIChtYXgtd2lkdGg6IDcyMHB4KSBhbmQgKG9yaWVudGF0aW9uOiBwb3J0cmFpdCkgewogICAgLnB2LXJvdGF0ZS1oaW50IHsKICAgICAgcG9zaXRpb246IGFic29sdXRlOwogICAgICByaWdodDogNTAlOwogICAgICBib3R0b206IDE4cHg7CiAgICAgIHotaW5kZXg6IDMyOwogICAgICBkaXNwbGF5OiBibG9jazsKICAgICAgcGFkZGluZzogN3B4IDEycHg7CiAgICAgIGJvcmRlcjogMXB4IHNvbGlkIHJnYmEoMjU1LCAyNTUsIDI1NSwgMC4xMik7CiAgICAgIGJvcmRlci1yYWRpdXM6IDk5OXB4OwogICAgICBiYWNrZ3JvdW5kOiByZ2JhKDEyLCAxNiwgMjMsIDAuNyk7CiAgICAgIGNvbG9yOiAjYjljNGQ0OwogICAgICBmb250LWZhbWlseTogdmFyKC0tcHYtZm9udC1tb25vKTsKICAgICAgZm9udC1zaXplOiA5cHg7CiAgICAgIGxldHRlci1zcGFjaW5nOiAwLjA4ZW07CiAgICAgIGxpbmUtaGVpZ2h0OiAxOwogICAgICB3aGl0ZS1zcGFjZTogbm93cmFwOwogICAgICB0cmFuc2Zvcm06IHRyYW5zbGF0ZVgoNTAlKTsKICAgICAgYmFja2Ryb3AtZmlsdGVyOiBibHVyKDEycHgpOwogICAgICAtd2Via2l0LWJhY2tkcm9wLWZpbHRlcjogYmx1cigxMnB4KTsKICAgIH0KICB9CgogIEBtZWRpYSAocHJlZmVycy1yZWR1Y2VkLW1vdGlvbjogcmVkdWNlKSB7CiAgICAucHYtdGh1bWIsCiAgICAucHYtcGxheS1idG4sCiAgICAucHYtdG9vbGJhciwKICAgIC5wdi1wcm9ncmVzcyBzcGFuIHsKICAgICAgdHJhbnNpdGlvbjogbm9uZSAhaW1wb3J0YW50OwogICAgfQoKICAgIC5wdi1jYXJkOmhvdmVyIC5wdi10aHVtYiB7CiAgICAgIHRyYW5zZm9ybTogbm9uZTsKICAgIH0KCiAgICAucHYtdGh1bWItc2hpbW1lciB7CiAgICAgIGFuaW1hdGlvbjogbm9uZTsKICAgIH0KICB9Cjwvc3R5bGU+Cg=="
									}
								},
      exampleList: [{"name":"季度业务汇报","description":"输入季度工作要点，输出结构化的演示文稿 JSON。","icon":"📈"},{"name":"技术方案分享","description":"输入技术架构演进逻辑，输出技术演讲幻灯片结构。","icon":"💻"},{"name":"产品发布演示","description":"输入产品功能亮点与市场价值，输出发布会演示结构。","icon":"🚀"}],
      loadingProgressApp: {
        className: "ElementVueItem",
        task: {
          template: "/webApps/document/aigcLoading.vue",
        },
      },
      feedbackDialog: {
        visible: false,
        placeholder: "输入问题描述或建议内容。",
        message: "",
      },
      interface: {
        sendFeedback: {
          url: webCpu.documentPrefix + "/user/sendFeedback",
          method: "post",
        },
      },
      styleIndex: "",
      content: {
        inputText: "",
        outputText: "",
      },
      inputPlaceholder: "提供一份线性的大纲内容（或主题、核心观点、数据要点），AI 将其解析并重构为 JSON 结构化数据。",
      description: "",
      query: {"documentId":"ec6d0ef4ddfde4d983fdf934e57e29bb"},
      accountData: {},
      flag: WebTool.adjustSize([false, false, true]),
      isSolo: WebTool.urlQuery(location.href, "solo"),
      defaultTemplate: "",
      dataEditable: true,
      title: "LeaferJS-演示文稿",
      introduce: "根据用户输入内容或需求描述，生成HTML版本的幻灯片",
      icon: "P"
    };
},"computed":{"myAccept":function () {
      return `.html, .md, .txt, .doc, .docx, .pdf, .ppt, .pptx, .xlsx, .xls, .csv`;
    },"viewMode":function () {
      return !this.modifyEnabled && !!this.currentVersionIndex;
    },"resultPanelHeight":function () {
      let ret = "";
      if (this.isSolo || this.content.isSolo) {
        if (this.step === 1) {
          ret = "100%";
        } else {
          ret = "calc( 100% - 165px )";
        }
      } else {
        if (this.step === 1) {
          ret = "calc( 100% - 50px )";
        } else {
          ret = "calc( 100% - 170px )";
        }
      }

      return ret;
    },"documentId":function () {
      let documentId = this.query.documentId;
      if (
        !documentId &&
        webCpu.currentCard.task &&
        webCpu.currentCard.task.vueItem &&
        webCpu.currentCard.task.vueItem.documentMeta
      ) {
        documentId = webCpu.currentCard.task.vueItem.documentMeta.documentId;
      }
      return documentId;
    },"currentTips":function () {
      let ret = this.waitingText;
      if (this.loading) {
        ret = this.loadingText;
      } else if (this.description || this.content.inputText) {
        ret = this.gettedTips;
      } else {
      }

      return ret;
    }},"methods":{"startModify":function() {
      this.$confirm(
        "当前版本为历史版本，是否基于当前版本进行修改？",
        webCpu.messages[lang].confirmDialogTitle,
        {
          confirmButtonText: webCpu.messages[lang].confirmYes,
          cancelButtonText: webCpu.messages[lang].confirmNo,
          type: "warning",
        }
      )
        .then(() => {
          this.modifyEnabled = true;
          // let tJson = WebTool.extractJSON(
          //   this.versionList[this.currentVersionIndex].result
          // );
          // this.updateResult(tJson);
        })
        .catch(() => {
          // 取消操作
        });
    },"checkDiff":function(index) {
      let e = window.event;
      e.stopPropagation();
      e.preventDefault();
      let style = {
        width: "100%",
        height: "100%",
      };
      let versionA = this.versionList[this.currentVersionIndex].result;
      let versionB = this.versionList[index].result;
      let baseContent = JSON.stringify(this.accountData);
      let elem = this.$refs.loadingEffectArea;
      let confirmCallback = () => {
        let content = diffCheckApp.card.task.vueItem.generateNewVersion();
        let currentId = MurmurHash.rule(baseContent, 31);
        let tId = MurmurHash.rule(JSON.stringify(content), 31);
        if (tId !== currentId) {
          this.confirmContent(content);
        } else {
          this.$message({
            type: "info",
            message: "内容没有变更。",
          });
        }
        webCpu.CardItem.dismissMask(elem);
      };
      let closeCallback = () => {
        let content = diffCheckApp.card.task.vueItem.generateNewVersion();
        let currentId = MurmurHash.rule(baseContent, 31);
        let tId = MurmurHash.rule(JSON.stringify(content), 31);
        if (tId !== currentId) {
          this.$confirm(
            "检测到有内容变更，是否生成新版本？",
            webCpu.messages[lang].confirmDialogTitle,
            {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          ).then(() => {
            this.confirmContent(content);
          });
        }
        webCpu.CardItem.dismissMask(elem);
      };
      let diffCheckApp = {
        className: "ElementVueItem",
        task: {
          template: webCpu.documentPrefix + "/transweb/diffCheckApp.vue",
          data: {
            versionA: WebTool.extractJSON(versionA),
            versionB: WebTool.extractJSON(versionB),
          },
          style: {
            width: "100%",
            height: "100%",
            overflow: "auto",
          },
        },
      };

      webCpu.renderCardDialog(
        elem,
        diffCheckApp,
        {
          dialogName: "checkDiffDialog",
          title: "版本差异检查",
          closeType: "back",
          titleStyle: {
            background: "var(--el-color-primary-light-9)",
            color: "var(--el-color-primary)",
          },
          menu: [
            {
              label: "确认",
              action: confirmCallback,
            },
          ],
          closeCallback: closeCallback,
        },
        style,
        {
          padding: "20px 30px 20px 30px",
        }
      );
    },"updateVersionList":function (versionId, callback) {
      versionId = versionId || JSON.stringify(this.accountData);
      aigcInterface.getHistoryVersion(versionId, (tData) => {
        if (tData && tData.ret === 0) {
          tData.data = tData.data || [];
          if (tData.data.length > 0) {
            this.versionList = tData.data.sort((a, b) => {
              return b._time - a._time;
            });
            for (let i = 0; i < this.versionList.length; i++) {
              let item = this.versionList[i];
              let jsonStr = JSON.stringify(WebTool.extractJSON(item.result));
              let temp = JSON.stringify(this.accountData);
              if (this.currentVersion === item.versionId || jsonStr === temp) {
                this.currentVersionIndex = i;
                this.currentVersion = "";
                break;
              }
            }
            if (typeof callback === "function") {
              callback();
            }
          } else {
            this.accountData = this.accountData || {};
            aigcInterface.recordDataVersion(this.accountData, "", (tData) => {
              console.log(tData);
              let content = JSON.stringify(this.accountData);
              this.updateVersionList(content);
              this.currentVersionIndex = 0;
              if (typeof callback === "function") {
                callback();
              }
            });
          }
        }
      });
    },"getVersionLabel":function (item) {
      if (!item || !item.versionId) {
        return false;
      }
      let str = item.versionId.slice(0, 5);
      str = `${str}@${new Date(item._time).toLocaleString()}`;
      return str;
    },"handleVersionChange":function (index) {
      this.versionList = this.versionList || [];
      console.log(index, this.versionList[index]);
      if (this.versionList[index] && this.versionList[index].result) {
        this.isStreaming = true;
        this.switchResultStatus(this.versionList[index].result, true);
        this.currentVersionIndex = index;
        this.modifyEnabled = false;
      }
    },"handleConfirm":function() {
      // 1. 获取并清理输入，防止纯空格绕过校验
      const requirement = this.modifyInfo?.trim();

      if (!requirement) {
        this.$message({
          type: "warning",
          message: "需要输入变更描述。",
        });
        return false;
      }

      // 2. 结构化 Prompt 模板，加入强制约束
      const promptContext = `
你是一个严谨的数据处理助手。请根据【修改需求】修改【原始数据】，并严格遵守【输出要求】。

【修改需求】
${requirement}

【原始数据】
${JSON.stringify(this.accountData)}

【输出要求】
1. 必须输出修改后的完整内容，保持原有的格式和层级结构完全一致。
2. 绝对不允许删减、折叠或省略【原始数据】中未要求修改的任何字段和细节。
3. 仅输出最终结果，不要包含任何开场白、解释性废话，也不要输出 Markdown 代码块标记（如 \`\`\` 等）。
`.trim();

      // 3. 执行生成
      this.modifyGenerate({
        documentId: this.query.documentId,
        context: promptContext,
      });
    },"triggerFileInput":function (e) {
      e = e || window.event;
      e.stopPropagation();
      e.preventDefault();
      this.$refs.hiddenFileInput.click();
    },"confirmTransfer":function (text) {
      this.$confirm(
        "文件中内容结构 Agent 所支持不一致，是否生成相应结构化内容？",
        webCpu.messages[lang].confirmDialogTitle,
        {
          confirmButtonText: webCpu.messages[lang].confirmYes,
          cancelButtonText: webCpu.messages[lang].confirmNo,
          type: "warning",
        }
      ).then(() => {
        this.content.inputText = text;
        this.handleGenerate();
      });
    },"checkDataStruct":function (json, template) {
      template = WebTool.base64ToUtf8(template);
      let obj = webCpu.getDslObj(template) || {};
      let data = typeof obj.data === "function" ? obj.data().accountData : {};
      let ret =
        Object.keys(json || {}).sort().join(",") ===
        Object.keys(data || {}).sort().join(",");
      return ret;
    },"onFileSelected":async function (event) {
      const file = event.target.files[0];
      if (!file) return;
      this.readingFile = true;
      let arr = file.name.split(".");
      let subfix = arr[arr.length - 1];

      if (subfix !== "html" && subfix !== "txt" && subfix !== "md") {
        let temp = await WebTool.extractTextFromFile(file);
        this.confirmTransfer(temp);
        this.readingFile = false;
        event.target.value = "";
        return true;
      }

      const reader = new FileReader();
      // 读取为文本
      reader.onload = (e) => {
        let text = e.target.result;

        if (subfix === "html") {
          let d = WebTool.extractAppTaskData(text);
          if (d && d.data && d.data.accountData) {
            this.isStreaming = true;
            let tData = d.data.accountData || d.data;
            this.switchResultStatus(tData);
          } else {
            this.confirmTransfer(text);
          }
        } else {
          let json = WebTool.markdownToObject(text);
          let ret = this.checkDataStruct(
            json,
            this.currentAppItem.task.template
          );
          if (json && ret) {
            this.isStreaming = true;
            this.switchResultStatus(json);
          } else {
            this.confirmTransfer(text);
          }
        }

        this.readingFile = false;
      };
      // ⚠️ 关键：按文本读取
      reader.readAsText(file, "utf-8");
      event.target.value = "";
    },"openFeedbackDialog":function () {
      this.feedbackDialog.visible = true;
    },"confirmSendFeedback":function () {
      let tempData = JSON.parse(JSON.stringify(this.accountData));
      if (!this.feedbackDialog.message.trim()) {
        this.$message({
          type: "warning",
          message: "需要输入有效问题描述。",
        });
        return false;
      }
      tempData.feedback = this.feedbackDialog.message;
      tempData.documentId = this.query.documentId;
      this.feedbackDialog.visible = false;
      this.adapter.sendFeedback(tempData, (tData) => {
        this.$message({
          type: "success",
          message: "感谢反馈！",
        });
      });
    },"handleContentCommand":function (type) {
      let params = this.accountData;
      if (type === "htmlDownload") {
        this.downloadHtmlItem({
          content: JSON.stringify(params),
          name: params.name || params.title,
          template: this.currentAppItem.task.template,
        });
      } else if (type === "markdownDownload") {
        this.downloadMarkdown(params);
      } else if (type === "readLocal") {
        this.triggerFileInput();
      } else if (type == "imageDownload") {
        this.downloadImageItem();
      } else if (type === "copyLink") {
        this.copyLink();
      } else {
        this.openContentEditor();
      }
    },"copyLink":function () {
      let documentId = this.documentId;
      let dataId = this.versionList[this.currentVersionIndex]._id;
      let url = location.origin + "/skill.html?solo=true&dataView=true&dataId=" + dataId + "&id=" + documentId;
      WebTool.copyString(url);
      this.$message({
        type: "success",
        message: "链接已复制到剪贴板。",
      });
    },"downloadImageItem":function () {
      let elem = $(this.$refs.resultComponentItem).find(
        ".ElementVueItem._task_container>div>div"
      )[0];
      if (!elem) {
        this.$message({
          type: "warning",
          message: "当前结果不支持导出图片。",
        });
        return false;
      } else {
        let name = this.accountData.name || this.accountData.title || "result";
        WebTool.transformImage(elem, name, "png", {
          download: true,
        });
      }
    },"searchContent":function (str) {
      let index = -1;
      if (typeof str === "object") {
        str = JSON.stringify(str);
      }
      for (let i = 0; i < this.versionList.length; i++) {
        let item = this.versionList[i];
        let result = JSON.stringify(WebTool.extractJSON(item.result));
        if (result === str) {
          index = i;
          break;
        }
      }
      return index;
    },"confirmContent":function (content) {
      let index = this.searchContent(content);
      let tContent = JSON.stringify(this.accountData);
      this.accountData = content;
      if (index === -1) {
        aigcInterface.recordDataVersion(content, tContent, (tData) => {
          console.log(tData);
          this.$message({
            type: "success",
            message: "已生成新版本。",
          });
          this.updateResult(content);
          this.updateVersionList(content);
          this.currentVersionIndex = 0;
        });
      } else {
        this.$message({
          type: "warning",
          message: "修改后与一个历史版本内容一致。",
        });
        this.$confirm(
          "修改后与一个历史版本内容一致，是否切换到该版本？",
          webCpu.messages[lang].confirmDialogTitle,
          {
            confirmButtonText: webCpu.messages[lang].confirmYes,
            cancelButtonText: webCpu.messages[lang].confirmNo,
            type: "warning",
          }
        ).then(() => {
          let tJson = WebTool.extractJSON(this.versionList[index].result);
          this.updateResult(tJson);
          this.accountData = content;
          this.currentVersionIndex = index;
        });
      }
    },"openContentEditor":function () {
      let elem = this.$refs.loadingEffectArea;
      let mdString = WebTool.objectToMarkdown(this.accountData);
      let app = {
        className: "CodeEditor",
        dsl: {
          changeCallback: function (dd) {},
          data: mdString,
          options: {
            lineNumbers: true,
            value: "",
            mode: "markdown",
          },
        },
      };

      let baseContent = JSON.stringify(this.accountData);
      let confirmCallback = () => {
        let content = WebTool.markdownToObject(app.card.task.data);
        let currentId = MurmurHash.rule(baseContent, 31);
        let tId = MurmurHash.rule(content, 31);
        if (tId !== currentId) {
          this.confirmContent(content);
        } else {
          this.$message({
            type: "info",
            message: "内容没有变更。",
          });
        }
        webCpu.CardItem.dismissMask(elem);
      };
      let copyScript = () => {
        let script = app.card.task.data;
        WebTool.copyString(script);
        this.$message({
          type: "success",
          message: "复制成功",
        });
      };
      let menu = [
        {
          label: "复制",
          action: copyScript,
        },
        {
          label: "确认",
          action: confirmCallback,
        },
      ];
      if (this.viewMode) {
        menu.length = 1;
      }
      webCpu.CardItem.renderCardDialog(
        elem,
        app,
        {
          title: "编辑内容",
          titleStyle: {
            background: "var(--el-color-primary-light-9)",
            color: "var(--el-color-primary)",
          },
          closeType: "back",
          dialogName: "codeEditorDialog",
          menu: menu,
        },
        {
          width: "100%",
          height: "100%",
        }
      );
    },"downloadMarkdown":function (params) {
      let md = WebTool.objectToMarkdown(params);
      let name = params.name || params.title || "未命名内容";
      if (!/\.md$/i.test(name)) {
        name += ".md";
      }
      WebTool.downloadString(md, name);
    },"downloadHtmlItem":function (params) {
      let adapter = webCpu.initAdapter({
        getTemplate: {
          url: webCpu.documentPrefix + "/transweb/components/ElementVueItem/example.html",
          method: "get",
          dataType: "text",
        },
      });
      adapter.getTemplate({}, (html) => {
        let documentId =
          this.query.documentId ||
          webCpu.currentCard.task.vueItem.documentMeta.documentId;
        let name = params.name || params.title || "未命名应用";

        // example.html 会把数据直接拼进 <script> 里：
        //   template: `{{template}}`  → JS 模板字符串
        //   accountData: {{content}}  → JS 对象字面量
        // 内容中只要出现 script 结束标签或 HTML 注释起始符，HTML 解析器就会提前结束脚本块；
        // 出现反引号 / ${ 则会截断模板字符串。这里按上下文分别转义。
        let escapeHtmlText = function (value) {
          return String(value === undefined || value === null ? "" : value)
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;");
        };
        let escapeHtmlAttr = function (value) {
          return escapeHtmlText(value).replace(/"/g, "&quot;");
        };

        let content = params.content;
        if (typeof content !== "string") {
          content = JSON.stringify(content === undefined ? {} : content);
        }
        // JSON 里的 < 统一转成 \u003c：脚本块不会被提前闭合，运行时解析后仍是原来的 <
        let safeContent = content
          .replace(/</g, "\\u003c")
          .replace(/\u2028/g, "\\u2028")
          .replace(/\u2029/g, "\\u2029");

        let template =
          typeof params.template === "string" ? params.template : "";
        // 先转义反斜杠，再处理反引号 / ${ / <，保证运行时拿到的模板与原始内容一致
        let safeTemplate = template
          .replace(/\\/g, "\\\\")
          .replace(/`/g, "\\`")
          .replace(/\$\{/g, "\\${")
          .replace(/</g, "\\u003c");

        let exportParams = Object.assign({}, params, {
          name: escapeHtmlText(name),
          content: safeContent,
          template: safeTemplate,
          agentName: escapeHtmlText(document.title),
          creator: escapeHtmlText(
            webCpu.currentCard.task.vueItem.currentSettings.creator ||
              "匿名用户"
          ),
          createTime: escapeHtmlText(new Date().toLocaleString()),
          userName: escapeHtmlText(localStorage.userName || "匿名用户"),
          agentUrl: escapeHtmlAttr(
            location.origin + "/skill.html?solo=true&id=" + documentId
          ),
        });

        html = html.bindData(exportParams);
        name = name + ".html";
        WebTool.downloadString(html, name, "text/html");
      });
    },"dismissLoadingStatus":function () {
      this.loading = false;
      let elem = this.$refs.loadingEffectArea;
      if (!this.isSolo && !this.content.isSolo) {
        elem = this.$refs.resultComponentItem;
      }
      webCpu.CardItem.dismissMask(elem);
    },"switchLoadingStatus":function (str) {
      this.step = 1;
      this.loading = true;
      this.$nextTick(() => {
        let elem = this.$refs.loadingEffectArea;
        if (!this.isSolo && !this.content.isSolo) {
          elem = this.$refs.resultComponentItem;
        }
        let app = JSON.parse(JSON.stringify(this.loadingProgressApp));
        webCpu.CardItem.renderCardDialog(
          elem,
          app,
          {
            dialogName: "progressDialog",
            headerHidden: true,
            style: {
              background: "transparent",
            },
          },
          {
            background: "transparent",
            marginTop: "-5px",
          }
        );
        this.currentTipsItem = app;
      });
    },"switchResultStatus":function (fullContent, flag) {
      if (!this.isStreaming) {
        return false;
      }
      this.dismissLoadingStatus();
      this.loading = false;
      this.step = 1;
      this.abortController = null;
      this.isStreaming = false;
      this.$nextTick(() => {
        let count = 0;
        try {
          let parsed = fullContent;
          if (typeof fullContent === "string") {
            parsed = WebTool.extractJSON(fullContent);
          }
          this.accountData = parsed;
          this.content.outputText = JSON.stringify(parsed);
          this.$nextTick(() => {
            if (!flag && this.dataEditable) {
              this.updateVersionList(this.content.outputText, () => {
                this.updateResult(parsed);
              });
            } else {
              this.updateResult(parsed);
            }
          });
        } catch (e) {
          this.goBack(true);
          if (count < 5) {
            this.handleGenerate();
            this.$message({
              type: "warning",
              message: "生成的内容格式不正确，正在重试...",
            });
            count++;
          } else {
            this.$message({
              type: "warning",
              message: "生成的内容格式不正确，请稍后再试",
            });
          }
        }
      });
    },"goBack":function (flag) {
      if (flag) {
        this._goBack();
      } else {
        this.$confirm(
          "退出后无法恢复，确定退出吗？",
          webCpu.messages[lang].confirmDialogTitle,
          {
            confirmButtonText: webCpu.messages[lang].confirmYes,
            cancelButtonText: webCpu.messages[lang].confirmNo,
            type: "warning",
          }
        ).then(() => {
          this._goBack();
        });
      }
    },"_goBack":function () {
      if (
        this.abortController &&
        typeof this.abortController.abort === "function"
      ) {
        this.abortController.abort();
      }
      this.step = 0;
      this.accountData = {};
      this.isStreaming = false;
      this.dismissLoadingStatus();
    },"updateResult":function (json) {
      let app = JSON.parse(JSON.stringify(this.currentAppItem));
      delete app.card;
      let elem = this.$refs.resultComponentItem;
      aigcInterface.updateResultView(json, app, elem);
    },"updateLoadingTips":function (result) {
      if (
        this.currentTipsItem &&
        this.currentTipsItem.card &&
        this.currentTipsItem.card.task
      ) {
        this.currentTipsItem.card.task.vueItem =
          this.currentTipsItem.card.task.vueItem || {};
        this.currentTipsItem.card.task.vueItem.result = result;
      }
    },"inputExampleText":function (item) {
      this.content.inputText = item.name || "";
      if (item.description) {
        this.content.inputText = item.name + ": " + item.description;
      }
    },"confirmGenerate":async function (query) {
      this.responseText = "";
      this.switchLoadingStatus();
      this.isStreaming = true;
      this.versionList = [];
      this.abortController = await aigcInterface.streamRequest(query, {
        immediateFinish: true,
        onMessage: (message) => {
          this.responseText = message;
          this.updateLoadingTips(message);
        },
        onStreamFinished: (result) => {
          this.switchResultStatus(result);
          this.content.outputText = result;
          this.versionList = [];
        },
        onError: (e) => {
          this.handleErrorCase();
        },
        onAction: (action) => {
          if (action === "stop") {
            this._goBack();
          } else if (action === "LOGIN") {
            aigcInterface.openLoginDialog(document.body, () => {
              this.confirmGenerate(query);
            });
            this.goBack(true);
          } else if (action === "RECHARGE") {
            aigcInterface.renderRechargeDialog(document.body);
            this.goBack(true);
          } else {
          }
        },
      });
    },"handleErrorCase":function () {
      this.$message({
        type: "warning",
        message: "请求出现异常，请重新尝试。",
      });
      this.goBack(true);
    },"handleGenerate":function () {
      if (!this.currentFile && !this.content.inputText) {
        this.$message({
          type: "warning",
          message: "需要输入具体需求描述。",
        });
        return false;
      }

      let query = this.query || {};
      query.context = this.content.inputText;
      query.documentId =
        query.documentId ||
        webCpu.currentCard.task.vueItem.documentMeta.documentId;
      if (this.currentFile) {
        WebTool.compressAndResizeBase64(
          this.currentFile,
          this.size,
          this.size,
          1
        ).then((base64) => {
          query.image = base64 || "";
          this.aigcRequest(query);
        });
      } else {
        if (location.host.includes("transweb.cn")) {
          this.confirmGenerate(query);
        } else {
          this.aigcRequest(query);
        }
      }
    },"aigcRequest":function (query, flag) {
      this.switchLoadingStatus();
      this.isStreaming = true;
      this.versionList = [];
      aigcInterface.requestPrompt(
        query.context,
        query.documentId,
        query,
        (content) => {
          this.responseText = content || "";
          this.content.outputText = this.responseText;
          this.switchResultStatus(this.responseText);
          this.versionList = [];
        },
        (e) => {
          this._goBack();
        }
      );
    },"modifyGenerate":function (query) {
      this.modifyLoading = true;
      query.documentId =
        query.documentId ||
        webCpu.currentCard.task.vueItem.documentMeta.documentId;
      query.baseContent = JSON.stringify(this.accountData);
      aigcInterface.requestPrompt(
        query.context,
        query.documentId,
        query,
        (content) => {
          this.modifyLoading = false;
          this.responseText = content || "";
          this.content.outputText = this.responseText;
          this.isStreaming = true;
          this.switchResultStatus(this.responseText);
        },
        (e) => {
          this.$message({
            type: "warning",
            message: "请求出现异常，请重新尝试。",
          });
          this.modifyLoading = false;
          this._goBack();
        }
      );
    }},"appMap":{"loginRegItem":{"url":"/mainApp/app/loginRegItem.js","key":"transweb_loginRegItem","cardName":"loginRegCard","dsl":{"data":{"position":"top","loginedMenu":[{"label":"⏱️ 历史记录","action":function (d, callback) {
                dialogMap.openDocumentApp(
                  webCpu.appConfig.aigcHistory,
                  {
                    data: {
                      content: {
                        isSolo: true,
                      },
                      query: {
                        documentId: d.documentId || webCpu.currentVueItem.query.documentId,
                      },
                    },
                  },
                  {
                    title: "⏱️ 历史记录",
                    callback: callback,
                  },
                  {
                    width: "100%",
                    height: "100%",
                  }
                );
              }}]}}}},"mounted":async function () {
    this.content = this.content || {};
    this.adapter = webCpu.initAdapter(this.interface);
    let auto = WebTool.urlQuery(location.href, "auto");
    let caseUrl = WebTool.urlQuery(location.href, "caseUrl");
    let dataId = WebTool.urlQuery(location.href, "dataId");

    if (this.accountData && Object.keys(this.accountData).length !== 0) {
      this.isStreaming = true;
      this.switchResultStatus(this.accountData);
    } else if (caseUrl) {
      this.isStreaming = true;
      let d = await WebTool.fetchHtmlDataSmart(caseUrl);
      this.switchResultStatus(d.data);
    } else if (dataId) {
      aigcInterface.getDataItem(dataId, (result, tData) => {
        this.isStreaming = true;
        this.currentVersion = tData.versionId;
        this.dataEditable =
          tData.folderName + "" === localStorage.folderName &&
          !WebTool.urlQuery(location.href, "dataView");
        this.switchResultStatus(result);
      }, (err)=> {
        this.$message({
          type: "warning",
          message: "获取数据失败，暂时没有权限访问。",
        });
      });
    } else {
      aigcInterface.handleAgentInput(location.href, (str) => {
        this.content.inputText = str;
        if (auto) {
          this.handleGenerate();
        }
      });
    }
  }};</script>
