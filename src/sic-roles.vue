<template>
  <v-card color="grey lighten-2">
    <v-toolbar flat color="grey darken-1" dark height="40px">
      <!--v-toolbar-side-icon></v-toolbar-side-icon-->
      <v-btn icon style="margin-left: 0px;" @click.native.stop="create">
        <v-icon>add</v-icon>
      </v-btn>
      <v-toolbar-title style="margin-left: 0px;" class="body-1">Roles</v-toolbar-title>
    </v-toolbar>
    <v-layout row wrap style="min-height: 100px;">
      <v-flex xs12 v-for="(p,i) in roles" :key="p.id">
        <v-card color="indigo lighten-2" class="white--text">
          <v-toolbar flat color="indigo lighten-1" dark height="20px">
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
            <div v-if="p.type" class="sic-card-chip">{{p.type}}</div>
          </div>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">Role</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md8>
                <v-text-field label="Name" required v-model="role.name"></v-text-field>
                <v-select label="Stakeholder Type" autocomplete v-model="role.type"
                  :items="['Customer','Partner','Internal']">
                </v-select>
                <v-select v-if="businessModel && role.type==='Customer'" :items="businessModel.customerSegments" v-model="role.customerSegmentRef"
                  label="Customer Segment" item-text="name" item-value="id"></v-select>
                <v-select v-if="businessModel && role.type==='Partner'" :items="businessModel.keyPartners" v-model="role.keyPartnerRef"
                  label="Key Partner" item-text="name" item-value="id"></v-select>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field multi-line label="Description" v-model="role.description"></v-text-field>
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
  name: 'SicRoles',
  components: {
  },
  props: {
    'roles': Array,
    'businessModel': Object
  },
  data () {
    return {
      dialog: false,
      editIdx: null,
      role: {description:"", customerSegmentRef: "", keyPartnerRef: "", type: []}
    }
  },
  mounted: function() {
  },
  methods: {
    del: function(i) {
      this.roles.splice(i, 1);
    },
    close: function() {
      this.dialog=false;
    },
    create: function() {
      this.role = {description:"", customerSegmentRef: "", keyPartnerRef: "", type: []};
      this.dialog=true;
    },
    edit: function(i) {
      this.role =  Object.assign( {}, this.roles[i]);
      this.editIdx = i;
      this.dialog=true;
    },
    save: function(i) {
      this.dialog=false;

      if (!this.role.id) {
        this.role.id=uuidv1();
        this.roles.push(this.role);
      } else {
        this.roles[this.editIdx] = Object.assign( {}, this.role);
      }
    }
  }
}
</script>
