<template>
  <div class="shop-list">
    <!-- start content -->
    <div class="content">
      <div class="content-body">
        <!-- start tool bar -->
        <div class="tool-bar">
          <div class="tool-bar-btn div-btn-add">
            <button
              class="t-btn btn-add"
              id="btn-add"
              @click="openModalCreateShop()"
            >
              <i class="t-icon t-icon-add"></i>
              <span>Thêm mới</span>
            </button>
          </div>
          <div
            class="tool-bar-btn div-btn-replication"
            title="Tính năng này đang được cập nhật"
          >
            <button
              class="t-btn btn-replication t-btn-disable"
              id="btn-replication"
              disabled
            >
              <i class="t-icon t-icon-replication"></i>
              <span>Nhân bản</span>
            </button>
          </div>
          <div class="tool-bar-btn div-btn-edit">
            <button
              class="t-btn btn-edit"
              id="btn-edit"
              @click="openEditDialog()"
            >
              <i class="t-icon t-icon-edit"></i>
              <span>Sửa</span>
            </button>
          </div>
          <div class="tool-bar-btn div-btn-delete">
            <button
              class="t-btn btn-delete"
              id="btn-delete"
              @click="openModalDeleteShop()"
            >
              <i class="t-icon t-icon-delete"></i>
              <span>Xóa</span>
            </button>
          </div>
          <div class="tool-bar-btn div-btn-load">
            <button class="t-btn btn-load" id="btn-load" @click="reLoadData()">
              <i class="t-icon t-icon-load"></i>
              <span>Nạp</span>
            </button>
          </div>
        </div>
        <!-- end tool bar -->

        <!-- start grid -->
        <div class="t-grid">
          <table
            id="tbl-list-store"
            cellspacing="0"
            cellpadding="0"
            width="100%"
          >
            <thead>
              <tr>
                <th class="col-15" fieldName="shopCode">
                  <div class="thead-text">Mã cửa hàng</div>
                  <div class="thead-filter">
                    <button class="t-btn condition">*</button>
                    <input
                      type="text"
                      class="t-input filter-text"
                      id="filter-shop-code"
                      v-model="filterGrid.storeCode"
                    />
                  </div>
                </th>
                <th class="col-21" fieldName="shopName">
                  <div class="thead-text">Tên cửa hàng</div>
                  <div class="thead-filter">
                    <button class="t-btn condition">*</button>
                    <input
                      type="text"
                      class="t-input filter-text"
                      id="filter-shop-name"
                      v-model="filterGrid.storeName"
                    />
                  </div>
                </th>
                <th class="col-42" fieldName="address">
                  <div class="thead-text">Địa chỉ</div>
                  <div class="thead-filter">
                    <button class="t-btn condition">*</button>
                    <input
                      type="text"
                      class="t-input filter-text"
                      id="filter-address"
                      v-model="filterGrid.address"
                    />
                  </div>
                </th>
                <th class="col-10" fieldName="phoneNumber">
                  <div class="thead-text">Số điện thoại</div>
                  <div class="thead-filter">
                    <button class="t-btn condition">*</button>
                    <input
                      type="text"
                      class="t-input filter-text"
                      id="filter-phone-number"
                      v-model="filterGrid.phoneNumber"
                    />
                  </div>
                </th>
                <th class="col-12" fieldName="status">
                  <div class="thead-text">Trạng thái</div>
                  <div class="thead-filter">
                    <select
                      type="text"
                      class="filter-select"
                      id="filter-status"
                      v-model="filterGrid.status"
                    >
                      <option
                        v-for="option in storeStatus"
                        :key="option.value"
                        :value="option.value"
                      >
                        {{ option.statusName }}
                      </option>
                    </select>
                  </div>
                </th>
              </tr>
            </thead>

            <div v-show="!isLoaded" class="loading">
              <div class="loader"></div>
              <div class="text">Đang nạp dữ liệu</div>
            </div>

            <div v-show="hasResult" class="not-find">
              <i class="notify">Không tìm thấy kết quả</i>
            </div>

            <tbody v-if="shops && shops.length" class="tbl-scroll">
              <tr
                class="row-data"
                v-for="shop in shops"
                :key="shop.storeId"
                @click="clickRow(shop.storeId)"
                @dblclick="openEditDialog(shop.storeId)"
                v-bind:class="isSelected(shop.storeId) ? 'selected' : ''"
              >
                <td class="col-15">{{ shop.storeCode }}</td>
                <td class="col-21">{{ shop.storeName }}</td>
                <td class="col-42">{{ shop.address }}</td>
                <td class="col-10">{{ shop.phoneNumber }}</td>
                <td class="col-12">{{ getStatusStoreName(shop.status) }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <!-- end grid -->

        <!-- begin footer -->
        <div class="footer">
          <div class="footer-content">
            <div class="pagination">
              <button
                class="t-btn btn-footer t-icon t-icon-left-shift t-btn-disable"
                id="btn-left-shift"
              ></button>
              <button
                class="t-btn btn-footer t-icon t-icon-previous t-btn-disable"
                id="btn-previous"
              >
                <i class=""></i>
              </button>
              <div class="footer-text">Trang</div>
              <div class="page-index">
                <input
                  type="text"
                  id="p-index"
                  class="p-index"
                  value="1"
                  v-model="page.pageNumber"
                />
              </div>
              <div class="footer-text">
                trên <span class="page-total">1</span>
              </div>
              <button
                class="t-btn btn-footer t-icon t-icon-next t-btn-disable"
                id="btn-next"
              ></button>
              <button
                class="t-btn btn-footer t-icon t-icon-right-shift t-btn-disable"
                id="btn-right-shift"
              ></button>
              <button
                class="t-btn btn-footer t-icon t-icon-refresh"
                id="btn-refresh"
              ></button>
              <div class="cb-footer">
                <select
                  name="cb-paging"
                  id="cb-paging"
                  class="cb-paging"
                  v-model="page.pageSize"
                >
                  <option
                    v-for="(number, index) in selectNumberStore"
                    :key="index"
                    :value="number"
                  >
                    {{ number }}
                  </option>
                </select>
              </div>
            </div>
            <div class="footer-text display-paging-text">
              Hiển thị <span class="begin-record"> 1 </span> -
              <span class="last-record"> 6</span> trên
              <span class="total-record"> 6 </span> kết quả
            </div>
          </div>
        </div>
        <!-- end footer -->
      </div>
    </div>
    <!-- end content -->

    <ModalCreateShop
      ref="ModalCreate"
      :msg="formMode"
      :selectedShopId="selectedShopId"
      @showAlertDialog="showAlertDialog"
    />

    <ModalDeletShop
      ref="ModalDelete"
      :selectedShopId="selectedShopId"
      @showAlertDelete="showAlertDelete"
    />
    <AlertModal
      v-show="showAlert"
      :visible="showAlert"
      :alertMessage="alertMessage"
    />
  </div>
</template>

<script>
import axios from "axios";
// import TheFooter from "../layout/TheFooter";
import ModalCreateShop from "../modal/FunctionModal/ModalCreateShop";
import ModalDeletShop from "../modal/FunctionModal/ModelDeleteShop";
import AlertModal from "../modal/AlertModal";
export default {
  name: "Content",

  components: {
    ModalCreateShop,
    ModalDeletShop,
    // TheFooter,
    AlertModal,
  },
  data() {
    return {
      formMode: "post",
      isLoaded: false,
      hasResult: false,
      canShowDialogDelete: false,
      shops: [],
      selectedShopId: null,
      storeStatus: [
        {
          statusName: "Tất cả",
          value: "",
        },
        {
          statusName: "Đang hoạt động",
          value: 0,
        },
        {
          statusName: "Đang đóng cửa",
          value: 1,
        },
      ],
      filterGrid: {
        storeCode: "",
        storeName: "",
        address: "",
        phoneNumber: "",
        status: 0,
      },

      page: {
        pageNumber: 1,
        pageSize: 50,
      },
      selectNumberStore: [15, 25, 50, 100],

      showAlert: false,
      alertMessage: "",
    };
  },

  props: {
    // msg: String,
  },

  created() {
     this.getData();
  //   this.getStoreByFilter(this.filterGrid);
  // },
  // watch: {
  //   filter() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  //   "filterGrid.storeCode"() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  //   "filterGrid.storeName"() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  //   "filterGrid.address"() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  //   "filterGrid.phoneNumber"() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  //   "filterGrid.status"() {
  //     this.getStoreByFilter(this.filterGrid);
  //   },
  },
  computed: {
    filter() {
      return this.getStoreByFilter(this.filterGrid);
    },
  },
  methods: {
    /**
     * Sự kiện mở dialog thêm mới
     * Created by: nctu (13.04.2021)
     */
    openModalCreateShop() {
      this.$refs.ModalCreate.show();
      // focus vào ô nhập liệu đầu tiên
      var input = this.$refs.ModalCreate;
      setTimeout(() => {
        input.$refs.storeCode.focus();
      }, 0);

      this.formMode = "post";
    },

    /**
     * Sự kiện mở dialog xóa (xác nhận xóa)
     * Created by: nctu (13.04.2021)
     */
    openModalDeleteShop() {
      if (this.selectedShopId == null || this.selectedShopId == "") {
        this.openAlertModal("Vui lòng chọn bản ghi để xóa");
        return;
      }
      this.$refs.ModalDelete.show();
    },

    /**
     * Sự kiện mở dialog để sửa
     * CreatedBy: nctu 15.04.2021
     */
    openEditDialog() {
      if (this.selectedShopId == null || this.selectedShopId == "") {
        this.openAlertModal("Vui lòng chọn bản ghi để sửa");
        return;
      }
      this.formMode = "put";
      this.$refs.ModalCreate.show();
      // focus vào ô nhập liệu đầu tiên
      var input = this.$refs.ModalCreate;
      setTimeout(() => {
        input.$refs.storeCode.focus();
      }, 0);
    },

    /**
     * Sự kiện đóng dialog thêm mới
     * Created by: nctu (13.04.2021)
     */
    closeCreateDialogForm() {
      this.$refs.ModalCreate.hide();
    },

    /**
     * Sự kiện đóng popup xác nhận xóa
     * createdBy: nctu 16.04.2021
     */
    closeDeletePopUp() {
      this.$refs.ModalDelete.hide();
    },

    /**
     * Sự kiện hiện thông báo: sau khi thêm, sửa, xóa
     * createdBy: nctu 16.04.2021
     */
    openAlertModal(message) {
      this.showAlert = true;
      this.alertMessage = message;
      setTimeout(() => {
        this.showAlert = false;
      }, 4000);
    },

    /**
     * Lấy danh sách trạng thái cửa hàng
     * CreatedBy: nctu 14.04.2021
     */
    getStatusStoreName(value) {
      var status = String;
      if (value == 0) {
        status = "Đang hoạt động";
      } else if (value == 1) {
        status = "Đang đóng cửa";
      } else {
        status = "";
      }
      return status;
    },

    /** Hàm load dữ liệu lên table
     * Created by: nctu (13.04.2021)
     */
    getData() {
      // 1. Xử lý processing load data
      this.isLoaded = false;
      // 3. Thực hiện việc lấy dữ liệu
      axios
        .get("http://localhost:35480/api/v1/stores/")
        .then((respone) => {
          this.shops = respone.data.data;
        })
        .then(() => {
          this.isLoaded = true;
        })
        .catch((error) => console.log(error));
    },

    /**
     * Lấy danh sách cửa hàng theo điều kiện lọc
     * CreatedBy: nctu 19.04.2021
     */
    getStoreByFilter(filter) {
      this.isLoaded = false;
      var url = `http://localhost:35480/api/v1/stores/filter?storeCode=${filter.storeCode}&storeName=${filter.storeName}&address=${filter.address}&phoneNumber=${filter.phoneNumber}&status=${filter.status}`;

      axios
        .get(url)
        .then((respone) => {
          this.shops = respone.data.data;
          if (this.shops.length == 0) {
            this.hasResult = true;
          }else{
            this.hasResult = false;
          }
        })
        .then(() => {
          this.isLoaded = true;
        })
        .catch((error) => console.log(error));
    },

    /**
     *  Sự kiện hiện thông báo sau khi thao tác thêm, sửa
     *  CreatedBy: nctu 19.04.2021
     */
    showAlertDialog(alertMessage) {
      console.log(alertMessage);
      this.alertMessage = alertMessage;
      this.openAlertModal(this.alertMessage);
      this.reLoadData();
      this.closeCreateDialogForm();
    },

    /**
     * Xác nhận xóa bản ghi thành công
     * CreatedBy: nctu 17.04.2021
     */
    showAlertDelete(alertMessage) {
      this.alertMessage = alertMessage;
      this.openAlertModal(this.alertMessage);
      this.reLoadData();
      this.closeDeletePopUp();
    },

    /**
     * kiểm tra xem đã click vào bản ghi cần chọn chưa
     * Nếu vị trí của bản ghi là -1 thì chưa được chọn (do mảng bắt đầu từ phần tử 0)
     * @param {any} storeId Thực thể cần thêm mới
     * CreatedBy: nctu 13.04.2021
     */
    isSelected(storeId) {
      // lấy ra vị trí của bản ghi có id = shopId
      // let indexShop = this.selectedShop.indexOf(shopId);
      if (this.selectedShopId == storeId) return true;
      return false;
    },

    /**
     * Thêm hoặc xóa hàng được click vào list selectedShop
     */
    clickRow(storeId) {
      this.selectedShopId = storeId;
    },

    /**
     * Xóa trạng thái các ô filter trên bảng danh sách
     * CreatedBy: nctu 13.04.2021
     */
    resetFilterGrid() {
      this.filterGrid.storeCode = "";
      this.filterGrid.storeName = "";
      this.filterGrid.address = "";
      this.filterGrid.phoneNumber = "";
      this.filterGrid.status = 0;
    },

    /**
     * load lại dữ liệu
     * CreatedBy: nctu 13.04.2021
     */
    reLoadData() {
      this.resetFilterGrid();
      this.getStoreByFilter(this.filterGrid);
      // this.getData();
    },
  },
};
</script>


<style  scoped>
@import "../../styles/layout/toolbar.css";
@import "../../styles/layout/content.css";
@import "../../styles/layout/footer.css";
@import "../../styles/animationLoading.css";

/* css cho animation loading */
.loading {
  width: calc(100% - 186px);
  height: calc(100vh - 218px);
  position: fixed;
  top: 160px;
  background-color: rgba(0, 0, 0, 0.3);
  color: #ffffff;
  text-align: center;
}
.loading .text {
  position: fixed;
  left: calc(50% + 20px);
  top: calc(50% + 90px);
}

.not-find {
  width: 100%;
  height: auto;
  text-align: center;
  background: transparent;
  position: absolute;
  line-height: 30px;
  font-weight: 500;
  font-size: 15px;
  margin-top: 8px;
}
</style>