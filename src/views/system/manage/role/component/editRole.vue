<template>
	<div class="system-edit-role-container">
		<el-dialog :title="(formData.id === 0 ? '添加' : '修改') + '角色'" v-model="isShowDialog" width="769px">
			<el-form ref="formRef" :model="formData" :rules="rules" size="default" label-width="90px">
				<el-row :gutter="35">
					<el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
						<el-form-item label="上级角色">
							<el-cascader :options="list" :props="{ label: 'name', value: 'id', checkStrictly: true, emitPath: false }" placeholder="请选择上级角色" clearable class="w100" v-model="formData.parentId">
								<template #default="{ node, data }">
									<span>{{ data.name }}</span>
									<span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
								</template>
							</el-cascader>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="角色名称" prop="name">
							<el-input v-model="formData.name" placeholder="请输入角色名称" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="排序">
							<el-input-number v-model="formData.listOrder" :min="0" controls-position="right" placeholder="请输入排序" class="w100" />
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="角色状态">
							<el-switch v-model="formData.status" :active-value="1" :inactive-value="0" inline-prompt active-text="启" inactive-text="禁"></el-switch>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
						<el-form-item label="角色描述">
							<el-input v-model="formData.remark" type="textarea" placeholder="请输入角色描述" maxlength="150"></el-input>
						</el-form-item>
					</el-col>
					<!-- <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
            <el-form-item label="菜单权限">
              <el-row :gutter="35">
                <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
                  <el-checkbox v-model="menuExpand" @change="handleCheckedTreeExpand($event)">展开/折叠</el-checkbox>
                  <el-checkbox v-model="menuNodeAll" @change="handleCheckedTreeNodeAll($event)">全选/全不选</el-checkbox>
                  <el-checkbox v-model="menuCheckStrictly" @change="handleCheckedTreeConnect($event)">父子联动</el-checkbox>
                </el-col>
                <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
                  <el-tree :data="menuData" ref="menuRef" :props="menuProps" :default-checked-keys="formData.menuIds" node-key="id" show-checkbox class="menu-data-tree tree-border" :check-strictly="!menuCheckStrictly" />
                </el-col>
              </el-row>
            </el-form-item>
          </el-col> -->
				</el-row>
			</el-form>
			<template #footer>
				<span class="dialog-footer">
					<el-button @click="onCancel" size="default">取 消</el-button>
					<el-button type="primary" @click="onSubmit" size="default" :loading="loading">{{ formData.id === 0 ? '新 增' : '修 改' }}</el-button>
				</span>
			</template>
		</el-dialog>
	</div>
</template>

<script lang="ts">
import { reactive, toRefs, defineComponent, ref, unref } from 'vue';
import api from '/@/api/system';
import { ElMessage } from 'element-plus';
import { getBackEndControlRoutes } from '/@/router/backEnd';

// 定义接口来定义对象的类型
interface MenuDataTree {
	id: number;
	pid: number;
	title: string;
	children?: MenuDataTree[];
}
interface DialogRow {
	id: number;
	parentId: number;
	name: string;
	status: number;
	listOrder: number;
	remark: string;
	// menuIds: Array<number>;
}
interface RoleState {
	loading: boolean;
	isShowDialog: boolean;
	formData: DialogRow;
	menuData: Array<MenuDataTree>;
	menuExpand: boolean;
	menuNodeAll: boolean;
	menuCheckStrictly: boolean;
	menuProps: {
		children: string;
		label: string;
	};
	rules: object;
}

const baseForm = {
	id: 0,
	parentId: -1,
	name: '',
	status: 1,
	listOrder: 0,
	remark: '',
	// menuIds: [],
};

export default defineComponent({
	name: 'systemEditRole',
	props: {
		list: Array,
	},
	setup(props, { emit }) {
		const formRef = ref<HTMLElement | null>(null);
		const menuRef = ref();
		const state = reactive<RoleState>({
			loading: false,
			isShowDialog: false,
			formData: {
				...baseForm,
			},
			// 表单校验
			rules: {
				name: [{ required: true, message: '角色名称不能为空', trigger: 'blur' }],
			},
			menuData: [],
			menuExpand: false,
			menuNodeAll: false,
			menuCheckStrictly: false,
			menuProps: {
				children: 'children',
				label: 'title',
			},
		});
		// 打开弹窗
		const openDialog = (row?: DialogRow) => {
			resetForm();
			getMenuData();
			if (row) {
				api.role.getRole(row.id).then((res: any) => {
					state.formData = res;
					// if (res.data.role) {
					// 	state.formData = res.data.role;
					// 	state.formData.menuIds = res.data.menuIds ?? [];
					// }
				});
			}
			state.isShowDialog = true;
		};
		// 关闭弹窗
		const closeDialog = () => {
			state.isShowDialog = false;
		};
		// 取消
		const onCancel = () => {
			closeDialog();
		};
		// 新增
		const onSubmit = () => {
			const formWrap = unref(formRef) as any;
			if (!formWrap) return;
			formWrap.validate((valid: boolean) => {
				if (valid) {
					state.loading = true;
					if (!state.formData.parentId) {
						state.formData.parentId = -1;
					}
					// state.formData.menuIds = getMenuAllCheckedKeys();
					if (state.formData.id === 0) {
						//添加
						api.role.addRole(state.formData)
							.then(() => {
								ElMessage.success('添加成功');
								closeDialog(); // 关闭弹窗
								resetMenuSession();
								emit('getList');
							})
							.finally(() => {
								state.loading = false;
							});
					} else {
						//修改
						api.role.editRole(state.formData)
							.then(() => {
								ElMessage.success('修改成功');
								closeDialog(); // 关闭弹窗
								resetMenuSession();
								emit('getList');
							})
							.finally(() => {
								state.loading = false;
							});
					}
				}
			});
		};
		// 获取菜单结构数据
		const getMenuData = () => {
			api.menu.getList({ status: -1 }).then((res: any) => {
				state.menuData = res;
			});
		};
		const resetForm = () => {
			state.menuCheckStrictly = false;
			state.menuExpand = false;
			state.menuNodeAll = false;
			state.formData = {
				...baseForm,
			};
		};
		/** 树权限（展开/折叠）*/
		const handleCheckedTreeExpand = (value: any) => {
			let treeList = state.menuData;
			for (let i = 0; i < treeList.length; i++) {
				menuRef.value.store.nodesMap[treeList[i].id].expanded = value;
			}
		};

		/** 树权限（全选/全不选） */
		const handleCheckedTreeNodeAll = (value: any) => {
			menuRef.value.setCheckedNodes(value ? state.menuData : []);
		};

		/** 树权限（父子联动） */
		const handleCheckedTreeConnect = (value: any) => {
			state.menuCheckStrictly = value ? true : false;
		};

		/** 所有菜单节点数据 */
		function getMenuAllCheckedKeys() {
			// 目前被选中的菜单节点
			let checkedKeys = menuRef.value.getCheckedKeys();
			// 半选中的菜单节点
			let halfCheckedKeys = menuRef.value.getHalfCheckedKeys();
			checkedKeys.unshift.apply(checkedKeys, halfCheckedKeys);
			return checkedKeys;
		}

		// 重置菜单session
		const resetMenuSession = () => {
			getBackEndControlRoutes();
		};
		return {
			openDialog,
			closeDialog,
			onCancel,
			onSubmit,
			menuRef,
			formRef,
			handleCheckedTreeExpand,
			getMenuAllCheckedKeys,
			handleCheckedTreeNodeAll,
			handleCheckedTreeConnect,
			resetMenuSession,
			...toRefs(state),
		};
	},
});
</script>

<style scoped lang="scss">
.tree-border {
	margin-top: 5px;
	border: 1px solid #e5e6e7 !important;
	background: #fff none !important;
	border-radius: 4px;
}

.system-edit-role-container {
	.menu-data-tree {
		border: var(--el-input-border, var(--el-border-base));
		border-radius: var(--el-input-border-radius, var(--el-border-radius-base));
		padding: 5px;
	}
}
</style>
