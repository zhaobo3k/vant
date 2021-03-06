<template>
  <div ref="root">
    <field
      :label="$t('label.address')"
      :placeholder="$t('placeholder.address')"
      maxlength="200"
      type="textarea"
      autosize
      rows="1"
      :value="value"
      :error="isError"
      :onIconClick="onIconClick"
      @input="$emit('input', $event)"
      @focus="handleFocus"
      @blur="handleBlur"
    >
      <div slot="icon">
        <span v-if="showIcon && isAndroid" class="van-address-edit-detail__finish-edit">{{ $t('complete') }}</span>
        <icon v-else-if="showIcon" name="clear"  />
      </div>
    </field>

    <cell-group class="van-address-edit-detail__suggest-list" v-if="showSearchList">
      <cell
        v-for="express in searchResult"
        :key="express.name + express.address"
        class="van-address-edit-detail__suggest-item"
        @click="onSuggestSelect(express)">
        <icon name="location" class="van-address-edit-detail__location" />
        <div class="van-address-edit-detail__item-info">
          <p class="van-address-edit-detail__title">{{ express.name }}</p>
          <p class="van-address-edit-detail__subtitle">{{ express.address }}</p>
        </div>
      </cell>
    </cell-group>
  </div>
</template>

<script>
import { create } from '../utils';
import Field from '../field';
import Cell from '../cell';
import CellGroup from '../cell-group';
import { isAndroid } from '../utils';

export default create({
  name: 'van-address-edit-detail',

  components: {
    Field,
    Cell,
    CellGroup
  },

  props: {
    value: {},
    isError: Boolean,
    searchResult: Array,
    showSearchResult: Boolean
  },

  data() {
    return {
      isAndroid: isAndroid(),
      isFocused: false
    };
  },

  computed: {
    showSearchList() {
      return this.showSearchResult && this.isFocused && this.searchResult.length > 0;
    },

    showIcon() {
      return this.value && this.isFocused;
    }
  },

  methods: {
    handleFocus(e) {
      this.isFocused = true;
      this.$emit('focus', e);
      this.$refs.root.scrollIntoView();
    },

    handleBlur(e) {
      // wait for click event finished
      setTimeout(() => {
        this.isFocused = false;
        this.$emit('blur', e);
      }, 100);
    },

    onIconClick() {
      if (this.isAndroid) {
        this.$refs.root.querySelector('.van-field__control').blur();
      } else {
        this.$emit('input', '');
      }
    },

    onSuggestSelect(express) {
      this.$emit('input', `${express.address || ''} ${express.name || ''}`.trim());
    }
  }
});
</script>
