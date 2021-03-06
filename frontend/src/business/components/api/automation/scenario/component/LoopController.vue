<template>

  <api-base-component
    @copy="copyRow"
    @remove="remove"
    :data="controller"
    :draggable="true"
    color="#02A7F0"
    background-color="#F4F4F5"
    :title="$t('api_test.automation.loop_controller')">

    <template v-slot:headerLeft>
      <i class="icon el-icon-arrow-right" :class="{'is-active': controller.active}" @click="active(controller)" style="margin-right: 10px"/>
      <el-radio @change="changeRadio" class="ms-radio" v-model="controller.loopType" label="LOOP_COUNT">{{$t('loop.loops_title')}}</el-radio>
      <el-radio @change="changeRadio" class="ms-radio" v-model="controller.loopType" label="FOREACH">{{$t('loop.foreach')}}</el-radio>
      <el-radio @change="changeRadio" class="ms-radio" v-model="controller.loopType" label="WHILE">{{$t('loop.while')}}</el-radio>
    </template>

    <div v-if="controller.loopType==='LOOP_COUNT'" draggable>
      <el-row>
        <el-col :span="8">
          <span class="ms-span ms-radio">{{$t('loop.loops')}}</span>
          <el-input-number size="small" v-model="controller.countController.loops" :placeholder="$t('commons.millisecond')" :max="1000*10000000" :min="0"/>
          <span class="ms-span ms-radio">次</span>
        </el-col>
        <el-col :span="8">
          <span class="ms-span ms-radio">{{$t('loop.interval')}}</span>
          <el-input-number size="small" v-model="controller.countController.interval" :placeholder="$t('commons.millisecond')" :max="1000*10000000" :min="0"/>
          <span class="ms-span ms-radio">秒</span>
        </el-col>
        <el-col :span="8">
          <span class="ms-span ms-radio">{{$t('loop.proceed')}}</span>
          <el-switch v-model="controller.countController.proceed"/>
        </el-col>
      </el-row>
    </div>

    <div v-else-if="controller.loopType==='FOREACH'" draggable>
      <el-row>
        <el-col :span="8">
          <el-input :placeholder="$t('api_test.request.condition_variable')" v-model="controller.forEachController.inputVal" size="small"/>
        </el-col>
        <el-col :span="1" style="margin-top: 6px">
          <span style="margin:10px 10px 10px">in</span>
        </el-col>
        <el-col :span="8">
          <el-input :placeholder="$t('api_test.request.condition_variable')" v-model="controller.forEachController.returnVal" size="small"/>
        </el-col>
        <el-col :span="7">
          <span class="ms-span ms-radio">{{$t('loop.interval')}}</span>
          <el-input-number size="small" v-model="controller.forEachController.interval" :placeholder="$t('commons.millisecond')" :max="1000*10000000" :min="0"/>
          <span class="ms-span ms-radio">秒</span>
        </el-col>
      </el-row>
    </div>
    <div v-else draggable>
      <el-input size="small" v-model="controller.whileController.variable" style="width: 20%" :placeholder="$t('api_test.request.condition_variable')"/>

      <el-select v-model="controller.whileController.operator" :placeholder="$t('commons.please_select')" size="small"
                 @change="change" style="width: 10%;margin-left: 10px">
        <el-option v-for="o in operators" :key="o.value" :label="$t(o.label)" :value="o.value"/>
      </el-select>
      <el-input size="small" v-model="controller.whileController.value" :placeholder="$t('api_test.value')" v-if="!hasEmptyOperator" style="width: 20%;margin-left: 20px"/>
      <span class="ms-span ms-radio">{{$t('loop.timeout')}}</span>
      <el-input-number size="small" v-model="controller.whileController.timeout" :placeholder="$t('commons.millisecond')" :max="1000*10000000" :min="0"/>
      <span class="ms-span ms-radio">秒</span>
    </div>

  </api-base-component>
</template>

<script>
  import ApiBaseComponent from "../common/ApiBaseComponent";

  export default {
    name: "MsLoopController",
    components: {ApiBaseComponent},
    props: {
      controller: {},
      node: {},
      index: Object,
    },
    data() {
      return {
        loading: false,
        operators: {
          EQ: {
            label: "commons.adv_search.operators.equals",
            value: "=="
          },
          NE: {
            label: "commons.adv_search.operators.not_equals",
            value: "!="
          },
          LIKE: {
            label: "commons.adv_search.operators.like",
            value: "=~"
          },
          NOT_LIKE: {
            label: "commons.adv_search.operators.not_like",
            value: "!~"
          },
          GT: {
            label: "commons.adv_search.operators.gt",
            value: ">"
          },
          LT: {
            label: "commons.adv_search.operators.lt",
            value: "<"
          },
          IS_EMPTY: {
            label: "commons.adv_search.operators.is_empty",
            value: "is empty"
          },
          IS_NOT_EMPTY: {
            label: "commons.adv_search.operators.is_not_empty",
            value: "is not empty"
          }
        }
      }
    },
    methods: {
      remove() {
        this.$emit('remove', this.controller, this.node);
      },
      copyRow() {
        this.$emit('copyRow', this.controller, this.node);
      },
      active(item) {
        item.active = !item.active;
        this.reload();
      },
      changeRadio() {
        this.controller.active = true;
        this.reload();
      },
      change(value) {
        if (value.indexOf("empty") > 0 && !!this.controller.value) {
          this.controller.value = "";
        }
      },
      reload() {
        this.loading = true
        this.$nextTick(() => {
          this.loading = false
        })
      },
    },
    computed: {
      hasEmptyOperator() {
        return !!this.controller.operator && this.controller.operator.indexOf("empty") > 0;
      }
    }
  }
</script>

<style scoped>
  .ms-span {
    margin: 10px;
  }

  .ms-radio {
    color: #606266;
    font-family: "Helvetica Neue", Helvetica, "PingFang SC", "Hiragino Sans GB", Arial, sans-serif;
    font-size: 13px;
    font-weight: normal;
  }

  .icon.is-active {
    transform: rotate(90deg);
  }
</style>
