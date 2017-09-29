<template>
  <div class="ui fluid search selection dropdown" :class="{ 'active visible': showMenu }" @click="openMenu">
    <i class="dropdown icon"></i>
    <input
      class="search"
      autocomplete="off"
      :tabindex="tabIndex"
      v-model="searchText"
      ref="input"

      @blur="blurInput"
      @keydown.up="prevItem"
      @keydown.down="nextItem"
      @keydown.enter.prevent=""
      @keyup.enter.prevent="selectEnterItem"
    />
    <div class="text" :class="{ 'default': isDefault }">{{ inputText }}</div>
    <div class="menu transition" @mousedown.prevent :class="{ 'visible': showMenu }" ref="menu" tabindex="-1">
      <template v-for="(option, i) in filteredOptions">
        <div
          class="item" :class="{ 'current': id === pointer }"
          @click.stop="selectItem(option)"
          @mouseover="markItem(i)"
        >{{ option.label }}</div>
      </template>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'basic-select',
    props: {
      selected: Object,
      options: Array,
      placeholder: String,
      tabIndex: String,
      onChange: Function
    },
    data: function () {
      return {
        pointer: -1,
        showMenu: false,
        searchText: '',
        selectedEntry: this.selected
      }
    },
    computed: {
      isDefault: function () {
        return !this.selectedEntry
      },
      inputText: function () {
        let placeholder = this.placeholder ? this.placeholder : ''
        if (this.searchText !== '') return ''
        return this.selectedEntry ? this.selectedEntry.label : placeholder
      },
      selectedLabel: function () {
        return this.selectedEntry ? this.selectedEntry.label : ''
      },
      filteredOptions: function () {
        if (this.searchText) {
          return this.options.filter(entry => entry.label.indexOf(this.searchText) >= 0)
        } else {
          return this.options
        }
      }
    },
    methods: {
      openMenu: function () {
        this.showMenu = true
        this.$refs.input.focus()
      },
      closeMenu: function () {
        this.showMenu = false
        this.searchText = ''
      },
      blurInput: function () {
        this.closeMenu()
      },
      selectItem: function (option) {
        this.selectedEntry = option
        this.closeMenu()
      },

      lowerIndex: function (index) {
        let newIndex = index - 1
        let maxIndex = this.filteredOptions.length - 1

        if (newIndex < 0) return maxIndex
        if (newIndex > maxIndex) return maxIndex
        return newIndex
      },
      raiseIndex: function (index) {
        let newIndex = index + 1
        let maxIndex = this.filteredOptions.length - 1

        if (index > maxIndex) return maxIndex
        if (newIndex > maxIndex) return 0
        return newIndex
      },

      prevItem: function () {
        if (!this.showMenu) {
          this.showMenu = true
          this.pointer = 0
        }
        this.pointer = this.lowerIndex(this.pointer)
      },
      nextItem: function () {
        if (!this.showMenu) {
          this.showMenu = true
          this.pointer = 0
        }
        this.pointer = this.raiseIndex(this.pointer)
      },
      selectEnterItem: function () {
        let maxIndex = this.filteredOptions.length - 1
        if (this.pointer > maxIndex || this.pointer < 0) {
          this.closeMenu()
        } else {
          this.selectedEntry = this.filteredOptions[this.pointer]
          this.closeMenu()
        }
      }
    },
    mounted: function () {
      this.selectedEntry = this.selected
    }
  }
</script>

<style>
  .ui.search.dropdown > input.search {
    z-index: 15;
  }
  .item.current {
    background: rgba(0, 0, 0, 0.05);
    color: rgba(0, 0, 0, 0.95);
  }
</style>
