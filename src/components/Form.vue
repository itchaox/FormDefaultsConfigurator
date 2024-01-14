<!--
 * @Version    : v1.00
 * @Author     : itchaox
 * @Date       : 2023-12-23 09:34
 * @LastAuthor : itchaox
 * @LastTime   : 2024-01-14 14:54
 * @desc       : 
-->

<script setup lang="ts">
  import { bitable } from '@lark-base-open/js-sdk';
  import { Close, CaretRight, InfoFilled } from '@element-plus/icons-vue';
  import FieldIcon from './fieldIcon.jsx';
  import { v4 as uuidv4 } from 'uuid';
  import { dayjs } from 'element-plus';

  import { BrowserSafari, PreviewOpen, PreviewClose, DocDetail, CopyLink } from '@icon-park/vue-next';

  const rawUrl = ref('');

  import useClipboard from 'vue-clipboard3';

  // FIXME 筛选
  const filterList = ref([]);

  const addFilter = () => {
    filterList.value.push({
      type: 1,
      id: uuidv4(), // ⇨ '9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
      name: '',
      value: '',
      isShow: true,
    });
  };

  // 字段列表

  const fieldList = ref([
    {
      type: 1,
      name: '普通字段',
    },
    {
      type: 5,
      name: '日期',
    },
    {
      type: 7,
      name: '复选框',
    },
  ]);

  // const fieldList = ref([
  //   {
  //     type: 1,
  //     name: '文本',
  //   },
  //   {
  //     type: 2,
  //     name: '数字',
  //   },
  //   {
  //     type: 3,
  //     name: '单选',
  //   },
  //   {
  //     type: 4,
  //     name: '多选',
  //   },
  //   {
  //     type: 5,
  //     name: '日期',
  //   },
  //   {
  //     type: 7,
  //     name: '复选框',
  //   },
  //   {
  //     type: 11,
  //     name: '人员',
  //   },
  //   {
  //     type: 13,
  //     name: '电话号码',
  //   },
  //   {
  //     type: 18,
  //     name: '单向关联',
  //   },
  //   {
  //     type: 21,
  //     name: '双向关联',
  //   },
  //   {
  //     type: 99002,
  //     name: '进度',
  //   },
  //   {
  //     type: 99003,
  //     name: '货币',
  //   },
  // ]);

  const filterFiledChange = async (item, index) => {
    item.value = '';

    if (item.type === 7) {
      filterList.value[index] = {
        ...filterList.value[index],
        value: '1',
      };
    }

    // let _activeItem = fieldList.value.find((i) => i.id === item.id);
    // // 文本项\数字类,不需要再掉数据
    // if (textMap.value.includes(_activeItem?.type)) {
    //   filterList.value[index] = {
    //     name: _activeItem.name,
    //     type: _activeItem.type,
    //     id: _activeItem.id,
    //     operator: 'is',
    //     value: '',
    //   };
    // }
  };

  const formDefaultUrl = ref();

  watchEffect(() => {
    if (!rawUrl.value) {
      formDefaultUrl.value = '';
    }
  });

  /**
   * @desc  : 生成转换后的地址
   */
  function generateFormDefaultUrl() {
    if (!rawUrl.value) {
      ElMessage({
        message: '请先填写表单地址!',
        type: 'error',
        duration: 1500,
        showClose: true,
      });
      return;
    }

    const prefix = 'https://bcmcjimpjd.feishu.cn/share/base/form';

    if (!rawUrl.value.startsWith(prefix)) {
      // rawUrl 不以指定前缀开始
      ElMessage({
        message: '表单地址不合法，请检查!',
        type: 'error',
        duration: 1500,
        showClose: true,
      });

      return;
    }

    const queryParams = filterList.value
      .map((item) => {
        // 日期处理
        let _formateTime = item.type === 5 && dayjs(item.value).format('YYYY/MM/DD HH:mm');

        // 将中文逗号替换为英文逗号
        const processedValue = item.type === 5 ? _formateTime : item.value.replace(/，/g, ',');

        let _data;

        // 是否隐藏问题
        if (item.isShow) {
          _data = `prefill_${encodeURIComponent(item.name)}=${encodeURIComponent(processedValue)}`;
        } else {
          _data = `prefill_${encodeURIComponent(item.name)}=${encodeURIComponent(
            processedValue,
          )}&hide_${encodeURIComponent(item.name)}=${encodeURIComponent(1)}`;
        }

        return _data;
      })
      .join('&');

    if (queryParams) {
      formDefaultUrl.value = rawUrl.value + `?${queryParams}`;
    } else {
      formDefaultUrl.value = rawUrl.value;
    }

    ElMessage({
      message: '生成成功',
      type: 'success',
      duration: 1500,
      showClose: true,
    });
  }

  const { toClipboard } = useClipboard();

  async function copy() {
    try {
      // 复制
      await toClipboard(formDefaultUrl.value);
      ElMessage({
        message: '复制成功',
        type: 'success',
        duration: 1500,
        showClose: true,
      });
      // 复制成功
    } catch (e) {
      // 复制失败
    }
  }

  function goUrl() {
    window.open(formDefaultUrl.value, '_blank');
  }

  function more() {
    window.open(
      'https://www.feishu.cn/hc/zh-CN/articles/763023425825-%E8%AE%BE%E7%BD%AE%E8%A1%A8%E5%8D%95%E9%A2%84%E5%A1%AB%E9%BB%98%E8%AE%A4%E5%80%BC',
      '_blank',
    );
  }

  const collapse = ref('0');
</script>

<template>
  <div class="home">
    <!-- <div class="tip">
      <div class="tip-text title">
        <span>操作步骤：</span>
        <el-tooltip
          placement="right"
          effect="customized"
        >
          <template #content>查看表单预填默认值<br />官方文档介绍</template>
          <el-icon
            class="tip-icon cursor"
            @click="more"
            ><QuestionFilled
          /></el-icon>
        </el-tooltip>
      </div>
      <div class="tip-text">1. 输入表单地址</div>
      <div class="tip-text">2. 添加默认值</div>
      <div class="tip-text">3. 点击【生成默认值地址】按钮即可</div>
    </div> -->

    <el-collapse
      v-model="collapse"
      class="collapse"
    >
      <!-- FIXME 筛选 -->
      <el-collapse-item name="1">
        <template #title>
          <el-icon class="mr5"><InfoFilled /></el-icon>
          <span class="collapse-title">更多操作信息</span>
        </template>
        <div
          class="tip"
          style="margin-bottom: 0"
        >
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            1. 支持默认值设置的字段类型：普通字段、复选框、日期
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            2. 普通字段包括: 文本、单选、多选、单向关联、双向关联、人员、数字、货币、进度、电话号码、超链接
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            3. 请确保默认值的问题名称与实际表单一致
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            4. 预填多条记录时，请使用逗号分隔（中文/英文皆可）
          </div>

          <div
            @click="more"
            class="more"
          >
            <div>
              <DocDetail
                theme="outline"
                size="13"
              />
            </div>
            <span>查看官方文档介绍</span>
          </div>
        </div>
      </el-collapse-item>
    </el-collapse>

    <div class="addView-line">
      <div class="addView-line-label">表单地址:</div>
      <el-input
        style="width: 75%"
        v-model="rawUrl"
        :title="rawUrl"
        clearable
        placeholder="请输入表单地址"
      />
    </div>

    <div>
      <div class="collapse-line-list">
        <div
          class="collapse-line"
          v-for="(item, index) in filterList"
          :key="item.id"
        >
          <el-button
            size="small"
            type="danger"
            link
            @click="() => (item.isShow = !item?.isShow)"
          >
            <preview-open
              v-if="item.isShow"
              theme="outline"
              size="20"
              fill="#333"
              strokeLinecap="square"
            />
            <preview-close
              v-else
              theme="outline"
              size="20"
              fill="#333"
              strokeLinecap="square"
            />
          </el-button>
          <div
            class="collapse-line-value"
            style="width: 50%"
          >
            <el-input
              v-model="item.name"
              :title="item.name"
              clearable
              size="small"
              placeholder="请输入问题名字"
            />
          </div>

          <!-- 字段名 -->
          <div class="collapse-line-filed">
            <el-select
              style="width: 105%"
              size="small"
              v-model="item.type"
              :title="item.name"
              @change="filterFiledChange(item, index)"
            >
              <el-option
                v-for="field in fieldList"
                :key="field.type"
                :label="field.name"
                :title="field.name"
                :value="field.type"
              >
                <field-icon :fieldType="field.type" />
                <span>
                  {{ field.name }}
                </span>
              </el-option>
            </el-select>
          </div>
          <!-- 值 -->
          <div
            class="collapse-line-value"
            style="width: 50%"
          >
            <el-input
              v-if="item.type === 1"
              v-model="item.value"
              :title="item.value"
              size="small"
              placeholder="请输入默认值"
              clearable
            />

            <el-date-picker
              v-else-if="item.type === 5"
              style="width: 100%"
              v-model="item.value"
              size="small"
              type="datetime"
              placeholder="请选择日期"
              format="YYYY/MM/DD HH:mm"
            />

            <el-select
              v-else-if="item.type === 7"
              size="small"
              v-model="item.value"
              :title="item.name"
            >
              <el-option
                label="勾选"
                value="1"
              >
                {{ '勾选' }}
              </el-option>

              <el-option
                label="不勾选"
                value="0"
              >
                {{ '不勾选' }}
              </el-option>
            </el-select>
            <!-- suffix-icon="x" -->
          </div>
          <el-button
            :icon="Close"
            class="collapse-delete"
            @click="() => filterList.splice(index, 1)"
            text
          />
        </div>
      </div>
      <el-button
        text
        @click="addFilter"
      >
        <el-icon><Plus /></el-icon>添加默认值
      </el-button>
    </div>

    <div class="default-url">
      <el-button
        type="primary"
        @click="generateFormDefaultUrl"
      >
        <el-icon size="20"><CaretRight /></el-icon>
        <span>生成默认值地址</span>
      </el-button>
      <div
        class="default-url-text"
        v-if="formDefaultUrl"
      >
        <div class="url-label">
          <div
            class="url-icon url-icon-preview"
            @click="goUrl"
          >
            <BrowserSafari
              size="16"
              theme="outline"
              strokeLinejoin="miter"
              strokeLinecap="butt"
            />
            <span>单击预览</span>
          </div>
          <div
            class="url-icon"
            @click="copy"
          >
            <CopyLink
              theme="outline"
              size="16"
            />
            <span>单击复制地址</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .home {
    font-size: 14px;
  }

  .addView-line {
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    .addView-line-label {
      margin-right: 10px;
      font-size: 14px;
      white-space: nowrap;
    }
  }

  .collapse-line-list {
    margin: 10px 0;
  }

  .collapse-line {
    display: flex;
    flex-wrap: nowrap;
    justify-content: space-between;
    margin-bottom: 10px;
    height: 24px;
  }

  .collapse-line-filed {
    margin-right: 5px;
  }

  .collapse-line-other {
    display: flex;
    justify-content: flex-end;
  }

  .collapse-line-value {
    margin: 0 5px;
  }

  .collapse-delete {
    padding: 5px;
    height: 24px;
  }

  .default-url {
    margin-top: 24px;
  }

  .default-url-text {
    margin-top: 24px;
  }

  .url-label {
    display: flex;
    align-items: center;
    .icon {
      color: #245bdb;
      padding: 2px;
      &:hover {
        cursor: pointer;
        background-color: #3370ff1a;
      }
    }
  }

  .url {
    display: inline-block;
    margin-top: 10px;
    max-width: 100%;
    color: #245bdb;
    word-wrap: break-word;
    text-decoration: none;
    white-space: normal;

    &:hover {
      opacity: 0.8;
      cursor: pointer;
      color: #3370ff;
    }
  }

  .tip {
    color: #8f959e;
    font-size: 12px;
    margin-bottom: 24px;
    .tip-text {
      margin-bottom: 4px;
    }

    .title {
      font-size: 14px;
      margin-bottom: 8px;
    }
  }
  .tip-icon {
    position: relative;
    top: 2px;
  }

  .cursor {
    cursor: pointer;
  }

  .collapse {
    margin-bottom: 14px;
  }
  .mr5 {
    margin-right: 5px;
  }

  .more {
    display: inline-flex;
    align-items: center;
    margin-top: 5px;
    color: #1456f0;
    cursor: pointer;

    span {
      /* margin-left: 5px; */
      padding-left: 2px;
    }

    div {
      position: relative;
      top: 2px;
    }
  }

  .url-icon {
    display: inline-flex;
    align-items: center;
    font-size: 16px;
    margin-right: 18px;
    color: #1456f0;
    &:hover {
      cursor: pointer;
    }

    span {
      padding-left: 4px;
    }
  }

  .url-icon-preview {
    position: relative;
  }

  .url-icon-preview::after {
    content: '';
    position: absolute;
    top: 0;
    right: -9px; /* 调整竖线的位置 */
    height: 100%;
    width: 1.5px; /* 竖线的宽度 */
    background-color: rgba(31, 35, 41, 0.15); /* 竖线的颜色 */
  }
</style>

<style>
  .el-popper.is-customized {
    padding: 6px 12px;
    background: linear-gradient(90deg, rgb(159, 229, 151), rgb(204, 229, 129));
  }

  .el-popper.is-customized .el-popper__arrow::before {
    background: linear-gradient(45deg, #b2e68d, #bce689);
    right: 0;
  }
</style>
