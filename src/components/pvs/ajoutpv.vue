<template>
  <div class="ajout">
    <v-card elevation="2" outlined class="mx-auto my-auto justify-center">
      <v-toolbar class="smallnavbar mb-3" flat height="34px" app>
          <v-toolbar-title class="darkgrey--text text-h5">
          معطيات المحضر
          </v-toolbar-title>
      </v-toolbar>
   <v-form dense ref="form" v-model="valideform" class="ma-0 pa-0 px-2">   
        <v-row no-gutters dense>
          <v-col cols="12" sm="3" class="ml-2">
            <v-select
            v-model="pvs.TypeSourcePvsID"
            :items="typesource"
            item-text="nom"
            item-value="id"
              label="نوع المصدر"
              :rules="nameRules"
              dense
              required
              outlined
            >
            </v-select>
            
          </v-col>
          <v-col cols="12" sm="3" class="ml-2">
            <v-select
            v-model="pvs.typePoliceJudicID"
              class="blue-lighten-6"
              label="صنف الضابطة القضائية"
              :rules="nameRules"
              :items="typepolice"
              item-text="nom"
              item-value="id"
              dense
              outlined
              required
            >
            </v-select>
          </v-col>
          <v-col cols="12" sm="3" class="ml-2">
            <v-text-field
            v-model="pvs.policeJudics"
              dense
              outlined
              label=" الضابطة القضائية   "
            ></v-text-field>
          </v-col>
          </v-row>

          <v-row no-gutters dense class="ma-0 pa-0">
          <v-col cols="12" sm="3" class="ml-2">
            <v-menu
              v-model="menu1"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                v-model="pvs.datePvs"
                  label="تاريخ المحضر"
                  append-icon="mdi-calendar"
                  clearable
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  outlined
                  dense
                ></v-text-field>
              </template>
              <v-date-picker v-model="pvs.datePvs" @input="menu1 = false"></v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="12" sm="3" class="ml-2">
            <v-menu
              v-model="menu"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                v-model="pvs.dateEnregPvs"
                  label="تاريخ التسجيل "
                  :rules="nameRules"
                  append-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  outlined
                  dense
                ></v-text-field>
              </template>
              <v-date-picker v-model="pvs.dateEnregPvs" @input="menu = false"></v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="12" sm="3" class="ml-2">
            <v-select
            v-model="pvs.typepvsID"
            :items="typepvs"
            item-text="nom"
            item-value="id"
              class="blue-lighten-6"
              label="نوع المحضر"
              :rules="nameRules"
              dense
              outlined
              required
            >
            </v-select>
          </v-col>
          <v-row no-gutters dense>
          <v-col cols="12" sm="3" class="ml-2">
            <v-text-field
            v-model="pvs.Numpvs"
              dense
              outlined
              required
              label="رقم المحضر"
              :rules="nameRules"
            ></v-text-field>
          </v-col>
          
          <v-col cols="12" sm="7" class="ml-2">
            <v-textarea
            v-model="pvs.sujetpvs"
              clearable
              dense
              label="موضوع المحضر"
              :rules="nameRules"
              required
              rows="1"
              outlined
              no-resize
            >
            </v-textarea>
          </v-col>
          </v-row>
        </v-row>

           <v-row dense justify align-content-center>
            <v-col cols="12" sm="4">
                  <v-file-input
                  v-model="file"
                    color="blue accent-4"
                    counter
                    label="أضف المُرفق"
                    placeholder="أضف المُرفق"
                    append-icon="mdi-file-plus"
                    outlined
                    dense
                    full-width
                    :rules="nameRules"
                  >
                    <template v-slot:selection="{ index, text }">
                      <v-chip
                        v-if="index < 2"
                        color="blue accent-4"
                        light
                        label
                        small
                      >
                        {{ text }}
                      </v-chip>

                      <span
                        v-else-if="index === 2"
                        class="text-overline grey--text text--lighten-3 mx-2"
                      >
                        +{{ files.length - 2 }} File(s)
                      </span>
                    </template>
                  </v-file-input></v-col>
                 </v-row>

        <v-row no-gutters dense>
          <v-col cols="12" sm="4"></v-col>
          <v-card-actions>
            <v-btn
              text
              height="30px"
              class="my-2 blue"
              elevation="2"
              :loading="load"
              :disabled="!valideform"
              @click="addPvs"
            >
              <v-icon left>mdi-notebook-plus-outline</v-icon>
              تسجيل المحضر
            </v-btn>
            <v-btn
              text
              height="30px"
              class="my-2 red"
              elevation="2"
              @click="reset"
            >
              إلغاء
            </v-btn>
            <v-spacer></v-spacer> </v-card-actions
        ></v-row>
      </v-form>
    </v-card>

    <v-snackbar v-model="snackbar.show"  height="50px" class="mt-12"
    :timeout="snackbar.timeout" :color="snackbar.color" light top>
    {{ snackbar.text }}
        <v-btn text @click="snackbar.show = false"> إغلاق </v-btn>
  </v-snackbar>

  </div>
</template>

<script>
import baseURL from "../../api/baseURL.js";
import axios from "axios";
export default {
  data() {
    const defaultForm = Object.freeze({
            typepvsID: 0,
            TypeSourcePvsID: 0,
            typePoliceJudicID:0,
            sujetpvs: '',
            Numpvs:"",
            dateEnregPvs: new Date(
            Date.now() - new Date().getTimezoneOffset() * 60000
            )
        .toISOString()
        .substr(0, 10),
            policeJudics: '',
            datePvs:"",
            heureRealisation:"",
            contreInnconue:null
    });
    return {
      pvs: Object.assign({}, defaultForm),
      typesource: [],
      typepvs: [],
      typepolice: [],
      file:null,

      valideform: true,
      menu: false,
      menu1: false,
      nameRules: [(v) => !!v || "حقل ضروري"],
      load:false,
      snackbar:{
        text:"",
        color:"",
        show:false,
      },
    };
  },
  computed: {},

  methods: {
    validate() {
      this.$refs.form.validate();
    },
    reset() {
      this.$refs.form.reset();
    },
    dialog(txt,clr){
      this.snackbar.text = txt;
      this.snackbar.color = clr;
      this.snackbar.show = true;
    },

    async addFile(idpv,nump){
          this.snackbar.show = false;
           let formData = new FormData(); 
           formData.append('file', this.file);

               await axios.post(`${baseURL.api}/pvs/addPdf/${idpv}.${nump}`,
                 formData, 
                 {headers: {   Authorization: `Bearer ${baseURL.token}`,
                               'Content-Type': 'multipart/form-data'      }
                    }).then(rep =>{
                      if(rep.status == 200 || rep.status == 201)
                        {
                          this.reset(); this.file = null;
                        this.dialog("تم تسجيل المحضر بنجاح","light-blue accent-1")
                        }
                    }).catch(err=>{
                      console.log(err);
                    });
      },

      async addPvs(){
        this.snackbar.show= false;
          this.validate();     this.load = true;
        await axios.post( `${baseURL.api}/pvs/store`,
                 { pv : this.pvs }, 
                 {headers: {   Authorization: `Bearer ${baseURL.token}`}
                    }).then(async rep=>{
                      if(rep.status == 200 || rep.status ==201)
                       await this.addFile(rep.data[0],rep.data[1]);
                      this.load = false;
                    }).catch(err=>{
                      this.dialog("تأكد من صحة المعلومات","red lighten-4");
                      this.load = false;
                    });
      }
  },
  
  created() {
    axios
      .get(baseURL.api + "/pvs/type/index", {
        headers: { Authorization: `Bearer ${baseURL.token}` },
      })
      .then((rep) => {
        this.typepvs = rep.data;
      });

    axios
      .get(baseURL.api + "/pvs/typesource/index", {
        headers: { Authorization: `Bearer ${baseURL.token}` },
      })
      .then((rep) => {
        this.typesource = rep.data;
      });

      axios
      .get(baseURL.api + "/pvs/typepolice/index", {
        headers: { Authorization: `Bearer ${baseURL.token}` },
      })
      .then((rep) => {
        this.typepolice = rep.data;
      });

  },
};
</script>
