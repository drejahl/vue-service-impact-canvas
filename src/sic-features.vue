<template>
  <v-card color="grey lighten-2">
    <v-toolbar flat color="grey darken-1" dark height="40px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="body-1">Service Features</v-toolbar-title>
    </v-toolbar>
    <v-layout row wrap style="min-height: 100px;">
      <v-flex xs12 v-for="(p,i) in features" :key="p.id">
        <v-card color="indigo lighten-2" class="white--text">
          <v-toolbar flat color="indigo lighten-1" dark height="20px">
            <v-btn flat dark icon small style="margin-left: 0px;" @click.native.stop="edit(i)">
              <v-icon size="16px">edit</v-icon>
            </v-btn>
            <v-btn flat dark icon small style="margin-left: -5px;" @click.native.stop="viewCard(i)">
              <v-icon v-if="p.backLogItemId" size="14px">fa-briefcase</v-icon>
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn flat dark icon small style="margin-right: 0px;" @click.native.stop="del(i)">
              <v-icon size="16px">clear</v-icon>
            </v-btn>
          </v-toolbar>
          <div class="sic-card-body">
            <p class="sic-card-name">{{p.name}}</p>
            <p class="sic-card-text">{{p.description}}</p>
            <div v-if="p.type" class="sic-card-chip">{{p.type}}</div>
          </div>
        </v-card>
      </v-flex>
    </v-layout>
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
                <v-text-field  multi-line label="When..." required v-model="feature.when"></v-text-field>
                <v-text-field  multi-line label="I want..." required v-model="feature.want"></v-text-field>
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
    <v-dialog v-model="trelloDialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Backlog Item</span>
        </v-card-title>
        <v-card-text v-if="trelloCard && listLabels">
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field label="Name" readonly v-model="trelloCard.name"></v-text-field>
                <v-text-field v-if="feature.name != trelloCard.name" label="Name in Service Impact Canvas" error readonly v-model="feature.name"></v-text-field>
                <v-text-field label="List" readonly v-model="trelloList.name"></v-text-field>
                <v-text-field multi-line label="Description" readonly v-model="trelloCard.desc"></v-text-field>
                <v-text-field v-if="feature.description != trelloCard.desc"  multi-line label="Description in Service Impact Canvas"
                  error readonly v-model="feature.description">
                </v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-select :items="listLabels" v-model="trelloCard.labels" readonly
                  label="Labels" item-text="name" multiple chips item-value="id"></v-select>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
          <v-btn v-if="trelloCard" color="blue darken-1" flat :href="trelloCard.url" target="_blank">View in Trello</v-btn>
          <v-btn :disabled="syncNotNeeded" color="blue darken-1" flat @click.native="updateTrello">SIC >> Trello</v-btn>
          <v-btn :disabled="syncNotNeeded" color="blue darken-1" flat @click.native="updateSIC">Trello >> SIC</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
import uuidv1 from 'uuid/v1';
const R = require('ramda');
import axios from 'axios';

export default {
  name: 'SicFeatures',
  components: {
  },
  props: {
    'features': Array,
    'serviceImpactCanvas': Object
  },
  computed: {
    syncNotNeeded: function() {
      return ( this.feature && this.trelloCard &&
        this.feature.name === this.trelloCard.name &&
        this.feature.description === this.trelloCard.desc)
    }
  },
  data () {
    return {
      dialog: false,
      trelloDialog: false,
      editIdx: null,
      trellokey: null,
      trelloCard: null,
      trelloList: null,
      listLabels: [],
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
    this.trellokey = sessionStorage.getItem('trellotoken');
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
      this.trelloDialog=false;
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
    viewCard: function(i) {
      // [HACK] - THIS NEEDS TO BE MOVE INTO THE APPLICTION!!!
      if (this.trellokey) {
        let self=this;
        self.feature =  Object.assign( {}, self.features[i]);
        const trelloUrl = 'https://api.trello.com/1/cards/' + self.feature.backLogItemId + "?key=19f3b4741602fda8ca5c66eaf3142a71&token=" + self.trellokey;
        axios.get(trelloUrl)
        .then( function(response) {
          self.trelloCard=response.data;
          const listUrl = 'https://api.trello.com/1/lists/' + self.trelloCard.idList + "?key=19f3b4741602fda8ca5c66eaf3142a71&token=" + self.trellokey;
          axios.get(listUrl)
          .then( function(response) {
            self.trelloList=response.data;
          })
          .catch( (error) => {
            sessionStorage.removeItem("trellotoken");
          });
          const labelUrl = 'https://api.trello.com/1/boards/' + self.trelloCard.idBoard + "/labels?key=19f3b4741602fda8ca5c66eaf3142a71&token=" + self.trellokey;
          axios.get(labelUrl)
          .then( function(response) {
            self.listLabels=response.data;
          })
          .catch( (error) => {
            sessionStorage.removeItem("trellotoken");
          });
        })
        .catch( (error) => {
          sessionStorage.removeItem("trellotoken");
        });
        this.editIdx = i;
        this.trelloDialog=true;
      }
    },
    updateTrello: function() {
      let self=this;
      this.trelloDialog=false;
      const labelUrl = 'https://api.trello.com/1/cards/' + self.trelloCard.id +
        "?name=" + self.feature.name + "&desc=" + self.feature.description +
        "&key=19f3b4741602fda8ca5c66eaf3142a71&token=" + self.trellokey;
      axios.put(labelUrl)
      .then( function(response) {
        self.listLabels=response.data;
      })
      .catch( (error) => {
        sessionStorage.removeItem("trellotoken");
      });
    },
    updateSIC: function() {
      this.trelloDialog=false;
      this.feature.name = this.trelloCard.name;
      this.feature.description = this.trelloCard.desc;
      this.save();
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
