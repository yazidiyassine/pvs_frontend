<template>
 <div>
     <v-card>
        <div class="blue lighten-4 pa-2 mb-6">
             إنشاء حساب جديد 
        </div>
     <v-col cols="12" sm="4">
  <v-form
    ref="valid"
    v-model="valid"
  >
  <v-row>

  </v-row>
    <v-text-field
      v-model="user.nom"
      :rules="nameRules"
      label="الإسم الكامل "
      dense outlined
      required
    ></v-text-field>
   
    <v-text-field
      v-model="user.email"
      :rules="emailRules"
      label="البريد الإلكتروني"
      dense outlined
      required
    ></v-text-field>
        <v-text-field
          v-model="user.numUser"
          :rules="numberRules"
          label=" رقم الهاتف"
          dense outlined
          required
        ></v-text-field>
  
    <v-select
      v-model="user.idRole"
      :items="items"
      item-text="nom"
      item-value="id"
      :rules="nameRules"
      dense outlined
      label="دوره"
      required
    ></v-select>
 
    <v-row>
        <v-col cols="12" sm="5" class="ml-6">
            <v-text-field
            type="password"
          v-model="user.password"
          label="كلمة السر"
          dense outlined
          :rules="Rulepass"
          required
        ></v-text-field>
        </v-col>
        <v-col cols="12" sm="6">
        <v-text-field
        ref="password"
        dense outlined
          v-model="password"
          type="password"
          label="تأكيد كلمة السر "
          :rules="[
              () => !!password || 'ضروري',
              () => password == this.user.password || 'خطأ',
              
            ]"
          required
        ></v-text-field>
        </v-col>
    </v-row>

    <v-checkbox
      v-model="user.active"
      label="نشط"
      :value="!user.active"

    ></v-checkbox>

    <v-btn
      :disabled="!valid"
      color="blue"
      class="mr-4 pa-0"
      :loading="loadValid"
      height="27px"
      @click="validate"
    >
      إنشاء
    </v-btn>

    <v-btn
      color="red"
      height="27px"
      class="mr-4 pa-0"
      @click="reset"
    >
       إلغاء
    </v-btn>

  </v-form>
   </v-col>
   </v-card>
  </div>
</template>
<script>
import axios from 'axios';
import baseURL from '@/api/baseURL';
  export default {
    data: () => ({
      valid: false,
      loadValid:false,
      user:{
          nom:'',
          email:'',
          password:'',
          active:false,
          numUser:'',
          idRole:null
      },
      password:'',
      name: '',
      nameRules: [
        v => !!v || '',
        v => (v && v.length <= 20) || '',
      ],
      email: '',
      emailRules: [
        v => !!v || ' ',
         v =>  /.+@.+\..+/.test(v) || '',
      ],
      numberRules: [
        v => !!v || '' ,
        v => /[0-9][0-9][0-9]/.test(v) || '',
      ],
      Rulepass: [
        v => !!v || ' ',
         v =>  (v && v.length >= 6) || 'ضعيف',
      ],
      items: [],
    }),
    methods: {
      async validate () {
          this.$refs.valid.validate();
          //this.loadValid = true
       await axios.post(baseURL.api+'/users/store',{
             users:this.user
         },{headers: { Authorization:`Bearer ${baseURL.token}`} 
        }).then(reponse=>{
          //this.openSnackbar("تم إنشاء الحساب بنجاح");
             this.reset();
        }).catch(er =>{
          // this.openSnackbar("تأكد من صحة المعلومات");
           return er;
          });
       
      },
      reset () {
        this.$refs.valid.reset()
      },
      
    },
    created(){
        axios.get(baseURL.api+'/users/role/index',{
            headers:{Authorization:`Bearer ${baseURL.token}`}
        }).then(res => {
            this.items = res.data;
        })
    }
  }
</script>