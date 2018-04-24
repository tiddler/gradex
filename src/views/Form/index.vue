<template>
  <v-app>
    <!-- Toolbar -->
    <v-toolbar 
      color="light-green darken-2" 
      dark 
      fixed
      clipped-left
      app
    >
      <!-- <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon> -->
      <v-btn icon class="hidden-xs-only" @click.stop="goBack()">
        <v-icon>arrow_back</v-icon>
      </v-btn>
      <v-toolbar-title>Grade X Inspection</v-toolbar-title>
    </v-toolbar>

    <!-- Store dialog -->
    <v-dialog v-model="confirm_dialog" max-width="500px">
      <v-card>
        <v-toolbar color="light-blue" dark>
          <v-toolbar-title>Save and Exit</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn icon @click.stop="confirm_dialog = false">
            <v-icon>close</v-icon>
          </v-btn>
        </v-toolbar>
        <v-list three-line class="pa-0">
          <v-list-tile>
            <v-list-tile-content @click.stop="markStatus('complete')">
              <v-list-tile-title>{{ dialog_items[0].title }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ dialog_items[0].subtitle }}</v-list-tile-sub-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-divider></v-divider>
          <v-list-tile>
            <v-list-tile-content @click.stop="markStatus('incomplete')">
              <v-list-tile-title>{{ dialog_items[1].title }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ dialog_items[1].subtitle }}</v-list-tile-sub-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-card>
    </v-dialog> 


    <!-- Crossing Modification dialog -->
    <v-dialog
      v-model="info_dialog" 
      fullscreen 
      hide-overlay 
      transition="dialog-bottom-transition"
    >
      <v-card>
      <v-toolbar dark color="light-blue">
        <v-btn icon @click.native="info_dialog = false" dark>
          <v-icon>close</v-icon>
        </v-btn>
        <v-toolbar-title>Crossing ID: {{crossing.id}}</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn dark flat @click.native="info_dialog = false">Save</v-btn>
        </v-toolbar-items>
        <v-tabs
          centered
          color="light-blue"
          slot="extension"
          slider-color="yellow"
          grow
          v-model="inventory_tabs"
        >
          <v-tab href="#tab-1">General</v-tab>
          <v-tab href="#tab-2">Crossing General</v-tab>
          <v-tab href="#tab-3">Crossing Geometry</v-tab>
          <v-tab href="#tab-4">Crossing Traffic</v-tab>
        </v-tabs>
      </v-toolbar>
      <v-tabs-items v-model="inventory_tabs">
        <v-tab-item :id="'tab-1'">
          <v-card>
            <v-card-text>
              <v-container grid-list-md>
                <v-layout wrap>
                  <v-flex xs2 sm2>
                    <v-subheader>Long English</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field :label=crossing.english single-line v-model="info_dia_items.english"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Long French</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field label="cannot decoding french" single-line v-model="info_dia_items.french"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Status *</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_items.status"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Type *</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-select :label=crossing.type :items="type_items" single-line v-model="info_dia_items.type"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>In Service *</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-menu
                      ref="menu1"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_inservice_from"
                      :return-value.sync="info_dia_items.in_service_from"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_from
                        v-model="info_dia_items.in_service_from"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_items.in_service_from"        
                        @change="$refs.menu1.save(info_dia_items.in_service_from)"
                        >
                      </v-date-picker>
                    </v-menu>          
                    <!-- <v-text-field :label=crossing.in_service_from single-line v-model="info_dia_items.in_service_from"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>To</v-subheader>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-menu
                      ref="menu2"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_inservice_to"
                      :return-value.sync="info_dia_items.in_service_to"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_to
                        v-model="info_dia_items.in_service_to"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_items.in_service_to" 
                        @change="$refs.menu2.save(info_dia_items.in_service_to)"
                        >
                      </v-date-picker>
                    </v-menu>   
                    <!-- <v-text-field :label=crossing.in_service_to single-line v-model="info_dia_items.in_service_to"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>Key Rt</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field :label=crossing.key_rt single-line v-model="info_dia_items.key_rt"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Railway </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.railway single-line v-model="info_dia_items.railway"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Jurisdiction *</v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.juris :items="juris_items" single-line v-model="info_dia_items.juris"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Subdivision </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.subdivision single-line v-model="info_dia_items.subdivision"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Mile </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.mile single-line v-model="info_dia_items.mile"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Spur </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.spur single-line v-model="info_dia_items.spur"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Mile </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.s_mile single-line v-model="info_dia_items.s_mile"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Province * </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.province :items="province_items" single-line v-model="info_dia_items.province"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Region </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.region :items="region_items" single-line v-model="info_dia_items.region"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Latitude </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.lat single-line v-model="info_dia_items.lat"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Longitude </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.lng single-line v-model="info_dia_items.lng"></v-text-field>
                  </v-flex> 
                  <v-flex xs2 sm2>
                    <v-subheader>Nearest Muni </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.nearest_muni single-line v-model="info_dia_items.nearest_muni"></v-text-field>
                  </v-flex>               
                  <v-flex xs2 sm2>
                    <v-subheader>Road/Hwy #Eng</v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.road_hwy single-line v-model="info_dia_items.road_hwy"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>GEO Text </v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field :label=crossing.geo_text single-line v-model="info_dia_items.geo_text"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Updatedby </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.updated_by single-line v-model="info_dia_items.updated_by"></v-text-field>
                  </v-flex>               
                  <v-flex xs2 sm2>
                    <v-subheader>on </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                  <v-menu
                      ref="menu3"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_updated_on"
                      :return-value.sync="info_dia_items.on"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.updated_on
                        v-model="info_dia_items.on"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_items.on"        
                        @change="$refs.menu3.save(info_dia_items.on)"
                        >
                      </v-date-picker>
                    </v-menu> 
                    <!-- <v-text-field :label=crossing.on single-line v-model="info_dia_items.on"></v-text-field> -->
                  </v-flex>    
                  <v-flex xs2 sm2>
                    <v-subheader>Memo </v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field :label=crossing.memo single-line v-model="info_dia_items.memo"></v-text-field>
                  </v-flex>    
                </v-layout>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
          </v-card>
        </v-tab-item>
      </v-tabs-items>
    </v-card>
    </v-dialog>

    <!-- Left Panel: navigation drawer -->
    <v-navigation-drawer 
      id="crosslist"
      fixed
      clipped
      app
      v-model="drawer"
      mobile-break-point=800
    >
      <!-- Site Info -->
      <v-card color="blue-grey lighten-5" id="crossing-info">
        <v-card-title primary-title>
          <div>
            <div class="headline"><strong>Crossing ID: </strong>{{ crossing_info.id }}</div>
            <div class="info-text"><strong>Type: </strong>{{ crossing_info.type }}</div>
            <div class="info-text"><strong>Region: </strong>{{ crossing_info.region + ' - ' + crossing_info.subdivision }}</div>
            <div class="info-text"><strong>Railway: </strong>{{ crossing_info.railway }}</div>
            <div class="info-text"><strong>Address: </strong>{{crossing_info.address }}</div>
            <div class="info-text"><strong>Last Inspect Date: </strong>{{crossing_info.last_inspect_date }}</div>
            <div class="info-text" v-if="crossing_info.last_inspector !== null"
            ><strong>Last Inspector: </strong>{{crossing_info.last_inspector }}</div>
          </div>
        </v-card-title>
        <!-- <v-btn color="info">Overview</v-btn> -->
        <v-btn large color="success" @click.native.stop="confirm_dialog = true">Finish Task</v-btn>
        <div>
          <v-progress-linear id="progress-bar" :value="answer_number / question_list.length * 100" height="8" color="teal"></v-progress-linear>
          <div>{{ answer_number +'/' + question_list.length }}</div>
        </div>
        <v-btn
          absolute
          dark
          fab
          bottom
          right
          small
          color="blue-grey"
          @click.native.stop="info_dialog = true"
        >
          <v-icon>create</v-icon>
        </v-btn>
      </v-card>
      
      <v-subheader>Questions:</v-subheader>
      
      <v-btn :color="getColor(question, index)" 
        :outline="question.answer==null && current_question != index" 
        fab 
        small 
        :dark="question.answer!=null" 
        v-for="(question, index) in question_list" 
        :key="index" 
        @click.stop="selectQuestion(index)"
      >
        {{ question.id }}
      </v-btn>
    </v-navigation-drawer>

    <!-- Content: Form List -->
    <v-content>
      <v-container fluid>
        <v-layout>
          <transition :name="transition" mode="out-in">
            <v-card id="right-panel" class="pa-3 text-xs-center text-sm-center" :key="current_question">
              <!-- <v-tabs
                slider-color="light-green darken-2"
                fixed-tabs
              >
                <v-tab v-for="item in tabs_text" :key="item.name" ripple>{{ item.name }}</v-tab>
              </v-tabs> -->
              
              <!-- dialog button -->
              <!-- <v-layout row justify-center>
                <v-btn color="primary" dark @click.native.stop="question_dialog = true">Open Dialog</v-btn>
                <v-dialog v-model="question_dialog" max-width="650">
                  <v-card>
                    <v-card-title class="headline">Select Question</v-card-title>
                    <v-card-text>Click on the question you want to jump to:</v-card-text>
                    <v-btn :color="getColor(question, index)" fab small :dark="question.answer!=null" v-for="(question, index) in question_list" :key="index" @click="selectQuestion(index)">
                      {{ question.id }}
                    </v-btn>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="green darken-1" flat="flat" @click.native="question_dialog = false">Confirm</v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>
              </v-layout> -->

              <!-- question details -->
              <div id="question-detail">
                <div class="headline">
                  <strong>Question ID: </strong>
                  {{ question_list[current_question].id + ' (' + (current_question + 1) +'/' + question_list.length + ')'}}
                </div>
                <hr>
                <div class="question-text mt-3">{{ question_list[current_question].text }}</div>
              </div>
              <div v-if="question_list[current_question].boolean == null">
                <v-layout row>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans1" label= "NC CA Required" ></v-checkbox>                              
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans2" label= "NC CA Taken"></v-checkbox>                              
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans3" label= "C CA Required" ></v-checkbox>                              
                  </v-flex>                       
                </v-layout>
                <v-layout row>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans4" label= "C CA Taken"></v-checkbox>                              
                  </v-flex>
                  <v-flex xs6 sm6>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans5" label= "C CA Developing Issue" ></v-checkbox>                              
                  </v-flex>                                                      
                </v-layout>  
                <v-layout row>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans6" label= "No Issues Noted" ></v-checkbox>                              
                  </v-flex> 
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans7" label= "Not Applicable"></v-checkbox>                              
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-checkbox v-model="question_list[current_question].answer" value="ans8" label= "Not Inspected" ></v-checkbox>                              
                  </v-flex>                                                        
                </v-layout>
              </div>
              <v-layout row v-else justify-center>
                <v-flex xs4 sm4>
                  <v-checkbox v-model="question_list[current_question].answer" value="ans1" label= "Yes" ></v-checkbox>                              
                </v-flex>
                <v-flex xs4 sm4>
                  <v-checkbox v-model="question_list[current_question].answer" value="ans2" label= "No" ></v-checkbox>                              
                </v-flex>
              </v-layout>

              <v-text-field
                name="input-1"
                label="Notes about inspections"
                textarea
                v-model="question_list[current_question].note"
              ></v-text-field>

              <!-- question direct -->
              <v-layout row justify-space-between>
                <v-btn large color="blue-grey" class="white--text" :disabled="current_question == 0" @click="movePre()">
                  <v-icon left dark>chevron_left</v-icon>
                  Backward
                </v-btn>
                <v-btn large color="blue-grey" class="white--text" :disabled="current_question == question_list.length - 1" @click="moveNext()">
                  Forward
                  <v-icon right dark>chevron_right</v-icon>
                </v-btn>
              </v-layout>
            </v-card>
          </transition>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import db from '@/utils/firestore'

export default {
  name: 'Form',
  data () {
    return {
      // left drawer
      drawer: true,
      crossing_info: {
        id: null,
        type: null,
        region: '',
        subdivision: '',
        railway: null,
        address: null
      },
      // pop-up dialog
      confirm_dialog: null,
      dialog_items: [
        {
          title: 'Mark As Completed',
          subtitle:
            'The End Time stamp will be added to the checklist information.'
        },
        {
          title: 'End Inspection For Now',
          subtitle:
            'Your inspection work will be saved. You may continue later to complete the inspection.'
        }
      ],

      // pop-up dialog for modifying crossing info
      crossing: {
        id: '304562',
        component: 'AWS',
        last_inspect_date: '01-20-2017',
        RSI_Name: 'Jeffrey Young',
        RSI_Region: 'ONT',
        railway_Maintainer: 'Richky Klein',
        attendign_railway_emp: 'Jay Price',
        address: '8731 Leuschke Roads East Audie',
        english: 'AWS: Mile 0 Mcmillan Bloedel Co Spur  23.9, Cartier Subdivision (Ottawa Valley Rail Link), Hwy 17  (Sturgeon Falls), (Y), CrossingID: 20517',
        french: '',
        status: 'Active - Currently: In use by the Railway today',
        type: 'Signals - AWS (Active Warning System)',
        in_service_from: '2014-06-26',
        in_service_to: 'NaN',
        railway: 'Ottawa Valley Railway',
        juris: 'Federal',
        key_rt: 'Unknown',
        subdivision: 'Cartier',
        mile: 23.9,
        spur: 'Mcmillan Bloedel Co',
        s_mile: 0,
        province: 'Ont.',
        region: 'ONT',
        longitude: -79.9371,
        latitude: 46.3662,
        road_hwy: 'Hwy 17  (Sturgeon Falls)',
        nearest_muni: 'ON -Ministry of Transportation',
        GEO_text: 'NaN',
        updated_by: 'NaN',
        updated_on: '2017-03-27',
        memo: '20517'
      },
      info_dialog: null,
      info_dia_items: {
        english: '',
        french: '',
        status: null,
        types: null,
        inservice_from: false,
        inservice_to: false,
        railway: '',
        juris: null,
        key_rt: '',
        subdivision: '',
        mile: '',
        spur: '',
        s_mile: '',
        region: null,
        province: null,
        lat: '',
        lng: '',
        nearest_muni: '',
        road_hwy: '',
        geo_text: '',
        updatedby: '',
        updated_on: false,
        memo: ''
      },
      // info lists
      status_items: [
        'Active - Currently: In use by the Railway today',
        'Inactive - Permanent: Change of ownership',
        'Inactive - Permanent: Crossing road apprch removed',
        'Inactive - Permanent: Railway will not use again',
        'Inactive - Permanent: Track Abandoned',
        'Inactive - Temporary: may be re-used'
      ],
      type_items: [
        'Crossing (Passive)',
        'Signals - AWS (Active Warning System)'
      ],
      juris_items: [
        'Federal',
        'Provincial'
      ],
      region_items: [
        'ATL',
        'ONT',
        'PAC',
        'PNR',
        'QUE'
      ],
      province_items: [
        'Alta.',
        'B.C.',
        'Man.',
        'N.B.',
        'N.L.',
        'N.S.',
        'N.W.T.',
        'Ont.',
        'Que.',
        'Sask.',
        'Y.T.'
      ],
      // date picker menu
      menu_inservice_from: false,
      menu_inservice_to: false,
      menu_updated_on: false,

      inventory_tabs: null,
 
      // question panel
      transition: 'slide-right',
      current_question: 0,
      question_dialog: false,
      question_list: [],
      current_list: []
    }
  },
  methods: {
    selectQuestion (n) {
      this.current_question = n
    },
    goBack () {
      this.$router.go(-1)
    },
    getColor (question, index) {
      if (index === this.current_question) {
        return 'primary'
      } else if (question.answer === null && question.color !== null) {
        return question.color
      } else {
        return 'success'
      }
    },
    movePre () {
      this.transition = 'slide-left'
      this.current_question -= 1
    },
    moveNext () {
      this.transition = 'slide-right'
      this.current_question += 1
    },
    markStatus (status) {
      let data = {}
      this.question_list.forEach((element, index) => {
        data[index] = {
          answer: element.answer,
          note: element.note
        }
      })
      db.doc(`/inspection_history/${this.$store.state.uid}/current_list/${this.crossing_info.id}`)
        .set({
          type: this.crossing_info.type,
          record: data
        })
      var found = this.current_list.findIndex((element) => {
        return element.crossing_id === this.crossing_info.id
      })
      this.current_list[found].status = status
      db.collection('inspection_list').doc(this.$store.state.uid).update({
        current_list: this.current_list
      })
      this.confirm_dialog = false
      this.$router.go(-1)
    }
  },
  computed: {
    answer_number: function () {
      return this.question_list.filter(question => {
        return question.answer != null
      }).length
    }
  },
  created: function () {
    const self = this
    const crossingId = this.$route.params.id
    const crossingType = this.$route.params.type
    if (crossingType.includes('AWS')) {
      this.question_list = require('./aws.json')
    } else if (crossingType.includes('WIS')) {
      this.question_list = require('./wis.json')
    } else if (crossingType.includes('WSS')) {
      this.question_list = require('./wss.json')
    } else {
      this.question_list = require('./passive.json')
    }
    let record = {}
    db.doc(`/inspection_history/${this.$store.state.uid}/current_list/${crossingId}`)
      .get()
      .then((doc) => {
        if (doc.data() !== undefined) {
          record = doc.data().record
          this.question_list.forEach((element, index) => {
            element.answer = record[index].answer
            element.note = record[index].note
          })
        } else {
          this.question_list.forEach((element, index) => {
            element.answer = null
            element.note = ''
          })
        }
      })
    // this.question_list
    db.collection('crossing').where('crossing_id', '==', crossingId)
      .get()
      .then((querySnapshot) => {
        querySnapshot.forEach(function (doc) {
          const data = doc.data()
          // console.log(data)
          self.crossing_info = {
            id: crossingId,
            type: crossingType,
            region: data.region,
            subdivision: data.subdivision,
            railway: data.railway,
            address: data.address,
            last_inspector: data.last_inspector,
            last_inspect_date: data.last_inspect_date
          }
        })
      })
      .catch(function (error) {
        console.log('Error getting documents: ', error)
      })

    db.collection('inspection_list').doc(this.$store.state.uid).get()
      .then((doc) => {
        if ('current_list' in doc.data()) {
          this.current_list = doc.data().current_list
        }
      })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#right-panel {
  width: 100%;
}

#crossing-info {
  width: 90%;
  margin-left: 5%;
  margin-top: 10px;
  margin-bottom: 10px;
  text-align: center;
}

.info-text {
  font-size: 1.3em;
}

#question-detail {
  height: 200px;
  max-height: 200px;
}

#progress-bar {
  width: 90%;
  margin-left: 5%;
}

.question-text {
  font-size: 1.5em;
}

/* slide-fade effect */
.slide-left-enter-active, .slide-right-leave-active {
  transition: all .3s;
}
.slide-left-leave-active, .slide-right-enter-active {
  transition: all .3s;
}
.slide-left-enter, .slide-right-leave-to {
  transform: translateX(-30px);
  opacity: 0;
}
.slide-left-leave-to, .slide-right-enter{
  /* transform: translateX(30px); */
  opacity: 0;
}
</style>