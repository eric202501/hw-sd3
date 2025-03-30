<script>
  export default {
    data() {
      return {
        visible: false,
      };
    },
    inject: ["showResult"],
    methods: {
      click(e) {
        switch (e.target.innerHTML) {
          case "美食 Delicacies":
            // Code here
            this.showResult(result);
            break;
          case "系所 Department":
            // Code here
            this.showResult(result);
            break;
          case "宿舍 Dormitory":
            // Code here
            this.showResult(result);
            break;
          case "行政 Administration":
            // Code here
            this.showResult(result);
            break;
        }
      }
    },
  }
</script>

<template>
  <div v-if="visible" class="d-flex flex-column position-absolute w-25 bg-light shadow p-3 rounded-3 gap-3">
    <button class="btn btn-outline-primary w-100 py-3 fw-bold rounded-3" @click="click">美食 Delicacies</button>
    <button class="btn btn-outline-success w-100 py-3 fw-bold rounded-3" @click="click">系所 Department</button>
    <button class="btn btn-outline-warning w-100 py-3 fw-bold rounded-3" @click="click">宿舍 Dormitory</button>
    <button class="btn btn-outline-danger w-100 py-3 fw-bold rounded-3" @click="click">行政 Administration</button>
  </div>
</template>