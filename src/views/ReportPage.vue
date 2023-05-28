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
                                <ion-label>Children List</ion-label>
                                <ion-toggle @ion-change="includeChildrenList"></ion-toggle>
                            </ion-item>

                            <ion-item>
                                <ion-label>Latest Children Record</ion-label>
                                <ion-toggle @ion-change="includeLatestRecord"></ion-toggle>
                            </ion-item>


                            <ion-accordion-group class="ion-no-padding" style="width: 100%">
                                <ion-accordion value="first">
                                    <ion-item slot="header">
                                        <ion-label>Children Records</ion-label>
                                        <ion-toggle @ion-change="includeChildrenRecords"></ion-toggle>
                                    </ion-item>
                                    <div slot="content" class="ion-padding-start ion-padding-end">
                                        <ion-segment :value="selectedTab" @ion-change="onSegmentChange">
                                            <ion-segment-button value="specificDateRange">
                                                <ion-label>Specific Date Range</ion-label>
                                            </ion-segment-button>
                                            <ion-segment-button value="history">
                                                <ion-label>History</ion-label>
                                            </ion-segment-button>
                                        </ion-segment>

                                        <ion-list v-if="selectedTab === 'specificDateRange'">
                                            <ion-grid class="ion-no-padding">
                                                <ion-row>
                                                    <ion-col>
                                                        <ion-item>
                                                            <ion-label>From</ion-label>
                                                            <input type="date" v-model="childrenRecords.from"
                                                                style="background-color: var(--ion-color-primary); color: white"
                                                                :max="currentDate" />
                                                        </ion-item>
                                                    </ion-col>
                                                    <ion-col>
                                                        <ion-item>
                                                            <ion-label>To</ion-label>
                                                            <input type="date" v-model="childrenRecords.to"
                                                                style="background-color: var(--ion-color-primary); color: white"
                                                                :max="currentDate" />
                                                        </ion-item>
                                                    </ion-col>
                                                </ion-row>
                                            </ion-grid>
                                        </ion-list>

                                        <!-- TODO -->
                                        <ion-list v-else-if="selectedTab === 'history'">
                                            <ion-item>
                                                <ion-label>Get History by:</ion-label>

                                                <input type="number" @keypress="numOnly($event)"
                                                    style="background-color: var(--ion-color-primary); color: white"
                                                    v-model="childrenRecords.year" placeholder="YYYY" min="2020"
                                                    :max="currentYear">
                                            </ion-item>

                                            <ion-item>
                                                <ion-label>History Filter: </ion-label>

                                                <ion-select v-model="childrenRecords.filter">
                                                    <ion-select-option value="annual">Annually</ion-select-option>
                                                    <ion-select-option value="quarter">Quarterly</ion-select-option>
                                                    <ion-select-option value="monthly">Monthly</ion-select-option>
                                                </ion-select>
                                            </ion-item>
                                        </ion-list>
                                    </div>
                                </ion-accordion>
                            </ion-accordion-group>
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
    IonSelect, IonSelectOption,
    toastController,
    useIonRouter,
    IonCardHeader,
    IonCardTitle,
    modalController,
    IonToggle,
    IonGrid,
    IonRow,
    IonCol,
    IonAccordion,
    IonAccordionGroup,
    IonSegment,
    IonSegmentButton
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
        IonSelect, IonSelectOption,
        IonCardHeader,
        IonCardTitle,
        IonToggle,
        IonGrid,
        IonRow,
        IonCol,
        IonAccordion,
        IonAccordionGroup,
        IonSegment,
        IonSegmentButton
    },
    data() {
        return {
            childrenRecords: {
                to: "",
                from: "",
                year: 2020,
                filter: ""
            },
            reportDetails: [] as string[],
            selectedTab: 'specificDateRange',
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
        numOnly(evt: KeyboardEvent): void {
            const keysAllowed: string[] = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '.'];
            const keyPressed: string = evt.key;

            if (!keysAllowed.includes(keyPressed)) {
                evt.preventDefault()
            }
        },
        onSegmentChange(event: any) {
            this.selectedTab = event.detail.value
        },
        includeChildrenList(event: any) {
            if (event.detail.checked) {
                this.reportDetails.push('childrenList')
            }
            else {
                this.reportDetails.splice(this.reportDetails.indexOf('childrenList'), 1)
            }
        },
        includeLatestRecord(event: any) {
            if (event.detail.checked) {
                this.reportDetails.push('latestRecord')
            }
            else {
                this.reportDetails.splice(this.reportDetails.indexOf('latestRecord'), 1)
            }
        },
        includeChildrenRecords(event: any) {
            if (event.detail.checked) {
                this.reportDetails.push('childrenRecords')
            }
            else {
                this.reportDetails.splice(this.reportDetails.indexOf('childrenRecords'), 1)
            }
        },
        checkFromTo() {
            let from = moment(this.childrenRecords.from)
            let to = moment(this.childrenRecords.to)
            let result = moment.duration(from.diff(to)).asDays()

            return result > 0 ? false : true
        },
        async test() {
            const toast = await toastController.create({
                duration: 1500,
                message: 'Please toggle any options.',
                position: 'top'
            })

            let body = {};

            if (this.reportDetails.indexOf('childrenList') !== -1) {
                body = {
                    ...body,
                    childrenList: true
                }
            }
            if (this.reportDetails.indexOf('latestRecord') !== -1) {
                body = {
                    ...body,
                    latestRecord: true
                }
            }
            if (this.reportDetails.indexOf('childrenRecords') !== -1) {
                if (this.checkFromTo() === false) {
                    toast.message = 'Invalid Date'
                    await toast.present()
                    return
                }
                if (this.childrenRecords.year > parseInt(moment().format('YYYY'))) {
                    toast.message = 'Invalid Year'
                    await toast.present()
                    return
                }

                if (this.selectedTab === 'specificDateRange') {
                    body = {
                        ...body,
                        childrenRecords: {
                            from: this.childrenRecords.from,
                            to: this.childrenRecords.to,
                        }
                    }
                }
                else if (this.selectedTab === 'history') {
                    body = {
                        ...body,
                        childrenRecords: {
                            year: this.childrenRecords.year,
                            filter: this.childrenRecords.filter,
                        }
                    }
                }
            }

            console.log(body);

            // api.post('/report', body)
            //     .then(response => response.data)
            //     .then((data) => {
            //         toast.message = 'Success!'
            //         // this.reportDetails = {
            //         //     from: "",
            //         //     to: ""
            //         // }
            //         // this.router.push("/dashboard");
            //     })
            //     .catch((error) => {
            //         toast.message = 'error'
            //     });

            await toast.present()
        }
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
    
  
<style scoped>
/* #all-child {
    --inner-padding-start: 0;
    /* --padding-start: 0;
}*/


/* #all-child::part(native) {
    padding-inline-start: 0;
} */
</style>