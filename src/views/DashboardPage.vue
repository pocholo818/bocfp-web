<template>
    <ion-page>
        <HeaderBar title="Dashboard" />

        <!-- content -->
        <ion-content :fullscreen="true" class="ion-padding">

            <ion-refresher slot="fixed" @ionRefresh="handleRefresh($event)">
                <ion-refresher-content></ion-refresher-content>
            </ion-refresher>

            <ion-card id="app-info" class="ion-margin-bottom">
                <ion-card-content>
                    BOCFP is an app that monitors children's BMI to ensure healthy growth and development.<br>
                    It helps identify health issues early, enables early intervention and tracks growth.<br><br>
                    It is done by calculating BMI using <strong><code>height</code></strong> and
                    <strong><code>weight</code></strong> and comparing it to standard charts for
                    children of that age and gender.
                </ion-card-content>
            </ion-card>

            <ion-grid>
                <ion-row>
                    <ion-col>
                        <ion-card>
                            <ion-card-header>
                                <ion-card-title>Total Child</ion-card-title>
                                <ion-card-subtitle style="font-size: 23px;">{{ childCount }}
                                    children</ion-card-subtitle>
                            </ion-card-header>
                        </ion-card>
                    </ion-col>
                    <ion-col>
                        <ion-card>
                            <ion-card-header>
                                <ion-card-title style="text-align: center;">Remarks</ion-card-title>
                                <ion-grid>
                                    <!-- content -->
                                    <ion-row class="remark">
                                        <ion-col>
                                            <ion-card-subtitle style="background-color: #FFFF00; color: black;">Underweight:
                                                <br>{{
                                                    childRemarks.Underweight
                                                }}</ion-card-subtitle>
                                        </ion-col>
                                        <ion-col>
                                            <ion-card-subtitle style="background-color: #41B883; color: white;">Normal: <br>
                                                {{ childRemarks.Normal }}</ion-card-subtitle>
                                        </ion-col>
                                        <ion-col>
                                            <ion-card-subtitle style="background-color: #FFA500; color: white;">Overweight:
                                                <br>{{
                                                    childRemarks.Overweight
                                                }}</ion-card-subtitle>
                                        </ion-col>
                                        <ion-col>
                                            <ion-card-subtitle style="background-color: #FF0000; color: white;">Obese: <br>
                                                {{ childRemarks.Obese }}</ion-card-subtitle>
                                        </ion-col>
                                    </ion-row>
                                </ion-grid>
                            </ion-card-header>
                        </ion-card>
                    </ion-col>
                </ion-row>
            </ion-grid>

            <ion-card class="ion-margin-bottom">
                <ion-card-content>
                    <h1>Remarks</h1>
                    <div>
                        <PieChart :data="remarks_data" :options="options" />
                    </div>
                </ion-card-content>
            </ion-card>

            <ion-card class="ion-margin-bottom">
                <ion-card-content>
                    <h1>Age</h1>
                    <div>
                        <BarChart :data="age_data" :options="ageOptions" />
                    </div>
                </ion-card-content>
            </ion-card>

            <ion-card class="ion-margin-bottom">
                <ion-card-content>
                    <h1>Purok</h1>
                    <div>
                        <BarChart :data="purok_data" :options="options" />
                    </div>
                </ion-card-content>
            </ion-card>
        </ion-content>

        <ion-footer class="ion-no-border ion-padding" style="padding-bottom: 6px; padding-top: 0;">
            <ion-toolbar color="none">
                <ion-button @click="fetchChildRemarks(), fetchChildCount(),
                    fetchChildPurok(), fetchChildAge()">Refresh Data</ion-button>
                <ion-button router-link="/report">Generate Report</ion-button>
            </ion-toolbar>
        </ion-footer>

    </ion-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
// ionic stuff
import {
    IonCard,
    IonCardTitle,
    IonCardSubtitle,
    IonCardHeader,
    IonCardContent,
    IonSearchbar,
    toastController,
    alertController,
    IonRefresher, IonRefresherContent,
    IonCol, IonRow, IonGrid,
} from '@ionic/vue';
// icons
import {
    eyeOutline,
    createOutline,
    trashOutline,
    addOutline
} from 'ionicons/icons';
import HeaderBar from '@/components/HeaderBar.vue';

import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';
ChartJS.register(ArcElement, Tooltip, Legend)
import PieChart from '@/components/PieChart.vue';
import BarChart from '@/components/BarChart.vue';
import { instance as api } from "@/network/Network";
import moment from 'moment';

export default defineComponent({
    name: 'ChildPage',
    components: {
        PieChart,
        BarChart,
        HeaderBar,
        IonCard,
        IonCardTitle,
        IonCardSubtitle,
        IonCardHeader,
        IonCardContent,
        IonRefresher, IonRefresherContent,
        IonCol, IonRow, IonGrid,
    },
    setup() {
        return {
            eyeOutline,
            createOutline,
            trashOutline,
            addOutline
        }
    },
    data() {
        return {
            isOpen: false,
            childList: [],
            childCount: 0,
            childRemarks: {},
            countTotalRemarks: 0,
            countTotalChildsOfAgePerRemark: [0, 0, 0, 0, 0, 0],
            remarks_data: {
                labels: ['Underweight', 'Normal', 'Overweight', 'Obese'],
                datasets: [
                    {
                        backgroundColor: ['#FFFF00', '#41B883', '#FFA500', '#FF0000'],
                        data: [0]
                    }
                ]
            },
            age_data: {
                labels: ['7 Years Old', '8 Years Old', '9 Years Old', '10 Years Old', '11 Years Old', '12 Years Old'],
                // labels: ['1 Year Old', '2 Years Old', '3 Years Old', '4 Years Old', '5 Years Old', '6 Years Old',
                //     '7 Years Old', '8 Years Old', '9 Years Old', '10 Years Old', '11 Years Old', '12 Years Old'],
                datasets: [
                    {
                        backgroundColor: '#FFFF00',
                        label: 'Underweight',
                        data: [0, 0, 0, 0, 0, 0]
                    },
                    {
                        backgroundColor: '#41B883',
                        label: 'Normal',
                        data: [0, 0, 0, 0, 0, 0]
                    },
                    {
                        backgroundColor: '#FFA500',
                        label: 'Overweight',
                        data: [0, 0, 0, 0, 0, 0]
                    },
                    {
                        backgroundColor: '#FF0000',
                        label: 'Obese',
                        data: [0, 0, 0, 0, 0, 0]
                    }
                ]
            },
            purok_data: {
                labels: ['Purok 1', 'Purok 2', 'Purok 3', 'Purok 4', 'Purok 5', 'Purok 6', 'Purok 7', 'Purok 8',
                    'Purok 9', 'Purok 10', 'Purok 11', 'Purok 12', 'Purok 13', 'Purok 14'],
                datasets: [
                    {
                        backgroundColor: ['#c0392b', '#27ae60', '#2980b9', '#f39c12', '#8e44ad', '#2c3e50',
                            '#f1c40f', '#e67e22', '#16a085', '#d35400', '#3498db', '#9b59b6', '#00FFFF', '#FF00FF'],
                        data: [0]
                    }
                ]
            },
            options: {
                // plugins: {
                //     legend: { display: false }
                // },
                plugins: {
                    legend: {
                        labels: {
                            generateLabels: (chart: any) => {
                                const datasets = chart.data.datasets;
                                return datasets[0].data.map((data: any, i: any) => ({
                                    text: `${chart.data.labels[i]} - ${data} (${this.convert2Float(data / Number(this.childCount) * 100)}%)`,
                                    fillStyle: datasets[0].backgroundColor[i],
                                    index: i
                                }))
                            }
                        }
                    }
                },
                responsive: true,
                maintainAspectRatio: false,
            },
            ageOptions: {
                // plugins: {
                //     legend: { display: false }
                // },
                plugins: {
                    legend: {
                        labels: {
                            generateLabels: (chart: any) => {
                                const datasets = chart.data.datasets;
                                return datasets[0].data.map((data: any, i: any) => ({
                                    text: '',
                                    // fillStyle: datasets[0].backgroundColor[i],
                                    index: i
                                }))
                            }
                        }
                    }
                },
                responsive: true,
                maintainAspectRatio: false,
            },
            header_row: [
                {
                    value: 'Name',
                    fontWeight: 'bold'
                },
                {
                    value: 'Date of Birth',
                    fontWeight: 'bold'
                },
                {
                    value: 'Cost',
                    fontWeight: 'bold'
                },
                {
                    value: 'Paid',
                    fontWeight: 'bold'
                }
            ],
            data_row: [
                {
                    type: String,
                    value: 'John Smith'
                },
                {
                    type: Date,
                    value: new Date(),
                    format: 'mm/dd/yyyy'
                },
                {
                    type: Number,
                    value: 1800
                },
                {
                    type: Boolean,
                    value: true
                }
            ]
        }
    },
    methods: {
        setOpen(isOpen: boolean) {
            this.isOpen = isOpen;
        },
        fetchChildRemarks() {
            api('/child/remarks/')
                .then((response) => response.data)
                .then((data) => {
                    this.childRemarks = data
                    // this.remarks_data.datasets[0].data = [data.Underweight_Percentage, data.Normal_Percentage, data.Overweight_Percentage, data.Obese_Percentage]
                    this.remarks_data.datasets[0].data = [data.Underweight, data.Normal, data.Overweight, data.Obese]
                })
        },
        fetchChildCount() {
            api('/child/count')
                .then((response) => response.data)
                .then((data) => {
                    this.childCount = data
                })
        },
        fetchChildData() {
            api('/child/data')
                .then((response) => response.data)
                .then((data) => {
                    this.childRemarks = data
                    this.remarks_data.datasets[0].data = [data.Underweight_Percentage, data.Normal_Percentage, data.Overweight_Percentage, data.Obese_Percentage]
                })
        },
        fetchChildAge() {
            api('/child/age/remarks')
                .then((response) => response.data)
                .then((data) => {
                    Object.entries(data).forEach((data_value: any, i: number) => {
                        const [key, value] = Object.entries(data_value)

                        enum remarkIndex {
                            "Underweight" = 0,
                            "Normal" = 1,
                            "Overweight" = 2,
                            "Obese" = 3
                        }
                        const colors = ['#FFFF00', '#41B883', '#FFA500', '#FF0000']

                        this.age_data.datasets[remarkIndex[key[1] as keyof typeof remarkIndex]] = {
                            backgroundColor: colors[i],
                            label: key[1] as string,
                            data: Object.values(value[1] as number[])
                        }
                    })

                    this.countTotalChildsOfAgePerRemark = Object.values(data as number[]).reduce((acc: any, curr: any) => {
                        curr.forEach((num: number, index: number) => {
                            acc[index] = (acc[index] || 0) + num;
                        });
                        return acc;
                    }, []);

                    this.ageOptions.plugins.legend.labels.generateLabels = (chart: any) => {
                        const datasets = chart.data.datasets;

                        return datasets[0].data.map((data: any, i: any) => ({
                            text: `${chart.data.labels[i]} - ${this.countTotalChildsOfAgePerRemark[i]}`,
                            // fillStyle: datasets[0].backgroundColor[i],
                            index: i
                        }))
                    }
                })
        },
        fetchChildPurok() {
            api('/child/purok')
                .then((response) => response.data)
                .then((data) => {
                    // console.log(data)
                    this.purok_data.datasets[0].data = [data['1'], data['2'], data['3'], data['4'], data['5'], data['6'],
                    data['7'], data['8'], data['9'], data['10'], data['11'], data['12'], data['13'], data['14']]
                })
        },
        handleRefresh(event: any) {
            setTimeout(() => {
                // Any calls to load data go here
                this.fetchChildRemarks()
                this.fetchChildCount()

                event.target.complete();
            }, 1000);
        },
        async generateReport() {
            this.fetchChildData()
        },
        convert2Float(number: any) {
            return parseFloat(number).toFixed(1)
        }
    },
    ionViewDidEnter() {
        this.fetchChildRemarks()
        this.fetchChildCount()
        this.fetchChildAge()
        this.fetchChildPurok()
    },
    watch: {
        $route() {
            this.$nextTick(this.fetchChildCount);
        }
    }
});
</script>

<style scoped>
ion-content {
    --background: var(--ion-color-light);
}

#app-info {
    --background: #36af7a;
    --color: white;
    text-align: center;
    box-shadow: none;
}

ion-col {
    display: flex;
    text-align: center;
}

ion-col>ion-card {
    flex: 1;
    margin: 0 !important;
}

ion-col>ion-card:nth-child(odd) {
    margin-right: 4px !important;
}

.remark {
    font-size: 15px;
}

.remark ion-col {
    /* padding: 5px; */
    margin: 0;
    display: block;
    height: 100%;
}

.remark ion-card-subtitle {
    padding: 7px;
    border-radius: 5px;
    font-size: 18px;
}
</style>
