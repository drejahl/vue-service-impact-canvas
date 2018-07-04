<template>
  <v-card color="grey lighten-2">
    <v-toolbar flat color="grey darken-1" dark height="40px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn v-if="editable" icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="body-1">Barriers</v-toolbar-title>
    </v-toolbar>
    <v-layout row wrap style="min-height: 100px;">
      <v-flex xs12 v-for="(p,i) in barriers" :key="p.id">
        <v-card color="indigo lighten-2" class="white--text">
          <v-toolbar flat color="indigo lighten-1" dark height="20px">
            <v-btn v-if="editable" flat dark icon small style="margin-left: 0px;" @click.native.stop="edit(i)">
              <v-icon size="16px">edit</v-icon>
            </v-btn>
            <v-btn v-if="enableComments" flat dark icon small style="margin-left: 0px;" @click.native.stop="$emit('newChannelComment', p)">
              <v-icon size="16px">add_comment</v-icon>
            </v-btn>
            <v-btn v-if="enableComments && p.id" flat dark icon small style="margin-left: 0px;" @click.native.stop="$emit('newChannelRating', p)">
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
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Barrier</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md8>
                <v-text-field label="Name" required v-model="barrier.name"></v-text-field>
                <v-select :items="serviceImpactCanvas.jobs" v-model="barrier.jobRef"
                  label="Affected Jobs" item-text="name" multiple chips item-value="id"></v-select>
              </v-flex>
              <v-flex xs12 sm12 md8>
                <v-select label="Type" multiple autocomplete chips v-model="barrier.type"
                  :items="['Technology', 'Business Process', 'Legislation', 'Regulatory']">
                </v-select>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field multi-line label="Description" v-model="barrier.description"></v-text-field>
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
  name: 'SicBarriers',
  components: {
  },
  props: {
    'barriers': Array,
    'serviceImpactCanvas': Object,
    'enableComments': Boolean,
    'editable': Boolean
  },
  data () {
    return {
      dialog: false,
      editIdx: null,
      barrier: {description:"", jobRef: [], type: []}
    }
  },
  mounted: function() {
  },
  methods: {
    del: function(i) {
      this.barriers.splice(i, 1);
    },
    close: function() {
      this.dialog=false;
    },
    create: function() {
      this.barrier = {description:"", jobRef: [], type: []};
      this.dialog=true;
    },
    edit: function(i) {
      this.barrier =  Object.assign( {}, this.barriers[i]);
      this.editIdx = i;
      this.dialog=true;
    },
    save: function(i) {
      this.dialog=false;

      if (!this.barrier.id) {
        this.barrier.id=uuidv1();
        this.barriers.push(this.barrier);
      } else {
        this.barriers[this.editIdx] = Object.assign( {}, this.barrier);
      }
    }
  }
}
</script>
