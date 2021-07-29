<template>
  <div class="autocomplete">
    <input
      type="text"
      @input="onChange"
      @keydown.down="onArrowDown"
      @keydown.up="onArrowUp"
      @keydown.enter="onEnter"
      v-model="search"
    style="width:100%"
    />
    <div class="dropdown">
    <ul
      id="autocomplete-results"
      v-show="isOpen"
      class="autocomplete-results"
    >
      <li
        class="loading"
        v-if="isLoading"
      >
        Loading results...
      </li>
      <li
        v-else
        v-for="(result, i) in results"
        :key="i"
        @click="setResult(result)"
        class="autocomplete-result"
        :class="{ 'is-active': i === arrowCounter }"
      >
        {{ result }}
      </li>
    </ul>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'SearchAutocomplete',
    props: {
      items: {
        type: Array,
        required: false,
        default: () => [],
      },
      isAsync: {
        type: Boolean,
        required: false,
        default: false,
      },
      actualitems: {
        type: Array,
        required: false,
        default: () => [],
      },
      SelectedUserkey : {
        type: String}
    },
   
    data() {
      return {
        isOpen: false,
        results: [],
        search: '',
        isLoading: false,
        arrowCounter: -1,
      };
    },
    watch: {
      items: function (value, oldValue) {
        if (value.length !== oldValue.length) {
          this.results = value;
          this.isLoading = false;
        }
      },
    },
    mounted() {
      document.addEventListener('click', this.handleClickOutside)
    },
    destroyed() {
      document.removeEventListener('click', this.handleClickOutside)
    },
    methods: {
      setResult(result) {
        
        this.search = result;
        this.isOpen = false;
        this.$emit('onChangeInput', this.filterActulaItemResultskey(this.SelectedUserkey));
      
      },
      filterActulaItemResultskey(key) {
      return this.actualitems.filter((item) => {
          return item.[key].toLowerCase().indexOf(this.search.toLowerCase()) > -1;
        });
      },
      filterResults() {
        this.results = this.items.filter((item) => {
          return item.toLowerCase().indexOf(this.search.toLowerCase()) > -1;
        });
      },
      onChange() {
        this.$emit('input', this.search);

        if (this.isAsync) {
          this.isLoading = true;
        } else {
          this.filterResults();
          this.isOpen = true;
        }
      },
      handleClickOutside(event) {
        if (!this.$el.contains(event.target)) {
          this.isOpen = false;
          this.arrowCounter = -1;
        }
      },
      onArrowDown() {
        if (this.arrowCounter < this.results.length) {
          this.arrowCounter = this.arrowCounter + 1;
        }
      },
      onArrowUp() {
        if (this.arrowCounter > 0) {
          this.arrowCounter = this.arrowCounter - 1;
        }
      },
      onEnter() {
        this.search = this.results[this.arrowCounter];
        this.isOpen = false;
        this.arrowCounter = -1;
        this.$emit('onChangeInput', this.search);
      },
    },
  };
</script>

<style>

  .autocomplete {
    position: relative;
  }

  .autocomplete-results {
    /* padding: 0;
    margin: 0;
    border: 1px solid #eeeeee;
    height: 120px;
    overflow: auto; */
     background-color: #F8F8F8;
  position: absolute;
  box-shadow: 0 1px 3px 0px;
  border-radius: 3px;
  border: 1px solid #FAFAFA;
  z-index: 100;
  max-height: 250px;
  overflow-y: auto;
      width: 100%;
    padding: 0px;
  }

  .autocomplete-result {
    list-style: none;
    text-align: left;
      margin: 0;
  padding: 0;
    cursor: pointer;
  }
.autocomplete-result li {
  padding: 4px 12px;
}
  .autocomplete-result.is-active,
  .autocomplete-result:hover {
    background-color: #4AAE9B;
    color: white;
  }
</style>