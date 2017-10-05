<template>
  <div class="ui fluid search selection dropdown inverted" :class="{ 'active visible': showMenu }" @click="openMenu">
    <i class="dropdown icon"></i>
    <input
      class="search"
      autocomplete="off"
      :tabindex="tabIndex"
      v-model="searchText"
      ref="input"

      @blur="blurInput"
      @keydown.up.prevent="prevItem"
      @keydown.down.prevent="nextItem"
      @keydown.enter.prevent=""
      @keyup.enter.prevent="selectEnterItem"
      @keydown.esc.prevent="closeMenu"
    />
    <div class="text" :class="{ 'default': isDefault }">{{ inputText }}</div>
    <div class="menu transition" @mousedown.prevent :class="{ 'visible': showMenu }" ref="menu" tabindex="-1">
      <template v-for="(option, i) in filteredOptions">
        <div
          class="item" :class="{ 'current': i === pointer, 'stop-hover': stopHover }"
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
        selectedEntry: this.selected,
        stopHover: false
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
          this.showMenu = true
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

      currentScrollBounds: function (element) {
        let topScroll = this.$refs.menu.scrollTop
        let menuHeight = this.$refs.menu.offsetHeight
        let elHeight = element.offsetHeight

        return {
          top: topScroll,
          menuHeight: menuHeight,
          elHeight: elHeight,
          first: Math.ceil(topScroll / elHeight),
          last: Math.floor((topScroll + menuHeight) / elHeight) - 1
        }
      },

      prevItem: function () {
        if (!this.showMenu) {
          this.showMenu = true
          this.pointer = this.pointer === -1 ? this.filteredOptions.length - 1 : this.pointer
        } else {
          this.pointer = this.lowerIndex(this.pointer)
        }
        this.stopHover = true

        let bounds = this.currentScrollBounds(this.$el)
        if (this.pointer < bounds.first || this.pointer > bounds.last) {
          this.$refs.menu.scrollTop = bounds.elHeight * this.pointer
        }
      },
      nextItem: function () {
        if (!this.showMenu) {
          this.showMenu = true
          this.pointer = this.pointer === -1 ? 0 : this.pointer
        } else {
          this.pointer = this.raiseIndex(this.pointer)
        }
        this.stopHover = true

        let bounds = this.currentScrollBounds(this.$el)
        if (this.pointer < bounds.first || this.pointer > bounds.last) {
          let elBottom = bounds.elHeight * (this.pointer + 1)
          this.$refs.menu.scrollTop = elBottom - bounds.menuHeight
        }
      },
      selectEnterItem: function () {
        let maxIndex = this.filteredOptions.length - 1
        if (this.pointer > maxIndex || this.pointer < 0) {
          this.closeMenu()
        } else {
          this.selectedEntry = this.filteredOptions[this.pointer]
          this.closeMenu()
        }
      },
      markItem: function (i) {
        this.pointer = i
        this.stopHover = false
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
  .stop-hover:hover {
    background: rgba(0, 0, 0, 0) !important;
  }
  .stop-hover.current {
    background: rgba(0, 0, 0, 0.05) !important;
    color: rgba(0, 0, 0, 0.95) !important;
  }
</style>
