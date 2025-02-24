<template>
  <div class="">
    <a-form :model="formState" :label-col="labelCol" :wrapper-col="wrapperCol">
      <a-form-item :label="item.label" v-for="(item, index) in configList" :key="index">
        <!-- input -->
        <a-input
          v-if="item.type === 'input'"
          v-model:value="formState[item.key]"
          :placeholder="item.placeholder"
          :addon-before="item.addonBefore"
          :addon-after="item.addonAfter"
          :disabled="item.disabled"
          allow-clear
        />
        <!-- 文本域 -->
        <a-textarea
          v-if="item.type === 'input-text'"
          v-model:value="formState[item.key]"
          :show-count="item.showcount"
          :maxlength="item.maxlength"
          :rows="item.height"
        />
        <!-- 开关 -->
        <a-switch
          v-if="item.type === 'switch'"
          v-model:value="formState[item.key]"
          :disabled="item.disabled"
          :checked-children="item.checkedValue"
          :un-checked-children="item.unCheckedValue"
        />
        <!-- 多选框 -->
        <a-checkbox-group
          v-if="item.type === 'checkbox'"
          v-model:value="formState[item.key]"
          :disabled="item.disabled"
        >
          <a-checkbox
            :value="e.value"
            name="type"
            v-for="(e, index) in item.options"
            :key="index"
            :disabled="e.disabled"
          >
            {{ e.label }}
          </a-checkbox>
        </a-checkbox-group>
        <!-- 单选框 -->
        <a-radio-group
          v-if="item.type === 'radio'"
          v-model:value="formState[item.key]"
          :disabled="item.disabled"
          :button-style="item.button ? 'solid' : 'outline'"
        >
          <div v-if="item.button">
            <a-radio-button
              :value="e.value"
              name="type"
              v-for="(e, index) in item.options"
              :key="index"
              :disabled="e.disabled"
            >
              {{ e.label }}
            </a-radio-button>
          </div>

          <div v-else>
            <a-radio
              :value="e.value"
              name="type"
              v-for="(e, index) in item.options"
              :key="index"
              :disabled="e.disabled"
            >
              {{ e.label }}
            </a-radio>
          </div>
        </a-radio-group>
        <!-- 选择器未完成 -->
        <a-select
          v-if="item.type === 'select'"
          v-model:value="formState[item.key]"
          :options="item.options"
          :disabled="item.disabled"
          show-search
          allowClear
          :mode="item.multiple"
        />
        <!-- 日期 -->
        <a-date-picker
          v-if="item.type === 'date-picker'"
          :disabled="item.disabled"
          :locale="locale"
          v-model:value="formState[item.key]"
          value-format="YYYY-MM-DD"
        />
        <!-- 日期时间 -->
        <a-date-picker
          :locale="locale"
          :disabled="item.disabled"
          v-if="item.type === 'date-time'"
          v-model:value="formState[item.key]"
          show-time
          format="YYYY-MM-DD HH:mm:ss"
          value-format="YYYY-MM-DD HH:mm:ss"
        />
        <!-- 月份 -->
        <a-date-picker
          :locale="locale"
          :disabled="item.disabled"
          v-if="item.type === 'date-month'"
          v-model:value="formState[item.key]"
          value-format="YYYY-MM"
          picker="month"
        />
        <!-- 开始结束时间 -->
        <a-range-picker
          :locale="locale"
          :disabled="item.disabled"
          v-if="item.type === 'date-ctime'"
          v-model:value="formState[item.key]"
          value-format="YYYY-MM-DD"
        />
        <!-- 时间 -->
        <a-time-picker
          :locale="locale"
          :disabled="item.disabled"
          v-if="item.type === 'time'"
          v-model:value="formState[item.key]"
          value-format="HH:mm:ss"
        />
        <!-- 数字 -->
        <a-input-number
          v-if="item.type === 'number'"
          v-model:value="formState[item.key]"
          :min="item.min"
          :disabled="item.disabled"
          :addon-after="item.addonAfter"
          :max="item.max"
        />
        <!-- 进度条 -->
        <a-slider
          v-if="item.type === 'slider'"
          :disabled="item.disabled"
          v-model:value="formState[item.key]"
          :marks="item.marks"
        />
        <!-- 评分 -->
        <a-rate
          v-if="item.type === 'rate'"
          v-model:value="formState[item.key]"
          :disabled="item.disabled"
          :allow-half="item.allowHalf"
          :character="item.character"
          :count="item.count"
        />

        <a-upload
          v-if="item.type === 'upload'"
          v-model:value="formState[item.key]"
          name="logo"
          action="/upload.do"
          list-type="picture"
        >
          <a-button>
            <template #icon><UploadOutlined /></template>
            Click to upload
          </a-button>
        </a-upload>

        <a-upload-dragger
          v-if="item.type === 'upload-dragger'"
          v-model:value="formState[item.key]"
          name="files"
          action="/upload.do"
        >
          <p class="ant-upload-drag-icon">
            <InboxOutlined />
          </p>
          <p class="ant-upload-text">Click or drag file to this area to upload</p>
          <p class="ant-upload-hint">Support for a single or bulk upload.</p>
        </a-upload-dragger>
        <!-- 按钮 -->
        <a-button
          v-if="item.type === 'button'"
          :disabled="item.disabled"
          type="primary"
          @click="onSubmit"
        >
          {{ item.label }}
        </a-button>
      </a-form-item>

      <a-form-item :wrapper-col="{ span: 14, offset: 4 }">
        <a-button type="primary" @click="onSubmit">Create</a-button>
        <a-button style="margin-left: 10px">Cancel</a-button>
      </a-form-item>
    </a-form>
  </div>
</template>

<script setup lang="ts">
import { reactive, toRaw } from 'vue';
import type { UnwrapRef } from 'vue';
import { UploadOutlined, UserOutlined, InboxOutlined } from '@ant-design/icons-vue';
import locale from 'ant-design-vue/es/date-picker/locale/zh_CN';

const props = defineProps<{
  config: object;
  model: object;
}>();

const emit = defineEmits<{
  (e: 'change', id: number): void;
  (e: 'update', value: string): void;
}>();

const config = reactive({
  input: <any>{
    type: 'input',
    label: '账号',
    placeholder: '请输入账号',
    addonBefore: 'Http://',
    addonAfter: '.com',
    disabled: false,
    key: 'input',
  },
  inputText: <any>{
    type: 'input-text',
    label: '文本域',
    placeholder: '请输入内容',
    maxlength: 100,
    showcount: true,
    height: 4,
    key: 'inputText',
  },
  switch: {
    type: 'switch',
    label: '开关',
    checkedValue: '开',
    unCheckedValue: '关',
    disabled: false,
    key: 'switch',
  },
  checkbox: {
    type: 'checkbox',
    label: '多选框',
    disabled: false,
    options: [
      { value: 1, label: '奔驰', disabled: true },
      { value: 2, label: '宝马' },
      { value: 3, label: '奥迪' },
    ],
    key: 'checkbox',
  },
  radio: {
    type: 'radio',
    label: '单选框',
    button: true,
    hide: true,
    options: [
      { value: 1, label: '捷豹', disabled: true },
      { value: 2, label: '卡迪拉克' },
      { value: 3, label: '马自达' },
    ],
    key: 'radio',
  },
  button: {
    type: 'button',
    label: '按钮',
  },
  select: {
    type: 'select',
    label: '选择器',
    disabled: false,
    multiple: 'multiple',
    options: [
      { value: 1, label: '捷豹' },
      { value: 2, label: '卡迪拉克' },
      { value: 3, label: '马自达' },
      { value: 4, label: '飞机' },
      { value: 5, label: '游轮' },
    ],
    key: 'select',
  },
  date: {
    type: 'date-picker',
    label: '日期',
    key: 'date',
  },
  datetime: {
    type: 'date-time',
    label: '日期时间',
    key: 'datetime',
  },
  month: {
    type: 'date-month',
    label: '月份',
    key: 'month',
  },
  ctime: {
    type: 'date-ctime',
    label: '开始结束时间',
    key: 'ctime',
  },
  time: {
    type: 'time',
    label: '时间',
    key: 'time',
  },
  number: {
    type: 'number',
    label: '数字',
    addonAfter: '$',
    min: 1,
    max: 100,
    key: 'number',
  },
  slider: {
    type: 'slider',
    label: '进度条',
    marks: {
      0: '0%',
      20: '20%',
      40: '40%',
      60: '60%',
      80: '80%',
      100: '100%',
    },
    key: 'slider',
  },
  rate: {
    type: 'rate',
    label: '打星',
    allowHalf: true,
    count: 10,
    key: 'rate',
  },
  upload: {
    type: 'upload',
    label: '上传',
    key: 'upload',
  },
  uploaddragger: {
    type: 'upload-dragger',
    label: '上传',
    key: 'uploaddragger',
  },
});

const model: any = reactive({
  input: '',
  inputText: '',
  switch: false,
  checkbox: [],
  radio: 0,
  button: false,
  select: [],
  date: '',
  datetime: '',
  month: '',
  ctime: '',
  time: '',
  number: 0,
  slider: 0,
  rate: 0,
  upload: <any>0,
  uploaddragger: <any>0,
});

const configList = Object.values(props.config).filter(item => {
  return item.hide !== false;
});

const formState: UnwrapRef<any> = reactive({
  ...props.model,
});

const onSubmit = () => {
  console.log('submit!', toRaw(formState));
};

const wrapperCol = { span: 14 };
const labelCol = { style: { width: '150px' } };
</script>

<style scoped lang="scss"></style>
