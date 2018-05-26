<template>
  <div :dir="dir" class="dropdown v-select" :class="dropdownClasses">
    <div ref="toggle" @mousedown.prevent="toggleDropdown" :class="['dropdown-toggle', 'clearfix']">

      <slot v-for="option in valueAsArray" name="selected-option-container" :option="(typeof option === 'object')?option:{[label]: option}"
        :deselect="deselect" :multiple="multiple" :disabled="disabled">
        <span class="selected-tag" v-bind:key="option.index">
          <slot name="selected-option" v-bind="(typeof option === 'object')?option:{[label]: option}">
            {{ getOptionLabel(option) }}
          </slot>
          <button v-if="multiple" :disabled="disabled" @click="deselect(option)" type="button" class="close" aria-label="Remove option">
            <span aria-hidden="true">&times;</span>
          </button>
        </span>
      </slot>

      <input ref="search" v-model="search" @keydown.delete="maybeDeleteValue" @keyup.esc="onEscape" @keydown.up.prevent="typeAheadUp"
        @keydown.down.prevent="typeAheadDown" @keydown.enter.prevent="typeAheadSelect" @blur="onSearchBlur" @focus="onSearchFocus"
        type="search" class="form-control" autocomplete="off" :disabled="disabled" :placeholder="searchPlaceholder" :tabindex="tabindex"
        :readonly="!searchable" :style="{ width: isValueEmpty ? '100%' : 'auto' }" :id="inputId" aria-label="Search for option">

      <button v-show="showClearButton" :disabled="disabled" @click="clearSelection" type="button" class="clear" title="Clear selection">
        <span aria-hidden="true">&times;</span>
      </button>

      <i v-if="!noDrop" ref="openIndicator" role="presentation" class="open-indicator"></i>

      <slot name="spinner">
        <div class="spinner" v-show="mutableLoading">Loading...</div>
      </slot>
    </div>

    <transition :name="transition">
      <ul ref="dropdownMenu" v-if="dropdownOpen" class="dropdown-menu" :style="{ 'max-height': maxHeight }">
        <li v-for="(option, index) in filteredOptions" v-bind:key="index" :class="{ active: isOptionSelected(option), highlight: index === typeAheadPointer }"
          @mouseover="typeAheadPointer = index">
          <a @mousedown.prevent="select(option)">
            <slot name="option" v-bind="(typeof option === 'object')?option:{[label]: option}">
              {{ getOptionLabel(option) }}
            </slot>
          </a>
        </li>
        <li v-if="!filteredOptions.length" class="no-options">
          <slot name="no-options">Sorry, no matching options.</slot>
        </li>
      </ul>
    </transition>
  </div>
</template>
<style src="./Select.css"></style>
<script type="text/babel" src="./SelectController.js"></script>