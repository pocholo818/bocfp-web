<template>
    <ion-header>
        <ion-toolbar style="">
            <ion-buttons slot="start">
                <ion-back-button :icon="closeOutline" defaultHref="/dashboard"></ion-back-button>
            </ion-buttons>

            <ion-title>Summary Report</ion-title>
        </ion-toolbar>
    </ion-header>

    <ion-content>
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
    </ion-content>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
// ionic stuff
import {
    IonSegment, IonSegmentButton,
    IonList, IonSelect, IonSelectOption,
    IonHeader, IonToolbar, IonTitle, IonButtons, IonBackButton,
    IonCol, IonRow, IonGrid,
} from '@ionic/vue';

import { instance as api } from "@/network/Network";
import moment from 'moment';
import pdfMake from 'pdfmake/build/pdfmake';
import Image from 'pdfmake/build/pdfmake';
// import myImage from '@/assets/images/logo.png'
import pdfFonts from 'pdfmake/build/vfs_fonts';
import table from 'pdfmake/build/pdfmake'; 
import { closeOutline } from 'ionicons/icons';
pdfMake.vfs = pdfFonts.pdfMake.vfs;

export default defineComponent({
    name: 'SummaryReportModal',
    components: {
        IonSegment, IonSegmentButton,
        IonList, IonSelect, IonSelectOption,
        IonHeader, IonToolbar, IonTitle, IonButtons, IonBackButton,
        IonCol, IonRow, IonGrid,
    },
    data() {
        return {
            selectedTab: 'specificDateRange',
            apiEndpoint: '',
            pdfPurokRemark: [
                ['Purok #','Underweight', 'Normal', 'Overweight', 'Obese', 'Total'],
                ['Purok 1', 'Row 1 Data', 'Row 1 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 2', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 3', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 4', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 5', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 6', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 7', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 8', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 9', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 10', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 11', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 12', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 13', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Purok 14', 'Row 2 Data', 'Row 2 Data', 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
                ['Total', 'Row 2 Data', 0, 'Row 3 Data', 'Row 4 Data', 'Row 4 Data'],
            ],
            childrenRecords: {
                to: "",
                from: "",
                year: 2020,
                filter: ""
            },
        }
    },
    setup() {
        return {
            closeOutline
        }
    },
    methods: {
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
        fetchChildPurokRemarks() {
            api('/child/purok/remarks')
                .then((response) => response.data)
                .then((data) => {
                    let totalChild = 0
                    let remarksTotal = [0,0,0,0]
                    let purokTotal = [0,0,0,0,0,0,0,0,0,0,0,0,0,0]
                    // console.log(data);
                    data.Underweight.forEach((element:any,index : any) => {
                             this.pdfPurokRemark[index+1][1] = element.toString();
                             remarksTotal[0] += Number(this.pdfPurokRemark[index+1][1]);
                            totalChild += Number(this.pdfPurokRemark[index+1][1])
                    });
                    data.Normal.forEach((element:any,index : any) => {
                             this.pdfPurokRemark[index+1][2] = element.toString();
                              remarksTotal[1] += Number(this.pdfPurokRemark[index+1][2]);
                              totalChild += Number(this.pdfPurokRemark[index+1][2])
                    });
                    data.Overweight.forEach((element:any,index : any) => {
                             this.pdfPurokRemark[index+1][3] = element.toString();
                             remarksTotal[2] += Number(this.pdfPurokRemark[index+1][3]);
                             totalChild += Number(this.pdfPurokRemark[index+1][3])
                    });
                    data.Obese.forEach((element:any,index : any) => {
                             this.pdfPurokRemark[index+1][4] = element.toString();
                             remarksTotal[3] += Number(this.pdfPurokRemark[index+1][4]);
                             totalChild += Number(this.pdfPurokRemark[index+1][4])
                    });

                    // remark total
                    this.pdfPurokRemark[this.pdfPurokRemark.length-1].forEach((row: any, i: number) => {
                        if(i >= 4) return
                        this.pdfPurokRemark[this.pdfPurokRemark.length-1][i+1] = remarksTotal[i]
                    })

                    console.log(data)

                    this.pdfPurokRemark.forEach((row: any, i: number) => {
                        if(i === 0 || i === this.pdfPurokRemark.length - 1) return

                        let total = 0

                        this.pdfPurokRemark[i].forEach((val: any, i: number) => {
                            if(i <= 0 || i >= 5) return
                            total += Number(val)
                        })

                        row[5] = total
                    })

                    this.pdfPurokRemark[this.pdfPurokRemark.length-1][5] = totalChild
                })
        },
        exportToPDF() {
            const getTableContent = () => {
            const tableLayout = {
                fillColor: (rowIndex: number, node: any, columnIndex: any) => {
                // Customize the background color of the table cells
                return rowIndex === 0 ? '#CCCCCC' : null; // Header row color: '#CCCCCC'
                }
            };

            const tableContent = {
                table: {
                headerRows: 1,
                body: this.pdfPurokRemark,
                widths: ['*', '*', '*', '*', '*','*'], // Adjust the column widths as needed
                layout: tableLayout,
                }
            };

            return tableContent;
            };

            const getHeaderContent = () => {
                return {
                    text: [
                    {
                        text: 'Repulic of the Philippines\n',
                        fontSize: 16,
                        bold: true,
                        
                    },
                    {
                        text: 'Barangay Hall Old Cabalan\n',
                        fontSize: 14,
                    },
                    {
                        text: [
                            'Purok 11 Narra Lane, Olongapo City\n',
                            'Zambales, Philippines 2200\n',
                        ],
                        fontSize: 13,
                    },
                    ],
                    alignment: 'center',
                    margin: [0, 20, 0, 10],
                };
            };

            const getFooterContent = () => {
                return {
                    text: `Prepared by ${localStorage.getItem('fname') || ''} ${localStorage.getItem('lname') || ''}`, // Replace with your desired footer content
                    // alignment: 'center',
                    fontSize: 12,
                    bold: true,
                    margin: [0, 14, 0, 0], // Adjust the margins as needed
                };
            };


            const documentDefinition : any = {
                    content: [
                        getHeaderContent(),
                        '\n',
                        // `Report Summary for ${test}`,                        
                        {
                            text: 'Report Summary\n',
                            alignment: 'center',
                            fontSize: 14,
                            bold: true
                        },
                        '\n',
                        getTableContent(),
                        '\n',
                        getFooterContent(),
                        `Date Covered: ${moment().format('MMMM D, YYYY')}`,

                    ]
                };

                pdfMake.createPdf(documentDefinition).download('example.pdf');
        },
        numOnly(evt: KeyboardEvent): void {
            const keysAllowed: string[] = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '.'];
            const keyPressed: string = evt.key;

            if (!keysAllowed.includes(keyPressed)) {
                evt.preventDefault()
            }
        },
        includeChildrenRecords(event: any) {
            if (this.selectedTab === 'specificDateRange')
                this.apiEndpoint = `childrenRecords=true&from=${this.childrenRecords.from}&to=${this.childrenRecords.to}`
            else if (this.selectedTab === 'history')
                this.apiEndpoint = `childrenRecords=true&year=${this.childrenRecords.year}&filter=${this.childrenRecords.filter}`

            console.log(this.apiEndpoint)
        },
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