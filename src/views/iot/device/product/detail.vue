<template>
	<div class="system-dic-container">
		<div class="content">
			<div class="cont_box">
				<div class="title">产品：{{ detail.name }}</div>
				<div class="pro-status"><span :class="developer_status == 1 ? 'on' : 'off'"></span>{{ developer_status == 1 ? '已发布' : '未发布' }}</div>

				<div class="pro-option" @click="CkOption"> {{ developer_status == 1 ? '停用' : '启用' }}</div>
			</div>
		</div>

		<div class="content-box">
			<el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick">
				<el-tab-pane label="产品信息" name="1">
					<div class="pro-box">
						<div class="protitle">产品信息</div>
						<el-button type="" :icon="Edit" class="buttonedit" @click="onOpenEditDic(detail)">编辑</el-button>
					</div>

					<div class="ant-descriptions-view">
						<table>
							<tbody>
								<tr class="ant-descriptions-row">
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">产品标识</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.key }}</td>
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">所属品类</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.categoryName }}</td>
									<!-- <th class="ant-descriptions-item-label ant-descriptions-item-colon">所属部门</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.deptName }}</td> -->
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">设备类型</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.deviceType }}</td>
								</tr>
								<tr class="ant-descriptions-row">
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">消息协议</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.messageProtocol }}</td>
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">链接协议</th>
									<td class="ant-descriptions-item-content" colspan="1">{{ detail.transportProtocol }}</td>

								</tr>
								<tr class="ant-descriptions-row">
									<th class="ant-descriptions-item-label ant-descriptions-item-colon">描述</th>
									<td class="ant-descriptions-item-content" colspan="5">{{ detail.desc }}</td>
								</tr>
							</tbody>
						</table>
					</div>
				</el-tab-pane>
				<el-tab-pane label="物模型" name="2">
					<div class="wu-box">
						<el-tabs type="border-card" v-model="activetab" @tab-click="wuhandleClick">
							<el-tab-pane label="属性定义" name="attr">
								<div class="wu-title">
									<div class="title">属性定义</div>
									<div><el-button type="primary" @click="onOpenEditAttr()">添加</el-button></div>
								</div>

								<el-table style="width: 100%" :data="tableData.data" v-if="activetab == 'attr'">
									<el-table-column label="属性标识" align="center" prop="key" />
									<el-table-column label="属性名称" prop="name" :show-overflow-tooltip="true" />
									<el-table-column prop="valueType" label="数据类型" width="100" align="center">
										<template #default="scope">
											<span>{{ scope.row.valueType.type }}</span>
										</template>
									</el-table-column>
									<el-table-column prop="decimals" label="精度" width="60" align="center">
										<template #default="scope">
											<span>{{ scope.row.valueType.decimals }}</span>
										</template>
									</el-table-column>
									<el-table-column prop="unit" label="单位" width="60" align="center">
										<template #default="scope">
											<span>{{ scope.row.valueType.unit }}</span>
										</template>
									</el-table-column>
									<el-table-column prop="accessMode" label="是否只读" width="120" align="center">
										<template #default="scope">
											<el-tag type="info" size="small" v-if="scope.row.accessMode">只读</el-tag>
											<el-tag type="success" size="small" v-else>读写</el-tag>
										</template>
									</el-table-column>
									<el-table-column label="说明" prop="desc" :show-overflow-tooltip="true" />
									<el-table-column label="操作" width="300" align="center" fixed="right">
										<template #default="scope">
											<el-button size="small" text type="warning" @click="onEditAttr(scope.row)">修改</el-button>
											<el-button size="small" text type="danger" @click="onRowDel(scope.row.key, 'attr')">删除</el-button>
										</template>
									</el-table-column>
								</el-table>
							</el-tab-pane>
							<el-tab-pane label="功能定义" name="fun">
								<div class="wu-title">
									<div class="title">功能定义</div>
									<div><el-button type="primary" @click="onOpenEditFun()">添加</el-button></div>
								</div>

								<el-table style="width: 100%" :data="tableData.data" v-if="activetab == 'fun'">
									<el-table-column label="功能标识" align="center" prop="key" />
									<el-table-column label="名称" prop="name" :show-overflow-tooltip="true" />

									<el-table-column label="描述" prop="desc" :show-overflow-tooltip="true" />
									<el-table-column label="操作" width="300" align="center" fixed="right">
										<template #default="scope">
											<el-button size="small" text type="warning" @click="onEditFun(scope.row)">修改</el-button>
											<el-button size="small" text type="danger" @click="onRowDel(scope.row.key, 'fun')">删除</el-button>
										</template>
									</el-table-column>
								</el-table>
							</el-tab-pane>
							<el-tab-pane label="事件定义" name="event">
								<div class="wu-title">
									<div class="title">事件定义</div>
									<div><el-button type="primary" @click="onOpenEditEvent()">添加</el-button></div>
								</div>

								<el-table style="width: 100%" :data="tableData.data" v-if="activetab == 'event'">
									<el-table-column label="事件标识" align="center" prop="key" />
									<el-table-column label="名称" prop="name" :show-overflow-tooltip="true" />
									<el-table-column prop="level" label="事件级别" width="120" align="center">
										<template #default="scope">
											<el-tag type="primary" size="small" v-if="scope.row.level == 0">普通</el-tag>
											<el-tag type="warning" size="small" v-if="scope.row.level == 1">警告</el-tag>
											<el-tag type="danger" size="small" v-if="scope.row.level == 2">紧急</el-tag>
										</template>
									</el-table-column>
									<el-table-column label="描述" prop="desc" :show-overflow-tooltip="true" />

									<el-table-column label="操作" width="300" align="center" fixed="right">
										<template #default="scope">
											<el-button size="small" text type="warning" @click="onEditEvent(scope.row)">修改</el-button>
											<el-button size="small" text type="danger" @click="onRowDel(scope.row.key, 'event')">删除</el-button>
										</template>
									</el-table-column>
								</el-table>
							</el-tab-pane>
							<el-tab-pane label="标签定义" name="tab">
								<div class="wu-title">
									<div class="title">标签定义</div>
									<div><el-button type="primary" @click="onOpenEditTab()">添加</el-button></div>
								</div>

								<el-table style="width: 100%" :data="tableData.data" v-if="activetab == 'tab'">
									<el-table-column label="属性标识" align="center" prop="key" />
									<el-table-column label="属性名称" prop="name" :show-overflow-tooltip="true" />
									<el-table-column prop="valueType" label="数据类型" width="120" align="center">
										<template #default="scope">
											<span>{{ scope.row.valueType.type }}</span>
										</template>
									</el-table-column>
									<el-table-column prop="accessMode" label="是否只读" width="120" align="center">
										<template #default="scope">
											<el-tag type="info" size="small" v-if="scope.row.accessMode">只读</el-tag>
											<el-tag type="success" size="small" v-else>读写</el-tag>
										</template>
									</el-table-column>
									<el-table-column label="描述" prop="desc" :show-overflow-tooltip="true" />
									<el-table-column label="操作" width="300" align="center" fixed="right">
										<template #default="scope">
											<el-button size="small" text type="warning" @click="onEditTag(scope.row)">修改</el-button>
											<el-button size="small" text type="danger" @click="onRowDel(scope.row.key, 'tab')">删除</el-button>
										</template>
									</el-table-column>
								</el-table>
							</el-tab-pane>
						</el-tabs>
						<pagination v-show="tableData.total > 0" :total="tableData.total" v-model:page="tableData.param.pageNum" v-model:limit="tableData.param.pageSize" @pagination="getList" />
					</div>
				</el-tab-pane>
				<el-tab-pane label="设备接入" name="3">
					<deviceIn></deviceIn>
				</el-tab-pane>
				<el-tab-pane label="数据解析" name="4" lazy>
					<dataParse v-if="activeName === '4'" :script="detail.scriptInfo" @updateScript="updateScript"></dataParse>
				</el-tab-pane>
			</el-tabs>
		</div>
		<EditDic ref="editDicRef" @typeList="typeList" />
		<EditAttr ref="editAttrRef" @typeList="getproperty" />
		<EditFun ref="editFunRef" @typeList="getfunction" />
		<EditEvent ref="editEventRef" @typeList="getevent" />
		<EditTab ref="editTabRef" @typeList="gettab" />
	</div>
</template>
<script lang="ts">
import { toRefs, reactive, onMounted, ref, defineComponent } from 'vue';
import { Delete, Edit, Search, Share, Upload } from '@element-plus/icons-vue';
import { ElMessageBox, ElMessage, FormInstance } from 'element-plus';

import EditDic from './component/editPro.vue';
import deviceIn from './component/deviceIn.vue';
import dataParse from './component/dataParse.vue';
import EditAttr from './component/editAttr.vue';
import EditFun from './component/editFun.vue';
import EditEvent from './component/editEvent.vue';
import EditTab from './component/editTab.vue';

import { useRoute } from 'vue-router';

import api from '/@/api/device';

interface TableDataState {
	ids: number[];
	detail: any;
	tableData: {
		data: [];
		total: number;
		loading: boolean;
		param: {
			pageNum: number;
			pageSize: number;
			name: string;
			deviceType: string;
			status: string;
			dateRange: string[];
		};
	};
}
export default defineComponent({
	name: 'deviceEditPro',
	components: { EditDic, EditAttr, EditFun, EditEvent, EditTab, deviceIn, dataParse },

	setup(prop, context) {
		const route = useRoute();
		const editDicRef = ref();
		const editAttrRef = ref();
		const editFunRef = ref();
		const editEventRef = ref();
		const editTabRef = ref();
		const state = reactive<TableDataState>({
			isShowDialog: false,
			activeName: '1', // 分类数据
			activetab: 'attr', // 分类数据
			detail: {},
			developer_status: 0,
			tableData: {
				data: [],
				total: 0,
				loading: false,
				param: {
					pageNum: 1,
					productId: route.params && route.params.id,
					pageSize: 10,
					status: '',
					dateRange: [],
				},
			},
		});

		onMounted(() => {
			const ids = route.params && route.params.id;
			api.product.detail(ids).then((res: any) => {
				state.detail = res.data;
				state.developer_status = res.data.status
			});

			//第一次加载
			api.model.property(state.tableData.param).then((res: any) => {
				state.tableData.data = res.Data;
				state.tableData.total = res.Total;
			});
		});

		//编辑属性
		const onEditAttr = (row: TableDataRow) => {
			editAttrRef.value.openDialog(row, route.params.id);
		};

		//编辑功能
		const onEditFun = (row: TableDataRow) => {
			editFunRef.value.openDialog(row, route.params.id);
		}


		//编辑事件
		const onEditEvent = (row: TableDataRow) => {
			editEventRef.value.openDialog(row, route.params.id);
		}

		//编辑标签
		const onEditTag = (row: TableDataRow) => {
			editTabRef.value.openDialog(row, route.params.id);
		}

		//打开添加属性弹窗
		const onOpenEditAttr = () => {
			editAttrRef.value.openDialog({ product_id: route.params.id, id: 0, accessMode: 0 });
		};

		//打开添加功能弹窗
		const onOpenEditFun = () => {
			editFunRef.value.openDialog({ product_id: route.params.id, id: 0 });
		};
		//打开添加事件弹窗
		const onOpenEditEvent = () => {
			editEventRef.value.openDialog({ product_id: route.params.id, id: 0, level: 0 });
		};

		//打开添加事件弹窗
		const onOpenEditTab = () => {
			editTabRef.value.openDialog({ product_id: route.params.id, id: 0, accessMode: 0 });
		};

		// 打开修改产品弹窗
		const onOpenEditDic = (row: TableDataRow) => {
			editDicRef.value.openDialog(row);
		};


		// 删除产品
		const onRowDel = (key, type) => {
			let msg = `此操作将永久删除该数据吗？，是否继续?`;

			if (key.length === 0) {
				ElMessage.error('请选择要删除的数据。');
				return;
			}
			ElMessageBox.confirm(msg, '提示', {
				confirmButtonText: '确认',
				cancelButtonText: '取消',
				type: 'warning',
			})
				.then(() => {
					if (type == 'attr') {
						api.model.propertydel(route.params.id, key).then(() => {
							ElMessage.success('删除成功');
							getproperty();
						});
					}
					if (type == 'fun') {
						api.model.functiondel(route.params.id, key).then(() => {
							ElMessage.success('删除成功');
							getfunction();
						});
					}
					if (type == 'event') {
						api.model.eventdel(route.params.id, key).then(() => {
							ElMessage.success('删除成功');
							getevent();
						});
					}
					if (type == 'tab') {
						api.model.tagdel(route.params.id, key).then(() => {
							ElMessage.success('删除成功');
							tagdel();
						});
					}
				})
				.catch(() => { });
		};


		//根据不同类型获取列表
		const getList = () => {
			switch (state.activetab) {
				case 'attr':
					getproperty();
					break;
				case 'fun':
					getfunction();
					break;
				case 'event':
					getevent();
					break;
				case 'tab':
					gettab();
					break;
			}
		};

		const getproperty = () => {
			api.model.property(state.tableData.param).then((res: any) => {
				state.tableData.data = res.Data;
				state.tableData.total = res.Total;
			});
		};

		const getfunction = () => {
			api.model.function(state.tableData.param).then((res: any) => {
				state.tableData.data = res.Data;
				state.tableData.total = res.Total;
			});
		};
		const getevent = () => {
			api.model.event(state.tableData.param).then((res: any) => {
				state.tableData.data = res.Data;
				state.tableData.total = res.Total;
			});
		};

		const gettab = () => {
			api.model.tag(state.tableData.param).then((res: any) => {
				state.tableData.data = res.Data;
				state.tableData.total = res.Total;
			});
		};

		const wuhandleClick = (tab: TabsPaneContext) => {
			state.activetab = tab.props.name;
			switch (tab.props.name) {
				case 'attr':
					getproperty();
					break;
				case 'fun':
					getfunction();
					break;
				case 'event':
					getevent();
					break;
				case 'tab':
					gettab();
					break;
			}
		};

		const handleClick = (tab: TabsPaneContext, event: Event) => {
			// console.log(tab, event);
		};

		const updateScript = (scriptInfo: string) => {
			state.detail.scriptInfo  = scriptInfo
		};

		const CkOption = () => {

			if (state.developer_status == 1) {
				api.product.undeploy({ id: route.params.id }).then((res: any) => {
					ElMessage.success('操作成功');
					state.developer_status = 0;
				});
			} else {
				api.product.deploy({ id: route.params.id }).then((res: any) => {
					ElMessage.success('操作成功');
					state.developer_status = 1;
				});
			}




		}

		return {
			Edit,
			updateScript,
			editDicRef,
			editAttrRef,
			editFunRef,
			editEventRef,
			editTabRef,
			CkOption,
			onRowDel,
			onEditFun,
			onEditEvent,
			onEditTag,
			onEditAttr,
			getList,
			getproperty,
			getfunction,
			getevent,
			gettab,
			wuhandleClick,
			onOpenEditTab,
			onOpenEditEvent,
			onOpenEditAttr,
			onOpenEditFun,
			onOpenEditDic,
			handleClick,
			...toRefs(state),
		};
	},
});
</script>
<style scoped>
.content {
	background: #fff;
	width: 100%;
	padding: 20px;
}

.content-box {
	background: #fff;
	width: 100%;
	padding: 20px;
	margin-top: 20px;
}

.cont_box {
	display: flex;
}

.cont_box .title {
	font-size: 24px;
}

.cont_box .pro-status {
	line-height: 40px;
	margin-left: 30px;
}

.cont_box .pro-status .on {
	background: #52c41a;
}

.cont_box .pro-status .off {
	background: #c41a1a;
}

.cont_box .pro-status span {
	position: relative;
	top: -1px;
	display: inline-block;
	width: 6px;
	height: 6px;
	vertical-align: middle;
	border-radius: 50%;
	margin-right: 5px;
}

.cont_box .pro-option {
	line-height: 40px;
	margin-left: 10px;
	color: #1890ff;
	cursor: pointer;
}

.content-box .pro-box {
	display: flex;
	padding: 10px;
}

.content-box .pro-box .protitle {
	font-size: 18px;
	font-weight: bold;
	line-height: 35px;
}

.content-box .pro-box .buttonedit {
	border: 0px;
	color: #1890ff;
}

table {
	border-collapse: collapse;
	text-indent: initial;
	border-spacing: 2px;
}

tbody {
	box-sizing: border-box;
	display: table-row-group;
	vertical-align: middle;
	border-color: inherit;
}

tr {
	display: table-row;
	vertical-align: inherit;
	border-color: inherit;
}

.ant-descriptions-view {
	width: 100%;
	overflow: hidden;
	border-radius: 4px;
}

.ant-descriptions-view {
	border: 1px solid #e8e8e8;
}

.ant-descriptions-view table {
	width: 100%;
	table-layout: fixed;
}

.ant-descriptions-view>table {
	table-layout: auto;
}

.ant-descriptions-row {
	border-bottom: 1px solid #e8e8e8;
}

.ant-descriptions-item-label {
	color: rgba(0, 0, 0, 0.85);
	font-weight: 400;
	font-size: 14px;
	line-height: 1.5;
}

.ant-descriptions-item-label {
	padding: 16px 24px;
	border-right: 1px solid #e8e8e8;
}

.ant-descriptions-item-label {
	background-color: #fafafa;
}

.ant-descriptions-item-content {
	padding: 16px 24px;
	border-right: 1px solid #e8e8e8;
	display: table-cell;
	color: rgba(0, 0, 0, 0.65);
	font-size: 14px;
	line-height: 1.5;
}

.wu-box {
	border: #e8e8e8 solid 1px;
	padding: 20px;
	width: 100%;
}

.wu-box .wu-title {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	padding: 20px;
	border-bottom: #e8e8e8 1px solid;
}

.wu-box .wu-title .title {
	font-size: 18px;
}</style>


