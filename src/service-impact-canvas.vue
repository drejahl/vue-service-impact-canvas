<template>
  <v-container fluid grid-list-md>
    <v-layout row wrap>
      <v-flex d-flex xs12 sm6 md2>
        <SicRoles v-if="canvas.type==='standard'" :roles="canvas.roles" :businessModel="businessModel" :editable="editable" :enableComments="enableComments"
          @newRoleRating="newRoleRating" @newRoleComment="newRoleComment" @displayComments="displayComments"/>
        <SicCustomerVision v-if="canvas.type==='outcome'" :customerVision="canvas.customerVision"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicJobs v-if="canvas.type==='standard'" :jobs="canvas.jobs" :serviceImpactCanvas="canvas" :businessModel="businessModel" :editable="editable" :enableComments="enableComments"
          @newJobRating="newJobRating" @newJobComment="newJobComment" @displayComments="displayComments"/>
        <SicExpectedOutcome v-if="canvas.type==='outcome'" :expectedOutcome="canvas.expectedOutcome"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <v-layout row wrap>
          <v-flex d-flex>
            <v-layout row wrap>
              <v-flex d-flex xs12>
                <SicBarriers :barriers="canvas.barriers" :serviceImpactCanvas="canvas" :editable="editable" :enableComments="enableComments"
                  @newBarrierRating="newBarrierRating" @newBarrierComment="newBarrierComment" @displayComments="displayComments"/>
              </v-flex>
              <v-flex d-flex xs12>
                <SicAmplifiers v-if="canvas.type==='outcome'" :amplifiers="canvas.amplifiers"/>
                <SicAccelerators v-if="canvas.type==='standard'" :accelerators="canvas.accelerators" :serviceImpactCanvas="canvas" :editable="editable" :enableComments="enableComments"
                  @newAcceleratorRating="newAcceleratorRating" @newAcceleratorComment="newAcceleratorComment" @displayComments="displayComments"/>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicImpacts :impacts="canvas.impacts" :serviceImpactCanvas="canvas" :editable="editable" :enableComments="enableComments"
          @newImpactRating="newImpactRating" @newImpactComment="newImpactComment" @displayComments="displayComments"/>
      </v-flex>
      <v-flex d-flex xs12 sm6 md2>
        <SicFeatures :features="canvas.features" :serviceImpactCanvas="canvas" :editable="editable" :enableComments="enableComments"
          @newFeatureRating="newFeatureRating" @newFeatureComment="newFeatureComment" @displayComments="displayComments"/>
      </v-flex>
      <v-flex v-if="enableComments" d-flex xs12 sm6 md2>
        <v-layout column style="max-height: 750px;overflow:scroll;">
          <v-card v-for="comment in comments._embedded.item" :key="comment.id" v-if="!comment.subRefId">
            <v-card-title>
              <v-chip>
                <v-avatar>
                  <img :src="comment.pictureURL" alt="user">
                </v-avatar>
                {{comment.userName}}
              </v-chip>
              <div style="text-align: left;">
                <span class="grey--text">{{formatedDate(comment.created, "DD-MM-YYYY HH:mm:ss")}}</span><br>
                <span class="grey--text">{{comment.refType}}</span><br>
                <span>{{comment.body}}</span>
                <star-rating v-if="comment.rating" :star-size="25" :rating="comment.rating" :read-only="true" :increment="0.01"></star-rating>
              </div>
            </v-card-title>
          </v-card>
        </v-layout>
      </v-flex>
    </v-layout>
    <v-dialog v-model="commentDialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Comments</span>
        </v-card-title>
      </v-card>
      <v-card v-for="comment in comments._embedded.item" :key="comment.id" v-if="comment.subRefId && comment.subRefId===elementId">
        <v-card-title>
          <v-chip>
            <v-avatar>
              <img :src="comment.pictureURL" alt="user">
            </v-avatar>
            {{comment.userName}}
          </v-chip>
          <div style="text-align: left;">
            <span class="grey--text">{{formatedDate(comment.created, "DD-MM-YYYY HH:mm:ss")}}</span><br>
            <span class="grey--text">{{comment.refType}}</span><br>
            <span>{{comment.body}}</span>
            <star-rating v-if="comment.rating" :star-size="25" :rating="comment.rating" :read-only="true" :increment="0.01"></star-rating>
          </div>
        </v-card-title>
      </v-card>
      <v-card>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="closeModal">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
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
import fecha from 'fecha'
import StarRating from 'vue-star-rating'

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
    SicAmplifiers,
    StarRating
  },
  props: {
    'canvas': Object,
    'businessModel': Object,
    'enableComments': Boolean,
    'comments': Object,
    'editable': Boolean
  },
  data () {
    return {
      commentDialog: false,
      elementId: null
    }
  },
  methods: {
    formatedDate: function(d,f){
      return fecha.format( parseInt(d),f);
    },
    newRoleRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newRoleComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    newJobRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newJobComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    newBarrierRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newBarrierComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    newAcceleratorRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newAcceleratorComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    newImpactRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newImpactComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    newFeatureRating: function(e) {
      this.$emit( "newSicRating", e)
    },
    newFeatureComment: function(e) {
      this.$emit( "newSicComment", e)
    },
    displayComments: function(e) {
      this.elementId = e.id;
      this.commentDialog = true;
    },
    closeModal: function() {
      this.commentDialog = false;
    }
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
