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
          <v-tab href="#tab-5">Crossing TSB</v-tab>
          <v-tab href="#tab-6">Crossing Info Sharing</v-tab>
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
                    <v-text-field :label=crossing.english single-line v-model="info_dia_tab1.english"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Long French</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field label="cannot decoding french" single-line v-model="info_dia_tab1.french"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Status *</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab1.status"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Type *</v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-select :label=crossing.type :items="type_items" single-line v-model="info_dia_tab1.type"></v-select>
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
                      :return-value.sync="info_dia_tab1.in_service_from"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_from
                        v-model="info_dia_tab1.in_service_from"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab1.in_service_from"        
                        @change="$refs.menu1.save(info_dia_tab1.in_service_from)"
                        >
                      </v-date-picker>
                    </v-menu>          
                    <!-- <v-text-field :label=crossing.in_service_from single-line v-model="info_dia_tab1.in_service_from"></v-text-field> -->
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
                      :return-value.sync="info_dia_tab1.in_service_to"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_to
                        v-model="info_dia_tab1.in_service_to"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab1.in_service_to" 
                        @change="$refs.menu2.save(info_dia_tab1.in_service_to)"
                        >
                      </v-date-picker>
                    </v-menu>   
                    <!-- <v-text-field :label=crossing.in_service_to single-line v-model="info_dia_tab1.in_service_to"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>Key Rt</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field :label=crossing.key_rt single-line v-model="info_dia_tab1.key_rt"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Railway </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.railway single-line v-model="info_dia_tab1.railway"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Jurisdiction *</v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.juris :items="juris_items" single-line v-model="info_dia_tab1.juris"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Subdivision </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.subdivision single-line v-model="info_dia_tab1.subdivision"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Mile </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.mile single-line v-model="info_dia_tab1.mile"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Spur </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.spur single-line v-model="info_dia_tab1.spur"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Mile </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.s_mile single-line v-model="info_dia_tab1.s_mile"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Province * </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.province :items="province_items" single-line v-model="info_dia_tab1.province"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Region </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.region :items="region_items" single-line v-model="info_dia_tab1.region"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Latitude </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.lat single-line v-model="info_dia_tab1.lat"></v-text-field>
                  </v-flex>              
                  <v-flex xs2 sm2>
                    <v-subheader>Longitude </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.lng single-line v-model="info_dia_tab1.lng"></v-text-field>
                  </v-flex> 
                  <v-flex xs2 sm2>
                    <v-subheader>Nearest Muni </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.nearest_muni single-line v-model="info_dia_tab1.nearest_muni"></v-text-field>
                  </v-flex>               
                  <v-flex xs2 sm2>
                    <v-subheader>Road/Hwy #Eng</v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.road_hwy single-line v-model="info_dia_tab1.road_hwy"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>GEO Text </v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field :label=crossing.geo_text single-line v-model="info_dia_tab1.geo_text"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Updatedby </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field :label=crossing.updated_by single-line v-model="info_dia_tab1.updated_by"></v-text-field>
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
                      :return-value.sync="info_dia_tab1.on"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.updated_on
                        v-model="info_dia_tab1.on"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab1.on"        
                        @change="$refs.menu3.save(info_dia_tab1.on)"
                        >
                      </v-date-picker>
                    </v-menu> 
                    <!-- <v-text-field :label=crossing.on single-line v-model="info_dia_tab1.on"></v-text-field> -->
                  </v-flex>    
                  <v-flex xs2 sm2>
                    <v-subheader>Memo </v-subheader>
                  </v-flex>
                  <v-flex xs10 sm10>
                    <v-text-field :label=crossing.memo single-line v-model="info_dia_tab1.memo"></v-text-field>
                  </v-flex>    
                </v-layout>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
          </v-card>
        </v-tab-item>
        <v-tab-item :id="'tab-2'">
          <v-card>
            <v-card-text>
              <v-container grid-list-md>
                <v-layout wrap>
                  <v-flex xs2 sm2>
                    <v-subheader>Type/TC ID: *</v-subheader>
                  </v-flex>
                  <v-flex xs6 sm6>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.typeID"></v-select>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>   / </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field label="  " single-line v-model="info_dia_tab2.typeID_num"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Access: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.access"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Railway: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.railway"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Jurisdiction: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="juris_items" single-line v-model="info_dia_tab2.juris"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Prov.: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="province_items" single-line v-model="info_dia_tab2.province"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Subdivision: *</v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="4.500" single-line v-model="info_dia_tab2.sub_t"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="province_items" single-line v-model="info_dia_tab2.sub_s"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Key Route: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="province_items" single-line v-model="info_dia_tab2.keyRoute"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Spur: *</v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="*** " single-line v-model="info_dia_tab2.spur_t"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="province_items" single-line v-model="info_dia_tab2.spur_s"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> GCR Appl.: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="province_items" single-line v-model="info_dia_tab2.gcr"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Pedestrain Protection: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.pedesP"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Whis. Cess.: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.whis"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Passive Protection: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.passiveP"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> WC Prov.: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field label="*** " single-line v-model="info_dia_tab2.wcProv"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>AWS Protection: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.aws"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> WC Auth.: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field label="*** " single-line v-model="info_dia_tab2.wcAuth"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Track Circuit: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.trackC"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Rd. Class 1: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.rdClass1"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>LED Lights: </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.led"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Data Recorder: </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.dataR"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Rd. Type: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.rdT"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Road Name/Hwy#: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-text-field label="Chemin Ste-Marie" single-line v-model="info_dia_tab2.rname"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Rd. Class 2: </v-subheader>
                  </v-flex>
                  <v-flex xs4 sm4>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.rdClass2"></v-select>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Road Auth. #1: </v-subheader>
                  </v-flex>
                  <v-flex xs8 sm8>
                    <v-text-field label="Saint-Polucanpe (QC)" single-line v-model="info_dia_tab2.rAuth1"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-checkbox label="Private" single-line v-model="info_dia_tab2.private_1"></v-checkbox>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Road Auth. #2: </v-subheader>
                  </v-flex>
                  <v-flex xs8 sm8>
                    <v-text-field label="Chemin Ste-Marie xxxx" single-line v-model="info_dia_tab2.rAuth2"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-checkbox label="Private" single-line v-model="info_dia_tab2.private_2"></v-checkbox>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader> Exemption: </v-subheader>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.exemp_s"></v-select>
                  </v-flex>
                  <v-flex xs8 sm8>
                    <v-text-field label="Chemin Ste-Marie" single-line v-model="info_dia_tab2.exemp_t"></v-text-field>
                  </v-flex>    
                  <v-flex xs2 sm2>
                    <v-subheader>Last Inspected</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-menu
                      ref="menu2_1"
                      lazy
                      :close-on-content-click="false"
                      v-model="memu_last_inspected"
                      :return-value.sync="info_dia_tab1.last_inspected"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_from
                        v-model="info_dia_tab2.last_inspected"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab2.last_inspected"        
                        @change="$refs.menu2_1.save(info_dia_tab2.last_inspected)"
                        >
                      </v-date-picker>
                    </v-menu>          
                    <!-- <v-text-field :label=crossing.in_service_from single-line v-model="info_dia_tab2.in_service_from"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader> By: </v-subheader>
                  </v-flex>
                  <v-flex xs6 sm6>
                    <v-text-field label=" Shuangsho" single-line v-model="info_dia_tab2.by_inspetec"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>Last Comp.A Insp</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-menu
                      ref="menu2_2"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_last_comp"
                      :return-value.sync="info_dia_tab2.last_comp"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_from
                        v-model="info_dia_tab2.last_comp"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab2.last_comp"        
                        @change="$refs.menu2_2.save(info_dia_tab2.last_comp)"
                        >
                      </v-date-picker>
                    </v-menu>          
                    <!-- <v-text-field :label=crossing.in_service_from single-line v-model="info_dia_tab2.in_service_from"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>By: </v-subheader>
                  </v-flex>
                  <v-flex xs6 sm6>
                    <v-text-field label=" Shuangsho" single-line v-model="info_dia_tab2.by_comp"></v-text-field>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-subheader>In Service From</v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-menu
                      ref="menu2_3"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_inservice_from_tab2"
                      :return-value.sync="info_dia_tab1.in_service_from"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_from
                        v-model="info_dia_tab1.in_service_from"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab1.in_service_from"        
                        @change="$refs.menu2_3.save(info_dia_tab1.in_service_from)"
                        >
                      </v-date-picker>
                    </v-menu>          
                    <!-- <v-text-field :label=crossing.in_service_from single-line v-model="info_dia_tab1.in_service_from"></v-text-field> -->
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-subheader>To</v-subheader>
                  </v-flex>
                  <v-flex xs2 sm2>
                    <v-menu
                      ref="menu2_4"
                      lazy
                      :close-on-content-click="false"
                      v-model="menu_inservice_to_tab2"
                      :return-value.sync="info_dia_tab1.in_service_to"
                    >
                      <v-text-field
                        slot="activator"
                        single-line
                        :label=crossing.in_service_to
                        v-model="info_dia_tab1.in_service_to"
                      ></v-text-field>
                      <v-date-picker 
                        v-model="info_dia_tab1.in_service_to" 
                        @change="$refs.menu2_4.save(info_dia_tab1.in_service_to)"
                        >
                      </v-date-picker>
                    </v-menu>   
                    <!-- <v-text-field :label=crossing.in_service_to single-line v-model="info_dia_tab1.in_service_to"></v-text-field> -->
                  </v-flex>              
                  <v-flex xs1 sm1>
                    <v-subheader>Status: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab2.status_s"></v-select>
                  </v-flex>
                </v-layout>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
          </v-card>
        </v-tab-item>
        <v-tab-item :id="'tab-3'">
          <v-card>
            <v-card-text>
              <v-container grid-list-md>
                <v-layout wrap>
                  <v-flex xs3 sm3>
                    <v-subheader>#Track: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field label="1" single-line v-model="info_dia_tab3.track"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Rail Approach: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.rail_app"></v-select>
                  </v-flex>  
                  <v-flex xs3 sm3>
                    <v-subheader>#Lanes: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field label="2" single-line v-model="info_dia_tab3.lanes"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Raod Approach: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.road_app"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Sildewalks(SW): </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.sw"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Approach Surface: </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.aSur1"></v-select>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.aSur2"></v-select>
                  </v-flex> 
                  <v-flex xs3 sm3>
                    <v-subheader>SW for Assistive Dev.: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.swAD"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Gradient SSD (Percentage): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="0.00" single-line v-model="info_dia_tab3.ssd1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="200.00" single-line v-model="info_dia_tab3.ssd2"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Area Lighting: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.al"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Gradient Stopped (Percentage): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.gs1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.gs2"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Track Type: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.trackType"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>DSSD (Meters): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.dssd1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.dssd2"></v-text-field>
                  </v-flex>        
                  <v-flex xs3 sm3>
                    <v-subheader>Incr. Mile Direct.: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.incrDirec"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>DStoppped (Meters): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.dstop1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.dstop2"></v-text-field>
                  </v-flex>      
                  <v-flex xs3 sm3>
                    <v-subheader>Track Angle (Degree): </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-text-field label="80/00" single-line v-model="info_dia_tab3.trackAngle"></v-text-field>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Departure (Seconds): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.depart1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.depart2"></v-text-field>
                  </v-flex>   
                  <v-flex xs3 sm3>
                    <v-subheader>Prepare to Stop Sign: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.prepareStop"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Advance Activation (Seconds): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.aActivation1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.aActivation2"></v-text-field>
                  </v-flex>      
                  <v-flex xs3 sm3>
                    <v-subheader>Intercon. Traffic Signal: </v-subheader>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-select :label=crossing.status :items="status_items" single-line v-model="info_dia_tab3.intercon"></v-select>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Preemption Time (Seconds): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.preemp1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.preemp2"></v-text-field>
                  </v-flex>
                  <v-flex xs6 sm6>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Distance to Intersections (Meters): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.disIntersection1"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.disIntersection2"></v-text-field>
                  </v-flex>   
                  <v-flex xs6 sm6>
                  </v-flex>
                  <v-flex xs3 sm3>
                    <v-subheader>Clearance Distance (Meters): </v-subheader>
                  </v-flex>
                  <v-flex xs1 sm1>
                    <v-text-field label="__" single-line v-model="info_dia_tab3.clearDis"></v-text-field>
                  </v-flex>     
                  <v-flex xs1 sm1>
                  </v-flex>                    
                  <v-flex xs1 sm1>
                  </v-flex>
                  <v-flex xs12 sm12>
                    Comment for Approach Direction Column #1:
                  </v-flex> 
                  <v-flex xs12 sm12>
                    <v-text-field label="  " single-line v-model="info_dia_tab3.comment1"></v-text-field>
                  </v-flex>                                                                                          
                  <v-flex xs12 sm12>
                    Comment for Approach Direction Column #2:
                  </v-flex> 
                  <v-flex xs12 sm12>
                    <v-text-field label="  " single-line v-model="info_dia_tab3.comment2"></v-text-field>
                  </v-flex> 
                </v-layout>
              </v-container>
            </v-card-text>
          </v-card>
        </v-tab-item>
        <v-tab-item :id="'tab-4'">
          <v-card>
            <v-card-title>
              <div>
                <h3 class="title-text">Train Traffic</h3>
              </div>
            </v-card-title>
            <v-data-table
              :headers="headers_train"
              :items="train_traffic"
              class="elevation-1"
              :pagination.sync="pagination_train"
            >
              <template slot="items" slot-scope="props">
                <td>{{ props.item.effective }}</td>
                <td class="text-xs-right">{{ props.item.nTrains }}</td>
                <td class="text-xs-right">{{ props.item.nPTrains }}</td>
                <td class="text-xs-right">{{ props.item.nFTrains }}</td>
                <td class="text-xs-right">{{ props.item.maxSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.maxSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.PmaxSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.PmaxSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.FmaxSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.FmaxSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.RSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.RSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.memo }}</td>
              </template>
              <template slot="no-data">
                <v-alert :value="true" color="cyan darken-1" icon="warning">
                  Sorry, nothing to display here :(
                </v-alert>
              </template>
            </v-data-table>
          </v-card>
          <v-card>
            <v-card-title>
              <div>
                <h3 class="title-text">Vehicle Traffic</h3>
              </div>
            </v-card-title>
            <v-data-table
              :headers="headers_vehicle"
              :items="vehicle_traffic"
              class="elevation-1"
              :pagination.sync="pagination_vehicle"
            >
              <template slot="items" slot-scope="props">
                <td>{{ props.item.effective }}</td>
                <td class="text-xs-right">{{ props.item.nVehicles }}</td>
                <td class="text-xs-right">{{ props.item.nPedestrains }}</td>
                <td class="text-xs-right">{{ props.item.RPSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.RPSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.RCSpeedL }}</td>
                <td class="text-xs-right">{{ props.item.RCSpeedR }}</td>
                <td class="text-xs-right">{{ props.item.DVehicle }}</td>
                <td class="text-xs-right">{{ props.item.ISource }}</td>
                <td class="text-xs-right">{{ props.item.memo }}</td>
                <td class="text-xs-right">{{ props.item.CPPerdestrain }}</td>
                <td class="text-xs-right">{{ props.item.CPVehicle }}</td>
              </template>
              <template slot="no-data">
                <v-alert :value="true" color="cyan darken-1" icon="warning">
                  Sorry, nothing to display here :(
                </v-alert>
              </template>
            </v-data-table>            
          </v-card>
        </v-tab-item>
        <v-tab-item :id="'tab-5'">
          <v-card>
            <v-card-title>
              <div>
                <h3 class="title-text">Vehicle / Pedestrain Accidents</h3>
              </div>
            </v-card-title>
            <v-data-table
              :headers="headers_accident"
              :items="vpAccident"
              class="elevation-1"
              :pagination.sync="pagination_accident"
            >
              <template slot="items" slot-scope="props">
                <td>{{ props.item.occurNum }}</td>
                <td class="text-xs-right">{{ props.item.occurYear }}</td>
                <td class="text-xs-right">{{ props.item.occurDate }}</td>
                <td class="text-xs-right">{{ props.item.occurTime }}</td>
                <td class="text-xs-right">{{ props.item.occurType }}</td>
                <td class="text-xs-right">{{ props.item.occurIncType }}</td>
                <td class="text-xs-right">{{ props.item.operator1 }}</td>
                <td class="text-xs-right">{{ props.item.operator2 }}</td>
                <td class="text-xs-right">{{ props.item.train1 }}</td>
                <td class="text-xs-right">{{ props.item.train2 }}</td>
                <td class="text-xs-right">{{ props.item.involvedTrain }}</td>
                <td class="text-xs-right">{{ props.item.involvedCars }}</td>
                <td class="text-xs-right">{{ props.item.involvedDCars }}</td>
                <td class="text-xs-right">{{ props.item.nearestTown }}</td>
                <td class="text-xs-right">{{ props.item.trackType }}</td>
                <td class="text-xs-right">{{ props.item.derail }}</td>
                <td class="text-xs-right">{{ props.item.taInjuries }}</td>
                <td class="text-xs-right">{{ props.item.taFatal }}</td>
                <td class="text-xs-right">{{ props.item.taSerious }}</td>
                <td class="text-xs-right">{{ props.item.taMinor }}</td>
                <td class="text-xs-right">{{ props.item.tfEmp }}</td>
                <td class="text-xs-right">{{ props.item.tfPass }}</td>
                <td class="text-xs-right">{{ props.item.tfMoto }}</td>
                <td class="text-xs-right">{{ props.item.tfMotoPass }}</td>
                <td class="text-xs-right">{{ props.item.tfTrespass }}</td>
                <td class="text-xs-right">{{ props.item.tfOther }}</td>
                <td class="text-xs-right">{{ props.item.tsEmp }}</td>
                <td class="text-xs-right">{{ props.item.tsPass }}</td>
                <td class="text-xs-right">{{ props.item.tsMoto }}</td>
                <td class="text-xs-right">{{ props.item.tsMotoPass }}</td>
                <td class="text-xs-right">{{ props.item.tsTraspass }}</td>
                <td class="text-xs-right">{{ props.item.tsOthers }}</td>
                <td class="text-xs-right">{{ props.item.tmEmp }}</td>
                <td class="text-xs-right">{{ props.item.tmPass }}</td>
                <td class="text-xs-right">{{ props.item.tmMoto }}</td>
                <td class="text-xs-right">{{ props.item.tmMotoPass }}</td>
                <td class="text-xs-right">{{ props.item.tmTrespass }}</td>
                <td class="text-xs-right">{{ props.item.tmOthers }}</td>
                <td class="text-xs-right">{{ props.item.cause1 }}</td>
                <td class="text-xs-right">{{ props.item.cause2 }}</td>
                <td class="text-xs-right">{{ props.item.cause3 }}</td>
                <td class="text-xs-right">{{ props.item.occuSummary }}</td>
              </template>
              <template slot="no-data">
                <v-alert :value="true" color="cyan darken-1" icon="warning">
                  Sorry, nothing to display here :(
                </v-alert>
              </template>
            </v-data-table>
          </v-card>
        </v-tab-item>
        <v-tab-item :id="'tab-6'">
          <v-card>
            <v-card-title>
              <div>
                <h3 class="title-text">Information Shring Details</h3>
              </div>
            </v-card-title>
            <v-data-table
              :headers="headers_info"
              :items="infoSharing"
              class="elevation-1"
              :pagination.sync="pagination_info"
            >
              <template slot="items" slot-scope="props">
                <td>{{ props.item.shareby }}</td>
                <td class="text-xs-right">{{ props.item.lastUpdated }}</td>
                <td class="text-xs-right">{{ props.item.subDate }}</td>
                <td class="text-xs-right">{{ props.item.reason }}</td>
                <td class="text-xs-right">{{ props.item.memo }}</td>
              </template>
              <template slot="no-data">
                <v-alert :value="true" color="cyan darken-1" icon="warning">
                  Sorry, nothing to display here :(
                </v-alert>
              </template>
            </v-data-table>
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
      info_dia_tab1: {
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
      info_dia_tab2: {
        typeID: null,
        typeID_num: '',
        access: null,
        railyway: null,
        juris: null,
        prov: null,
        sub_t: '',
        sub_s: null,
        keyRoute: null,
        spur_t: '',
        spur_s: null,
        gcr: null,
        pedesP: null,
        whis: null,
        passiveP: null,
        wcProv: '',
        aws: null,
        wcAuth: '',
        trackC: null,
        rdClass1: null,
        led: null,
        dataR: null,
        rdT: null,
        rname: '',
        rdClass2: null,
        rAuth1: '',
        private_1: '',
        rAuth2: '',
        private_2: '',
        exemp_s: null,
        exemp_t: '',
        last_inspected: false,
        by_inspetec: '',
        last_comp: false,
        by_comp: '',
        status_s: null
      },
      info_dia_tab3: {
        track: '',
        rail_app: null,
        lanes: '',
        road_app: null,
        sw: null,
        aSur1: null,
        aSur2: null,
        swAD: null,
        ssd1: '',
        ssd2: '',
        al: null,
        gs1: '',
        gs2: '',
        trackType: null,
        dssd1: '',
        dssd2: '',
        incrDirec: null,
        dstop1: '',
        dstop2: '',
        trackAngle: '',
        depart1: '',
        depart2: '',
        prepareStop: null,
        aActivation1: '',
        aActivation2: '',
        intercon: null,
        preemp1: '',
        preemp2: '',
        disIntersection1: '',
        disIntersection2: '',
        clearDis: '',
        comment1: '',
        comment2: ''
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
      menu_inservice_from_tab2: false,
      menu_inservice_to_tab2: false,
      menu_updated_on: false,
      memu_last_inspected: false,
      menu_last_comp: false,
      inventory_tabs: null,
      // question panel
      transition: 'slide-right',
      current_question: 0,
      question_dialog: false,
      question_list: [],
      current_list: [],
      headers_train: [
        { text: 'Effective As Of', align: 'right', value: 'effective' },
        { text: '# of Trains Per Day', align: 'center', value: 'nTrains' },
        { text: '# of Pass Trains Per Day', align: 'center', value: 'nPTrains' },
        { text: '# of Freight Trains Per Day', align: 'center', value: 'nFTrains' },
        { text: 'Overall Maximum Speed (MPH) for Rail Approach from Left', value: 'maxSpeedL', align: 'center' },
        { text: 'Overall Maximum Speed (MPH) for Rail Approach from Right', value: 'maxSpeedR', align: 'center' },
        { text: 'Passengers Maximum Speed (MPH) for Rail Approach from Left', value: 'PmaxSpeedL', align: 'center' },
        { text: 'Passengers Maximum Speed (MPH) for Rail Approach from Right', value: 'PmaxSpeedR', align: 'center' },
        { text: 'Freight Maximum Speed (MPH) for Rail Approach from Left', value: 'FmaxSpeedL', align: 'center' },
        { text: 'Freight Maximum Speed (MPH) for Rail Approach from Right', value: 'FmaxSpeedR', align: 'center' },
        { text: 'Rail Design Speed (MPH) for Rail Approach from Left', value: 'RSpeedL', align: 'center' },
        { text: 'Rail Design Speed (MPH) for Rail Approach from Right', value: 'RSpeedR', align: 'center' },
        { text: 'Memo                   ', value: 'memo', align: 'center', sortable: false }
      ],
      pagination_train: {},
      train_traffic: [
        {
          value: false,
          effective: '2003/01/01',
          nTrains: 16800.00,
          nPTrains: 1200.00,
          nFTrains: 800.00,
          maxSpeedL: 120.00,
          maxSpeedR: 120.00,
          PmaxSpeedL: 90.00,
          PmaxSpeedR: 90.00,
          FmaxSpeedL: 80.00,
          FmaxSpeedR: 80.00,
          RSpeedL: 85.00,
          RSpeedR: 85.00,
          memo: 'good good'
        },
        {
          value: false,
          effective: '2003/01/02',
          nTrains: 16800.00,
          nPTrains: 1200.00,
          nFTrains: 800.00,
          maxSpeedL: 120.00,
          maxSpeedR: 120.00,
          PmaxSpeedL: 90.00,
          PmaxSpeedR: 90.00,
          FmaxSpeedL: 80.00,
          FmaxSpeedR: 80.00,
          RSpeedL: 85.00,
          RSpeedR: 85.00,
          memo: 'good good'
        },
        {
          value: false,
          effective: '2003/01/03',
          nTrains: 16800.00,
          nPTrains: 1200.00,
          nFTrains: 800.00,
          maxSpeedL: 120.00,
          maxSpeedR: 120.00,
          PmaxSpeedL: 90.00,
          PmaxSpeedR: 90.00,
          FmaxSpeedL: 80.00,
          FmaxSpeedR: 80.00,
          RSpeedL: 85.00,
          RSpeedR: 85.00,
          memo: 'good good'
        }
      ],
      headers_vehicle: [
        { text: 'Effective As Of', align: 'right', value: 'effective' },
        { text: '# of Vehicles Per Day', align: 'center', value: 'nVehicles' },
        { text: '# of Pedestrains Per Day', align: 'center', value: 'nPedestrains' },
        { text: 'Road Posted/Unposted Speed (KPH) for Road Approach From Left', align: 'RPSpeedL', value: 'center' },
        { text: 'Road Posted/Unposted Speed (KPH) for Road Approach From Right', value: 'RPSpeedR', align: 'center' },
        { text: 'Road Crossing Design Speed (KPH) for Road Approach From Left', value: 'RCSpeedL', align: 'center' },
        { text: 'Road Crossing Design Speed (KPH) for Road Approach From Right', value: 'RCSpeedR', align: 'center' },
        { text: 'Design Vehicle', value: 'DVehicle', align: 'center' },
        { text: 'Information Source', value: 'ISource', align: 'center' },
        { text: 'Memo                   ', value: 'memo', align: 'center', sortable: false },
        { text: 'Cross Product Daily Train by Perdestrain', value: 'CPPerdestrain', align: 'center' },
        { text: 'Cross Product Daily Train by Vehicle', value: 'CPVehicle', align: 'center' }
      ],
      pagination_vehicle: {},
      vehicle_traffic: [
        {
          value: false,
          effective: '2003/02/01',
          nVehicles: 16800.00,
          nPedestrains: 1200.00,
          RPSpeedL: 800.00,
          RPSpeedR: 120.00,
          RCSpeedL: 120.00,
          RCSpeedR: 90.00,
          DVehicle: 90.00,
          ISource: 80.00,
          memo: 'good good',
          CPPerdestrain: 85.00,
          CPVehicle: 85.00
        }
      ],
      headers_accident: [
        { text: 'Occurence #', align: 'center', value: 'occurNum' },
        { text: 'Occurence Year', align: 'center', value: 'occurYear' },
        { text: 'Occurence Date', align: 'center', value: 'occurDate' },
        { text: 'Occurence Time', align: 'center', value: 'occurTime' },
        { text: 'Occurence Type', align: 'center', value: 'occurType' },
        { text: 'Occurence Acc. Inc. Type', align: 'center', value: 'occurIncType' },
        { text: 'Train Operator #1', align: 'center', value: 'operator1' },
        { text: 'Train Operator #2', align: 'center', value: 'operator2' },
        { text: 'Train Type #1', align: 'center', value: 'train1' },
        { text: 'Train Type #2', align: 'center', value: 'train2' },
        { text: 'Involved # of Trains', align: 'center', value: 'involvedTrain' },
        { text: 'Involved # of Cars', align: 'center', value: 'involvedCars' },
        { text: 'Involved # of Dangerous Goods Cars', align: 'center', value: 'involvedDCars' },
        { text: 'Nearest Town / City', align: 'center', value: 'nearestTown' },
        { text: 'Track Type', align: 'center', value: 'trackType' },
        { text: 'Derailment', align: 'center', value: 'derail' },
        { text: 'Total All Injuries', align: 'center', value: 'taInjuries' },
        { text: 'Total All Fatal', align: 'center', value: 'taFatal' },
        { text: 'Total All Serious', align: 'center', value: 'taSerious' },
        { text: 'Total All Minor', align: 'center', value: 'taMinor' },
        { text: 'Total Fatal Employees', align: 'center', value: 'tfEmp' },
        { text: 'Total Fatal Passengers', align: 'center', value: 'tfPass' },
        { text: 'Total Fatal Motorists', align: 'center', value: 'tfMoto' },
        { text: 'Total Fatal Motorist Passengers', align: 'center', value: 'tfMotoPass' },
        { text: 'Total Fatal Trespassers', align: 'center', value: 'tfTrespass' },
        { text: 'Total Fatal Others', align: 'center', value: 'tfOther' },
        { text: 'Total Serious Employees', align: 'center', value: 'tsEmp' },
        { text: 'Total Serious Passengers', align: 'center', value: 'tsPass' },
        { text: 'Total Serious Motorists', align: 'center', value: 'tsMoto' },
        { text: 'Total Serious Motorist Passengers', align: 'center', value: 'tsMotoPass' },
        { text: 'Total Serious Trespassers', align: 'center', value: 'tsTraspass' },
        { text: 'Total Serious Others', align: 'center', value: 'tsOthers' },
        { text: 'Total Minor Employees', align: 'center', value: 'tmEmp' },
        { text: 'Total Minor Passengers', align: 'center', value: 'tmPass' },
        { text: 'Total Minor Motorists', align: 'center', value: 'tmMoto' },
        { text: 'Total Minor Motorist Passengers', align: 'center', value: 'tmMotoPass' },
        { text: 'Total Minor Trespassers', align: 'center', value: 'tmTrespass' },
        { text: 'Total Minor Others', align: 'center', value: 'tmOthers' },
        { text: 'Primary Cause Level 1', align: 'center', value: 'cause1' },
        { text: 'Primary Cause Level 2', align: 'center', value: 'cause2' },
        { text: 'Primary Cause Level 3', align: 'center', value: 'cause3' },
        { text: 'Occurence Summary', align: 'center', value: 'occuSummary' }
      ],
      pagination_accident: {},
      vpAccident: [

      ],
      headers_info: [
        { text: 'Shared By', align: 'left', value: 'shareby' },
        { text: 'Last Updated Date', align: 'left', value: 'lastUpdated' },
        { text: 'Submission Date', align: 'left', value: 'subDate' },
        { text: 'Shared Reason', align: 'left', value: 'reason' },
        { text: 'Shared Memo', align: 'left', value: 'memo' }
      ],
      pagination_info: {},
      infoSharing: [

      ]
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