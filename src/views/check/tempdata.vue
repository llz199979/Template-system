<!-- 系统管理/日志管理 -->
<template>
  <div class="height_100 log">
    <div
      class="buttonGroup"
      v-if="tableInfo.addVisible && tableInfo.updateVisible"
    >
      <el-date-picker
        v-model="page.time"
        type="date"
        @keydown.enter.native="selectName"
        @clear="selectName"
        value-format="yyyy-MM-dd"
        style="width: 180px; margin-right: 10px"
        placeholder="选择日期"
      >
      </el-date-picker>

      <el-select
        clearable
        @change="getVal(page.cpuId)"
        style="margin-right: 11px"
        size="small"
        v-model="page.cpuId"
        filterable
        :placeholder="$t('m.placeholder.equipList')"
      >
        <el-option
          v-for="item in get_DeviceList.records"
          :key="item.macId"
          :label="item.name"
          :value="item.macId"
        >
        </el-option>
      </el-select>
      <el-select
        clearable
        @change="getVal(page.type)"
        style="margin-right: 11px; width: 150px"
        size="small"
        v-model="page.type"
        filterable
        :placeholder="$t('m.placeholder.typetemp')"
      >
        <el-option
          v-for="item in type"
          :key="item.id"
          :label="item.name"
          :value="item.id"
        >
        </el-option>
      </el-select>
      <el-button type="primary" @click="selectName" size="small">
        <i class="el-icon-search"></i>
        {{ $t("m.button.select") }}</el-button
      >
    </div>

    <el-table
      ref="multipleTable"
      :data="getInfo"
      tooltip-effect="dark"
      height="calc(100% - 70px)"
      style="width: 100%; margin-bottom: 6px"
      :row-key="getRowKey"
      :expand-row-keys="expands"
      @row-dblclick="dblclick"
      @select="select"
      @select-all="selectAll"
    >
      <el-table-column type="selection"> </el-table-column>
      <el-table-column
        type="index"
        :index="table_index"
        align="center"
        width="60"
        :label="$t('m.label.orderNumber')"
      />
      <el-table-column
        align="center"
        show-overflow-tooltip
        prop="time"
        :label="$t('m.label.date')"
      >
      </el-table-column>
      <el-table-column
        align="center"
        show-overflow-tooltip
        prop="temperature"
        :label="$t('m.label.temperature')"
      >
      </el-table-column>
      <el-table-column
        align="center"
        show-overflow-tooltip
        prop="url"
        :label="$t('m.label.picture')"
      >
        <template slot-scope="scope">
          <viewer>
            <img
              :src="scope.row.url"
              style="width: 50px; height: 50px"
              srcset
            />
          </viewer>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import labelTop from "../../components/page/label";
export default {
  name: "tempdata",
  components: {
    labelTop,
  },
  data() {
    return {
      addVisible: true, //添加框控制器
      updateVisible: true, //修改框控制器
      expands: [],
      selection: [], //选中的条目
      tableInfo: {
        //当前组件向子组件table 传值
        addVisible: true, //添加框控制器
        updateVisible: true, //修改框控制器
        currentPage: 1,
        pageSize: 20,
        get: "get_Temp", //vuex中获取当前table列表的getter函数
      },
      page: {
        time: "2021-02-01",
        type: "",
        cpuId: "",
        currentPage: 1,
        pageSize: 20,
        nameOrMacId: "",
      },
      corporation: {},
      getRowKey(row) {
        //
        return row.id;
      },
      type: [
        {
          id: "1",
          name: "正常",
        },
        {
          id: "2",
          name: "高温",
        },
      ],
    };
  },
  computed: {
    getInfo() {
      // 获取当前路由需要展示的信息table表
      if (this.tableInfo.get) {
        return this.$store.getters[this.tableInfo.get];
      }
    },
    get_DeviceList() {
      return this.$store.getters.get_DeviceList;
    },
  },
  mounted() {
    this.$store.dispatch("getTemp", this.page);
    this.$store.dispatch("getDeviceList", this.page);
  },
  methods: {
    table_index(index) {
      return (
        (this.tableInfo.currentPage - 1) * this.tableInfo.pageSize + index + 1
      );
    },
    select(selection) {
      // 单选    子组件table => 父组件传值
      this.selection = selection;
      console.log(this.selection);
    },
    selectAll(selection) {
      // 多选 子组件table => 父组件传值
      // console.log(selection)
      this.selection = selection;
      console.log(this.selection);
    },
    dblclick(row, cloumn, event) {},
    getVal(val) {
      this.$store.dispatch("getTemp", this.page);
    },
    selectName() {
      this.$store.dispatch("getTemp", this.page);
    },
  },
};
</script>

<style scoped>
.log {
  margin: 0 8px;
  text-align: left;

  /* background:#ffa5a5; */
}
.chest {
  width: 500px;
  height: 500px;
  margin: 0 auto;
  position: relative;
}
.heart {
  position: absolute;
  z-index: 2;
  background: linear-gradient(-90deg, #f50a45 0%, #d5093c 40%);
  animation: beat 2s ease 0s infinite normal;
}
.heart.center {
  background: linear-gradient(-45deg, #b80734 0%, #d5093c 40%);
}
.heart.top {
  z-index: 3;
}
.sided {
  top: 100px;
  width: 220px;
  height: 220px;
  border-radius: 110px;
}
.center {
  width: 210px;
  height: 210px;
  bottom: 100px;
  left: 145px;
  transform: rotateZ(225deg);
}
.left {
  left: 62px;
}
.right {
  right: 62px;
}
@keyframes beat {
  0% {
    transform: scale(1) rotate(225deg);
    box-shadow: 0 0 40px #d5093c;
  }
  50% {
    transform: scale(1.1) rotate(225deg);
    box-shadow: 0 0 70px #d5093c;
  }
  100% {
    transform: scale(1) rotate(225deg);
    box-shadow: 0 0 40px #d5093c;
  }
}
.input_260 {
  width: 260px;
}
</style>