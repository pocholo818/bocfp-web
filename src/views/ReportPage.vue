<template>
    <ion-page>
        <ion-header>
            <ion-toolbar style="">
                <ion-buttons slot="start">
                    <ion-back-button text="Back" defaultHref="/dashboard"></ion-back-button>
                </ion-buttons>
            </ion-toolbar>
        </ion-header>

        <!-- content -->
        <ion-content>
            <ion-content class="ion-padding">
                <ion-card>
                    <ion-card-content>
                        <ion-card-header>
                            <ion-card-title>Generate Report</ion-card-title>
                        </ion-card-header>

                        <ion-list>
                            <ion-item>
                                <ion-label>From Date:</ion-label>

                                <input type="date" v-model="reportDetails.from"
                                    style="background-color: var(--ion-color-primary); color: white" :max="currentDate" />
                            </ion-item>

                            <ion-item>
                                <ion-label>To Date:</ion-label>

                                <input type="date" v-model="reportDetails.to"
                                    style="background-color: var(--ion-color-primary); color: white" :max="currentDate" />
                            </ion-item>

                            <!-- <ion-item>
                                <ion-label>Get History by:</ion-label>
                                <ion-toggle v-model="historyToggle">Default Toggle</ion-toggle>

                                <input type="number" v-model="reportDetails.history" @keypress="numOnly($event)"
                                    :disabled="!historyToggle"
                                    style="background-color: var(--ion-color-primary); color: white" placeholder="YYYY"
                                    min="2020" :max="currentYear">
                            </ion-item>

                            <ion-item>
                                <ion-label>History Filter: </ion-label>

                                <ion-select v-model="reportDetails.historyFilter" :disabled="!historyToggle">
                                    <ion-select-option value="all">All</ion-select-option>
                                    <ion-select-option value="annual">Anually</ion-select-option>
                                    <ion-select-option value="quarter">Quarterly</ion-select-option>
                                </ion-select>
                            </ion-item> -->

                        </ion-list>
                    </ion-card-content>
                </ion-card>

                <!-- Save -->
                <!-- <ion-button expand="block"
                    :href="`https://localhost.com:5000/report?from=${reportDetails.from}&to=${reportDetails.to}`">Save</ion-button><br><br><br> -->
                <ion-button expand="block" @click="test">Save</ion-button><br><br><br>
            </ion-content>

        </ion-content>
    </ion-page>
</template>
    
<script lang="ts">
import { defineComponent, readonly } from 'vue';
// icons
import {
    eyeOutline,
    createOutline,
    trashOutline
} from 'ionicons/icons';
// ionic stuff
import {
    // IonInput,
    IonList,
    IonCard,
    IonCardContent,
    IonBackButton,
    IonButtons, IonHeader, IonToolbar,
    IonDatetime, IonDatetimeButton, IonModal,
    IonItem,
    // IonSelect, IonSelectOption,
    toastController,
    useIonRouter,
    IonCardHeader,
    IonCardTitle,
    modalController,
    // IonToggle,

} from '@ionic/vue';
import CropModal from '@/components/CropModal.vue'
import { FilePicker } from '@capawesome/capacitor-file-picker';
import { instance as api } from "@/network/Network";
import moment from 'moment';

export default defineComponent({
    name: 'ChildPage2',
    components: {
        // HeaderBar,
        // IonInput,
        IonList,
        IonCard,
        IonCardContent,
        IonBackButton,
        IonButtons, IonHeader, IonToolbar,
        // IonDatetime, IonDatetimeButton, IonModal,
        IonItem,
        // IonSelect, IonSelectOption,
        IonCardHeader,
        IonCardTitle,
        // IonToggle,
    },
    data() {
        return {
            reportDetails: {
                to: "",
                from: "",
                history: "",
                historyFilter: ""
            },
            historyToggle: false
        }
    },
    setup() {
        const router = useIonRouter();

        return {
            eyeOutline,
            createOutline,
            trashOutline,
            router
        }
    },
    methods: {
        async generateReport() {
            const toast = await toastController.create({
                duration: 1500,
                position: 'top'
            })

            const data = this.reportDetails;

            // check if inputs r empty
            if (this.reportDetails.from && this.reportDetails.to) {

                api.post('/report', data)
                    .then(response => response.data)
                    .then((data) => {
                        toast.message = 'Success!'
                        // this.reportDetails = {
                        //     from: "",
                        //     to: ""
                        // }
                        // this.router.push("/dashboard");
                    })
                    .catch((error) => {
                        toast.message = 'error'
                    });

            }
            else {
                toast.message = "Guardian's details are incomplete"
            }

            await toast.present();
        },
        numOnly(evt: KeyboardEvent): void {
            const keysAllowed: string[] = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '.'];
            const keyPressed: string = evt.key;

            if (!keysAllowed.includes(keyPressed)) {
                evt.preventDefault()
            }
        },
        test() {
            console.log(this.reportDetails)
        }
        // historyToggle() {
        //     if (this.historyToggle == true) {

        //     }
        // }
    },
    computed: {
        currentDate() {
            return moment().format('YYYY-MM-DD');
        },
        currentYear() {
            return moment().format('YYYY');
        }
    }
});



</script>
    
  
<style scoped></style>