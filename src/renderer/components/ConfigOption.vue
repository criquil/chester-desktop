<template>
  <div>
    <Select filterable v-if="options && type === 'select'" v-model="override" @on-change="setOverride" placeholder="">
      <Option v-for="option in options" :value="option" :key="option">{{ option }}</Option>
    </Select>

    <RadioGroup v-if="options && type === 'radio'" v-model="override" type="button" @on-change="setOverride">
      <Radio v-for="option in options" :label="option" :value="option" :key="option"></Radio>
    </RadioGroup>

    <template v-if="!options">
      <Button type="ghost" v-if="!inInputMode" @click="showInput">{{override || configs[config]}}</Button>

      <template v-if="inInputMode">
        <Input type="text" v-model="override" @on-enter="closeInput" @on-change="setOverride" placeholder="">
          <div slot="append">
            <Button icon="checkmark" @click="closeInput"></Button>
            <Button icon="close" @click="cancelInput"></Button>
          </div>
        </Input>
      </template>
    </template>
  </div>
</template>

<script>
  import { mapState } from 'vuex';

  export default {
    name: 'ConfigOption',
    components: { },

    data () {
      return {
        error: null,
        inInputMode: false,
        override: null
      }
    },

    computed: {
      ...mapState({
        configs: state => state.Project.configs,
        overrides: state => state.Project.overrides,
      })
    },

    props: {
      config: String,
      type: String,
      options: Array
    },

    created () {
      this.override = this.configs[this.config];
    },
    watch: {
      'configs': 'resetOverride'
    },
    methods: {
      showInput () {
        this.inInputMode = true;
      },
      cancelInput () {
        this.inInputMode = false;
        this.resetOverride();
      },
      closeInput () {
        this.inInputMode = false;
      },
      setOverride () {
        this.$store.commit('setOverride', {
          [this.config]: this.override
        });
      },
      resetOverride () {
        this.override = this.configs[this.config];
        this.$store.commit('removeOverride', this.config);
      }
    }
  }
</script>

<style>
</style>
