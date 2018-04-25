<template>
  <v-card color="teal lighten-3">
    <v-toolbar flat color="teal lighten-2" dark height="30px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="body-1">Service Features</v-toolbar-title>
    </v-toolbar>
    <v-container fluid style="min-height: 0;" grid-list-lg>
      <v-layout row wrap>
        <v-flex xs12 v-for="(p,i) in features" :key="p.id">
          <v-card color="blue lighten-2" class="white--text">
            <v-toolbar flat color="blue lighten-1" dark height="20px">
              <v-btn flat dark icon small style="margin-left: 0px;" @click.native.stop="edit(i)">
                <v-icon size="16px">edit</v-icon>
              </v-btn>
              <v-toolbar-title style="margin-left: 0px;" class="caption">{{p.name}}</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn flat dark icon small style="margin-right: 0px;" @click.native.stop="del(i)">
                <v-icon size="16px">clear</v-icon>
              </v-btn>
            </v-toolbar>
            <v-card-title primary-title>
              <v-icon v-if="p.backLogItemId">fa-trello</v-icon>
              <div class="caption">{{p.description}}</div>
            </v-card-title>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Service Feature</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field label="Name" required v-model="feature.name"></v-text-field>
                <v-select :items="serviceImpactCanvas.impacts" v-model="feature.impactRef"
                  label="Addressed Impact" item-text="name" item-value="id" @input="impactSelected"></v-select>
              </v-flex>
              <v-flex xs12>
                <v-select label="Type" autocomplete v-model="feature.type"
                  :items="['User Story', 'Job Story', 'Feature']">
                </v-select>
              </v-flex>
              <v-flex v-if="feature.type==='Feature' || feature.type==='User Story'" xs12 sm12 md12>
                <v-text-field  multi-line label="Description" v-model="feature.description"></v-text-field>
              </v-flex>
              <v-flex v-if="feature.type==='Job Story'" xs12 sm12 md12>
                <v-select v-if="feature.type==='Job Story' && feature.impactRef!==''":items="jobs" v-model="feature.jobRef"
                  label="Job" item-text="name" item-value="id"></v-select>
                <v-text-field label="When..." required v-model="feature.when"></v-text-field>
                <v-text-field label="I want..." required v-model="feature.want"></v-text-field>
                <v-text-field  multi-line label="So I can..." v-model="feature.can"></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
          <v-btn color="blue darken-1" flat @click.native="save">Ok</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
import uuidv1 from 'uuid/v1';
const R = require('ramda');

export default {
  name: 'SicFeatures',
  components: {
  },
  props: {
    'features': Array,
    'serviceImpactCanvas': Object
  },
  data () {
    return {
      dialog: false,
      editIdx: null,
      feature: {
        type: "Job Story",
        description:"",
        impactRef: "",
        when: "",
        want: "",
        can: "",
        jobRef: ""},
      jobs: []
    }
  },
  mounted: function() {
  },
  methods: {
    impactSelected: function(id) {
      var impact = R.find(R.propEq('id', id), this.serviceImpactCanvas.impacts);
      this.jobs = [];

      if (impact) {
        impact.barrierRef.forEach( b => {
          let barrier = R.find(R.propEq('id', b), this.serviceImpactCanvas.barriers);
          if (barrier) {
            barrier.jobRef.forEach( j => {
              let job = R.find(R.propEq('id', j), this.serviceImpactCanvas.jobs);
              if (job) {
                this.jobs.push(job);
              }
            })
          }
        })
        impact.acceleratorRef.forEach( a => {
          let accelerator = R.find(R.propEq('id', a), this.serviceImpactCanvas.accelerators);
          if (accelerator) {
            accelerator.jobRef.forEach( j => {
              let job = R.find(R.propEq('id', j), this.serviceImpactCanvas.jobs);
              if (job) {
                this.jobs.push(job);
              }
            })
          }
        })
      }
    },
    del: function(i) {
      this.features.splice(i, 1);
    },
    close: function() {
      this.dialog=false;
    },
    create: function() {
      this.feature = {
        type: "Job Story",
        description:"",
        impactRef: "",
        when: "",
        want: "",
        can: "",
        jobRef: ""};
      this.dialog=true;
    },
    edit: function(i) {
      this.feature =  Object.assign( {}, this.features[i]);
      this.impactSelected(this.feature.impactRef);
      this.editIdx = i;
      this.dialog=true;
    },
    save: function(i) {
      this.dialog=false;

      if (!this.feature.id) {
        this.feature.id=uuidv1();
        this.features.push(this.feature);
      } else {
        this.features[this.editIdx] = Object.assign( {}, this.feature);
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
