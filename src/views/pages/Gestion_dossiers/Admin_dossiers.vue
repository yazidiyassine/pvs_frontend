<template>
<div>
  <v-tabs
      v-model="tab"
       active-class="smallnavbar"
       height="35px"
       class="mb-3"
    >
    <v-tab
      class="text-h6 mx-10"
      >
      <v-icon right>mdi-folder-open</v-icon>
        المحاضر   
      </v-tab>
      <v-tab
      class="text-h6 mx-10"
      
      >
      <v-icon right>mdi-folder-open</v-icon>
     الشكايات 
      </v-tab>
       </v-tabs>
    <v-tabs-items v-model="tab">

      <v-tab-item>
        <supprimer_pvsVue />
      
      </v-tab-item>

      <v-tab-item>
      <supprimer_plainteVue></supprimer_plainteVue>
      </v-tab-item>

      </v-tabs-items>
      

  </div>
</template>

<script>
import supprimer_plainteVue from '@/components/plaintes/supprimer_plainte.vue';
import supprimer_pvsVue from '@/components/pvs/supprimer_pvs.vue';

  export default {
    components: {
      supprimer_plainteVue,
      supprimer_pvsVue,
    },
    data() {
      return {
        tab: null,
      }
    }
  }
</script>
