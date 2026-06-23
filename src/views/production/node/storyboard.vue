<template>
  <t-card class="storyboard">
    <div class="titleBar dragHandle pr">
      <div class="title">{{ $t("workbench.production.node.storyboard.title") }}</div>
      <Handle :id="props.handleIds.target" type="target" :position="Position.Left" style="left: calc(-1 * var(--td-comp-paddingLR-xl))" />
      <Handle :id="props.handleIds.source" type="source" :position="Position.Right" style="right: calc(-1 * var(--td-comp-paddingLR-xl))" />
    </div>
    <div class="content">
      <t-empty v-if="!storyboard.length" style="margin-top: 16px"></t-empty>
      <t-checkbox-group v-model="selectedIds">
        <div class="frameGrid">
          <template v-for="(item, index) in storyboard" :key="item.id">
            <!-- ===== 首尾帧模式：双卡片布局 ===== -->
            <div v-if="item.modelMode === 'firstLastFrame'" class="frameItem frameItem--dual" @mouseenter="setHoveredFrame(index)" @mouseleave="setHoveredFrame(null)">
              <!-- 入场衔接描述 -->
              <div v-if="item.inTransitionDesc" class="transitionDesc transitionDesc--in">
                <span class="transitionLabel">入场:</span> {{ item.inTransitionDesc }}
              </div>

              <div class="dualFrameCard">
                <!-- Sxx-01 首帧 -->
                <div class="frameCard frameCard--first">
                  <div class="frameLabel">S{{ String(index + 1).padStart(2, "0") }}-01 首帧</div>
                  <div
                    class="frameImage"
                    :style="{ width: `${200 * gridScale}px`, height: `${200 * gridScale}px` }">
                    <div class="ac frameCheckbox" :style="{ transform: `scale(${styleMaxSize})` }">
                      <t-checkbox :checked="selectedIds.includes(item.id!)" @click.stop :key="item?.id || index" :value="item.id" />
                      <t-tag class="frameTypeTag" :style="{ backgroundColor: tagColors[index % tagColors.length] }">
                        S{{ String(index + 1).padStart(2, "0") }}
                      </t-tag>
                    </div>
                    <t-image
                      v-if="item.firstFramePath"
                      :src="item.firstFramePath"
                      fit="contain"
                      class="frameImg"
                      @click="editStoryboaryImage(item, [item.firstFramePath], null, 'firstFrame')" />
                    <div v-else class="generatingPlaceholder" @click="editStoryboaryImage(item, [], null, 'firstFrame')">
                      <t-empty size="small" title="首帧待生成" />
                    </div>
                    <!-- 继承标记 -->
                    <t-tag v-if="index > 0 && !item.firstFramePath" theme="warning" variant="light" size="small" class="inheritTag">
                      🔗 继承 S{{ String(index).padStart(2, "0") }}-02
                    </t-tag>
                  </div>
                </div>

                <!-- 箭头 -->
                <div class="frameArrow">→</div>

                <!-- Sxx-02 尾帧 -->
                <div class="frameCard frameCard--last">
                  <div class="frameLabel">S{{ String(index + 1).padStart(2, "0") }}-02 尾帧</div>
                  <div
                    class="frameImage"
                    :style="{ width: `${200 * gridScale}px`, height: `${200 * gridScale}px` }">
                    <t-image
                      v-if="item.lastFramePath"
                      :src="item.lastFramePath"
                      fit="contain"
                      class="frameImg"
                      @click.stop="editStoryboaryImage(item, [item.lastFramePath], null, 'lastFrame')" />
                    <div v-else class="generatingPlaceholder" @click.stop="editStoryboaryImage(item, [], null, 'lastFrame')">
                      <t-empty size="small" title="尾帧待生成" />
                    </div>
                  </div>
                </div>
              </div>

              <!-- 出场衔接描述 -->
              <div v-if="item.outTransitionDesc" class="transitionDesc transitionDesc--out">
                <span class="transitionLabel">出场:</span> {{ item.outTransitionDesc }}
              </div>

              <!-- 操作按钮 -->
              <div class="dualFrameActions">
                <t-tooltip theme="primary" content="编辑首尾帧信息">
                  <t-button size="small" variant="outline" @click.stop="editInfo(item)"><i-edit /> 编辑</t-button>
                </t-tooltip>
                <t-tooltip theme="primary" content="删除此分镜">
                  <t-button size="small" variant="outline" theme="danger" @click.stop="removeFn(item.id!)"><i-delete /></t-button>
                </t-tooltip>
              </div>
            </div>

            <!-- ===== 非首尾帧模式：原单卡片布局 ===== -->
            <div v-else class="frameItem" @mouseenter="setHoveredFrame(index)" @mouseleave="setHoveredFrame(null)">
              <div class="addBetween addBetween--left" :class="{ expanded: hoveredIndex === index }">
                <t-button
                  theme="primary"
                  variant="outline"
                  shape="circle"
                  @click.stop="editStoryboaryImage(item, [index > 0 ? storyboard[index - 1]?.src || '' : '', item.src || ''], index - 1)">
                  <template #icon><i-plus /></template>
                </t-button>
              </div>

              <div class="frameCard">
                <div
                  class="frameImage"
                  :style="{
                    width: `${200 * gridScale}px`,
                    height: `${200 * gridScale}px`,
                  }">
                  <div class="ac frameCheckbox" :style="{ transform: `scale(${styleMaxSize})` }">
                    <t-checkbox :checked="selectedIds.includes(item.id!)" @click.stop :key="item?.id || index" :value="item.id" />
                    <t-tag class="frameTypeTag" :style="{ backgroundColor: tagColors[index % tagColors.length] }">
                      S{{ String(index + 1).padStart(2, "0") }}
                    </t-tag>
                    <t-tag v-if="item.modelMode" class="modeLabelTag" size="small" variant="outline" :theme="getModeTheme(item.modelMode)">
                      {{ getModeShortLabel(item.modelMode) }}
                    </t-tag>
                  </div>

                  <t-image
                    v-if="item.src && item.state == '已完成'"
                    :src="item.src"
                    fit="contain"
                    class="frameImg"
                    @click="editStoryboaryImage(item, [item.src])">
                    <template #overlayContent>
                      <div class="imageToolsWrap show">
                        <ImageTools :style="{ transform: `scale(${styleMaxSize})` }" :src="item.src" position="br" />
                      </div>
                    </template>
                  </t-image>
                  <div v-else class="generatingPlaceholder" @click="editStoryboaryImage(item, [])">
                    <t-loading v-if="item.state === '生成中'" size="small" />
                    <t-tooltip v-else-if="item.state == '生成失败'" :content="item?.reason">
                      <span style="color: #ff4d4f">生成失败</span>
                    </t-tooltip>
                    <t-empty v-else size="small" :title="$t('workbench.production.node.storyboard.notGenerated')" />
                  </div>
                  <t-tooltip theme="primary" :content="$t('workbench.production.node.storyboard.deleteNode')">
                    <div class="remove ac" :style="{ transform: `scale(${styleMaxSize})` }" @click.stop="removeFn(item.id!)">
                      <i-delete theme="outline" size="18" fill="#fff" />
                    </div>
                  </t-tooltip>
                  <t-tooltip theme="primary" :content="$t('workbench.production.node.storyboard.editNode')">
                    <div class="editNode ac" :style="{ transform: `scale(${styleMaxSize})` }" @click.stop="editInfo(item)">
                      <i-edit theme="outline" size="18" fill="#fff" />
                    </div>
                  </t-tooltip>
                </div>
              </div>
              <div class="addBetween addBetween--right" :class="{ expanded: hoveredIndex === index }">
                <t-button
                  theme="primary"
                  variant="outline"
                  shape="circle"
                  @click.stop="
                    editStoryboaryImage(item, [item.src || '', index < (storyboard?.length ?? 0) - 1 ? storyboard[index + 1]?.src || '' : ''], index)
                  ">
                  <template #icon><i-plus /></template>
                </t-button>
              </div>
            </div>
          </template>
        </div>
      </t-checkbox-group>

      <div class="scaleControl">
        <span>{{ $t("workbench.production.node.storyboard.scaleRatio") }}</span>
        <t-input-number v-model="gridScale" :min="0.1" :max="3" :step="0.1" :decimal-places="1" size="small" style="width: 120px" />
      </div>
      <div class="ac" style="gap: 6px; margin-bottom: 6px; flex-wrap: wrap">
        <t-tag theme="primary" variant="light">{{ $t("workbench.production.node.storyboard.selectedCount", { count: selectedIds.length }) }}</t-tag>
        <t-button size="small" :disabled="!storyboard.length" theme="default" variant="outline" @click="selectedIds = []">
          {{ $t("workbench.production.node.storyboard.clearSelection") }}
        </t-button>
        <t-button size="small" :disabled="!storyboard.length" theme="default" variant="outline" @click="selectAll">
          {{ $t("workbench.production.node.storyboard.selectAll") }}
        </t-button>
        <t-button theme="danger" size="small" :disabled="!storyboard.length || !selectedIds.length" @click="handleDeleteSelected">批量删除</t-button>
      </div>
      <div class="ac" style="gap: 10px">
        <t-button block @click="previewAll" :disabled="!storyboard.length">{{ $t("workbench.production.node.storyboard.gridPreview") }}</t-button>
        <t-button block @click="batchGenerateImage" :disabled="!storyboard.length || !selectedIds.length" :loading="generateLoading">
          {{ $t("workbench.production.node.storyboard.generateImage") }}
        </t-button>

        <!-- <t-button block @click="batchGenerateImage" :disabled="!storyboard.length" :loading="generateLoading">
          {{ $t("workbench.production.node.storyboard.batchGenerateImage") }}
        </t-button> -->
      </div>
    </div>
    <editImage v-model="visible" v-if="visible" :flowData="currentRow" type="storyboard" @save="save" />
    <t-image-viewer
      v-model:visible="previewVisible"
      v-if="previewVisible"
      :images="previewImages"
      :onClose="closePreview"
      :onDownload="downLoadImage"
      :imageScale="{ max: 10, min: 0.1 }" />
  </t-card>
</template>

<script setup lang="ts">
import { useLocalStorage } from "@vueuse/core";
import editImage from "../components/editImage/index.vue";
import { LoadingPlugin } from "tdesign-vue-next";
import { Handle, Position, type Edge } from "@vue-flow/core";
import axios from "@/utils/axios";
import type { AssetItem, Storyboard } from "../utils/flowBuilder";
import projectStore from "@/stores/project";
import productionAgentStore from "@/stores/productionAgent";
const { project } = storeToRefs(projectStore());
const { episodesId } = storeToRefs(productionAgentStore());

const props = defineProps<{
  id: string;
  handleIds: {
    target: string;
    source: string;
  };
  assetsData: AssetItem[];
}>();

const storyboard = defineModel<Storyboard[]>({ required: true });

const visible = ref(false);
const previewVisible = ref(false);
const previewImages = ref<string[]>([]);
const gridScale = useLocalStorage("storyboardGridScale", 1);

const hoveredIndex = ref<number | null>(null);
const selectedIds = ref<number[]>([]);

// 逐镜模式标签辅助
const MODE_SHORT_LABEL: Record<string, string> = {
  firstLastFrame: "首尾帧",
  firstFrame: "首帧",
  multiModal: "多参",
  videoExtension: "延长",
  videoEditing: "编辑",
  text: "文本",
};
const MODE_THEME: Record<string, "primary" | "success" | "warning" | "danger" | "default"> = {
  firstLastFrame: "primary",
  firstFrame: "primary",
  multiModal: "success",
  videoExtension: "warning",
  videoEditing: "danger",
  text: "default",
};
function getModeShortLabel(mode?: string) { return MODE_SHORT_LABEL[mode || ""] || mode || ""; }
function getModeTheme(mode?: string) { return MODE_THEME[mode || ""] || "default"; }
function previewSingleImage(src: string) { previewImages.value = [src]; previewVisible.value = true; }
function downloadSingleImage(src: string) { const a = document.createElement("a"); a.href = src; a.download = ""; a.click(); }

function setHoveredFrame(index: number | null) {
  hoveredIndex.value = index;
}

function selectAll() {
  selectedIds.value = storyboard.value.map((s) => s.id!).filter(Boolean);
}
function handleDeleteSelected() {
  const dialog = DialogPlugin.confirm({
    header: $t("workbench.assets.confirmDeleteHeader"),
    body: $t("workbench.production.node.storyboard.confirmBatchDeleteBody", { index: selectedIds.value.length }),
    confirmBtn: $t("workbench.assets.deleteBtn"),
    cancelBtn: $t("workbench.assets.cancelBtn"),
    theme: "warning",
    onConfirm: async () => {
      try {
        if (!selectedIds.value.length) {
          dialog.destroy();
          return window.$message.error($t("workbench.production.node.storyboard.pleaseSelectImage"));
        }
        axios.post("/production/storyboard/batchDelete", {
          ids: selectedIds.value,
          projectId: project.value?.id,
        });
        storyboard.value = storyboard.value.filter((i) => !selectedIds.value.includes(i.id!));
        selectedIds.value = [];
        window.$message.success($t("workbench.production.node.storyboard.deleteSuccess"));
      } catch (e) {
        window.$message.error((e as any)?.message || $t("workbench.production.node.storyboard.removeFailed"));
      } finally {
        dialog.destroy();
      }
    },
  });
}
const currentRow = ref<{
  flowId?: number | null;
  resultImages: { src: string; prompt: string }[];
  referanceImages: string[];
}>({
  flowId: null,
  resultImages: [],
  referanceImages: [],
});

const tagColors = ["#5bccb3", "#9c7cfc", "#fbbf24", "#5b9afc", "#e86b6b", "#7cb8fc", "#e8a855", "#34d399"];

function closePreview() {
  previewImages.value = [];
}
async function downLoadImage() {
  LoadingPlugin(true);
  const allIds = (storyboard.value ?? []).filter((s) => s.src).map((s) => s.id!);
  if (!allIds.length) {
    window.$message.warning($t("workbench.production.node.storyboard.noPreviewImages"));
    LoadingPlugin(false);
    return;
  }
  try {
    const res = await axios.post(
      "/production/storyboard/downPreviewImage",
      {
        storyboardIds: allIds,
      },
      { responseType: "blob" },
    );
    // 创建下载链接
    const url = URL.createObjectURL(res as unknown as Blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = `storyboardImagePreview-${Date.now()}.png`;
    a.click();
    URL.revokeObjectURL(url);
  } catch {
    window.$message.error($t("workbench.production.node.storyboard.imageLoadFailed"));
  } finally {
    LoadingPlugin(false);
  }
}
async function previewAll() {
  LoadingPlugin(true);
  const allIds = (storyboard.value ?? []).filter((s) => s.src).map((s) => s.id!);
  if (!allIds.length) {
    window.$message.warning($t("workbench.production.node.storyboard.noPreviewImages"));
    LoadingPlugin(false);
    return;
  }
  try {
    const { data } = await axios.post("/production/storyboard/previewImage", {
      storyboardIds: allIds,
      projectId: project.value?.id,
    });
    previewImages.value = [data];
    previewVisible.value = true;
  } catch {
    window.$message.error($t("workbench.production.node.storyboard.imageLoadFailed"));
  } finally {
    LoadingPlugin(false);
  }
}
const currentRowStoryboardInfo = ref<{ id: number | null; insertAfterIndex: number | null }>({
  id: null,
  insertAfterIndex: null,
});
const styleMaxSize = computed(() => {
  if (gridScale.value <= 1) return gridScale.value;
  else 1;
});
const generateLoading = ref(false);
async function batchGenerateImage() {
  if (!selectedIds.value.length) return window.$message.warning("请先选择分镜面板");
  generateLoading.value = true;
  try {
    await productionAgentStore().batchGenerateStoryboard(selectedIds.value, true);
    window.$message.success($t("workbench.production.node.storyboard.batchGenerateSuccess"));
    selectedIds.value = [];
  } catch (e) {
    window.$message.error($t("workbench.production.node.storyboard.batchGenerateFailed"));
  } finally {
    generateLoading.value = false;
  }
}
function editStoryboaryImage(item: Storyboard, images: string[], insertAfterIndex: number | null = null, frameType?: "firstFrame" | "lastFrame") {
  console.log("[storyboard] 点击帧:", frameType || "默认", "| 图片:", images[0]?.substring(0, 50) || "无", "| prompt来源:", frameType === "firstFrame" ? "firstFramePrompt" : frameType === "lastFrame" ? "lastFramePrompt" : "prompt");
  currentRowStoryboardInfo.value = {
    id: insertAfterIndex == null ? item?.id! : null,
    insertAfterIndex,
    frameType: frameType ?? null,
  };
  currentRow.value = {
    flowId: item?.flowId ?? null,
    resultImages: [],
    referanceImages: [],
  };

  if (currentRowStoryboardInfo.value.id) {
    let imagesPush: string[] = [];

    if (item.associateAssetsIds && item.associateAssetsIds.length > 0) {
      const assetsImages: string[] = [];
      for (const id of item.associateAssetsIds) {
        // 先查顶层 asset
        const asset = props.assetsData.find((a) => a.id === id);
        if (asset) {
          if (asset.src) assetsImages.push(asset.src);
          continue;
        }
        // 再查 derive
        for (const a of props.assetsData) {
          const derive = a.derive?.find((d) => d.id === id);
          if (derive) {
            if (derive.src) assetsImages.push(derive.src);
            break;
          }
        }
      }
      imagesPush = imagesPush.concat(assetsImages);
    }
    // if (item?.referenceIds && item.referenceIds.length > 0) {
    //   const referenImages = storyboard.value
    //     .filter((s) => item.referenceIds!.includes(s.id))
    //     .map((s) => s.src)
    //     .filter(Boolean) as string[];
    //   imagesPush = imagesPush.concat(referenImages);
    // }
    currentRow.value.referanceImages = imagesPush;
    // 首帧使用 firstFramePrompt，尾帧使用 lastFramePrompt
    const framePrompt = frameType === "firstFrame" ? (item.firstFramePrompt ?? item.prompt)
                     : frameType === "lastFrame"  ? (item.lastFramePrompt ?? item.prompt)
                     : item.prompt;
    currentRow.value.resultImages = [{ src: images.length ? images[0] : "", prompt: framePrompt ?? "" }];
  } else {
    currentRow.value.referanceImages = images.filter(Boolean);
  }
  visible.value = true;
}

async function save({ imageUrl, flowId }: { imageUrl: string; flowId: number }) {
  if (!imageUrl) return;

  const { id, insertAfterIndex } = currentRowStoryboardInfo.value;

  // 插入模式：在两张图之间新增一条分镜
  if (id === null && insertAfterIndex !== null) {
    const newFrame: Storyboard = {
      duration: 0,
      prompt: "",
      src: imageUrl,
      videoDesc: "",
      shouldGenerateImage: 1,
      state: "已完成",
    };
    const { data } = await axios.post("/production/storyboard/addStoryboard", {
      ...newFrame,
      projectId: project.value?.id,
      scriptId: episodesId.value,
      flowId,
    });

    storyboard.value.splice(insertAfterIndex + 1, 0, { ...newFrame, id: data.id!, flowId });
    productionAgentStore().setFlowData();
    return;
  }

  // 更新模式：根据帧类型更新对应字段
  const target = storyboard.value.find((s) => s.id === id);
  if (target) {
    if (currentRowStoryboardInfo.value.frameType === "firstFrame") {
      target.firstFramePath = imageUrl;
    } else if (currentRowStoryboardInfo.value.frameType === "lastFrame") {
      target.lastFramePath = imageUrl;
    } else {
      target.src = imageUrl;
    }
    target.state = "已完成";
    target.flowId = flowId;
  }
  await axios.post("/production/storyboard/updateStoryboardUrl", {
    id: id,
    url: imageUrl,
    flowId,
  });
}

async function removeFn(id: number) {
  // 检查是否有后续分镜继承此分镜的尾帧
  const idx = storyboard.value.findIndex((s) => s.id === id);
  let inheritWarning = "";
  if (idx !== -1 && idx < storyboard.value.length - 1) {
    const nextItem = storyboard.value[idx + 1];
    const currentItem = storyboard.value[idx];
    if (nextItem?.modelMode === "firstLastFrame" && !nextItem.firstFramePath && currentItem?.lastFramePath) {
      inheritWarning = `\n\n⚠ S${String(idx + 2).padStart(2, "0")}-01 首帧正继承自此分镜的尾帧图，删除后需手动为 S${String(idx + 2).padStart(2, "0")}-01 补充首帧图。`;
    }
  }

  const dialog = DialogPlugin.confirm({
    header: $t("workbench.assets.confirmDeleteHeader"),
    body: $t("workbench.production.node.storyboard.confirmDeleteBody") + inheritWarning,
    confirmBtn: $t("workbench.assets.deleteBtn"),
    cancelBtn: $t("workbench.assets.cancelBtn"),
    theme: "warning",
    onConfirm: async () => {
      if (!id) {
        const index = storyboard.value.findIndex((s) => s.id === id);
        if (index !== -1) {
          storyboard.value.splice(index, 1);
        }
        dialog.destroy();
        return;
      }
      try {
        await axios.post("/production/storyboard/removeFrame", {
          id,
          projectId: project.value?.id,
        });
        const index = storyboard.value.findIndex((s) => s.id === id);
        if (index !== -1) {
          storyboard.value.splice(index, 1);
        }
      } catch (e) {
        window.$message.error((e as any)?.message || $t("workbench.production.node.storyboard.removeFailed"));
      } finally {
        dialog.destroy();
      }
    },
  });
}

function editInfo(item: Storyboard) {
  const isDual = item.modelMode === "firstLastFrame";
  const formData = reactive({
    prompt: item.prompt ?? "",
    videoDesc: item?.videoDesc ?? "",
    firstFramePrompt: item?.firstFramePrompt ?? "",
    lastFramePrompt: item?.lastFramePrompt ?? "",
    firstFrameState: item?.firstFrameState ?? "",
    lastFrameState: item?.lastFrameState ?? "",
  });

  const fields: any[] = [
    h("div", { class: "editInfoField" }, [
      h("label", { class: "editInfoLabel" }, $t("workbench.production.node.storyboard.prompt")),
      h(resolveComponent("t-textarea"), {
        value: formData.prompt,
        placeholder: $t("workbench.production.node.storyboard.promptPlaceholder"),
        autosize: { minRows: 3, maxRows: 6 },
        "onUpdate:value": (v: string) => (formData.prompt = v),
      }),
    ]),
    h("div", { class: "editInfoField" }, [
      h("label", { class: "editInfoLabel" }, $t("workbench.production.node.storyboard.videoDesc")),
      h(resolveComponent("t-textarea"), {
        value: formData.videoDesc,
        placeholder: $t("workbench.production.node.storyboard.videoDescPlaceholder"),
        autosize: { minRows: 3, maxRows: 6 },
        "onUpdate:value": (v: string) => (formData.videoDesc = v),
      }),
    ]),
  ];

  // 首尾帧模式增加首帧/尾帧 prompt 编辑
  if (isDual) {
    fields.push(
      h("div", { class: "editInfoField" }, [
        h("label", { class: "editInfoLabel" }, "首帧状态 (firstFrameState)"),
        h(resolveComponent("t-textarea"), {
          value: formData.firstFrameState,
          placeholder: "该镜头开始时的画面状态描述",
          autosize: { minRows: 2, maxRows: 4 },
          "onUpdate:value": (v: string) => (formData.firstFrameState = v),
        }),
      ]),
      h("div", { class: "editInfoField" }, [
        h("label", { class: "editInfoLabel" }, "尾帧状态 (lastFrameState)"),
        h(resolveComponent("t-textarea"), {
          value: formData.lastFrameState,
          placeholder: "该镜头结束时的画面状态描述",
          autosize: { minRows: 2, maxRows: 4 },
          "onUpdate:value": (v: string) => (formData.lastFrameState = v),
        }),
      ]),
      h("div", { class: "editInfoField" }, [
        h("label", { class: "editInfoLabel" }, "首帧图提示词 (firstFramePrompt)"),
        h(resolveComponent("t-textarea"), {
          value: formData.firstFramePrompt,
          placeholder: "用于生成首帧图的 AI 提示词",
          autosize: { minRows: 2, maxRows: 4 },
          "onUpdate:value": (v: string) => (formData.firstFramePrompt = v),
        }),
      ]),
      h("div", { class: "editInfoField" }, [
        h("label", { class: "editInfoLabel" }, "尾帧图提示词 (lastFramePrompt)"),
        h(resolveComponent("t-textarea"), {
          value: formData.lastFramePrompt,
          placeholder: "用于生成尾帧图的 AI 提示词",
          autosize: { minRows: 2, maxRows: 4 },
          "onUpdate:value": (v: string) => (formData.lastFramePrompt = v),
        }),
      ]),
    );
  }

  const bodyVNode = () => h("div", { class: "editInfoForm" }, fields);

  const confirmDialog = DialogPlugin.confirm({
    header: $t("workbench.production.node.storyboard.editInfo"),
    body: bodyVNode,
    width: 520,
    confirmBtn: {
      content: $t("common.submit"),
      theme: "primary",
      loading: false,
    },
    onConfirm: async () => {
      confirmDialog.update({ confirmBtn: { content: $t("common.submitting"), loading: true } });
      try {
        const payload: Record<string, any> = {
          id: item.id,
          prompt: formData.prompt,
          videoDesc: formData.videoDesc,
        };
        if (isDual) {
          payload.firstFrameState = formData.firstFrameState;
          payload.lastFrameState = formData.lastFrameState;
          payload.firstFramePrompt = formData.firstFramePrompt;
          payload.lastFramePrompt = formData.lastFramePrompt;
        }
        await axios.post("/production/storyboard/editStoryboardInfo", payload);
        item.prompt = formData.prompt;
        item.videoDesc = formData.videoDesc;
        if (isDual) {
          item.firstFrameState = formData.firstFrameState;
          item.lastFrameState = formData.lastFrameState;
          item.firstFramePrompt = formData.firstFramePrompt;
          item.lastFramePrompt = formData.lastFramePrompt;
        }
        window.$message.success($t("common.editSuccess"));
      } catch (e) {
        window.$message.error((e as any)?.message || $t("common.editFailed"));
      } finally {
        confirmDialog.update({ confirmBtn: { content: $t("common.submit"), loading: false } });
        confirmDialog.destroy();
      }
    },
  });
}
</script>

<style lang="scss" scoped>
.storyboard {
  min-width: 500px;
  max-width: 100vw;
  user-select: text;
  cursor: default;

  .titleBar {
    cursor: grab;
    user-select: none;
  }
  .title {
    background-color: #000;
    width: fit-content;
    padding: 5px 10px;
    color: #fff;
    border-radius: 8px 0;
    font-size: 16px;
  }

  .content {
    margin-top: 12px;
  }

  .frameGrid {
    display: flex;
    flex-wrap: wrap;
    align-items: flex-start;
    gap: 0;
  }

  .frameItem {
    position: relative;
    display: inline-flex;
    align-items: flex-start;
    margin: 4px;
  }

  .addBetween {
    position: absolute;
    z-index: 10;
    top: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    opacity: 0;
    pointer-events: none;
    span {
      line-height: 1;
      white-space: nowrap;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    &.expanded {
      opacity: 1;
      pointer-events: auto;
    }
    &:hover {
      // background: var(--td-brand-color);
      // color: #fff;
      // transform: scale(1.15);
    }
    &--left {
      transform: translate(calc(-50% - 4px), -50%);
    }
    &--right {
      transform: translate(calc(50% + 4px), -50%);
      right: 0;
    }
  }

  .frameCard {
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition:
      transform 0.2s,
      box-shadow 0.2s;
  }

  .frameImage {
    position: relative;
    border-radius: 8px;
    overflow: hidden;
    flex-shrink: 0;
    transition: opacity 0.2s ease;
    &:hover {
      .remove,
      .editNode {
        opacity: 1;
      }
    }
    .remove {
      position: absolute;
      top: 3px;
      right: 3px;
      z-index: 9999;
      padding: 5px;
      border-radius: 10px;
      background-color: rgba(220, 50, 50, 0.7);
      cursor: pointer;
      opacity: 0;
      transform-origin: top right;
      &:hover {
        background-color: rgba(220, 50, 50, 1);
      }
    }
    .editNode {
      position: absolute;
      bottom: 3px;
      left: 3px;
      z-index: 9999;
      padding: 5px;
      border-radius: 10px;
      background-color: rgba(24, 144, 255, 0.7);
      cursor: pointer;
      transform-origin: bottom left;
      opacity: 0;
      &:hover {
        background-color: rgba(24, 144, 255, 1);
      }
    }
  }

  .generatingPlaceholder {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 6px;
    background-color: var(--td-bg-color-container-hover, #f5f5f5);
    font-size: 12px;
  }

  .frameImg {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .frameImage {
    :deep(.imageToolsWrap) {
      opacity: 1;
      pointer-events: auto;
    }
  }

  .frameCheckbox {
    position: absolute;
    left: 3px;
    top: 3px;
    z-index: 3;
    transform-origin: top left;
  }

  .frameTypeTag {
    color: #fff;
    font-size: 10px;
    font-weight: 600;
    border: none;
    z-index: 2;
    padding: 0 4px;
    line-height: 18px;
    border-radius: 3px;
  }

  .modeLabelTag {
    font-size: 9px;
    padding: 0 3px;
    line-height: 16px;
    margin-left: 2px;
  }

  .frameTag {
    position: absolute;
    right: 8px;
    bottom: 8px;
    color: #fff;
    font-size: 12px;
    font-weight: 600;
    border: none;
  }

  .scaleControl {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 8px;
    font-size: 13px;
    color: var(--td-text-color-primary, #333);
  }

  .frameInfo {
    margin-top: 6px;
    font-size: 12px;
    color: var(--td-text-color-primary, #333);
    line-height: 1.4;
    max-width: 200px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
}
:deep(.t-image__wrapper) {
  background-color: transparent !important;
}
.editInfoForm {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 4px 0;
}

.editInfoField {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.editInfoLabel {
  font-size: 13px;
  color: var(--td-text-color-secondary);
}

/* ===== 首尾帧双卡片布局样式 ===== */
.frameItem--dual {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding: 12px;
  border: 1px dashed var(--td-component-border);
  border-radius: 8px;
  background: var(--td-bg-color-container);
}

.transitionDesc {
  font-size: 11px;
  color: var(--td-text-color-placeholder);
  line-height: 1.4;
  padding: 4px 8px;
  background: var(--td-bg-color-secondarycontainer);
  border-radius: 4px;
}

.transitionLabel {
  font-weight: 600;
  color: var(--td-brand-color);
}

.dualFrameCard {
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: center;
}

.frameCard--first,
.frameCard--last {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 4px;
}

.frameLabel {
  font-size: 11px;
  font-weight: 600;
  color: var(--td-text-color-secondary);
}

.frameArrow {
  font-size: 20px;
  color: var(--td-brand-color);
  font-weight: 700;
}

.inheritTag {
  position: absolute;
  bottom: 4px;
  left: 4px;
  z-index: 3;
}

.dualFrameActions {
  display: flex;
  gap: 6px;
  justify-content: center;
  margin-top: 4px;
}

/* 双卡片图片与单卡片共用 .frameImg 样式 */
</style>
