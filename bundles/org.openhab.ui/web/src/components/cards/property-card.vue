<template>
  <f7-card expandable ref="card" class="property-card" :animate="$f7.data.themeOptions.expandableCardAnimation !== 'disabled'" card-tablet-fullscreen v-on:card:opened="cardOpening" v-on:card:closed="cardClosed">
    <f7-card-content :padding="false">
      <div :class="`bg-color-${color}`" :style="{height: '150px'}">
        <f7-card-header text-color="white" class="display-block">
          {{title || 'Something'}}
          <div class="property-stats"><small v-if="subtitle">{{subtitle}}</small></div>
          <br>
          <!-- <h1>State</h1> -->
        </f7-card-header>
        <f7-link
          card-close
          color="white"
          class="card-opened-fade-in"
          :style="{position: 'absolute', right: '15px', top: '15px'}"
          icon-f7="multiply_circle_fill"
        ></f7-link>
      </div>
      <div class="card-content-padding" v-if="opened">
        <generic-widget-component :context="listContext" />
        <p class="padding-top margin-horizontal">
          <f7-button outline round :color="color" :href="`/analyzer/?items=${items.map((m) => m.name).join(',')}`">Analyze{{items.length > 1 ? ' all' : ''}}</f7-button>
        </p>
        <p class="margin-horizontal">
          <f7-button fill round large card-close :color="color">Close</f7-button>
        </p>
      </div>
    </f7-card-content>
  </f7-card>
</template>

<style lang="stylus">
.property-card
  height 150px
.property-stats
  font-weight normal
</style>

<script>
import itemDefaultListComponent from '@/components/widgets/standard/list/default-list-item'
import CardMixin from './card-mixin'

export default {
  mixins: [CardMixin],
  computed: {
    listContext () {
      let pointsByType = []
      for (let pointType in this.itemsByPointType) {
        pointsByType.push([
          {
            component: 'oh-list-item',
            config: {
              title: pointType,
              divider: true
            }
          },
          ...this.itemsByPointType[pointType].map((p) => itemDefaultListComponent(p, true))
        ])
      }

      return {
        store: this.$store.getters.trackedItems,
        component: {
          component: 'oh-list',
          config: {
            mediaList: true
          },
          slots: {
            default: pointsByType.flat()
          }
        }
      }
    },
    itemsByPointType () {
      const points = {}
      this.items.forEach((item) => {
        const pointType = item.metadata.semantics.value.replace('Point_', '')
        if (!points[pointType]) points[pointType] = []
        points[pointType].push(item)
      })
      return points
    }
  }
}
</script>
