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
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title>Grade X Inspection</v-toolbar-title>
    </v-toolbar>

    <!-- Store dialog -->
    <v-dialog v-model="confirm_dialog" max-width="500px">
      <v-card>
        <v-toolbar color="light-blue" dark>
          <v-toolbar-title>Finish Inspection Task</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn icon @click.stop="confirm_dialog = false">
            <v-icon>close</v-icon>
          </v-btn>
        </v-toolbar>
        <v-list three-line class="pa-0">
          <v-list-tile>
            <v-list-tile-content>
              <v-list-tile-title>{{ dialog_items[0].title }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ dialog_items[0].subtitle }}</v-list-tile-sub-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-divider></v-divider>
          <v-list-tile>
            <v-list-tile-content>
              <v-list-tile-title>{{ dialog_items[1].title }}</v-list-tile-title>
              <v-list-tile-sub-title>{{ dialog_items[1].subtitle }}</v-list-tile-sub-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-card>
    </v-dialog> 


    <!-- Crossing Modification dialog -->
    <v-dialog v-model="info_dialog" max-width="800px">
      <v-card>
        <v-card-title>
          <div class="headline"> Crossing ID: {{crossing.id}} </div>
          <v-spacer></v-spacer>
          <v-btn icon @click.stop="info_dialog = false">
            <v-icon>close</v-icon>
          </v-btn>
        </v-card-title>
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
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="error" large round @click.native="info_dialog = false">Close</v-btn>
          <v-btn color="primary" large round @click.native="info_dialog = false">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Left Panel: navigation drawer -->
    <v-navigation-drawer 
      id="crosslist"
      clipped
      floating
      app
      v-model="drawer"
      mobile-break-point=800
    >
      <!-- Site Info -->
      <v-card color="blue-grey lighten-5" id="crossing-info">
        <v-card-title primary-title>
          <div>
            <div class="headline"><strong>Crossing ID: </strong> {{ crossing.id }}</div>
            <div class="headline"><strong>Type: </strong>
              {{ crossing.component }}
              <v-btn icon @click.native.stop="info_dialog = true">
                <v-icon>create</v-icon>
              </v-btn>
            </div>
            <div class="info-text mt-3"><strong>RSI Name: </strong>{{ crossing.RSI_Name }}</div>
            <div class="info-text"><strong>RSI Region: </strong>{{ crossing.RSI_Region }}</div>
            <div class="info-text"><strong>Maintainer: </strong>{{ crossing.railway_Maintainer }}</div>
            <div class="info-text"><strong>Railway Emp: </strong>{{ crossing.attendign_railway_emp }}</div>
            <div class="info-text"><strong>Address: </strong>{{crossing.address }}</div>
            <div class="info-text"><strong>Last Inspection: </strong>{{ crossing.last_inspect_date }}</div>  
          </div>
        </v-card-title>
        <v-btn color="info">Overview</v-btn>
        <v-btn color="success" @click.native.stop="confirm_dialog = true">Finish Task</v-btn>
        <v-progress-circular
            :size="70"
            :width="10"
            :rotate="-90"
            :value="answer_number / question_list.length * 100"
            color="teal"
        >
          {{ answer_number +'/' + question_list.length }}
        </v-progress-circular>
        <div>
          <v-progress-linear id="progress-bar" :value="answer_number / question_list.length * 100" height="8" color="teal"></v-progress-linear>
          <div>{{ answer_number +'/' + question_list.length }}</div>
        </div>
      </v-card>
      
      <v-btn :color="get_color(question, index)" 
        :outline="question.answer==null && current_question != index" 
        fab 
        small 
        :dark="question.answer!=null" 
        v-for="(question, index) in question_list" 
        :key="index" 
        @click="select_question(index)"
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
                    <v-btn :color="get_color(question, index)" fab small :dark="question.answer!=null" v-for="(question, index) in question_list" :key="index" @click="select_question(index)">
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

              <v-text-field
                name="input-1"
                label="Notes about inspections"
                textarea
                v-model="question_list[current_question].note"
              ></v-text-field>

              <!-- question direct -->
              <v-layout row justify-space-between>
                <v-btn large color="blue-grey" class="white--text" :disabled="current_question == 0" @click="move_pre()">
                  <v-icon left dark>chevron_left</v-icon>
                  Backward
                </v-btn>
                <v-btn large color="blue-grey" class="white--text" :disabled="current_question == question_list.length - 1" @click="move_next()">
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
export default {
  name: "Form",
  data() {
    return {
      // left drawer
      drawer: true,
      crossing: {
        //presented info
        id: "304562",
        component: "AWS",
        last_inspect_date: "01-20-2017",
        RSI_Name: "Jeffrey Young",
        RSI_Region: "ONT",
        railway_Maintainer: "Richky Klein",
        attendign_railway_emp: "Jay Price",
        address: "8731 Leuschke Roads East Audie",
        //detailed info
        english: "AWS: Mile 0 Mcmillan Bloedel Co Spur  23.9, Cartier Subdivision (Ottawa Valley Rail Link), Hwy 17  (Sturgeon Falls), (Y), CrossingID: 20517",
        french: "",
        status: "Active - Currently: In use by the Railway today",
        type: "Signals - AWS (Active Warning System)",
        in_service_from: "2014-06-26",
        in_service_to: "NaN",
        railway: "Ottawa Valley Railway",
        juris: "Federal",
        key_rt: "Unknown",
        subdivision: "Cartier",
        mile: 23.9,
        spur: "Mcmillan Bloedel Co",
        s_mile: 0, //in DB, this column is named "miles"
        province: "Ont.",
        region: "ONT",
        longitude: -79.9371,
        latitude: 46.3662,
        road_hwy: "Hwy 17  (Sturgeon Falls)",
        nearest_muni: "ON -Ministry of Transportation",
        GEO_text: "NaN",
        updated_by: "NaN",
        updated_on: "2017-03-27",  //in DB, this column is named "on"
        memo: "20517",
      },
      // pop-up dialog
      confirm_dialog: null,
      dialog_items: [
        {
          title: "Mark As Completed",
          subtitle:
            "The End Time stamp will be added to the checklist information."
        },
        {
          title: "End Inspection For Now",
          subtitle:
            "Your inspection work will be saved. You may continue later to complete the inspection."
        }
      ],
      // pop-up dialog for modifying crossing info
      info_dialog: null,
      info_dia_items: {
          english: "",
          french: "", //cannot use utf-8 for French
          status: null,
          types: null,
          inservice_from: false,
          inservice_to: false,
          railway: "",
          juris: null,
          key_rt: "",
          subdivision: "",
          mile: "",
          spur: "",
          s_mile: "",
          region: null,
          province: null,
          lat: "",
          lng: "",
          nearest_muni: "",
          road_hwy: "",
          geo_text: "",
          updatedby: "",
          updated_on:false,
          memo: ""
      },
      // info lists
      status_items: [
        'Active - Currently: In use by the Railway today',
        'Inactive - Permanent: Change of ownership',
        'Inactive - Permanent: Crossing road apprch removed',
        'Inactive - Permanent: Railway will not use again',
        'Inactive - Permanent: Track Abandoned',
        'Inactive - Temporary: may be re-used',
      ],
      type_items: [
        'Crossing (Passive)',
        'Signals - AWS (Active Warning System)',
      ],
      juris_items: [
        'Federal',
        'Provincial',
      ],
      region_items: [
        'ATL',
        'ONT',
        'PAC',
        'PNR',
        'QUE',
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
        'Y.T.',
      ],
      // date picker menu
      menu_inservice_from: false, 
      menu_inservice_to: false, 
      menu_updated_on: false, 
      // question panel
      transition: "slide-right",
      current_question: 0,
      question_dialog: false,
      question_list: [
        {
          id: "1a",
          text:
            "Track Bond Conditions (complete, undamaged, ensures conductivity)",
          answer: null,
          note: ""
        },
        {
          id: "1b",
          text: "Track lead Conditions (complete, undamaged, secured)",
          answer: null,
          note: ""
        },
        {
          id: "1c",
          text:
            "Insulated joint Conditions (end post intact, tie plates clear, bolts tight, spikes reversed,), prevent current from flowing between rails separated by the insulation.",
          answer: null,
          note: ""
        },
        {
          id: "1d",
          text:
            "Detect a shunt of 0.06 ohm resistance when the shunt is connected across the track rails of any part of the circuit. Adjustment, Contamination of track circuit, signal in vicinity.",
          answer: null,
          note: ""
        },
        {
          id: "1e",
          text:
            "Provide a set of fouling wires that consist of at least two discrete conductors and must ensure proper operation of the track circuit when the circuit is shunted. Single duplex wire with single plug is not permitted",
          answer: null,
          note: ""
        },
        {
          id: "1f",
          text:
            "Track circuit Polarity (Staggered and in conformity to the design plan)",
          answer: null,
          note: ""
        },
        {
          id: "2",
          text: "Warning Time insufficient, or Approach Time Variance",
          answer: null,
          note: ""
        },
        {
          id: "3",
          text: "Bell operation, location and configuration",
          answer: null,
          note: ""
        },
        {
          id: "4a",
          text:
            "Gate Conditions (arm covers lane, gate light condition, reflecting tape)",
          answer: null,
          note: ""
        },
        {
          id: "4b",
          text:
            "Operation of gates (Begin its descent once the calculated gate arm clearance time has elapsed (min 3 sec), descent time horizontal within 10 and 15 sec , ascent time within 6 to 12 seconds)",
          answer: null,
          note: ""
        },
        {
          id: "5a",
          text: "Interconnection - Railway (preemption time, perform test) ",
          answer: null,
          note: ""
        },
        {
          id: "5b",
          text:
            "Interconnection - Road Authority (preemption time, perform test)",
          answer: null,
          note: ""
        },
        {
          id: "6a",
          text:
            "Cut-out Control Feature (equipped with a control feature to minimize the operation of the warning system where required)",
          answer: null,
          note: ""
        },
        {
          id: "6b",
          text:
            "Switch Circuit Controller Cut Out (cuts out only when the switch point is within one-half inch of full reverse position (when equipped))",
          answer: null,
          note: ""
        },
        {
          id: "7a",
          text:
            "Light Unit Condition (physical condition of light units, compare lens deflection with plans, note type)",
          answer: null,
          note: ""
        },
        {
          id: "7b",
          text:
            "Light Unit Configuration (light units cover all road approaches)",
          answer: null,
          note: ""
        },
        {
          id: "7c",
          text:
            "Light Alignment (all lights visible to the appropriate alignment point)",
          answer: null,
          note: ""
        },
        {
          id: "7d",
          text:
            "Visibility of Warning System From SSD (at least one set of lights are visible from SSD)",
          answer: null,
          note: ""
        },
        {
          id: "7e",
          text:
            "Light Unit Flash Rate & Operation (flash alternately and uniformly and at the prescribed flash rate)",
          answer: null,
          note: ""
        },
        {
          id: "8a",
          text: "Record of weekly tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "8b",
          text:
            "Record of monthly, quarterly and semiannual tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "8c",
          text: "Record of annual tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "8d",
          text: "Record of two year tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "8e",
          text: "Record of Four Year Tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "8f",
          text: "Record of Ten Year Tests (no missing or overdue tests)",
          answer: null,
          note: ""
        },
        {
          id: "9",
          text:
            "Directional Stick Circuit (timing device, stick release, stick circuit indication)",
          answer: null,
          note: ""
        },
        {
          id: "10a",
          text:
            "Standby Battery Conditions (clean & free of corrosion, voltage reading taken pre/post 15 min power off)",
          answer: null,
          note: ""
        },
        {
          id: "10b",
          text:
            "AC Power and Components (phases consistent and equal under load)",
          answer: null,
          note: ""
        },
        {
          id: "10c",
          text: "Power Off Light (visible, functional)",
          answer: null,
          note: ""
        },
        {
          id: "11",
          text:
            "Battery Isolation Condition (no voltage potential between power supplies)",
          answer: null,
          note: ""
        },
        {
          id: "12",
          text:
            "Circuit Grounds (no leakage to ground, perform test while system is active and at rest)",
          answer: null,
          note: ""
        },
        {
          id: "13",
          text:
            "Relay Conditions (securely mounted, contact condition, no rust or debris in housing, properly identified, corresponds to the circuit plan)",
          answer: null,
          note: ""
        },
        {
          id: "14",
          text:
            "Design Plan Conditions (complete, accurate, legible, up to date)",
          answer: null,
          note: ""
        },
        {
          id: "15a",
          text:
            "Signage - Railway Responsibility (SRCS, # tracks, emergency notification, etc.)",
          answer: null,
          note: ""
        },
        {
          id: "15b",
          text:
            "Signage - Road Authority Responsibility (Railway Crossing Ahead, Advisory Speed tab, Prepare to Stop at Railway Crossing, etc.)",
          answer: null,
          note: ""
        },
        {
          id: "16a",
          text: "Railway Sightlines (at 5m, note quadrants)",
          answer: null,
          note: ""
        },
        {
          id: "17a",
          text: "Crossing Surface Condition",
          answer: null,
          note: ""
        },
        {
          id: "17b",
          text: "Crossing Surface Width",
          answer: null,
          note: ""
        },
        {
          id: "17c",
          text:
            "Crossing Surface Flangeway (width, depth, condition of the flangeway)",
          answer: null,
          note: ""
        },
        {
          id: "17d",
          text: "Crossing Road Approach Conditions",
          answer: null,
          note: ""
        },
        {
          id: "17e",
          text: "Crossing Road Approach Geometery",
          answer: null,
          note: ""
        },
        {
          id: "17f",
          text:
            "Location of Grade Crossing (Restrictions on the proximity of intersections and entranceways to grade crossings)",
          answer: null,
          note: ""
        },
        {
          id: "17g",
          text:
            "Stopping on the crossing surface (queuing or stopping on crossing surface issues)",
          answer: null,
          note: ""
        },
        {
          id: "18",
          text:
            "Unnecessary Activation of Warning System (Equipment left standing in a manner that causes the activation of the warning system at a public grade crossing other than for the purpose of crossing that grade crossing)",
          answer: null,
          note: ""
        },
        {
          id: "19",
          text:
            "Obstruction of Grade Crossing (equipment left standing on a crossing surface or for switching operations for more than five minutes in a manner that obstructs a public grade crossing or activates the gates when vehicle or pedestrian traffic waiting to cross)",
          answer: null,
          note: ""
        },
        {
          id: "20",
          text:
            "Evidence of Trespassing (cut fence, worn footpath, witnessed event)",
          answer: null,
          note: ""
        },
        {
          id: "21",
          text:
            "Whistle Cessation Concern (introduced since GCR but does not meet requirements, or pre-GCR but is concern for safety)",
          answer: null,
          note: ""
        },
        {
          id: "22a",
          text:
            "Persons performing duties did not appear familiar with equipment/components within their responsibility",
          answer: null,
          note: ""
        },
        {
          id: "22b",
          text:
            "Persons performing duties did not appear familiar with the railways inspection and testing requirements, and other procedures",
          answer: null,
          note: ""
        },
        {
          id: "22c",
          text:
            "Persons performing duties did not appear to have an understanding of safety requirements during testing, and/or how to protect safe railway operations during the undertaking of their duties",
          answer: null,
          note: ""
        },
        {
          id: "22d",
          text:
            "performing duties were not able to correctly answer all questions and perform all tasks requested by the RSI at the time of inspection, and/or did not appear confident and knowledgeable when implementing them",
          answer: null,
          note: ""
        },
        {
          id: "23",
          text:
            "Timetable, Logbook, Maintenance Manuals (all applicable information available, complete, correct)",
          answer: null,
          note: ""
        },
        {
          id: "24",
          text: "Light Units Voltage (within threshold)",
          answer: null,
          note: ""
        },
        {
          id: "25",
          text: "Software and Calibration",
          answer: null,
          note: ""
        },
        {
          id: "26",
          text: "Operational Tests (post repair/replacement)",
          answer: null,
          note: ""
        },
        {
          id: "27",
          text:
            "Switch Circuit Controller (Adjustment and condition, centering device condition)",
          answer: null,
          note: ""
        },
        {
          id: "28",
          text:
            "Condition of Structures (masts, ladders, platforms, bases, foundation, junction boxes)",
          answer: null,
          note: ""
        },
        {
          id: "29",
          text:
            "External Condition of Housing (foundations, door, locks, free of debris)",
          answer: null,
          note: ""
        },
        {
          id: "30",
          text:
            "Internal Condition of Housing (clean, sealed, ventilation, moisture, no evidence of infestation)",
          answer: null,
          note: ""
        },
        {
          id: "31",
          text:
            "Location Conditions (equipment, tagging, wiring, lighting arresters)",
          answer: null,
          note: ""
        },
        {
          id: "32",
          text:
            "Clearance - Vertical/Horizontal (light units, cantilever, gate)",
          answer: null,
          note: ""
        },
        {
          id: "33",
          text:
            "Operational Issues (queuing, departure time, hidden train operation, two+ tracks, standing railway equipment, crossing not used)",
          answer: null,
          note: ""
        },
        {
          id: "34",
          text:
            "Cables and Line Wires (pole line, aerial, underground wire, cable clearance)",
          answer: null,
          note: ""
        },
        {
          id: "35",
          text:
            "Misc. issues - Railway (other safety Issues of Railway responsibility)",
          answer: null,
          note: ""
        },
        {
          id: "36",
          text:
            "Misc. issues - Road Authority (other safety issues of Road Authority responsibility)",
          answer: null,
          note: ""
        }
      ]
    };
  },
  methods: {
    select_question(n) {
      this.current_question = n;
    },

    get_color(question, index) {
      if (index == this.current_question) {
        return "primary";
      } else if (question.answer == null) {
        return "blue-grey";
      } else {
        return "success";
      }
    },

    move_pre() {
      this.transition = "slide-left";
      this.current_question -= 1;
    },

    move_next() {
      this.transition = "slide-right";
      this.current_question += 1;
    }
  },
  computed: {
    answer_number: function() {
      return this.question_list.filter(question => {
        return question.answer != null;
      }).length;
    }
  }
};
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
  transform: translateX(30px);
  opacity: 0;
}
</style>