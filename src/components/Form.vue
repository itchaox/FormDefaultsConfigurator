<!--
 * @Version    : v1.00
 * @Author     : itchaox
 * @Date       : 2023-12-23 09:34
 * @LastAuthor : itchaox
 * @LastTime   : 2024-01-06 12:29
 * @desc       : 
-->

<script setup lang="ts">
  import { bitable } from '@lark-base-open/js-sdk';
  import { Close, CaretRight, InfoFilled } from '@element-plus/icons-vue';
  import FieldIcon from './fieldIcon.jsx';
  import { v4 as uuidv4 } from 'uuid';
  import { dayjs } from 'element-plus';

  import { BrowserChrome } from '@icon-park/vue-next';

  const rawUrl = ref('https://bcmcjimpjd.feishu.cn/share/base/form/shrcnBAfWeR3Dky4fnWlOL4B9xG');

  import useClipboard from 'vue-clipboard3';

  // FIXME 筛选
  const filterList = ref([]);

  const addFilter = () => {
    filterList.value.push({
      type: 1,
      id: uuidv4(), // ⇨ '9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
      name: '',
      value: '',
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

  /**
   * @desc  : 生成转换后的地址
   */
  function generateFormDefaultUrl() {
    const queryParams = filterList.value
      .map((item) => {
        // 日期处理
        let _formateTime = item.type === 5 && dayjs(item.value).format('YYYY/MM/DD HH:mm');

        // 将中文逗号替换为英文逗号
        const processedValue = item.type === 5 ? _formateTime : item.value.replace(/，/g, ',');

        return `prefill_${encodeURIComponent(item.name)}=${encodeURIComponent(processedValue)}`;
      })
      .join('&');

    formDefaultUrl.value = rawUrl.value + `?${queryParams}`;

    console.log(formDefaultUrl.value);
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
    <div class="tip">
      <div class="tip-text title">
        <span>操作步骤：</span>
        <el-tooltip
          placement="right"
          effect="customized"
        >
          <template #content>查看表单预填默认值<br />更多操作详情</template>
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
    </div>

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
            2. 普通字段包括: 文本、单选、多选、单向关联、双向关联、人员、数字、货币、进度、电话号码
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
          <span> 表单默认值地址：</span>
          <BrowserChrome
            @click="goUrl"
            class="icon"
            theme="outline"
            strokeLinejoin="miter"
            strokeLinecap="butt"
            title="在浏览器中打开"
          />
        </div>
        <div
          class="url"
          @click="copy"
        >
          {{ formDefaultUrl }}
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
    margin-right: 10px;
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
    margin-top: 20px;
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
    margin-top: 5px;
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
