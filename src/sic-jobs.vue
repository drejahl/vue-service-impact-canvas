<template>
  <v-card color="teal lighten-3">
    <v-toolbar flat color="teal lighten-2" dark height="40px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="subheading">Jobs</v-toolbar-title>
    </v-toolbar>
    <v-layout row wrap>
      <v-flex xs12 v-for="(p,i) in jobs" :key="p.id">
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
          <div class="sic-card-body">
            <p class="sic-card-text">{{p.description}}</p>
            <div v-for="t in p.type" class="sic-card-chip">{{t}}</div>
          </div>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="800px">
      <v-card>
        <v-card-title>
          <span class="headline">Customer Job</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md8>
                <v-text-field label="Name" required v-model="job.name"></v-text-field>
                <v-select :items="serviceImpactCanvas.roles" v-model="job.roleRef"
                  label="Roles" item-text="name" multiple chips item-value="id"></v-select>
                <v-select v-if="businessModel" :items="businessModel.keyActivities" v-model="job.keyActivityRef"
                  label="Related key activity" item-text="name" item-value="id"></v-select>
              </v-flex>
              <v-flex xs12 sm6>
                <v-select label="Type" multiple autocomplete v-model="job.type"
                  :items="['OneTime', 'Recurring']">
                </v-select>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field multi-line label="Description" v-model="job.description"></v-text-field>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field multi-line label="Intended outcome" v-model="job.outcome"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-text-field multi-line label="Success Criteria" v-model="job.successCriteria"></v-text-field>
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

export default {
  name: 'SicJobs',
  components: {
  },
  props: {
    'jobs': Array,
    'serviceImpactCanvas': Object,
    'businessModel': Object
  },
  data () {
    return {
      dialog: false,
      editIdx: null,
      job: {description:"", outcome: "", type: [], keyActivityRef: "", roleRef: [] }
    }
  },
  mounted: function() {
  },
  methods: {
    del: function(i) {
      this.jobs.splice(i, 1);
    },
    close: function() {
      this.dialog=false;
    },
    create: function() {
      this.job = {description:"", outcome: "", type: [], keyActivityRef: "", roleRef: []};
      this.dialog=true;
    },
    edit: function(i) {
      this.job =  Object.assign( {}, this.jobs[i]);
      this.editIdx = i;
      this.dialog=true;
    },
    save: function(i) {
      this.dialog=false;

      if (!this.job.id) {
        this.job.id=uuidv1();
        this.jobs.push(this.job);
      } else {
        this.jobs[this.editIdx] = Object.assign( {}, this.job);
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
