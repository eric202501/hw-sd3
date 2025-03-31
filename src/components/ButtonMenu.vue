<script>
import locData from "@/assets/data2.json"; // 匯入地標數據

export default {
  props: {
    visible: Boolean
  },
  inject: ["showResult"],
  methods: {
    click(e) {
      let classifyCode = null;

      switch (e.target.innerHTML.trim()) {
        case "美食 Delicacies":
          classifyCode = "0"; break;
        case "系所 Department":
          classifyCode = "1"; break;
        case "行政 Administration":
          classifyCode = "2"; break;
        case "宿舍 Dormitory":
          classifyCode = "3"; break;
        default:
          return;
      }

      const result = locData
        .filter(loc => loc.classify?.includes(classifyCode))
        .map(loc => loc.name);

      this.showResult(result);
      this.$emit('close');
    }

  }
};
</script>

<template>
  <div v-if="visible" class="button-menu d-flex flex-column bg-light shadow p-3 rounded-3 gap-3">
    <button class="btn btn-outline-primary w-100 py-3 fw-bold rounded-3" @click="click">美食 Delicacies</button>
    <button class="btn btn-outline-success w-100 py-3 fw-bold rounded-3" @click="click">系所 Department</button>
    <button class="btn btn-outline-warning w-100 py-3 fw-bold rounded-3" @click="click">宿舍 Dormitory</button>
    <button class="btn btn-outline-danger w-100 py-3 fw-bold rounded-3" @click="click">行政 Administration</button>
  </div>
</template>

<style scoped>
.button-menu {
  position: absolute;
  margin-top: 2rem;
  right: 0px;
}

.button-menu button:hover {
  color: #000000;
}
@media (max-width: 768px) {
  .button-menu {
    display: flex;
  }
  .button-menu button {
    flex: 1;
    font-size: 0.85rem;
  }
}
</style>
