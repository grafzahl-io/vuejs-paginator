<template>
  <div class="v-paginator">
    <div class="pagination">
      <button v-if="config.show_first_last_buttons" class="btn btn-default paginator-jump-to-first" @click="fetchData(url_builder(1))" :disabled="current_page == 1">&laquo;
      </button>
      <button class="btn btn-default" @click="prevPage()" :disabled="!prev_page_url">
        {{config.previous_button_text}}
      </button>
      <span>{{ config.page_label_text }} {{current_page}} {{ config.of_label_text }} {{last_page}}</span>
      <button class="btn btn-default" @click="nextPage()" :disabled="!next_page_url">
        {{config.next_button_text}}
      </button>
      <button v-if="config.show_first_last_buttons" class="btn btn-default paginator-jump-to-last" @click="fetchData(url_builder(last_page))" :disabled="current_page == last_page">&raquo;
      </button>
    </div>
    <div class="jumpto" v-if="config.show_jump_to_input">
      <input type="text" class="form-control" v-model="jumpto"/><button class="btn btn-default" @click="jumpTo()">{{ config.jump_to_button_text }}</button>
    </div>
  </div>
</template>

<script>
import {utils} from './utils'
export default {
  props: {
    resource_url: {
      type: String,
      required: true
    },
    url_builder: {
      type: Function,
      required: false,
      default () {
        return function() { return false; }
      }
    },
    custom_template : '',
    options: {
      type: Object,
      required: false,
      default () {
        return {}
      }
    }
  },
  data () {
    return {
      current_page: '',
      last_page: '',
      next_page_url: '',
      prev_page_url: '',
      query: '',
      jumpto: 1,
      config: {
          remote_data: 'data',
          remote_current_page: 'current_page',
          remote_last_page: 'last_page',
          remote_next_page_url: 'next_page_url',
          remote_prev_page_url: 'prev_page_url',
          jump_to_button_text: 'Jump to',
          previous_button_text: 'Previous',
          next_button_text: 'Next',
          page_label_text: 'Page',
          of_label_text: 'of',
          show_jump_to_input: true,
          show_first_last_buttons: true,
      }
    }
  },
  methods: {
    jumpTo () {
      if(this.jumpto == this.current_page) {
        return;
      }

      if(this.jumpto <= 1) {
        this.jumpto = 1;
      }
      
      var last = parseInt(this.last_page);
      if(this.jumpto >= last) {
        this.jumpto = last;
      }

      var url = this.url_builder(this.jumpto);
      this.fetchData(url);
    },
    nextPage() {
      Bus.$emit("paginatorNextPageClicked");
      this.fetchData(this.next_page_url)
    },
    prevPage() {
      Bus.$emit("paginatorPrevPageClicked");
      this.fetchData(this.prev_page_url)
    },
    setUriQuery(string) {
      this.query = string;
    },
    fetchData (pageUrl) {
      Bus.$emit("paginatorRequestStart");
      pageUrl = pageUrl || this.resource_url
      if(this.query !== '') {
        pageUrl = pageUrl + '?' + this.query
      }
      var self = this
      axios.get(pageUrl).then(response => {
        Bus.$emit("paginatorRequestFinish", response);
        self.handleResponseData(response.data);
      }).catch(response => {
        Bus.$emit("paginatorRequestFailed", response);
        console.log('Fetching data failed.', response);
      });
    },
    handleResponseData (response) {
      this.makePagination(response)
      let data = utils.getNestedValue(response, this.config.remote_data)
      this.$emit('update', response)
    },
    makePagination (data) {
      this.current_page = utils.getNestedValue(data, this.config.remote_current_page)
      this.last_page = utils.getNestedValue(data, this.config.remote_last_page)
      this.next_page_url = (this.current_page === this.last_page) ? null : utils.getNestedValue(data, this.config.remote_next_page_url);
      this.prev_page_url = (this.current_page === 1) ? null : utils.getNestedValue(data, this.config.remote_prev_page_url);
    },
    initConfig(){
      this.config = utils.merge_objects(this.config, this.options)
    }
  },
  watch : {
    resource_url () {
      this.fetchData()
    }
  },
  created () {
    this.initConfig()
    this.fetchData()
  }
}
</script>
