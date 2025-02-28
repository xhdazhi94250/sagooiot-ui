<template>
	<div class="system-edit-dept-container">
		<el-dialog :title="(ruleForm.deptId ? '修改' : '添加') + '部门'" v-model="isShowDialog" width="769px">
			<el-form ref="formRef" :model="ruleForm" :rules="rules" size="default" label-width="90px">
				<el-row :gutter="35">
					<!-- <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
						<el-form-item label="所属区域" prop="organizationId">
							<el-cascader :options="orgData" :props="{ checkStrictly: true, emitPath: false, value: 'id', label: 'name' }" placeholder="请选择区域" clearable class="w100" v-model="ruleForm.organizationId">
								<template #default="{ node, data }">
									<span>{{ data.name }}</span>
									<span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
								</template>
							</el-cascader>
						</el-form-item>
					</el-col> -->
					<el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
						<el-form-item label="上级部门">
							<el-cascader :options="deptData" :props="{ checkStrictly: true, emitPath: false, value: 'deptId', label: 'deptName' }" placeholder="请选择部门" clearable class="w100" v-model="ruleForm.parentId">
								<template #default="{ node, data }">
									<span>{{ data.deptName }}</span>
									<span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
								</template>
							</el-cascader>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="部门名称" prop="deptName">
							<el-input v-model="ruleForm.deptName" placeholder="请输入部门名称" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="负责人" prop="leader">
							<el-input v-model="ruleForm.leader" placeholder="请输入负责人" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="手机号" prop="phone">
							<el-input v-model="ruleForm.phone" placeholder="请输入手机号" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="邮箱" prop="email">
							<el-input v-model="ruleForm.email" placeholder="请输入" clearable></el-input>
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="排序">
							<el-input-number v-model="ruleForm.orderNum" :min="0" :max="999" controls-position="right" placeholder="请输入排序" class="w100" />
						</el-form-item>
					</el-col>
					<el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
						<el-form-item label="部门状态">
							<el-switch v-model="ruleForm.status" :active-value="1" :inactive-value="0" inline-prompt active-text="启" inactive-text="禁"></el-switch>
						</el-form-item>
					</el-col>
				</el-row>
			</el-form>
			<template #footer>
				<span class="dialog-footer">
					<el-button @click="onCancel" size="default">取 消</el-button>
					<el-button type="primary" @click="onSubmit" size="default">{{ ruleForm.deptId ? '修 改' : '添 加' }}</el-button>
				</span>
			</template>
		</el-dialog>
	</div>
</template>

<script lang="ts">
import { reactive, toRefs, defineComponent, ref, unref } from 'vue';
import api from '/@/api/system';
import { phoneValidate } from '/@/utils/validator';
import { ElMessage } from 'element-plus';

interface RuleFormState {
	deptId?: number;
	parentId: number;
	organizationId: number | null;
	orderNum: number;
	status: number;
	deptName: string;
	leader: string;
	phone: string;
	email: string;
	children?: RuleFormState[];
}
interface DeptSate {
	isShowDialog: boolean;
	ruleForm: RuleFormState;
	deptData: RuleFormState[];
	orgData: any[];
	rules: object;
}

const baseForm: RuleFormState = {
	parentId: -1, // 上级部门
	deptName: '', // 部门名称
	organizationId: 1, // 组织名称
	orderNum: 0,
	leader: '',
	phone: '',
	email: '',
	status: 1,
};

export default defineComponent({
	name: 'systemEditDept',
	setup(prop, { emit }) {
		const formRef = ref<HTMLElement | null>(null);
		const state = reactive<DeptSate>({
			isShowDialog: false,
			ruleForm: {
				...baseForm,
			},
			deptData: [], // 部门数据
			orgData: [], // 组织数据
			rules: {
				organizationId: [{ required: true, message: '组织不能为空', trigger: 'blur' }],
				deptName: [{ required: true, message: '部门名称不能为空', trigger: 'blur' }],
				leader: [{ required: true, message: '负责人不能为空', trigger: 'blur' }],
				phone: [{ validator: phoneValidate, trigger: 'blur' }],
				email: [{ type: 'email', message: '请输入正确邮箱', trigger: 'blur' }],
			},
		});

		// 打开弹窗
		const openDialog = (row?: RuleFormState | number) => {
			resetForm();
			api.dept.getList({ status: 1 }).then((res: any) => {
				state.deptData = res || [];
			});

			api.org.getList({ status: 1 }).then((res: any) => {
				state.orgData = res || [];
			});
			if (row && typeof row === 'object') {
				state.ruleForm = row;
			} else if (row && typeof row === 'number') {
				state.ruleForm.parentId = row;
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
			console.log(state.ruleForm.parentId);
			const formWrap = unref(formRef) as any;
			if (!formWrap) return;
			formWrap.validate((valid: boolean) => {
				if (valid) {
					console.log(state.ruleForm);
					console.log(state.ruleForm.parentId);
					if (!state.ruleForm.parentId) {
						state.ruleForm.parentId = -1;
					}
					console.log(state.ruleForm.parentId);
					if (!state.ruleForm.deptId) {
						//添加
						api.dept.add(state.ruleForm).then(() => {
							ElMessage.success('添加成功');
							closeDialog(); // 关闭弹窗
							emit('deptList');
						});
					} else {
						//修改
						api.dept.edit(state.ruleForm).then(() => {
							ElMessage.success('修改成功');
							closeDialog(); // 关闭弹窗
							emit('deptList');
						});
					}
				}
			});
		};
		const resetForm = () => {
			state.ruleForm = {
				...baseForm,
			};
		};
		return {
			openDialog,
			closeDialog,
			onCancel,
			onSubmit,
			formRef,
			...toRefs(state),
		};
	},
});
</script>
