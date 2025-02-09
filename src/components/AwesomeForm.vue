<template>
  <el-form :model="formModel">
    <el-form-item v-for="item in config" :key="item.fieldName" v-bind="item.fieldProps">
      <!-- 用来渲染el-input, el-select等表单控件 -->
      <component
        :is="componentMap[item.controlName]"
        v-model="formModel[item.fieldName]"
        v-bind="item.controlProps"
        v-on="getEventHandlers(item)"
      >
        <template v-if="item.options">
          <!-- 用来渲染 el-option, el-radio, el-checkbox等option类组件 -->
          <component
            v-for="option in item.options"
            :key="option.value"
            :is="optionComponentMap[item.controlName]"
            :label="option.label"
            :value="option.value"
          ></component>
        </template>
      </component>
    </el-form-item>
  </el-form>
</template>

<script setup>
import { computed } from "vue";
import { ElInput, ElSelect, ElOption } from "element-plus";

const props = defineProps({
  config: [],
  modelValue: {},
  eventHandlers: {},
});

const componentMap = {
  input: ElInput,
  select: ElSelect,
};

const optionComponentMap = {
  select: ElOption,
};

const initialValueMap = {
  input: () => "",
  select: (prop) => (prop.multiple ? [] : ""),
};

const getEventHandlers = (item) => {
  const handlers = {};
  if (item.events) {
    Object.entries(item.events).forEach(([eventName, eventCallbackName]) => {
      // args即为element控件事件回调的参数集
      handlers[eventName] = (...args) => {
        if (props.eventHandlers[eventCallbackName]) {
          // 确保拿到element控件事件回调参数集的同时还能自由抛出更多参数
          return props.eventHandlers[eventCallbackName](...args);
        }
      };
    });
  }
  return handlers;
};

const emits = defineEmits(["modelValue:update"]);

const formModel = computed({
  get: () => {
    if (props.config) {
      props.config.forEach((item) => {
        props.modelValue[item.fieldName] = initialValueMap[item.controlName](item.controlProps);
      });
    }
    return props.modelValue;
  },
  set: (val) => {
    emits("modelValue:update", val);
  },
});
</script>

<style lang="scss" scoped></style>
