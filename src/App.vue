<template>
  <div id="app">
    <v-app>
      <v-container fluid justify-center grid-list-lg>
        <v-layout column>  
          
          <h4 class="text-lg-right subheading mb-3">Phaidra Vue Components Example</h4>
        
          <v-flex xs4>
            <v-alert v-for="(alert, i) in alerts" :type="(alert.type === 'danger' ? 'error' : alert.type)" :value="true" transition="slide-y-transition" :key="i">
              <v-layout row><v-flex class="pa-3">{{alert.msg}}</v-flex><v-spacer></v-spacer><v-btn icon @click.native="dismiss(alert)"><v-icon>close</v-icon></v-btn></v-layout>
            </v-alert>
          </v-flex>

          <v-layout row>  
            <v-flex xs5>
              <v-text-field v-model="solrbaseurl" :label="'solr'"></v-text-field>
            </v-flex>
            <v-flex xs4>
              <v-text-field v-model="apibaseurl" :label="'phaidra-api'"></v-text-field>
            </v-flex>
            <template v-if="token">
              <v-flex xs5>
                <h3 class="font-weight-light pt-4">Logged in [{{ token }}]</h3>
              </v-flex>
              <v-flex xs1>
                <v-btn raised single-line color="primary lighten-2" class="mt-3" @click="logout()">Logout</v-btn>
              </v-flex>
            </template>
            <template v-else>
              <v-flex xs2> 
                <v-text-field v-model="credentials.username" :label="'username'" ></v-text-field>
              </v-flex>
              <v-flex xs2>
                <v-text-field 
                  v-model="credentials.password" 
                  :label="'password'" 
                  :append-icon="psvis ? 'visibility' : 'visibility_off'"
                  @click:append="toggleVisibility"
                  :type="psvis ? 'password' : 'text'"
                ></v-text-field>
              </v-flex>
              <v-flex xs1>
                <v-btn raised single-line color="primary lighten-2" class="mt-3" @click="login()">Login</v-btn>
              </v-flex>
            </template>
          </v-layout> 

          <v-layout row> 
            <v-flex xs2>
              <v-navigation-drawer permanent>
                <v-toolbar flat>
                  <v-list>
                    <v-list-tile>
                      <v-list-tile-title class="title">{{ $t('Examples') }}</v-list-tile-title>
                    </v-list-tile>
                  </v-list>
                </v-toolbar>
                <v-divider></v-divider>
                <v-list>
                  <v-item-group v-model="window" class="shrink mr-4" mandatory tag="v-flex">
                    <v-item>
                      <div slot-scope="{ active, toggle }">
                        <v-list-tile @click="toggle">
                          <v-list-tile-content>
                            <v-list-tile-title>{{ $t('Display') }}</v-list-tile-title>
                          </v-list-tile-content>
                        </v-list-tile>
                      </div>
                    </v-item>
                    <v-item>
                      <div slot-scope="{ active, toggle }">
                        <v-list-tile @click="toggle">
                          <v-list-tile-content>
                            <v-list-tile-title>{{ $t('Edit') }}</v-list-tile-title>
                          </v-list-tile-content>
                        </v-list-tile>
                      </div>
                    </v-item>
                    <v-item>
                      <div slot-scope="{ active, toggle }">
                        <v-list-tile @click="toggle">
                          <v-list-tile-content>
                            <v-list-tile-title>{{ $t('Submit') }}</v-list-tile-title>
                          </v-list-tile-content>
                        </v-list-tile>
                      </div>
                    </v-item>
                  </v-item-group>
                </v-list>
              </v-navigation-drawer>
            </v-flex>

            <v-flex>
              <v-window v-model="window">
                <v-window-item>
                  <v-flex>
                    <v-card>
                      <v-toolbar flat>
                        <v-toolbar-title>Display</v-toolbar-title>
                        <v-divider class="mx-3" inset vertical></v-divider>
                        <v-text-field v-model="pid" :placeholder="'o:123456789'"></v-text-field>
                        <v-spacer></v-spacer>
                        <v-btn raised single-line class="right" color="primary lighten-2" @click="loadDisplay()">Load</v-btn>
                      </v-toolbar>
                      <v-card-text>
                        <p-d-jsonld 
                          ref="display"
                          :pid="pid"
                          :jsonld="displayjsonld"
                          v-on:load-jsonld="displayjsonld = $event"
                        ></p-d-jsonld>
                      </v-card-text>
                    </v-card>
                  </v-flex>
                </v-window-item>
                <v-window-item>
                  <v-flex>
                    <v-card>
                      <v-toolbar flat>
                        <v-toolbar-title>Edit</v-toolbar-title>
                        <v-divider class="mx-3" inset vertical></v-divider>
                        <v-text-field v-model="pid" :placeholder="'o:123456789'"></v-text-field>
                        <v-spacer></v-spacer>
                        <v-btn raised single-line class="right" color="primary lighten-2" @click="loadEdit()">Load</v-btn>
                      </v-toolbar>
                      <v-card-text>
                        <p-i-form 
                          ref="edit"
                          :form="editform"
                          :templating="false"
                          v-on:load-form="editform = $event"
                          v-on:object-saved="objectSaved($event)"
                        ></p-i-form>
                      </v-card-text>
                    </v-card>
                  </v-flex>
                </v-window-item>
                <v-window-item>
                  <v-flex>
                    <v-card>
                      <v-toolbar flat>
                        <v-toolbar-title>Submit</v-toolbar-title>
                        <v-divider class="mx-3" inset vertical></v-divider>
                        <v-spacer></v-spacer>
                      </v-toolbar>
                      <v-card-text>
                        <p-i-form
                          :form="form"
                          :contentmodel="'container'" 
                          :addbutton="false"
                          v-on:object-created="objectCreated($event)"
                          v-on:load-form="form = $event"
                        ></p-i-form>
                      </v-card-text>
                    </v-card>
                  </v-flex>
                </v-window-item>
              </v-window>
            </v-flex>

          </v-layout>
        </v-layout>
      </v-container>
    </v-app>
  </div>
</template>

<script>
import fields from 'phaidra-vue-components/src/utils/fields'

export default {
  name: 'app',
  computed: {
    token: function() {
      return this.$store.state.user.token
    },
    alerts: function () {
      return this.$store.state.alerts.alerts
    }
  },
  data () {
    return {
      window: 2,
      displayjsonld: {},
      editform: {},
      form: {
        sections: [
          {
            title: 'Thesis container',
            id: 1,
            fields: []
          },
          {
            title: 'Thesis document',
            id: 2,
            type: 'member',
            multiplicable: true,
            fields: []
          }
        ]
      },
      pid: '',
      solrbaseurl: 'https://app01.cc.univie.ac.at:8983/solr/phaidra_sandbox',
      apibaseurl: 'https://services.phaidra-sandbox.univie.ac.at/api',
      credentials: {
        username: '',
        password: ''
      },
      psvis: true
    }
  },
  methods: {
    loadDisplay: function() {
      this.$refs.display.loadMetadata(this.pid)
    },
    loadEdit: function() {
      this.editform = {}
      this.$refs.edit.loadMetadata(this.pid)
    },
    login: function () {
      this.$store.dispatch('login', this.credentials)
    },
    logout: function () {
      this.$store.dispatch('logout')
      document.cookie = 'X-XSRF-TOKEN='
    },
    objectCreated: function (event) {
      this.$store.commit('setAlerts', [{ type: 'success', msg: 'Object ' + event + ' created' }])
    },
    objectSaved: function (event) {
      this.$store.commit('setAlerts', [{ type: 'success', msg: 'Metadata for object ' + event + ' saved' }])
    },
    toggleVisibility: function () {
      this.psvis = !this.psvis
    },    
    dismiss: function (alert) {
      this.$store.commit('clearAlert', alert)
    },
    getCookie: function (name) {
      var value = "; " + document.cookie
      var parts = value.split("; " + name + "=")
      if (parts.length == 2) {
        var val = parts.pop().split(";").shift()
        return val === ' ' ? null : val
      }
    }
  },
  mounted: function () {
    var token = this.getCookie('X-XSRF-TOKEN')
    if (token) {
      this.$store.commit('setToken', token)
    }

    this.$store.commit('setInstanceApi', this.apibaseurl)
    this.$store.commit('setInstanceSolr', this.solrbaseurl)
    this.$store.commit('setSuggester', { suggester: 'getty', url: 'https://ws.gbv.de/suggest/getty/' })

    // container

    // resource type
    var rt = fields.getField('resource-type')
    rt.value = 'https://pid.phaidra.org/vocabulary/8MY0-BQDQ'
    this.form.sections[0].fields.push(rt)

    // thesis type
    var genre = fields.getField('genre')
    genre.value = 'https://pid.phaidra.org/vocabulary/P2YP-BMND'
    this.form.sections[0].fields.push(genre)

    // title
    this.form.sections[0].fields.push(fields.getField('title'))

    // translated title
    var translatedTitle = fields.getField('title')
    translatedTitle.label = this.$t('Translated title')
    translatedTitle.type = 'bf:ParallelTitle'
    this.form.sections[0].fields.push(translatedTitle)

    // author
    var aut = fields.getField('role')
    aut.role = 'role:aut'
    this.form.sections[0].fields.push(aut)

    // abstract
    this.form.sections[0].fields.push(fields.getField('abstract'))

    // keywords
    this.form.sections[0].fields.push(fields.getField('keyword'))

    // 'basis' classification
    var subject = fields.getField('subject')
    subject.vocabulary = 'basisklassifikation'
    subject.label = 'Basisklassifikation'
    this.form.sections[0].fields.push(subject)

    // study plan
    this.form.sections[0].fields.push(fields.getField('study-plan'))

    // advisor
    var advisor = fields.getField('role')
    advisor.role = 'role:advisor'
    this.form.sections[0].fields.push(advisor)

    // co-advisor
    var coadvisor = fields.getField('role')
    coadvisor.role = 'role:coadvisor'
    this.form.sections[0].fields.push(coadvisor)

    // 1. assesor
    var assessor = fields.getField('role')
    assessor.role = 'role:assessor'
    this.form.sections[0].fields.push(assessor)

    // 2. assessor
    var assessor2 = fields.getField('role')
    assessor2.role = 'role:assessor'
    this.form.sections[0].fields.push(assessor2)

    // member
    var rt = fields.getField('resource-type')
    rt.value = 'https://pid.phaidra.org/vocabulary/69ZZ-2KGX'
    this.form.sections[1].fields.push(rt)
    this.form.sections[1].fields.push(fields.getField('file'))
    this.form.sections[1].fields.push(fields.getField('title'))
    this.form.sections[1].fields.push(fields.getField('language'))
    var issued = fields.getField('date-edtf')
    issued.type = 'dcterms:issued'
    this.form.sections[0].fields.push(issued)
    this.form.sections[1].fields.push(fields.getField('number-of-pages'))
    var mt = fields.getField('mime-type')
    mt.value = 'application/pdf'
    this.form.sections[1].fields.push(mt)
    var license = fields.getField('license')
    license.value = 'http://rightsstatements.org/vocab/InC/1.0/'
    this.form.sections[1].fields.push(license)
  }
}
</script>

<style>
#app {
  font-family: "Roboto", sans-serif, Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.right {
  float: right;
}
</style>