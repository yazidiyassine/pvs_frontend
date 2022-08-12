<template>
  <div>
    <v-card elevation="2" outlined class="mx-auto my-auto py-3">
      <v-form class="px-5">
        <v-row dense justify align-content-center no-gutters>
          <v-col cols="12" sm="4">
            <v-text-field
              v-model="cherchant"
              label="رقم المحضر"
              class="mt-2 ml-4 pa-0"
              outlined
              dense
            ></v-text-field>
          </v-col>
          <v-col>
            <v-card-actions>
              <v-btn
                text
                @click="chercher_pvs"
                class="mt-1 blue"
                elevation="2"
                :loading="load"
                height="30px"
              >
                <v-icon right>mdi-magnify</v-icon>
                بحث
              </v-btn>
            </v-card-actions>
          </v-col>
        </v-row>
      </v-form>
    </v-card>

    <v-card class="mt-5" v-show="affiche_table">
      <div>
        <v-data-table
          :headers="headers"
          :items="pvs"
          no-data-text="معلومات غير متاحة"
          class="elevation-1"
          hide-default-footer
          :loading="table_vide"
          loading-text="إنتظر قليلا"
        >
          <template v-slot:[`item.lien`]="{ item }">
            <v-chip
              color="blue lighten-4"
              lighten
              small
              fab
              @click="redirect(item.pvs.hasfichier.lien)"
            >
              {{ getstatus(item.traitID) }}
              <v-icon small class="mr-2"> mdi-download </v-icon>
            </v-chip>
          </template>
          <template v-slot:[`item.action`]="{ item }">
            <v-btn small color="blue lighten-5" @click="deleteItem(item.pvs.id)"
              ><v-icon left> mdi-delete </v-icon>
              حذف
            </v-btn>
          </template>
        </v-data-table>
      </div>
      <div class="text-center"></div>
    </v-card>
    <v-dialog v-model="dialog" max-width="400px">
      <v-card>
        <v-card-text class="d-flex justify-center pa-2 font-weight-black">
          {{ messagedialog }}
        </v-card-text>
        <v-card-actions class="d-flex justify-center">
          <v-btn
            color="red lighten-2 mx-4"
            height="24px"
            @click="dialog = false"
          >
            إلغاء
          </v-btn>

          <v-btn
            color="green lighten-2 mx-4"
            height="24px"
            :loading="load_dialog"
            @click="valideDeleteItem"
          >
            تأكيد
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
import baseURL from "@/api/baseURL";
import axios from "axios";

export default {
  data() {
    return {
      load: false,
      affiche_table: false,
      table_vide: false,
      headers: [
        { text: "رقم المحضر", value: "pvs.Numpvs" },
        { text: "تاريخ التسجيل ", value: "pvs.dateEnregPvs" },
        { text: "موضوع المحضر", value: "pvs.sujetpvs" },
        { text: "الملف", value: "lien", sortable: false },
        { text: "تغيير", value: "action", sortable: false },
      ],
      pvs: [],
      cherchant: "",
      idpvsdelete: -1,
      dialog: false,
      messagedialog: "jjjjjj",
      load_dialog: false,
    };
  },
  methods: {
    redirect(link) {
      var names = link.split("/");
      var fileLink = document.createElement("a");
      fileLink.href = baseURL.backendPDF + "/dossiers_pvs/" + names[2];
      fileLink.target = "_blank";
      fileLink.click();
    },
    getstatus(traitID) {
      if (traitID == 3) return "معالج";
      else return " غير معالج";
    },
    chercher_pvs() {
      this.load = true;
      axios
        .post(
          baseURL.api + "/pvs/ByNumpvs",
          {
            Numpvs: this.cherchant,
          },
          { headers: { Authorization: `Bearer ${baseURL.token}` } }
        )
        .then((rep) => {
          if (rep.status == 200 || rep.status == 201) {
            this.pvs = rep.data;
          }
          this.load = false;
          this.affiche_table = true;
        })
        .catch((err) => {
          this.load = false;
          this.affiche_table = true;
        });
    },
    deleteItem(id) {
      this.idpvsdelete = id;
      this.dialog = true;
    },
    valideDeleteItem() {
      this.load_dialog = true;
      axios
        .delete(baseURL.api + "/pvs/delete/" + this.idpvsdelete, {
          headers: { Authorization: `Bearer ${baseURL.token}` },
        })
        .then((rep) => {
          if (rep.status == 200 || rep.status == 201) {
            this.chercher_pvs();
            this.load_dialog = false;
            this.dialog = false;
          }
          this.load = false;
          this.affiche_table = true;
        })
        .catch((err) => {
          this.load = false;
          this.affiche_table = true;
        });
    },
  },
};
</script>
