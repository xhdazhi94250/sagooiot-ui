<template>
  <div class="system-dic-container">
    <el-card shadow="hover">
      <div>
        <el-row>
          <el-col :span="12"   v-for="item in notice_send_gateway"  >
            <div class="ant-cards " v-if="item.status==1">
              <div class="ant-card-body line">
                <div class="content">
                  <div class="left">
                    <img
                      :src="'/imgs/notice/'+item.value+'.svg'"
                      alt="dingding"
                      style="height: 100px;width: 100px;"
                      v-col="'image'"
                    />
                    <div class="context">
                      <div class="title" v-col="'title'">{{item.label}}</div>
                      <div class="desc" v-col="'desc'">
                        {{item.remark}}
                      </div>
                    </div>
                  </div>
                   <div class="right"  v-col="'handle'">
                    <div
                      class="ant-space ant-space-horizontal ant-space-align-center"
                      style="gap: 8px;"
                    >
                      <div class="ant-space-item" style="" v-auth="'setting'">
                        <router-link :to="'/iotmanager/noticeservices/config/setting/'+item.value" class="link-type" >
                        <div class="action">
                            <div class="btn">
                              <img
                                src="data:image/png;base64,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DVyQ+d9Hqne0ztIc458ABZEw7O9MrlIK90tqAxeE1/7oYA9qDAWRkMx0LlvkKW87gKL/V+77WywKIokorBoKIiXb11m/zlFqrbT+VB0FKZaYVfUUpJg40qnkX+Dh0mLGUJBiaFlXS0GKjSTdWb8USrVJx1EQKSkr6yiITiy9nckLHIUbFbAYwLx8c1AQHcLWjKEgfqM40XHtaS7cOSPNU/lk9ffUYXe57jq8WVGXXCDjKEggGPNMMrSiYZ1SKPrF/akpgxBk5tRROHNKFSaNi8fnQXrTWRz8II039/f6jZeC+CVYaLxpQS5ZWINlS6YWajOS/53X/pRBrKYFifu1Pw0t3TjRz2t/rFWFgpiNZu3j+9F9qF+3CX6LpUtOOo6CSEmFU8drf8LhGtispgV54MZZsbyXNxdg7maTNY/u5bU/gZ3mECYyLcic6aNxx9XTQ9iZ/VPmrvzhtT+W52RakBye3JU/OVHGV7uW0wqmvdx1Pwfez+CNfSf9TsjXIH4JFho/uKoh6Xh4rFDdSP89iN+D6K7NcfzzB6GfAW/lTWd5cPboLkRBdMkFMo5PkEAwFpgku7KhA8B8nbUoiA61wMZQkMBQ5ploaFXDHcrDT3TWoiA61AIbQ0ECQ1noKbKioQsKI76pka9BSpVEUetQkKJw+Sjuu/nmWVUJr7vYKfgEKZZYoPUUJFCcBSbz1i4flT0yar0CbpauS0GkpEKpoyChYC0kyqqGJDx1hQfvQgCT85VTEBMJfbImBTGKH4C3smHRoIcJI/WxYkZD8wm3eqHpPmO6PgWxPfilzW/9DfCW2N5nRPujILYHS0GMJkRBjOIXLE5BBJDCK6Eg4bENZmYKEgxHzVkoiCa4kg1b2rzrj4C6vGQLcqFPE9jZ3lRn/AckipmMTGBp8+7bAdxNRgYIKNXS3lgr/p1VWB1SkDxk6+/dfZ7n4F9hwee8IxPIetnLf7Nmzp9NM6IgBRJY2rz7WQBfMx1UrNZX2NXeWFf0e+fCYERBClC99uddFzuu+0wY8Dnn8AQ8qOs2N9VusoEPBRGksLT5rWbAaxSUssQvAc/7ffuac77ld5qgxlMQIcn69btWe0rdJyxnmQYBBdzZ1lS3TmNoaEMoSBFolzfvrRlAJveTldzfTDyviKEsHZGAGlLKu0ch8VBr41n7bQNFQTQT+c49uz6fqPRqNYdzGIBBoOfxpjk9NsOgIDanw96ME6AgxiNgAzYToCA2p8PejBOgIMYjYAM2E6AgNqfD3owToCDGI2ADNhOgIDanw96ME6AgxiNgAzYToCA2p8PejBOgIMYjYAM2E6AgNqfD3owToCDGI2ADNhOgIDanw96ME6AgxiNgAzYToCA2p8PejBOgIMYjYAM2E6AgNqfD3owToCDGI2ADNhOgIDanw96ME6AgxiNgAzYToCA2p8PejBOgIMYjYAM2E6AgNqfD3owT+C+fWh4jlDOagwAAAABJRU5ErkJggg=="
                                style="height: 40px;width: 40px;"
                              />
                              <div>管理</div>
                            </div>
                        </div>
                        </router-link>
                      </div>
                      <div class="ant-space-item" v-auth="'edit'">
                        <div class="action" @click="onOpenEdit(item)">
                            <div class="btn">
                              <img
                                src="data:image/png;base64,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"
                                style="height: 40px;width: 40px;"
                              />
                              <div>配置</div>
                            </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </el-col>

        </el-row>
      </div>
    </el-card>

   <EditDic ref="editDicRef" @dataList="dataList" />
	 <!--<LevelDic ref="levelDicRef" @dataList="dataList" />-->
  </div>
</template>

<script lang="ts">
import { toRefs, reactive, onMounted, ref, defineComponent,getCurrentInstance } from "vue";
import { ElMessageBox, ElMessage, FormInstance } from "element-plus";
import EditDic from './component/edit.vue';
// import LevelDic from './component/level.vue';

import alarm from "/@/api/alarm";

// 定义接口来定义对象的类型
interface TableDataRow {
  id: number;
  name: string;
  key: string;
  createBy: string;
}
interface TableDataState {
  ids: number[];
  tableData: {
    data: Array<TableDataRow>;
    total: number;
    loading: boolean;
    param: {
      pageNum: number;
      pageSize: number;
      name: string;
      level: number;
      triggerType: number;
    };
  };
}

export default defineComponent({
  name: "setlist",
   components: { EditDic },

  setup() {
    const addDicRef = ref();
    const levelDicRef = ref();
    const editDicRef = ref();
    const detailRef = ref();
    const queryRef = ref();
    const { proxy } = getCurrentInstance() as any;

    const { notice_send_gateway } = proxy.useDict('notice_send_gateway');

    const state = reactive<TableDataState>({
      tableData: {
        data: [],
        total: 0,
        loading: false,
        param: {
          pageNum: 1,
          pageSize: 20,
          name: "",
          level: "",
          triggerType: "",
        },
      },
    });
    // 初始化表格数据
    const initTableData = () => {
      //dataList();
    };
    const dataList = () => {
      state.tableData.loading = true;
      alarm.common
        .getList(state.tableData.param)
        .then((res: any) => {
          state.tableData.data = res.list;
          state.tableData.total = res.Total;
        })
        .finally(() => (state.tableData.loading = false));
    };
    // 打开新增菜单弹窗
    const onOpenAdd = (row?: TableDataRow) => {
      editDicRef.value.openDialog();
    };
    const onOpenLevel = (row?: TableDataRow) => {
      levelDicRef.value.openDialog();
    };
    // 打开修改模型弹窗
    const onOpenEdit = (row: TableDataRow) => {
      editDicRef.value.openDialog({ ...row });
    };
    //打开数据记录
    const onOpenRecord = (row: TableDataRow) => {
      detailRef.value.openDialog(row);
    };
    const onRowDel = (row?: TableDataRow) => {
      let msg = "你确定要删除所选数据？";
      let ids: number[] = [];
      if (row) {
        msg = `此操作将永久删除模型：“${row.name}”，是否继续?`;
        ids = row.id;
      } else {
        ids = state.ids;
      }
      if (ids.length === 0) {
        ElMessage.error("请选择要删除的数据。");
        return;
      }
      ElMessageBox.confirm(msg, "提示", {
        confirmButtonText: "确认",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          alarm.common.delete(ids).then(() => {
            ElMessage.success("删除成功");
            dataList();
          });
        })
        .catch(() => {});
    };

    // 页面加载时
    onMounted(() => {
      initTableData();
    });
    /** 重置按钮操作 */
    const resetQuery = (formEl: FormInstance | undefined) => {
      if (!formEl) return;
      formEl.resetFields();
      dataList();
    };
    const onActionStatus = (item: TableDataRow[]) => {
      if (item.status == 0) {
        alarm.common.deploy({ id: item.id }).then((res: any) => {
          dataList();
        });
      } else {
        alarm.common.undeploy({ id: item.id }).then((res: any) => {
          dataList();
        });
      }
    };

    return {
      onActionStatus,
      addDicRef,
      editDicRef,
      detailRef,
      queryRef,
      levelDicRef,
      notice_send_gateway,
      onOpenRecord,
      onOpenLevel,
      onOpenAdd,
      onOpenEdit,
      onRowDel,
      dataList,
      resetQuery,
      ...toRefs(state),
    };
  },
});
</script>

<style>
.line{
  border: 1px solid var(--next-border-color-light);;

}
.ant-cards {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  color: rgba(0, 0, 0, 0.85);
  font-size: 14px;
  font-variant: tabular-nums;
  line-height: 1.5715;
  list-style: none;
  font-feature-settings: "tnum", "tnum";
  position: relative;
  border-radius: 2px;
  padding: 10px;
}
.ant-card-body {
  padding: 24px;

}

.ant-space {
  display: inline-flex;
}
.ant-space-align-center {
  align-items: center;
}
.ant-btn {
  line-height: 1.5715;
  position: relative;
  display: inline-block;
  font-weight: 400;
  white-space: nowrap;
  text-align: center;
  background-image: none;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.645, 0.045, 0.355, 1);
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  touch-action: manipulation;
  height: 32px;
  padding: 4px 15px;
  font-size: 14px;
  border-radius: 2px;

}
.ant-btn-link {
  color: #5b8fd9;
  border-color: transparent;
  background: transparent;
  box-shadow: none;
}
.context {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100px;
  margin-left: 15px;
}
.context .title {
  font-size: 24px;
}
.context .desc {
  font-size: 14px;
}

.content {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.content .left {
  display: flex;
}
.content .right .action {
  display: flex;
  justify-content: center;
  width: 100px;
  height: 100px;
  border: 1px solid #5b8fd9;
}
.content .right .action .btn {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100px;
  height: 100px;
}
</style>
