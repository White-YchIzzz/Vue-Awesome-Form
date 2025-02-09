<template>
  <main v-loading="loading" class="awesome-form-container">
    <h1>Awesome Form</h1>
    <AwesomeForm v-model="formData" :config="config" :event-handlers="eventHandlers" />
    <el-button type="primary" @click="onSubmit">Submit</el-button>
  </main>
</template>

<script setup>
import { onMounted, reactive, ref } from "vue";
import AwesomeForm from "./components/AwesomeForm.vue";
// import { configList } from "./dsl.json";

const formData = reactive({});
const config = ref([]);
const loading = ref(false);

const onSubmit = () => {
  console.log(formData);
};

const getDsl = () => {
  loading.value = true;
  return fetch("http://localhost:3000/dsl")
    .then((r) => r.json())
    .then((r) => {
      console.log("实际数据: ", r);
      config.value = r.configList;
    })
    .finally(() => {
      loading.value = false;
    });
};

const eventHandlers = {
  fn1: (...args) => {
    // 第一件事
    fetch("http://localhost:3000/layer-list")
      .then((r) => r.json())
      .then((r) => {
        console.log("layerList数据: ", r);
        // 第二件事
        const target = config.value.find((item) => item.fieldName === "layerList");
        target.controlProps.disabled = false;
        target.options = r;
      });
  },
};

onMounted(() => {
  getDsl();
});
</script>

<style lang="scss" scoped>
.awesome-form-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
