<template>
  <InputGroup :className="className">
    <input v-model="keyword" :placeholder="placeholder" class="group_prepend group_input" :class="{ 'group_input-block': isBlock, 'error':inputError }" type="text" />

    <template #input-group-append>
      <button class="btn group_append" @click="searchClick">
        <font-awesome-icon class="text-white" icon="magnifying-glass" />
      </button>
    </template>
  </InputGroup>
</template>

<script lang="ts">
  import { Component, Vue, Prop } from 'vue-property-decorator';

  import InputGroup from '@/components/InputGroup.vue';

  @Component({
    components: {
      InputGroup,
    },
  })
  export default class SearchBar extends Vue {
    // props
    @Prop({ default: '' }) className?: string;
    @Prop({ default: '' }) placeholder?: string;
    @Prop({ default: true }) isBlock!: boolean;

    // data
    keyword = '';
    inputError = false;

    // hook

    // methods
    searchClick(): void {
      if(!this.keyword || /[0-9]+/g.test(this.keyword)){
        this.inputError = true;
        return;
      }

      this.inputError = false;

      this.$emit('searchClick', this.keyword);
    }
  }
</script>