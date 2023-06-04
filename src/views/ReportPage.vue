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
                                                                :max="currentDate" @change="includeChildrenRecords" />
                                                        </ion-item>
                                                    </ion-col>
                                                    <ion-col>
                                                        <ion-item>
                                                            <ion-label>To</ion-label>
                                                            <input type="date" v-model="childrenRecords.to"
                                                                style="background-color: var(--ion-color-primary); color: white"
                                                                :max="currentDate" @change="includeChildrenRecords" />
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

                                                <ion-select v-model="childrenRecords.filter"
                                                    @ionChange="includeChildrenRecords">
                                                    <ion-select-option value="annually">Annually</ion-select-option>
                                                    <ion-select-option
                                                        value="semi-annually">Semi-Annually</ion-select-option>
                                                    <ion-select-option value="quarterly">Quarterly</ion-select-option>
                                                    <ion-select-option value="monthly">Monthly</ion-select-option>
                                                </ion-select>
                                            </ion-item>
                                        </ion-list>
                                    </div>
                                </ion-accordion>
                            </ion-accordion-group>

                            <ion-accordion-group>

                                <ion-accordion value="first">
                                    <ion-item slot="header">
                                        <ion-label>Children Records by Remarks</ion-label>
                                        <ion-toggle @ion-change="includeChildrenRecordsRemark"></ion-toggle>
                                    </ion-item>
                                    <div slot="content" class="ion-padding-start ion-padding-end">
                                        <ion-list>
                                            <ion-label>Remark</ion-label>
                                            <ion-item class="dropdown ion-margin-bottom" lines="none">
                                                <ion-select placeholder="Select Remark" v-model="childrenRemark"
                                                    @ion-change="includeChildrenRecordsRemark">
                                                    <ion-select-option value="underweight">Underweight</ion-select-option>
                                                    <ion-select-option value="normal">Normal</ion-select-option>
                                                    <ion-select-option value="overweight">Overweight</ion-select-option>
                                                    <ion-select-option value="obese">Obese</ion-select-option>
                                                </ion-select>
                                            </ion-item>
                                        </ion-list>
                                    </div>
                                </ion-accordion>
                            </ion-accordion-group>

                            <ion-accordion-group>
                                <ion-accordion value="first">
                                    <ion-item slot="header">
                                        <ion-label>Children Records by Purok</ion-label>
                                        <ion-toggle @ion-change="includeChildrenRecordsPurok"></ion-toggle>
                                    </ion-item>
                                    <div slot="content" class="ion-padding-start ion-padding-end">
                                        <ion-list>
                                            <ion-item class="dropdown ion-margin-bottom" lines="none">
                                                <ion-select placeholder="Select Purok" v-model="childrenPurok"
                                                    @ionChange="includeChildrenRecordsPurok">
                                                    <ion-select-option value="1">1</ion-select-option>
                                                    <ion-select-option value="2">2</ion-select-option>
                                                    <ion-select-option value="3">3</ion-select-option>
                                                    <ion-select-option value="4">4</ion-select-option>
                                                    <ion-select-option value="5">5</ion-select-option>
                                                    <ion-select-option value="6">6</ion-select-option>
                                                    <ion-select-option value="7">7</ion-select-option>
                                                    <ion-select-option value="8">8</ion-select-option>
                                                    <ion-select-option value="9">9</ion-select-option>
                                                    <ion-select-option value="10">10</ion-select-option>
                                                    <ion-select-option value="11">11</ion-select-option>
                                                    <ion-select-option value="12">12</ion-select-option>
                                                    <ion-select-option value="13">13</ion-select-option>
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
                <!-- <ion-button expand="block" @click="test">Save</ion-button><br><br><br> -->
                <ion-button expand="block" @click="test()" :disabled="isSaveDisabled">Save</ion-button><br><br><br>
            </ion-content>

        </ion-content>
    </ion-page>
</template>
    
<script lang="ts">
import { defineComponent, watch, watchEffect } from 'vue';
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
            downloadReportHref: "http://localhost:5000/report?",
            childrenRecordsHref: "",
            childrenRemarkHref: "",
            childrenPurokHref: "",
            childrenRecords: {
                to: "",
                from: "",
                year: 2020,
                filter: ""
            },
            childrenRemark: "",
            childrenPurok: "",
            reportDetails: [] as string[],
            selectedTab: 'specificDateRange',
            isSaveDisabled: true
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
    created() {
        watchEffect(() => {
            console.log(this.childrenRecords)

            if (this.reportDetails.indexOf('childrenRecords') !== -1) {
                if ((this.childrenRecords.from.length && this.childrenRecords.to.length) ||
                    (this.childrenRecords.year >= 2020 && this.childrenRecords.filter.length)) {
                    this.isSaveDisabled = false
                }
                else {
                    this.isSaveDisabled = true
                }
            }
            else if (this.reportDetails.indexOf('childrenPurok') !== -1) {
                if (this.childrenPurok.length)
                    this.isSaveDisabled = false
                else
                    this.isSaveDisabled = true
            }
            else if (this.reportDetails.indexOf('childrenRemark') !== -1) {
                if (this.childrenRemark.length)
                    this.isSaveDisabled = false
                else
                    this.isSaveDisabled = true
            }
            else if (this.reportDetails.length) {
                this.isSaveDisabled = false
            }
            else {
                this.isSaveDisabled = true
            }
        })
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
            this.includeChildrenRecords(event)

            if (this.selectedTab === 'specificDateRange') {
                this.childrenRecords.year = 2020
                this.childrenRecords.filter = ''
            }
            else if (this.selectedTab === 'history') {
                this.childrenRecords.from = ''
                this.childrenRecords.to = ''
            }
        },
        includeChildrenList(event: any) {
            if (event.detail.checked) {
                this.reportDetails.push('childrenList')
                this.downloadReportHref += "childrenList=true&"
            }
            else {
                this.reportDetails = this.reportDetails.splice(this.reportDetails.indexOf('childrenList'), -1)
                this.downloadReportHref = this.downloadReportHref.replace("childrenList=true&", "")
            }
        },
        includeLatestRecord(event: any) {
            if (event.detail.checked) {
                this.reportDetails.push('latestRecord')
                this.downloadReportHref += "latestRecord=true&"
            }
            else {
                this.reportDetails = this.reportDetails.splice(this.reportDetails.indexOf('latestRecord'), -1)
                this.downloadReportHref = this.downloadReportHref.replace("latestRecord=true&", "")
            }
        },
        includeChildrenRecords(event: any) {
            if (event.target.value || event.detail.checked) {
                this.reportDetails.push('childrenRecords')
                if (this.selectedTab === 'specificDateRange')
                    this.childrenRecordsHref = `childrenRecords=true&from=${this.childrenRecords.from}&to=${this.childrenRecords.to}&`
                else if (this.selectedTab === 'history')
                    this.childrenRecordsHref = `childrenRecords=true&year=${this.childrenRecords.year}&filter=${this.childrenRecords.filter}&`
            }
            if (event.target.localName === 'ion-toggle' && !event.detail.checked) {
                this.reportDetails = this.reportDetails.splice(this.reportDetails.indexOf('childrenRecords'), -1)
                this.childrenRecordsHref = ""
            }
        },
        includeChildrenRecordsRemark(event: any) {
            if (event.target.value || event.detail.checked) {
                this.reportDetails.push('childrenRemark')
                this.childrenRemarkHref = `childrenRemark=${this.childrenRemark}&`
            }
            if (event.target.localName === 'ion-toggle' && !event.detail.checked) {
                this.reportDetails = this.reportDetails.splice(this.reportDetails.indexOf('childrenRemark'), -1)
                this.childrenRemarkHref = ""
            }
        },
        includeChildrenRecordsPurok(event: any) {
            if (event.target.value || event.detail.checked) {
                this.reportDetails.push('childrenPurok')
                this.childrenPurokHref = `childrenPurok=${this.childrenPurok}&`
            }
            if (event.target.localName === 'ion-toggle' && !event.detail.checked) {
                this.reportDetails = this.reportDetails.splice(this.reportDetails.indexOf('childrenPurok'), -1)
                this.childrenPurokHref = ""
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
                position: 'top'
            })

            if (this.reportDetails.length) {
                console.log(this.downloadReportHref + this.childrenRemarkHref + this.childrenPurokHref + this.childrenRecordsHref)
                // window.open(this.downloadReportHref + this.childrenRemarkHref + this.childrenPurokHref + this.childrenRecordsHref, "_self")
            }
            else {
                toast.message = 'Please select any toggle'
                await toast.present();
            }
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