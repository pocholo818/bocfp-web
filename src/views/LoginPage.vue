<template>
    <ion-page>
        <ion-content>

            <div id="loginCard">
                <ion-card>
                    <ion-card-content>
                        <ion-card-title
                            style="font-size: 28px; font-weight: 500; text-align:center;"
                        >
                            Login to BOCFP
                        </ion-card-title><br>

                        <img src="@/assets/images/logo.png" class="ion-margin-bottom">

                        <ion-item>
                            <ion-label position="floating">Username</ion-label>
                            <!-- <ion-icon slot="start" :name="personOutline"></ion-icon> -->
                            <ion-input placeholder="Username" @keyup.enter="login()"
                                v-model="loginDetails.username"></ion-input>
                        </ion-item>

                        <ion-item class="ion-margin-bottom">
                            <ion-label position="floating">Password</ion-label>
                            <ion-input type="password" @keyup.enter="login()" placeholder="Password"
                                v-model="loginDetails.password"></ion-input>
                        </ion-item><br>

                        <ion-button size="large" @click="login()" expand="block">Login</ion-button>
                    </ion-card-content>
                </ion-card>
            </div>

        </ion-content>
    </ion-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
// ionic stuff
import {
    IonContent,
    IonCard,
    IonCardContent,
    IonCardTitle,
    IonItem,
    IonInput,
    IonLabel,
    IonPage,
    toastController
} from '@ionic/vue';
import { personOutline } from 'ionicons/icons';
import router from '@/router';
import SHA256 from 'crypto-js/sha256';
import { instance as api } from "@/network/Network";

export default defineComponent({
    name: 'ChildPage',
    components: {
        IonContent,
        IonCard,
        IonCardContent,
        IonCardTitle,
        // IonHeader,
        IonItem,
        IonInput,
        IonLabel,
        IonPage,
    },
    setup() {
        return {
            personOutline
        }
    },
    data() {
        return {
            isOpen: false,
            loginDetails: {
                "username": "",
                "password": ""
            }
        };
    },
    methods: {
        setOpen(isOpen: boolean) {
            this.isOpen = isOpen;
        },
        async login() {
            const toast = await toastController.create({
                duration: 1500,
                position: 'top'
            })

            let data = Object.assign({}, this.loginDetails) // clone this.loginDetails, not reference
            data.password = SHA256(this.loginDetails.password).toString()

            api.post('/user/login', data)
                .then(response => response.data)
                .then(data => {            
                    
                    if (data.message == "Success!") {
                        // store data
                        localStorage.setItem('user_id', data.user_id)
                        localStorage.setItem('fname', data.fname)
                        localStorage.setItem('admin_power', data.admin_power)
                        localStorage.setItem('access_token', data.accessToken)
                        localStorage.setItem('refresh_token', data.refreshToken)
                        localStorage.setItem('is_logged_in', 'true')

                        toast.message = data['message']
                        this.loginDetails = {
                            username: "",
                            password: ""
                        }
                        router.push('/dashboard')
                    }
                    else {
                        toast.message = data.message
                    }
                })
                .catch(error => {
                    console.log(error)
                })
            await toast.present();
        },
        // createApp(){

        // }
    },
    mounted() {
        // if(!sessionStorage.getItem('fname')){
        //     router.push('/dashboard')
        // }
    }
});
</script>

<style scoped>
ion-content {
    --background: var(--ion-color-primary);
}

#loginCard {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}

#loginCard ion-card {
    width: 500px;
}

#loginCard ion-card-content {
    padding: 44px 32px 24px;
}

img {
    width: 150px;
    height: 150px;
    display: block;
    margin-left: auto;
    margin-right: auto;
}

/* .loginCard ion-card{
    background-color: var(--ion-color-primary);
} */
</style>
