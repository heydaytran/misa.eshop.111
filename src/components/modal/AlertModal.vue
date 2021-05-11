<template>
  <div class="alert-modal">
    <div class="alert-form">
      <div class="alert-text">{{ alertMessage }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    visible: Boolean,
    alertMessage: String,
  },
  data() {
    return {};
  },
  methods: {
    /**
     * Sự kiện ẩn thông báo
     * CreatedBy: nctu 19.04.2021
     */
    hide() {
      this.visible = false;
    },

    /**
     * Sự kiện hiện form thông báo
     * CreatedBy: nctu 19.04.2021
     */
    show() {
      this.visible = true;
    },
  },
};
</script>

<style scoped>
.success {
  background-color: #187d1e;
}
.fail {
  background-color: #ffe7af;
}

.alert-modal {
  width: fit-content;
  min-width: 200px;
  height: 80px;
  min-height: 30px;
  color: #000;
  font-size: 15px;
  background-color: #e99f30;
  border-radius: 3px;
  /* opacity: 0.9; */
  z-index: 1000;
  box-shadow: 0 3px 6px rgb(0 0 0 / 16%), 0 3px 6px rgb(1 1 2 / 15%);
  display: flex;
  align-items: center;
  padding: 1px 1px 1px 14px;
  position: fixed;
  top: 60px;
  right: 30px;
  align-items: center;
}

/* .alert-modal {
  min-width: 100px;
  padding: 3px;
  background-color: #434774;
  position: absolute;
  bottom: 80px;
  right: 28px;
  top: 12px;
  left: calc(50% - 120px);
  border-radius: 3px;
  box-shadow: 1px 1px 4px #000000;
  z-index: 2;
} */

.alert-form {
  width: calc(100% - 40px);
  height: 62px;
  padding: 8px 20px;
  color: #434774;
  font-weight: bold;
  border-top-right-radius: 5px;
  background-color: #ffffff;
  font-size: 15px;
  vertical-align: middle;

}

</style>