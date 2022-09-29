<template>
  <InputGroup :className="className">
    <input v-model="keyword" :placeholder="placeholder" class="group_prepend group_input" :class="{ 'group_input-block': isBlock }" type="text" />

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

    // hook

    // methods
    searchClick(): void {
      this.$emit('searchClick', this.keyword);
    }
  }
</script>