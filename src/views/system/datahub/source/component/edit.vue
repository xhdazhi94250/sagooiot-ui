<template>
	<div class="system-edit-dic-container">
		<el-dialog :title="(ruleForm.sourceId !== 0 ? '修改' : '添加') + '数据源'" v-model="isShowDialog" width="769px">
			<el-form :model="ruleForm" ref="formRef" :rules="rules" size="default" label-width="110px">
				<el-form-item label="数据源标识" prop="key">
					<el-input v-model="ruleForm.key" placeholder="请输入数据源名称" :disabled="ruleForm.sourceId" />
				</el-form-item>
				<el-form-item label="数据源名称" prop="name">
					<el-input v-model="ruleForm.name" placeholder="请输入数据源名称" />
				</el-form-item>

				<el-form-item label="描述" prop="desc">
					<el-input v-model="ruleForm.desc" type="textarea" placeholder="请输入内容"></el-input>
				</el-form-item>

				<el-form-item label="数据来源" prop="from">
					<el-radio-group v-model="ruleForm.from">
						<el-radio :label="1">api导入</el-radio>
						<!-- <el-radio :label="2">数据库</el-radio>
						<el-radio :label="3">文件</el-radio> -->
						<el-radio :label="4">设备</el-radio>
						<el-radio :label="2">数据库</el-radio>
					</el-radio-group>
				</el-form-item>

				<!-- <el-divider content-position="left">规则表达式</el-divider>

				<div v-for="(item, index) in rule" :key="index">
					<el-form-item label="正则表达式">
						<el-input v-model="item.expression" placeholder="请输入正则表达式" />
					</el-form-item>

					<el-form-item label="替换内容">
						<el-input v-model="item.replace" placeholder="请输入替换内容" class="w-35" />
		
						<div class="conicon">
							<el-icon @click="delRule(index)" v-if="index > 0"><Delete /></el-icon>
						</div>
					</el-form-item>
				</div>
				<div style="padding: 10px">
					<el-button type="primary" class="addbutton" @click="addRule">增加</el-button>
				</div> -->
				<el-divider content-position="left">数据源配置</el-divider>

				<div v-if="ruleForm.from == 1">
					<el-form-item label="请求方法">
						<el-select v-model="config.method" placeholder="请选择请求方法">
							<el-option v-for="item in methodData" :key="item.value" :label="item.label" :value="item.value" />
						</el-select>
					</el-form-item>

					<el-form-item label="请求地址">
						<el-input v-model="config.url" placeholder="请输入请求地址" />
					</el-form-item>

					<el-form-item label="定时请求">
						<div style="display:flex">
							<el-input v-model="config.cronExpression" placeholder="请输入cron表达式" />
							<el-button type="success"  @click="showCron('config')" style="    margin-left: 5px;">设置</el-button>

						</div>
						<!-- <ul style="list-style: none">
							<li>
								<el-icon><ele-WarningFilled /></el-icon> */5 * * * * ? : 每隔5秒执行一次
							</li>
							<li>
								<el-icon><ele-WarningFilled /></el-icon> 20 */1 * * * ? : 每隔1分钟执行一次
							</li>
							<li>
								<el-icon><ele-WarningFilled /></el-icon> 30 0 23 * * ? : 每天23点执行一次
							</li>
							<li>
								<el-icon><ele-WarningFilled /></el-icon> 0 0 1 * * ? : 每天凌晨1点执行一次
							</li>
							<li>
								<el-icon><ele-WarningFilled /></el-icon> 0 0 1 1 * ? : 每月1号凌晨1点执行一次
							</li>
						</ul> -->
					</el-form-item>

					<!-- <el-form-item label="更新时间">
							<el-input v-model="config.interval" placeholder="请输入更新时间" class="w-35" />
							<el-select v-model="config.intervalUnit" placeholder="请选择单位">
								<el-option v-for="item in unitData" :key="item.value" :label="item.label" :value="item.value" />
							</el-select>
						</el-form-item> -->

					<div class="box-content">
						<div>
							<div
								v-for="(item, index) in requestParams"
								:key="index"
								style="padding: 10px; border: 1px solid #eee; margin-bottom: 10px; position: relative">
								<div class="conicon" style="width: 100%; text-align: right; position: absolute; right: -8px; top: -8px; color: red">
									<el-icon @click="delParams(index)"><CircleClose /></el-icon>
								</div>

								<div style="display: flex">
									<el-divider content-position="left">请求参数</el-divider>
								</div>
								<div class="content-f" v-for="(aaa, bbb) in item" :key="bbb">
									<el-select v-model="aaa.type" placeholder="参数类型" style="width: 320px">
										<el-option v-for="item in paramData" :key="item.value" :label="item.label" :value="item.value" />
									</el-select>
									<el-input v-model="aaa.name" placeholder="请输入参数标题" style="width: 320px" />
									<el-input v-model="aaa.key" placeholder="请输入参数名" style="width: 320px" />
									<el-input v-model="aaa.value" placeholder="请输入参数值" style="width: 320px" />
									<div class="conicon">
										<el-icon @click="delParamss(index, bbb)"><Delete /></el-icon>
									</div>
								</div>
								<el-button type="primary" class="addbutton" @click="addParams(index)">增加</el-button>
								<div style=""></div>
								<!-- <el-button type="danger" class="addbutton" @click="delParams(index)">删除分组</el-button> -->

								<!-- <div class="conicon" style="  width: 100%; text-align: right;">
									<el-icon @click="delParams(index)" style="border: 2px solid rgb(96 98 102);;border-radius: 5px;   "><Minus /></el-icon>
								</div> -->
							</div>
						</div>
					</div>
					<el-button type="success" class="addbutton" @click="addParamss">增加分组</el-button>
				</div>

				<div v-if="ruleForm.from == 4">
					<el-form-item label="选择设备">
						<el-select v-model="devconfig.deviceKey" filterable placeholder="请选择设备" @change="setNode">
							<el-option v-for="item in sourceData" :key="item.id" :label="item.key" :value="item.id">
								<span style="float: left">{{ item.name }}</span>
								<span style="float: right; font-size: 13px">{{ item.key }}</span>
							</el-option>
						</el-select>
					</el-form-item>
			
				</div>

				<div v-if="ruleForm.from == 2">

					<el-form-item label="数据来源" prop="type">
					<el-radio-group v-model="tabconfig.type" >
						<el-radio label="mysql">mysql</el-radio>
						<el-radio label="mssql">mssql</el-radio>
					
					</el-radio-group>
				</el-form-item>

					<div class="inline">
						<el-form-item label="主机地址" >
							<el-input v-model="tabconfig.host" placeholder="请输入主机地址"   />
						</el-form-item>

						<el-form-item label="端口号">
							<el-input v-model="tabconfig.port" placeholder="请输入端口号" />
						</el-form-item>
					</div>
					<div class="inline">
						<el-form-item label="用户名">
							<el-input v-model="tabconfig.user" placeholder="请输入用户名" />
						</el-form-item>

						<el-form-item label="密码">
							<el-input v-model="tabconfig.passwd" placeholder="请输入密码" />
						</el-form-item>
					</div>
					
						<el-form-item label="数据库名称">
							<el-input v-model="tabconfig.dbName" placeholder="请输入数据库名称" />
						</el-form-item>

						<el-form-item label="执行方式" prop="queryType">
							<el-radio-group v-model="tabconfig.queryType" >
								<el-radio label="tableName">数据表</el-radio>
								<el-radio label="sql">Sql</el-radio>
							
							</el-radio-group>
						</el-form-item>
						
						<el-form-item label="">
							<el-input
								v-model="tabconfig.tableName"
								type="textarea"
								:placeholder="tabconfig.queryType=='sql'?'请输入sql语句':'请输入表名称'"
							/>
						</el-form-item>

						<!-- <el-form-item label="表名称">
							<el-input v-model="tabconfig.tableName" placeholder="请输入表名称" />
						</el-form-item> -->

						<el-form-item label="主键字段">
							<el-input v-model="tabconfig.pk" placeholder="请输入主键字段" />
						</el-form-item>

						<el-form-item label="每次获取数量">
							<el-input v-model="tabconfig.num" placeholder="请输入每次获取数量" />
						</el-form-item>

						<el-form-item label="任务表达式">

						<div style="display:flex">
								<el-input v-model="tabconfig.cronExpression" placeholder="请输入cron任务表达式" />
							    <el-button type="success"  @click="showCron('tabconfig')" style="    margin-left: 5px;">设置</el-button>

						</div>
							
						</el-form-item>
				</div>
			</el-form>
			<template #footer>
				<span class="dialog-footer">
					<el-button @click="onTest" type="warning" size="default" v-if="ruleForm.sourceId > 0">测试</el-button>
					<el-button @click="onCancel" size="default">取 消</el-button>
					<el-button type="primary" @click="onSubmit" size="default">{{ ruleForm.sourceId !== 0 ? '修 改' : '添 加' }}</el-button>
				</span>
			</template>
		</el-dialog>

		<el-dialog v-model="dialogVisible" title="返回Json数据" width="30%">
			<JsonViewer :value="jsonData" boxed sort theme="jv-dark" @click="onKeyclick" />

			<template #footer>
				<span class="dialog-footer">
					<el-button @click="dialogVisible = false">关闭</el-button>
				</span>
			</template>
		</el-dialog>

		<el-dialog v-model="cronShow" title="选择Cron规则" width="60%">
			<vue3cron @handlelisten="handlelisten" :type="crontype" @close="cronclose"></vue3cron>
		</el-dialog>
	</div>
</template>

<script lang="ts">
import { reactive, toRefs, defineComponent, ref, unref } from 'vue';
import api from '/@/api/datahub';
import 'vue3-json-viewer/dist/index.css';
import vue3cron from '/@/components/vue3cron/vue3cron.vue';

import { ElMessage } from 'element-plus';
import { Delete, Plus, CircleClose, Minus, Right } from '@element-plus/icons-vue';

interface RuleFormState {
	sourceId: number;
	name: string;
	from: number;
	key: string;
	userName: string;
	password: string;
	desc: string;
	status: number;
}
interface DicState {
	isShowDialog: boolean;
	ruleForm: RuleFormState;
	rules: {};
}

export default defineComponent({
	name: 'Edit',
	components: { Delete, Plus, CircleClose, Minus, Right,vue3cron },

	setup(prop, { emit }) {
		const myRef = ref<HTMLElement | null>(null);
		const formRef = ref<HTMLElement | null>(null);
		const state = reactive<DicState>({
			isShowDialog: false,
			dialogVisible: false,
			cronShow:false,
			crontype:'',
			config: {},
			devconfig: {},
			tabconfig: {},
			sourceData: [],
			sourceId: 0,
			jsonData: '',
			ruledata: [
				{
					expression: '',
					replace: '',
					//params: {},
				},
			],
			rule: [
				{
					expression: '',
					replace: '',
					// params: {
					// 	name: '',
					// 	value: '',
					// },
				},
			],
			requestParams: [
				[
					{
						type: '',
						key: '',
						name: '',
						value: '',
					},
				],
			],
			methodData: [
				{
					label: 'GET',
					value: 'get',
				},
				{
					label: 'POST',
					value: 'post',
				},
				// {
				// 	label: 'PUT',
				// 	value: 'put',
				// },
			],

			unitData: [
				{
					label: '秒',
					value: 'second',
				},
				{
					label: '分',
					value: 'minute',
				},
				{
					label: '时',
					value: 'hour',
				},
				{
					label: '天',
					value: 'day',
				},
			],

			paramData: [
				{
					lable: 'header',
					value: 'header',
				},
				{
					lable: 'body',
					value: 'body',
				},
				{
					lable: 'param',
					value: 'param',
				},
			],

			ruleForm: {
				sourceId: 0,
				name: '',
				from: 1,
				key: '',
				rule: [],
				config: {
					method: '',
					url: '',
					interval: '',
					intervalUnit: '',
					requestParams: [],
				},
				desc: '',
			},
			rules: {
				key: [{ required: true, message: '数据源标识不能为空', trigger: 'blur' }],
				name: [{ required: true, message: '数据源名称不能为空', trigger: 'blur' }],
				from: [{ required: true, message: '数据源类型不能为空', trigger: 'blur' }],
			},
		});
		const delParams = (index) => {
			state.requestParams.splice(index, 1);
		};

		const delParamss = (index, bbb) => {
			state.requestParams[index].splice(bbb, 1);
		};

		const addParamss = () => {
			state.requestParams.push([
				{
					type: '',
					key: '',
					name: '',
					value: '',
				},
			]);

			console.log(state.requestParams);
		};
		const addParams = (index) => {
			state.requestParams[index].push({
				type: '',
				key: '',
				name: '',
				value: '',
			});
		};

		const delRule = (index) => {
			state.rule.splice(index, 1);
		};

		const addRule = () => {
			state.rule.push({
				expression: '',
				replace: '',
				// params: {
				// 	name: '',
				// 	value: '',
				// },
			});
		};
		// 打开弹窗
		const openDialog = (row: RuleFormState | null) => {
			resetForm();
			getDevData();

			console.log(state.requestParams);
			if (row) {
				state.sourceId = row.sourceId;
				api.common.detail(row.sourceId).then((res: any) => {
					state.ruleForm = res.data;
					if (res.data.from == 1) {
						state.config = res.data.apiConfig;
						state.requestParams = res.data.apiConfig.requestParams;
					} else if (res.data.from == 4) {
						state.devconfig = res.data.deviceConfig;
					} else if (res.data.from == 2) {
						state.tabconfig = res.data.dbConfig;
					}
					res.data.sourceRule.forEach((item, index) => {
						state.rule[index].expression = item.expression;
						state.rule[index].replace = item.replace;
						// state.rule[index].params.name = Object.keys(item.params);
						// state.rule[index].params.value = item.params[Object.keys(item.params)];
					});
				});
			}
			state.isShowDialog = true;
		};
		const onKeyclick = (e) => {
			if (e.target.innerText && e.target.className == 'jv-key') {
				let str = e.target.innerText;
				str = str.substr(0, str.length - 1);
				console.log(str);
			}
		};
		const resetForm = () => {
			state.devconfig = {};
			state.tabconfig = {};
			state.ruleForm = {
				sourceId: 0,
				name: '',
				from: 1,
				key: '',
				rule: [],
				config: {
					method: '',
					url: '',
					interval: '',
					intervalUnit: '',
					requestParams: [],
				},
				desc: '',
			};
		};
		// 关闭弹窗
		const closeDialog = () => {
			state.isShowDialog = false;
		};
		// 取消
		const onCancel = () => {
			closeDialog();
		};

		const getDevData = () => {
			api.common.getdevList({}).then((res: any) => {
				state.sourceData = res.device;
			});
		};

		const setNode = (event) => {
			state.sourceData.forEach((item, index) => {
				if (item.id == event) {
					state.devconfig.productKey = item.product.key;
					state.devconfig.deviceKey = item.key;
				}
			});
		};

		const onTest = () => {
			if (state.ruleForm.from == 1) {
				api.common.api(state.sourceId).then((res: any) => {
					state.jsonData = JSON.parse(res.data);
					state.dialogVisible = true;
					console.log(res);
				});
			} else if (state.ruleForm.from == 4) {
				api.common.devapi(state.sourceId).then((res: any) => {
					state.jsonData = JSON.parse(res.data);
					state.dialogVisible = true;
					console.log(res);
				});
			}
		};
		// 新增
		const onSubmit = () => {
			const formWrap = unref(formRef) as any;
			console.log(formWrap);
			if (!formWrap) return;
			formWrap.validate((valid: boolean) => {
				
				if (valid) {
					//修改rule数据
					// state.rule.forEach((item, index) => {
					// 	item.params[item.params.name] = item.params.value;
					// 	delete item.params.name;
					// 	delete item.params.value;
					// });

					state.ruleForm.rule = state.rule;
					if (state.ruleForm.from == 1) {
						state.config.requestParams = state.requestParams;
						state.ruleForm.config = state.config;
					} else if (state.ruleForm.from == 4) {
						state.ruleForm.config = state.devconfig;
					}else if (state.ruleForm.from == 2) {
						state.ruleForm.config = state.tabconfig;
					}

					
					if (state.ruleForm.sourceId !== 0) {
						//修改

						if (state.ruleForm.from == 1) {
							api.common.edit(state.ruleForm).then(() => {
								ElMessage.success('数据源类型修改成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						} else if (state.ruleForm.from == 4) {
							api.common.devedit(state.ruleForm).then(() => {
								ElMessage.success('数据源类型修改成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						}else if (state.ruleForm.from == 2) {
							api.common.dbedit(state.ruleForm).then(() => {
								ElMessage.success('数据源类型修改成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						}
					} else {
						//添加
						if (state.ruleForm.from == 1) {
							api.common.add(state.ruleForm).then(() => {
								ElMessage.success('数据源类型添加成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						} else if (state.ruleForm.from == 4) {
							api.common.devadd(state.ruleForm).then(() => {
								ElMessage.success('数据源类型添加成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						}else if (state.ruleForm.from == 2) {
							api.common.dbadd(state.ruleForm).then(() => {
								ElMessage.success('数据源类型添加成功');
								closeDialog(); // 关闭弹窗
								emit('typeList');
							});
						}
					}
				}
			});
		};
		
		const handlelisten = (e) => {
			console.log(e);
			if(e.type=='config'){
				state.config.cronExpression=e.cron
			}else if(e.type=='tabconfig'){
				state.tabconfig.cronExpression=e.cron
			}
		};
		const showCron=(type)=>{
				state.crontype=type
				state.cronShow=true;

		};
		const cronclose=()=>{
			state.cronShow=false;
		}

		return {
			cronclose,
			showCron,
			onTest,
			addRule,
			delRule,
			handlelisten,
			addParams,
			addParamss,
			delParamss,
			delParams,
			onKeyclick,
			openDialog,
			setNode,
			closeDialog,
			getDevData,
			onCancel,
			onSubmit,
			formRef,
			myRef,
			...toRefs(state),
		};
	},
});
</script>
<style>
.inline{
	display: inline-flex;
}
.el-input__wrapper {
	width: 98%;
}

.box-content {
	border: 1px solid #e8e8e8;
	margin: 10px;
	padding: 10px;
}

.content-f {
	display: flex;
	margin-bottom: 10px;
}
.content-f .el-input__wrapper {
	margin-right: 5px;
}
.addbutton {
	width: 100%;
	margin-top: 10px;
	background: #fff;
	border: 1px solid #d1d1d1;
	color: #8d8b8b;
}
.conicon {
	width: 55px;
	height: 25px;

	font-size: 28px;
	line-height: 28px;
	cursor: pointer;
}
.jv-node {
	margin-left: 25px;
}
</style>
