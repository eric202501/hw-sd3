<script>
import locData from "@/assets/data2.json"; // 匯入 data.json

export default {
  props: {
    visible: Boolean
  },
  inject: ["showResult"],
  methods: {
    click(e) {
      let classifyCode = null;

      // 依據按鈕文字決定分類代碼
      switch (e.target.innerHTML.trim()) {
        case "美食 Delicacies":
          classifyCode = "0";
          break;
        case "系所 Department":
          classifyCode = "1";
          break;
        case "宿舍 Dormitory":
          classifyCode = "2";
          break;
        case "行政 Administration":
          classifyCode = "3";
          break;
        default:
          return; // 無對應分類則不處理
      }

      // 根據 classifyCode 過濾地點名稱
      const result = locData
        .filter(loc => loc.classify.includes(classifyCode))
        .map(loc => loc.name);

      this.showResult(result);
      this.$emit('close'); // 點擊後關閉選單
    }
  }
};
</script>

<template>
  <div
    v-if="visible"
    class="d-flex flex-column position-absolute end-0 top-100 bg-light shadow p-3 rounded-3 gap-3 z-3"
  >
    <button class="btn btn-outline-primary w-100 py-3 fw-bold rounded-3" @click="click">美食 Delicacies</button>
    <button class="btn btn-outline-success w-100 py-3 fw-bold rounded-3" @click="click">系所 Department</button>
    <button class="btn btn-outline-warning w-100 py-3 fw-bold rounded-3" @click="click">宿舍 Dormitory</button>
    <button class="btn btn-outline-danger w-100 py-3 fw-bold rounded-3" @click="click">行政 Administration</button>
  </div>
</template>
