<template>
  <div class="tdesign-demo-form-status">
    <!--
      1. statusIcon 值为 true，显示默认图标。默认图标有 成功、失败、警告 等，不同的状态图标不同
      2. statusIcon 值为 false，不显示图标
      3. statusIcon 值类型为 function，可以自定义右侧状态图标
      4. statusIcon 为 slot(插槽)，可自定义右侧状态图标
    -->

    <div>
      <t-radio-group v-model="formStatusIcon" variant="default-filled">
        <t-radio-button :value="true">显示校验图标</t-radio-button>
        <t-radio-button :value="false">隐藏校验图标</t-radio-button>
      </t-radio-group>
    </div>
    <br /><br />
    <t-form
      :data="formData"
      :rules="rules"
      :labelWidth="80"
      :statusIcon="formStatusIcon"
      ref="formValidatorStatus"
      @reset="onReset"
      @submit="onSubmit"
    >
      <t-form-item label="失败" name="fail">
        <t-input v-model="formData.fail" placeholder="校验不通过状态"></t-input>
      </t-form-item>
      <t-form-item label="警告" name="warning">
        <t-input v-model="formData.warning" placeholder="校验警告状态"></t-input>
      </t-form-item>
      <t-form-item label="成功" name="success">
        <t-input v-model="formData.success" placeholder="不带绿色边框的成功状态"></t-input>
      </t-form-item>
      <t-form-item label="成功" name="success" successBorder>
        <t-input v-model="formData.success" placeholder="带绿色边框的成功状态"></t-input>
      </t-form-item>
      <t-form-item label="失败" name="failB" :statusIcon="false">
        <t-input v-model="formData.failB" placeholder="隐藏状态图标"></t-input>
      </t-form-item>
      <t-form-item label="警告" name="warningB">
        <t-input v-model="formData.warningB" placeholder="校验警告状态"></t-input>
      </t-form-item>
      <t-form-item label="加载中" name="loading">
        <t-input v-model="formData.loading" placeholder="正在校验中，请稍等"></t-input>
        <template #statusIcon>
          <div style="width: 24px; display: flex; justify-content: center">
            <t-loading slot="statusIcon" size="small"></t-loading>
          </div>
        </template>
      </t-form-item>

      <!-- 自定义图标：可以使用渲染函数 statusIcon，也可以使用插槽 statusIcon -->
      <t-form-item label="帮助" :statusIcon="getStatusIcon" name="help">
        <t-input v-model="formData.help" placeholder="自定义右侧图标"></t-input>
        <!-- <t-icon slot="statusIcon" name='help-circle' size="24px" style="color: #0006"/> -->
      </t-form-item>

      <t-form-item v-for="(item, index) in addlist" :key="item.id" label="新增" :name="item.name">
        <t-input v-model="formData[item.name]"></t-input>
        <t-button
          v-if="item.id === 0 || item.id === lastAddItem - 1"
          @click="addItem"
          slot="statusIcon"
          variant="dashed"
        >
          <icon name="add" size="16px" style="color: #0004" />
        </t-button>
        <t-button v-if="item.id > 0" @click="removeItem(item, index)" slot="statusIcon" variant="dashed">
          <icon name="remove" size="16px" style="color: #0004" />
        </t-button>
      </t-form-item>

      <t-form-item :statusIcon="false" style="margin-left: 80px">
        <t-button theme="primary" type="submit" style="margin-right: 10px">提交</t-button>
        <t-button theme="default" variant="base" type="reset">重置</t-button>
      </t-form-item>
    </t-form>
  </div>
</template>
<script lang="jsx">
import { Icon } from 'tdesign-icons-vue';

const INITIAL_DATA = {
  fail: '',
  warning: '',
  success: '',
  failB: '',
  warningB: '',
  loading: '',
  add: '',
  help: '',
};

export default {
  components: {
    Icon,
  },
  data() {
    return {
      formStatusIcon: true,
      formData: { ...INITIAL_DATA },
      rules: {
        fail: [{ required: true, message: '必填', type: 'error' }],
        warning: [{ required: true, message: '必填', type: 'warning' }],
        success: [{ validator: () => true }],
        failB: [{ required: true, message: '必填', type: 'error' }],
        warningB: [{ required: true, message: '必填', type: 'warning' }],
      },
      addlist: [{ id: 0, name: 'add0' }],
      lastAddItem: 1,
    };
  },
  mounted() {
    this.$refs.formValidatorStatus.validate();
  },
  methods: {
    addItem() {
      const addNum = this.lastAddItem;
      INITIAL_DATA[`add${addNum}`] = '';
      this.addlist.push({ id: addNum, name: `add${addNum}` });
      this.lastAddItem += 1;
    },
    removeItem(item, index) {
      delete INITIAL_DATA[`add${item.id}`];
      this.addlist.splice(index, 1);
    },
    onReset() {
      this.$message.success('重置成功');
    },
    onSubmit({ validateResult, firstError }) {
      if (validateResult === true) {
        this.$message.success('提交成功');
      } else {
        console.log('Errors: ', validateResult);
        this.$message.warning(firstError);
      }
    },
    getStatusIcon() {
      return <Icon name="help-circle" size="16px" style="color: #0006" />;
    },
  },
};
</script>

<style>
.tdesign-demo-form-status .t-input {
  width: 520px;
}
</style>
