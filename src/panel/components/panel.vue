<template>
  <div ref="window">
    <div class="col-auto mr-auto d-flex" v-if="opts.leftButtons.length > 0">
      <template v-for="button of opts.leftButtons">
        <div v-if="(button.if || typeof button.if === 'undefined')" class="mr-2" v-bind:key="button">
          <hold-button @trigger="$emit(button.event)" :class="typeof button.state !== 'undefined' && state[button.state] === 2 ? 'btn-success' : button.class" v-if="button.hold" :icon="button.icon">
            <template slot="title">{{button.text}}</template>
            <template slot="onHoldTitle">{{button.textWhenHold}}</template>
          </hold-button>
          <a v-else-if="button.href" class="btn btn-shrink btn-with-icon" :href="button.href" :class="typeof button.state !== 'undefined' && state[button.state] === 2 ? 'btn-success' : button.class">
            <div class="text">
              <template v-if="typeof button.state !== 'undefined' &&  typeof state[button.state] !== 'undefined'">
                {{button.text[state[button.state]]}}
              </template>
              <template v-else>{{ button.text }}</template>
            </div>
            <div class="btn-icon" v-if="button.icon">
              <template v-if="typeof button.state !== 'undefined' && state[button.state] === 1">
                <fa icon="circle-notch" spin fixed-width></fa>
              </template>
              <template v-else-if="typeof button.state !== 'undefined' && state[button.state] === 2">
                <fa icon="check" fixed-width></fa>
              </template>
              <fa v-else :icon="button.icon" fixed-width></fa>
            </div>
          </a>
          <button @click="$emit(button.event)" type="button" class="btn btn-shrink btn-with-icon" :class="button.class" :icon="button.icon" v-else>
            <div class="text">
              <template v-if="typeof button.state !== 'undefined' && typeof state[button.state] !== 'undefined'">
                {{button.text[state[button.state]]}}
              </template>
              <template v-else>{{ button.text }}</template>
            </div>
            <div class="btn-icon" v-if="button.icon">
              <template v-if="typeof button.state !== 'undefined' && state[button.state] === 1">
                <fa icon="circle-notch" spin fixed-width></fa>
              </template>
              <template v-else-if="typeof button.state !== 'undefined' && state[button.state] === 2">
                <fa icon="check" fixed-width></fa>
              </template>
              <fa v-else :icon="button.icon" fixed-width></fa>
            </div>
          </button>
        </div>
      </template>
    </div>
    <div class="col-auto ml-auto text-right form-inline d-block pull-right">
      <template v-for="button of opts.rightButtons">
        <div class="ml-2 d-inline-block" v-bind:key="button">
          <a class="btn btn-shrink btn-with-icon" style="flex-direction: row;" v-if="button.href && (button.if || typeof button.if === 'undefined')" :target="button.target || '_self'" :href="button.href" :class="typeof button.state !== 'undefined' && state[button.state] === 2 ? 'btn-success' : button.class">
            <div class="text">
              {{ button.text }}
            </div>
            <div class="btn-icon" v-if="button.icon">
              <template v-if="typeof button.state !== 'undefined' && state[button.state] === 1">
                <fa icon="circle-notch" spin fixed-width></fa>
              </template>
              <template v-else-if="typeof button.state !== 'undefined' && state[button.state] === 2">
                <fa icon="check" fixed-width></fa>
              </template>
              <fa v-else :icon="button.icon" fixed-width></fa>
            </div>
          </a>
          <button type="button" class="btn btn-shrink btn-with-icon" style="flex-direction: row;"
            v-else-if="button.if || typeof button.if === 'undefined'"
            :class="typeof button.state !== 'undefined' && state[button.state] === 2 ? 'btn-success' : button.class"
            :disabled="button.state !== 'undefined' && state[button.state] === 1"
            @click="$emit(button.event)">
            <div class="text">
              <template v-if="typeof button.state !== 'undefined' && typeof state[button.state] !== 'undefined'">
                {{button.text[state[button.state]]}}
              </template>
              <template v-else>{{ button.text }}</template>
            </div>
            <div class="btn-icon" v-if="button.icon">
              <template v-if="typeof button.state !== 'undefined' && state[button.state] === 1">
                <fa icon="circle-notch" spin fixed-width></fa>
              </template>
              <template v-else-if="typeof button.state !== 'undefined' && state[button.state] === 2">
                <fa icon="check" fixed-width></fa>
              </template>
              <fa v-else :icon="button.icon" fixed-width></fa>
            </div>
          </button>
        </div>
      </template>
      <div class="ml-2 d-inline-block" v-if="!opts.hideCardsButton" >
        <button class="btn btn-shrink btn-with-icon p-0" style="flex-direction: row;" v-on:click="showAs='cards'" v-bind:class="[ showAs === 'cards' ? 'btn-dark' : 'btn-outline-dark' ]">
          <div class="btn-icon">
            <fa icon="th-large" fixed-width></fa>
          </div>
        </button>
      </div>
      <div class="ml-2 d-inline-block" v-if="!opts.hideTableButton" >
        <button class="btn btn-shrink btn-with-icon p-0" style="flex-direction: row;" v-on:click="showAs='table'" v-bind:class="[ showAs === 'table' ? 'btn-dark' : 'btn-outline-dark' ]">
          <div class="btn-icon">
            <fa icon="th-list" fixed-width></fa>
          </div>
        </button>
      </div>
      <div class="ml-2 d-inline-block" v-if="!opts.hideSearchInput">
        <fa icon="search" class="text-muted" style="position: relative; left: 2.2rem;" fixed-width></fa>
        <input type="search" class="form-control w-auto pl-5" v-model="search" placeholder="Search...">
      </div>
    </div>
  </div>
</template>

<script lang="ts">
  import Vue from 'vue'

  export default Vue.extend({
    props: ['commons', 'options', 'state'],
    components: {
      holdButton: () => import('./holdButton.vue'),
    },
    data: function () {
      return {
        search: '',
        showAs: 'cards',
        opts: {
          leftButtons: [],
          rightButtons: [],

          hideCardsButton: false,
          hideTableButton: false,
          hideSearchInput: false,
          ...this.options
        }
      }
    },
    watch: {
      search: function (value) {
        this.$emit('search', value)
      },
      showAs: function (value) {
        this.$emit('event', { type: 'showAs', value })
      }
    },
    mounted: function () {
      // set showAs if cards are hidden
      if (this.opts.hideCardsButton) this.showAs = 'table'
    }
  })
</script>

<style scoped>
@media only screen and (max-width: 1000px) {
  .btn-shrink {
    padding: 0!important;
  }
  .btn-shrink .text {
    display: none !important;
  }
  .btn-shrink .btn-icon {
    background: transparent !important;
  }
}

.btn-with-icon {
  padding: 0;
  display: flex;
  flex-direction: row-reverse;
}

.btn-with-icon .text + .btn-icon {
  background: rgba(0,0,0,0.15);
}

.btn-with-icon .btn-icon {
  display: inline-block;
  padding: 0.375rem 0.4rem;
  flex-shrink: 10;
}

.btn-with-icon .text {
  padding: 0.375rem 0.4rem;
}
</style>
