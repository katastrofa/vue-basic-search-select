<template>
  <div
    class="ui fluid search selection dropdown"
    :class="{ 'active visible': showMenu }"
    tabindex="-1"
    @click="openMenu"
    @focusin="gotFocus"
  >
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
      @keyup.esc.prevent="closeMenuEsc"
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
    name: 'basic-search-select',
    props: {
      selected: Object,
      options: Array,
      placeholder: String,
      tabIndex: [String, Number]
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
      gotFocus: function () {
        this.$refs.input.focus()
      },
      openMenu: function () {
        this.showMenu = true
        this.$refs.input.focus()
      },
      closeMenu: function () {
        this.showMenu = false
        this.searchText = ''
      },
      closeMenuEsc: function (event) {
        if (this.showMenu) {
          event.customPrevent = true
        }
        this.closeMenu()
      },
      blurInput: function () {
        this.closeMenu()
      },
      selectItem: function (option) {
        this.selectedEntry = option
        this.closeMenu()
        this.$emit('select-change', this.selectedEntry)
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
          this.$emit('select-change', this.selectedEntry)
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
    background: rgba(0, 0, 0, 0);
  }
  .stop-hover.current {
    background: rgba(0, 0, 0, 0.05);
    color: rgba(0, 0, 0, 0.95) ;
  }

  .ui.inverted .search.dropdown, .ui.inverted .search.dropdown .text {
    background: #404040;
    color: rgba(255, 255, 255, .9);
  }
  .ui.inverted .selection.active, .ui.inverted .selection.active:hover {
    border-color: rgba(200, 20, 20, .5);
  }
  .ui.inverted .selection.active .menu, .ui.inverted .selection.active:hover .menu, .ui.inverted .selection.active .menu:hover {
    background: #202020;
    border-color: rgba(200, 20, 20, .5);
  }
  .ui.inverted .search.dropdown .menu > .item {
    color: rgba(255, 255, 255, .9);
    border-top: 1px solid rgba(200, 200, 200, .3);
  }
  .ui.inverted .search.dropdown .menu > .item.current {
    background: rgba(220, 220, 220, .1);
    color: rgba(255, 255, 255, .9);
  }

  .ui.inverted div::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, .1);
  }
  .ui.inverted div::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, .25);
  }
</style>






