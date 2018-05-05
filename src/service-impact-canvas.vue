<template>
  <v-container fluid grid-list-md>
    <v-layout row wrap>
      <v-flex d-flex xs12 sm6 md2>
        <SicRoles v-if="canvas.type==='standard'" :roles="canvas.roles" :businessModel="businessModel"/>
        <SicCustomerVision v-if="canvas.type==='outcome'" :customerVision="canvas.customerVision"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicJobs v-if="canvas.type==='standard'" :jobs="canvas.jobs" :serviceImpactCanvas="canvas" :businessModel="businessModel"/>
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
                <SicAmplifiers v-if="canvas.type==='outcome'" :amplifiers="canvas.amplifiers"/>
                <SicAccelerators v-if="canvas.type==='standard'" :accelerators="canvas.accelerators" :serviceImpactCanvas="canvas"/>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicImpacts :impacts="canvas.impacts" :serviceImpactCanvas="canvas"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicFeatures :features="canvas.features" :serviceImpactCanvas="canvas"/>
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
<style>
.sic-card-body {
  padding-top: 5px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  text-align: left;
}
.sic-card-name {
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 5px;
  margin-right: 0px;
  margin-left: 0px;
}
.sic-card-text {
  font-size: 12px;
  font-weight: normal;
  margin-bottom: 5px;
  margin-right: 0px;
  margin-left: 0px;
}
.sic-card-chip {
  background-color: white;
  font-size: 12px;
  color: black;
  height: 18px;
  border-radius: 9px;
  display: inline-block;
  padding-left: 8px;
  padding-right: 8px;
  margin-bottom: 5px;
  margin-right: 3px;
}
</style>
