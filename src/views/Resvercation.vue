<template>
  <div>
    <a-input-search
      placeholder="input search text"
      style="width: 200px"
      @search="onSearch"
    />
    <a-divider />
    <a-table :columns="columns" :data-source="tableContent">
      <a slot="name" slot-scope="text">{{ text }}</a>
      <span slot="action" slot-scope="text, record">
        <a @click="() => Delete(record)">Delete</a>
      </span>
    </a-table>
    <a-button type="primary" @click="ShowDialog">
      添加
    </a-button>
    <a-modal
      title="Title"
      :visible="visible"
      :confirm-loading="confirmLoading"
      @ok="Insert"
      @cancel="ShowDialog"
    >
      <a-input
        class="dialog"
        v-model="newcustID"
        placeholder="CustID"
      ></a-input>
      <a-input
        class="dialog"
        v-model="newresvType"
        placeholder="ResvType"
      ></a-input>
      <a-input
        class="dialog"
        v-model="newresvKey"
        placeholder="ResvKey"
      ></a-input>
    </a-modal>
  </div>
</template>
<script>
const columns = [
  {
    title: "CustID",
    dataIndex: "custID",
    key: "custID",
    scopedSlots: { customRender: "name" },
  },
  {
    title: "ResvType",
    dataIndex: "resvType",
    key: "resvType",
  },
  {
    title: "ResvKey",
    dataIndex: "resvKey",
    key: "resvKey",
  },
  {
    title: "Action",
    key: "action",
    scopedSlots: { customRender: "action" },
  },
];

export default {
  beforeMount() {
    this.Refresh();
  },
  data() {
    return {
      tableContent: [],
      columns,

      visible: false,
      confirmLoading: false,

      newcustID: "",
      newresvType: "",
      newresvKey: "",
    };
  },
  methods: {
    Delete(item) {
      this.$http
        .post("/delete", "type=resv&custID" + item.custID
        + "&resvType=" + item.resvType
        + "&resvKey=" + item.resvKey)
        .then((res) => {
          this.Refresh();
        });
    },
    Refresh() {},
    ShowDialog() {
      this.Clean();
      this.visible = !this.visible;
    },
    Insert() {
      this.$http
        .post(
          "/resv",
          "custID=" +
            this.newcustID +
            "&resvType=" +
            this.newresvType +
            "&resvKey=" +
            this.newresvKey
        )
        .then((res) => {
          if (res.data == "1") {
            this.$message.success("添加成功");
            this.Refresh();
            this.ShowDialog();
          } else {
            this.$message.error("添加失败");
            this.Clean();
          }
        });
    },
    Clean() {
        this.newcustID = "";
        this.newresvType = "";
        this.newresvKey = "";
    },
    onSearch(value) {
      console.log("type=resv" + "&custId=" + value)
      this.$http
        .post("/search", "type=resv" + "&custID=" + value)
        .then((res) => {
          this.tableContent = res.data;
        });
    },
  },
};
</script>

<style scoped>
a-modal a-input {
  padding-bottom: 10px;
}
</style>
