<template>
  <v-container fluid grid-list-md>
    <v-layout row wrap>
      <v-flex d-flex xs12 sm6 md2>
        <SicRoles v-if="canvas.type==='standard'" :roles="canvas.roles" :businessModel="businessModel"/>
        <SicCustomerVision v-if="canvas.type==='outcome'" :customerVision="canvas.customerVision"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicJobs v-if="canvas.type==='standard'" :jobs="canvas.jobs" :serviceImpactCanvas="canvas"/>
        <SicExpectedOutcome v-if="canvas.type==='outcome'" :expectedOutcome="canvas.expectedOutcome"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <v-layout row wrap>
          <v-flex d-flex>
            <v-layout row wrap>
              <v-flex d-flex xs12>
                <SicBarriers :barriers="canvas.barriers" :serviceImpactCanvas="canvas"/>
              </v-flex>
              <v-flex d-flex xs12>
                <SicAmplifiers v-if="canvas.type==='standard'" :amplifiers="canvas.amplifiers"/>
                <SicAccelerators v-if="canvas.type==='outcome'" :accelerators="canvas.accelerators"/>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicImpacts :impacts="canvas.impacts"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicFeatures :features="canvas.features"/>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import SicRoles from './sic-roles.vue';
import SicJobs from './sic-jobs.vue';
import SicBarriers from './sic-barriers.vue';
import SicAccelerators from './sic-accelerators.vue';
import SicImpacts from './sic-impacts.vue';
import SicFeatures from './sic-features.vue';
import SicCustomerVision from './sic-customer-vision.vue';
import SicExpectedOutcome from './sic-expected-outcome.vue';
import SicAmplifiers from './sic-amplifiers.vue';

export default {
  name: 'VueServiceImpactCanvas',
  components: {
    SicRoles,
    SicJobs,
    SicBarriers,
    SicAccelerators,
    SicImpacts,
    SicFeatures,
    SicCustomerVision,
    SicExpectedOutcome,
    SicAmplifiers
  },
  props: {
    'canvas': Object,
    'businessModel': Object
  },
  data () {
    return {
    }
  },
  methods: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
