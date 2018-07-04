<template>
  <v-card color="grey lighten-2">
    <v-toolbar flat color="grey darken-1" dark height="40px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn v-if="editable" icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="body-1">Jobs</v-toolbar-title>
    </v-toolbar>
    <v-layout row wrap style="min-height: 100px;">
      <v-flex xs12 v-for="(p,i) in jobs" :key="p.id">
        <v-card color="indigo lighten-2" class="white--text">
          <v-toolbar flat color="indigo lighten-1" dark height="20px">
            <v-btn v-if="editable" flat dark icon small style="margin-left: 0px;" @click.native.stop="edit(i)">
              <v-icon size="16px">edit</v-icon>
            </v-btn>
            <v-btn v-if="enableComments" flat dark icon small style="margin-left: 0px;" @click.native.stop="$emit('newJobComment', p)">
              <v-icon size="16px">add_comment</v-icon>
            </v-btn>
            <v-btn v-if="enableComments && p.id" flat dark icon small style="margin-left: 0px;" @click.native.stop="$emit('newJobRating', p)">
              <ion-icon name="star-outline" style="font-size: 16px;margin-bottom: 3px;"></ion-icon>
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn v-if="enableComments && p.id" flat dark icon small style="margin-left: 0px;" @click.native.stop="$emit('displayComments', p)">
              <ion-icon name="chatboxes" style="font-size: 16px;margin-bottom: 3px;"></ion-icon>
            </v-btn>
            <v-btn v-if="editable" flat dark icon small style="margin-right: 0px;" @click.native.stop="del(i)">
              <v-icon size="16px">clear</v-icon>
            </v-btn>
          </v-toolbar>
          <div class="sic-card-body">
            <p class="sic-card-name">{{p.name}}</p>
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
    'businessModel': Object,
    'enableComments': Boolean,
    'editable': Boolean
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
