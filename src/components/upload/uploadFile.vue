<template>
	<el-upload
		:accept="accept"
		:show-file-list="false"
		v-model:file-list="fileList"
		:limit="1"
		:headers="headers"
		:before-upload="beforeAvatarUpload"
		:action="uploadUrl"
		:on-success="updateImg"
	>
		<el-button>
			<el-icon> <ele-Upload /> </el-icon>
			数据导入
		</el-button>
	</el-upload>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { ElMessage } from 'element-plus';
import type { UploadProps } from 'element-plus';
import getOrigin from '/@/utils/origin';

const headers = {
	Authorization: 'Bearer ' + localStorage.token,
};

const emit = defineEmits(['update']);

const props = defineProps({
	accept: {
		type: String,
		default: '',
	},
	url: {
		type: String,
		default: '/common/singleFile',
	},
	// https://zhgy.sagoo.cn/api/v1/common/singleFile
});

const fileList = ref<any[]>([
	// {
	// 	name: '',
	// 	url: '',
	// },
]);

const uploadUrl: string = getOrigin(import.meta.env.VITE_API_URL + props.url);

const updateImg = (res: any) => {
	console.log(res)
	if (res.code === 0) {
		ElMessage.success('文件上传成功');
	} else {
		ElMessage.error(res.message);
	}
	fileList.value = []
	emit('update', res.data.path)
};

const beforeAvatarUpload: UploadProps['beforeUpload'] = (rawFile) => {
	if (rawFile.size / 1024 / 1024 > 2) {
		ElMessage.error('文件不能超过2MB!');
		return false;
	}
	return true;
};
</script>
