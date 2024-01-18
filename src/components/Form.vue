<!--
 * @Version    : v1.00
 * @Author     : itchaox
 * @Date       : 2023-12-23 09:34
 * @LastAuthor : itchaox
 * @LastTime   : 2024-01-14 15:50
 * @desc       : 
-->

<script setup lang="ts">
  import { bitable } from '@lark-base-open/js-sdk';
  import { Close, CaretRight, InfoFilled } from '@element-plus/icons-vue';
  import FieldIcon from './fieldIcon.jsx';
  import { v4 as uuidv4 } from 'uuid';
  import { dayjs } from 'element-plus';

  import { useI18n } from 'vue-i18n';

  const { t } = useI18n();

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
      name: t('General Fields'),
    },
    {
      type: 5,
      name: t('date'),
    },
    {
      type: 7,
      name: t('checkbox'),
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
        message: t('Please fill in the form address first'),
        type: 'error',
        duration: 1500,
        showClose: true,
      });
      return;
    }

    // const prefix = 'https://bcmcjimpjd.feishu.cn/share/base/form';

    // if (!rawUrl.value.startsWith(prefix)) {
    //   // rawUrl 不以指定前缀开始
    //   ElMessage({
    //     message: t('f1'),
    //     type: 'error',
    //     duration: 1500,
    //     showClose: true,
    //   });

    //   return;
    // }

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
      message: t('Successful generation'),
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
        message: t('Copy Success'),
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
          <span class="collapse-title">{{ $t('More operational information') }}</span>
        </template>
        <div
          class="tip"
          style="margin-bottom: 0"
        >
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            {{ $t('1') }}
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            {{ $t('2') }}
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            {{ $t('3') }}
          </div>
          <div
            class="tip-text"
            style="margin-bottom: 0"
          >
            {{ $t('4') }}
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
            <span>{{ $t('Check out the official documentation for an introduction') }}</span>
          </div>
        </div>
      </el-collapse-item>
    </el-collapse>

    <div class="addView-line">
      <div class="addView-line-label">{{ $t('Form address') }}</div>
      <el-input
        style="width: 75%"
        v-model="rawUrl"
        :title="rawUrl"
        clearable
        :placeholder="$t('Please enter the form address')"
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
              :placeholder="$t('Please enter the name of the question')"
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
              <!-- <el-option
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
              </el-option> -->

              <el-option
                :key="1"
                :label="$t('General Fields')"
                :title="$t('General Fields')"
                :value="1"
              >
                <field-icon :fieldType="1" />
                <span>
                  {{ t('General Fields') }}
                </span>
              </el-option>
              <el-option
                :key="5"
                :label="$t('date')"
                :title="$t('date')"
                :value="5"
              >
                <field-icon :fieldType="5" />
                <span>
                  {{ t('date') }}
                </span>
              </el-option>
              <el-option
                :key="7"
                :label="$t('checkbox')"
                :title="$t('checkbox')"
                :value="7"
              >
                <field-icon :fieldType="7" />
                <span>
                  {{ t('checkbox') }}
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
              :placeholder="$t('Please enter the default value')"
              clearable
            />

            <el-date-picker
              v-else-if="item.type === 5"
              style="width: 100%"
              v-model="item.value"
              size="small"
              type="datetime"
              :placeholder="$t('Please select a date')"
              format="YYYY/MM/DD HH:mm"
            />

            <el-select
              v-else-if="item.type === 7"
              size="small"
              v-model="item.value"
              :title="item.name"
            >
              <el-option
                :label="$t('tick')"
                value="1"
              >
                {{ $t('tick') }}
              </el-option>

              <el-option
                :label="$t('unchecked')"
                value="0"
              >
                {{ $t('unchecked') }}
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
        class="add"
        text
        @click="addFilter"
      >
        <el-icon class="add-icon"><Plus /></el-icon>{{ $t('Adding default values') }}
      </el-button>
    </div>

    <div class="default-url">
      <el-button
        type="primary"
        @click="generateFormDefaultUrl"
      >
        <el-icon size="20"><CaretRight /></el-icon>
        <span>{{ $t('Generate default value address') }}</span>
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
            <span>{{ $t('Click to preview') }}</span>
          </div>
          <div
            class="url-icon"
            @click="copy"
          >
            <CopyLink
              theme="outline"
              size="16"
            />
            <span>{{ $t('Click to copy the address') }}</span>
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

  .add-icon {
    margin-right: 5px;
  }

  .add {
    color: #1456f0 !important;
    font-size: 16px;
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
