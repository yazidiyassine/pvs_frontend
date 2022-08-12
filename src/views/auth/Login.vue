<template>
    <div>
        <v-container
                class="fill-height"
                fluid
        >
            <v-row
                    align="center"
                    justify="center"
            >
                <v-col
                        cols="12"
                        md="4"
                        sm="8"
                >
                    <v-card class="mt-14 elevation-12">
                        <v-toolbar
                                color="primary"
                                dark
                                flat
                        >
                            <v-toolbar-title>تسجيل الدخول</v-toolbar-title>
                            <v-spacer/>
                        </v-toolbar>
                        <v-card-text>
                            <v-form ref="loginForm">
                                <v-text-field
                                        label="البريد اللالكتروني"
                                        name="email"
                                        type="email"
                                        v-model="user.email"
                                />
                                <v-text-field
                                        id="password"
                                        label="كلمة السر"
                                        name="password"
                                        type="password"
                                        v-model="user.password"

                                />
                            </v-form>
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer/>
                            <v-btn @click="loginUser" color="primary">دخول</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-col>
            </v-row>
        </v-container>
    </div>
</template>

<script>
import {mapActions} from 'vuex';
    export default {
        name: "Login",
        data() {
            return {
                user: {
                    email: '',
                    password: ''
                }
            }
        },
        methods: {
            ...mapActions({
                login: 'user/loginUser'
            }),
            loginUser() {
                 if(this.$refs.loginForm.validate()){
                    this.login(this.user)
                    .then(() => {
                        this.$router.push('/')
                        console.log(response);
                    })

                }
            }
        }
    }
</script>

<style scoped>

</style>