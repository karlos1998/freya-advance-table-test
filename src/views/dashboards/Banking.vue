<script setup>
import { useLayout } from '@/layout/composables/layout';
import { FilterMatchMode } from '@primevue/core/api';
import { useConfirm } from 'primevue/useconfirm';
import { useToast } from 'primevue/usetoast';
import { onBeforeUnmount, onMounted, ref, watch } from 'vue';

const { getPrimary, getSurface, isDarkTheme } = useLayout();

const confirm = useConfirm();
const toast = useToast();

const chartData = ref(null);
const chartOptions = ref(null);
const currencyChartOptions = ref(null);

const usdChartData = ref(null);
const btcChartData = ref(null);
const poundChartData = ref(null);

const pieData = ref(null);
const pieOptions = ref(null);

const dateRanges = ref([]);
const selectedDate = ref('');

const dateRanges2 = ref([]);
const selectedDate2 = ref('');

const op = ref(null);
const op2 = ref(null);
const op3 = ref(null);
const menu = ref(null);

const selectedCard = ref([]);

const displayBasic = ref(false);
const cardName = ref(null);
const cardno = ref('');
const cardDate = ref(null);
const cvv = ref(null);

const accounts = ref(null);
const accountNumber = ref(null);
const selectedAccount = ref(null);
const filteredAccounts = ref([]);

const subscriptions = ref(null);
const selectedSubscription = ref(null);
const filteredSubscriptions = ref([]);

let subscription = null;
const amount = ref(null);
const transactions = ref([]);
const items = ref([]);
const inputValue = ref(null);
const cards = ref([
    {
        logo: '/demo/images/logo-freya-single.svg',
        cardNo: '5454-5454-9999-8888',
        validDate: '05/28',
        name: 'John Doe'
    },
    {
        logo: '/demo/images/logo-freya-single.svg',
        cardNo: '5454-5454-9999-7777',
        validDate: '08/26',
        name: 'John Doe'
    }
]);

const filterSalesTable = ref({
    global: { value: null, matchMode: FilterMatchMode.CONTAINS }
});

onMounted(() => {
    selectedCard.value = cards.value[0];

    dateRanges.value = [
        { name: 'Daily', code: 'DAY' },
        { name: 'Weekly', code: 'WEEK' },
        { name: 'Monthly', code: 'MONTH' }
    ];

    dateRanges2.value = [
        { name: 'Last 7 Days', code: '7day' },
        { name: 'Last 30 Days', code: '30day' },
        { name: 'Last 90 Days', code: '90day' }
    ];

    selectedDate.value = dateRanges.value[2];
    selectedDate2.value = dateRanges2.value[0];

    accounts.value = [
        {
            photo: '/demo/images/avatar/amyelsner.png',
            accountNo: '** 4848',
            name: 'Amy Elsner'
        },
        {
            photo: '/demo/images/avatar/annafali.png',
            accountNo: '** 4848',
            name: 'Anna Fali'
        },
        {
            photo: '/demo/images/avatar/bernardodominic.png',
            accountNo: '** 4848',
            name: 'Bernardo Dominic'
        },
        {
            photo: '/demo/images/avatar/ivanmagalhaes.png',
            accountNo: '** 4848',
            name: 'Ivan Magalhaes'
        },
        {
            photo: '/demo/images/avatar/stephenshaw.png',
            accountNo: '** 4848',
            name: 'Stephen Shaw'
        }
    ];

    subscriptions.value = [
        {
            accountNo: '548268',
            name: 'Electric Bill',
            amount: 15,
            due: 'close'
        },
        {
            image: '/demo/images/dashboard/brands/hbo-logo.png',
            accountNo: '845152848',
            name: 'TV Subscription',
            amount: 120,
            due: ''
        },
        {
            image: '/demo/images/dashboard/brands/netflix-logo.png',
            accountNo: '659815523',
            name: 'Netflix Subscription',
            amount: 48,
            due: 'close'
        },
        {
            image: '/demo/images/dashboard/brands/harvard-logo.png',
            accountNo: '*6585122',
            name: 'Education Payment',
            amount: 45,
            due: 'late'
        }
    ];

    transactions.value = [
        {
            image: '/demo/images/avatar/amyelsner.png',
            accountNo: '** 4848',
            action: 'Bank Transfer',
            name: 'Amy Elsner',
            amount: 112.0
        },
        {
            image: '/demo/images/avatar/annafali.png',
            accountNo: '** 4848',
            action: 'Bank Transfer',
            name: 'Anna Fali',
            amount: -112.0
        },
        {
            image: '/demo/images/dashboard/brands/netflix-logo.png',
            accountNo: '** 4848',
            action: 'Subscription Payment',
            name: 'Netflix Subscription',
            amount: -48.0
        },
        {
            image: '',
            accountNo: '** 4848',
            action: 'Bill Payment',
            name: 'Electric Bill',
            amount: -48.0
        },
        {
            image: '/demo/images/avatar/ivanmagalhaes.png',
            accountNo: '** 4848',
            action: 'Bank Transfer',
            name: 'Ivan Magalhaes',
            amount: -112.0
        },
        {
            image: '/demo/images/avatar/stephenshaw.png',
            accountNo: '** 4848',
            action: 'Bank Transfer',
            name: 'Stephen Shaw',
            amount: 112.0
        }
    ];

    items.value = [
        {
            icon: 'pi pi-refresh',
            label: 'Re-send or Pay'
        },
        {
            icon: 'pi pi-external-link',
            label: 'Details'
        },
        {
            icon: 'pi pi-download',
            label: 'Download doc'
        }
    ];

    initChart();
});

onBeforeUnmount(() => {
    if (subscription) {
        subscription.unsubscribe();
    }
});

watch([selectedDate, selectedDate2], () => {
    onDateChangeBarChart();
});

watch(selectedAccount, (newVal) => {
    if (newVal) {
        inputValue.value = newVal.accountNo;
    }
});

watch(
    [getPrimary, getSurface, isDarkTheme],
    () => {
        initChart();
    },
    { immediate: true }
);

function initChart() {
    const documentStyle = getComputedStyle(document.documentElement);
    const textColor = documentStyle.getPropertyValue('--text-color');
    const textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
    const surfaceBorder = documentStyle.getPropertyValue('--surface-border');

    chartData.value = {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
        datasets: [
            {
                label: 'Income',
                data: [8000, 8100, 5600, 5500, 4000, 6500, 5900, 8000, 8100, 5600, 5500, 4000],
                fill: false,
                borderColor: documentStyle.getPropertyValue('--p-green-300'),
                tension: 0.4,
                borderWidth: 2,
                backgroundColor: '#4caf5061',
                borderRadius: 6
            },
            {
                label: 'Expenses',
                data: [1200, 5100, 6200, 3300, 2100, 6200, 4500, 1200, 5100, 6200, 3300, 2100],
                borderColor: documentStyle.getPropertyValue('--p-red-300'),
                backgroundColor: '#ff3d3238',
                tension: 0.4,
                borderWidth: 2,
                borderRadius: 6
            }
        ]
    };

    usdChartData.value = {
        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
        datasets: [
            {
                label: 'Euro to US Dollar',
                backgroundColor: documentStyle.getPropertyValue('--primary-100'),
                borderColor: documentStyle.getPropertyValue('--primary-100'),
                data: [1.1, 1.12, 1.15, 1.18, 1.2, 1.25, 1.3],
                barThickness: 10
            }
        ]
    };

    btcChartData.value = {
        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
        datasets: [
            {
                label: 'Bitcoin to US Dollar',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-100'),
                borderColor: documentStyle.getPropertyValue('--p-primary-100'),
                data: [35000, 40000, 45000, 55000, 60000, 65000, 60000],
                barThickness: 10
            }
        ]
    };

    poundChartData.value = {
        labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
        datasets: [
            {
                label: 'GBP to US Dollar',
                backgroundColor: documentStyle.getPropertyValue('--p-primary-100'),
                borderColor: documentStyle.getPropertyValue('--p-primary-100'),
                data: [1.3, 1.35, 1.4, 1.45, 1.5, 1.55, 1.6],
                barThickness: 10
            }
        ]
    };

    chartOptions.value = {
        animation: {
            duration: 0
        },
        maintainAspectRatio: false,
        plugins: {
            legend: {
                labels: {
                    color: textColor,
                    usePointStyle: true,
                    boxHeight: 15,
                    pointStyleWidth: 17,
                    padding: 14
                }
            }
        },
        scales: {
            x: {
                ticks: {
                    color: textColorSecondary
                },
                grid: {
                    color: surfaceBorder
                }
            },
            y: {
                ticks: {
                    color: textColorSecondary
                },
                grid: {
                    color: surfaceBorder
                }
            }
        }
    };
    currencyChartOptions.value = {
        animation: {
            duration: 0
        },
        plugins: {
            legend: {
                labels: {
                    color: textColor,
                    usePointStyle: true,
                    boxHeight: 15,
                    pointStyleWidth: 17,
                    padding: 14
                }
            },
            tooltip: {
                callbacks: {
                    label: function (context) {
                        let label = context.dataset.label || '';

                        if (label) {
                            label += ':';
                        }

                        if (context.parsed.y !== null) {
                            label += new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(context.parsed.y);
                        }
                        return label;
                    }
                }
            }
        },
        scales: {
            x: {
                ticks: {
                    color: textColorSecondary
                },
                grid: {
                    color: surfaceBorder
                }
            },
            y: {
                ticks: {
                    color: textColorSecondary
                },
                grid: {
                    color: surfaceBorder
                }
            }
        }
    };

    pieData.value = {
        labels: ['Entertainment', 'Platform', 'Shopping', 'Transfers'],
        datasets: [
            {
                data: [300, 50, 100, 80],
                backgroundColor: [documentStyle.getPropertyValue('--p-primary-300'), documentStyle.getPropertyValue('-p--orange-300'), documentStyle.getPropertyValue('--p-green-300'), documentStyle.getPropertyValue('--p-cyan-300')],
                borderColor: surfaceBorder
            }
        ]
    };

    pieOptions.value = {
        animation: {
            duration: 0
        },
        maintainAspectRatio: false,
        plugins: {
            legend: {
                labels: {
                    color: textColor,
                    usePointStyle: true,
                    padding: 14,
                    boxHeight: 15,
                    pointStyleWidth: 17
                },
                position: 'bottom'
            }
        }
    };
}

function onDateChangeBarChart() {
    const documentStyle = getComputedStyle(document.documentElement);

    const monthlyData = {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
        datasets: [
            {
                label: 'Income',
                data: [8000, 8100, 5600, 5500, 4000, 6500, 5900, 8000, 8100, 5600, 5500, 4000],
                fill: false,
                borderColor: documentStyle.getPropertyValue('--p-green-300'),
                tension: 0.4,
                borderWidth: 2,
                backgroundColor: '#4caf5061',
                borderRadius: 6
            },
            {
                label: 'Expenses',
                data: [1200, 5100, 6200, 3300, 2100, 6200, 4500, 1200, 5100, 6200, 3300, 2100],
                borderColor: documentStyle.getPropertyValue('--p-red-300'),
                backgroundColor: '#ff3d3238',
                tension: 0.4,
                borderWidth: 2,
                borderRadius: 6
            }
        ]
    };

    const dailyData = {
        labels: [
            'Day 1',
            'Day 2',
            'Day 3',
            'Day 4',
            'Day 5',
            'Day 6',
            'Day 7',
            'Day 8',
            'Day 9',
            'Day 10',
            'Day 11',
            'Day 12',
            'Day 13',
            'Day 14',
            'Day 15',
            'Day 16',
            'Day 17',
            'Day 18',
            'Day 19',
            'Day 20',
            'Day 21',
            'Day 22',
            'Day 23',
            'Day 24',
            'Day 25',
            'Day 26',
            'Day 27',
            'Day 28',
            'Day 29',
            'Day 30'
        ],
        datasets: [
            {
                label: 'Income',
                data: [100, 200, 150, 50, 75, 150, 200, 250, 300, 400, 350, 500, 550, 700, 600, 650, 550, 450, 350, 300, 250, 200, 150, 100, 50, 75, 150, 200, 250],
                fill: false,
                borderColor: documentStyle.getPropertyValue('--p-green-300'),
                tension: 0.4,
                borderWidth: 2,
                backgroundColor: '#4caf5061',
                borderRadius: 6
            },
            {
                label: 'Expenses',
                data: [75, 150, 100, 200, 250, 300, 350, 400, 450, 550, 600, 650, 550, 700, 600, 550, 350, 400, 300, 250, 200, 150, 100, 50, 75, 150, 200, 250, 300],
                borderColor: documentStyle.getPropertyValue('--p-red-300'),
                backgroundColor: '#ff3d3238',
                tension: 0.4,
                borderWidth: 2,
                borderRadius: 6
            }
        ]
    };

    const weeklyData = {
        labels: [
            'Week 1',
            'Week 2',
            'Week 3',
            'Week 4',
            'Week 5',
            'Week 6',
            'Week 7',
            'Week 8',
            'Week 9',
            'Week 10',
            'Week 11',
            'Week 12',
            'Week 13',
            'Week 14',
            'Week 15',
            'Week 16',
            'Week 17',
            'Week 18',
            'Week 19',
            'Week 20',
            'Week 21',
            'Week 22',
            'Week 23',
            'Week 24'
        ],
        datasets: [
            {
                label: 'Income',
                data: [2500, 2000, 1500, 1000, 500, 2000, 2500, 3000, 3500, 4000, 4500, 5000, 6000, 7000, 6000, 5000, 4000, 3500, 3000, 2500, 2000, 1500, 1000, 500],
                fill: false,
                borderColor: documentStyle.getPropertyValue('--p-green-300'),
                tension: 0.4,
                borderWidth: 2,
                backgroundColor: '#4caf5061',
                borderRadius: 6
            },
            {
                label: 'Expenses',
                data: [1500, 1000, 500, 2000, 2500, 3000, 3500, 4000, 4500, 5000, 6000, 7000, 6000, 5000, 4000, 3500, 3000, 2500, 2000, 1500, 1000, 500, 2000, 2500],
                borderColor: documentStyle.getPropertyValue('--p-red-300'),
                backgroundColor: '#ff3d3238',
                tension: 0.4,
                borderWidth: 2,
                borderRadius: 6
            }
        ]
    };

    let newBarData = { ...chartData.value };
    switch (selectedDate.value.name) {
        case 'Monthly':
            newBarData = monthlyData;
            break;
        case 'Weekly':
            newBarData = weeklyData;
            break;
        case 'Daily':
            newBarData = dailyData;
            break;
        default:
            break;
    }

    chartData.value = newBarData;
}

function onDateChangePieChart() {
    const documentStyle = getComputedStyle(document.documentElement);

    const last30Data = {
        labels: ['Entertainment', 'Platform', 'Shopping', 'Transfers'],
        datasets: [
            {
                data: [300, 50, 100, 80],
                backgroundColor: [documentStyle.getPropertyValue('--p-primary-300'), documentStyle.getPropertyValue('--p-orange-300'), documentStyle.getPropertyValue('--p-green-300'), documentStyle.getPropertyValue('--cp-yan-300')]
            }
        ]
    };

    const last7Data = {
        labels: ['Entertainment', 'Platform', 'Shopping', 'Transfers'],
        datasets: [
            {
                data: [450, 50, 200, 120],
                backgroundColor: [documentStyle.getPropertyValue('--p-primary-300'), documentStyle.getPropertyValue('--p-orange-300'), documentStyle.getPropertyValue('--p-green-300'), documentStyle.getPropertyValue('--p-cyan-300')]
            }
        ]
    };

    const last90Data = {
        labels: ['Entertainment', 'Platform', 'Shopping', 'Transfers'],
        datasets: [
            {
                data: [30, 200, 150, 20],
                backgroundColor: [documentStyle.getPropertyValue('--pp-rimary-300'), documentStyle.getPropertyValue('--p-orange-300'), documentStyle.getPropertyValue('--p-green-300'), documentStyle.getPropertyValue('--p-cyan-300')]
            }
        ]
    };

    let newPieData = { ...pieData.value };
    switch (selectedDate2.value.code) {
        case '7day':
            newPieData = last7Data;
            break;
        case '30day':
            newPieData = last30Data;
            break;
        case '90day':
            newPieData = last90Data;
            break;
        default:
            break;
    }

    pieData.value = newPieData;
}

const filterAccounts = (event) => {
    let filtered = [];
    let query = event.query;

    for (let i = 0; i < accounts.value.length; i++) {
        let country = accounts.value[i];
        if (country.name.toLowerCase().indexOf(query.toLowerCase()) == 0) {
            filtered.push(country);
        }
    }

    filteredAccounts.value = filtered;
};

const filterSubscription = (event) => {
    const filtered = [];
    const query = event.query;

    for (let i = 0; i < subscriptions.value.length; i++) {
        let country = subscriptions.value[i];
        if (country.name.toLowerCase().indexOf(query.toLowerCase()) == 0) {
            filtered.push(country);
        }
    }
    filteredSubscriptions.value = filtered;
};

function showBasicDialog() {
    displayBasic.value = true;
}

function addCreditCard() {
    const card = {
        logo: '/demo/images/logo-freya-single.svg',
        cardNo: cardno.value,
        validDate: cardDate.value,
        name: cardName.value
    };
    cards.value.push(card);
    displayBasic.value = false;
}

const confirm1 = (name, amount) => {
    confirm.require({
        message: 'Are you sure that you want to send $' + amount + ' to ' + name + '?',
        header: 'Confirmation',
        icon: 'pi pi-exclamation-triangle',
        accept: () => {
            toast.add({ severity: 'info', summary: 'Confirmed', detail: 'You sent $' + amount + ' to ' + name, life: 3000 });
        },
        reject: () => {
            toast.add({ severity: 'warn', summary: 'canceled', detail: 'Your transaction canceled', life: 3000 });
        }
    });
};

const confirm2 = (name, amount) => {
    confirm.require({
        message: 'Are you sure that you want to pay $' + amount + ' for your ' + name + '?',
        header: 'Confirmation',
        icon: 'pi pi-exclamation-triangle',
        accept: () => {
            toast.add({ severity: 'info', summary: 'Confirmed', detail: 'You sent $' + amount + ' to ' + name, life: 3000 });
        },
        reject: () => {
            toast.add({ severity: 'warn', summary: 'canceled', detail: 'Your transaction canceled', life: 3000 });
        }
    });
};
</script>

<template>
    <div class="layout-banking-dashboard">
        <div class="grid grid-cols-12 gap-4">
            <div class="col-span-12">
                <Message
                    class="!h-18 !rounded-3xl !bg-surface-0 dark:!bg-surface-900 !font-medium !text-surface-500 dark:!text-surface-400 !outline-transparent !-mt-1 !ml-4 !mb-4"
                    :class="{ 'dark-mode': isDarkTheme }"
                    :pt="{
                        root: { style: { borderLeft: 'none' } },
                        content: '!p-6'
                    }"
                >
                    👋 Hello! Welcome to Freya! Before start please complete your profile to know you better.</Message
                >
            </div>
            <div class="col-span-12">
                <div class="flex flex-col sm:flex-row items-center gap-6">
                    <div class="flex flex-col sm:flex-row items-center gap-4 pb-2">
                        <img src="/demo/images/avatar/amyelsner.png" class="w-12 h-12 flex-shrink-0" />
                        <div class="flex flex-col items-center sm:items-start">
                            <span class="text-surface-800 dark:text-surface-50 text-2xl font-medium m-0 mb-1">Welcome Amy</span>
                            <p class="text-surface-600 dark:text-surface-200 m-0">Your last login was on 01/02/2023 at 10:24 am</p>
                        </div>
                    </div>
                    <div class="flex flex-wrap gap-2 sm:ml-auto justify-center">
                        <div class="exchnage-rates flex">
                            <div class="flex items-center p-2 cursor-pointer hover:bg-surface-0 dark:hover:bg-surface-900 rounded duration-200" @click="op.toggle($event)">
                                <i class="pi pi-angle-up text-green-500 mr-1"></i>
                                <i class="pi pi-dollar"></i> <span>1.32</span>
                            </div>
                            <Popover ref="op" :style="{ width: '300px' }">
                                <h5 class="font-medium m-0 mb-1">USD to EUR</h5>
                                <p class="m-0 p-0 mb-4">
                                    Lorem ipsum, dolor sit amet consectetur adipisicing elit. <a class="ml-1">more detail <i class="pi pi-arrow-up" style="transform: rotate(45deg)"></i></a>
                                </p>

                                <Chart type="bar" :height="180" :data="usdChartData" :options="currencyChartOptions"></Chart>
                            </Popover>
                            <div class="flex items-center p-2 cursor-pointer hover:bg-surface-0 dark:hover:bg-surface-900 rounded duration-200" @click="op2.toggle($event)">
                                <i class="pi pi-angle-down mr-1" style="color: #ff6e49"></i>
                                <i class="pi pi-fw pi-bitcoin"></i> <span>60,000</span>
                            </div>
                            <Popover ref="op2" :style="{ width: '300px' }">
                                <h5 class="font-medium m-0 mb-1">BTC to USD</h5>
                                <p class="m-0 p-0 mb-4">
                                    Lorem ipsum, dolor sit amet consectetur adipisicing elit. <a class="ml-1">more detail <i class="pi pi-arrow-up" style="transform: rotate(45deg)"></i></a>
                                </p>

                                <Chart type="bar" :height="180" :data="btcChartData" :options="currencyChartOptions"></Chart>
                            </Popover>
                            <div class="flex items-center p-2 cursor-pointer hover:bg-surface-0 dark:hover:bg-surface-900 rounded duration-200" @click="op3.toggle($event)">
                                <i class="pi pi-angle-up text-green-500 mr-1"></i>
                                <i class="pi pi-pound"></i> <span>1.60</span>
                            </div>
                            <Popover ref="op3" :style="{ width: '300px' }">
                                <h5 class="font-medium m-0 mb-1">GBP to USD</h5>
                                <p class="m-0 p-0 mb-4">
                                    Lorem ipsum, dolor sit amet consectetur adipisicing elit. <a class="ml-1">more detail <i class="pi pi-arrow-up" style="transform: rotate(45deg)"></i></a>
                                </p>

                                <Chart type="bar" :height="180" :data="poundChartData" :options="currencyChartOptions"></Chart>
                            </Popover>
                        </div>
                        <div class="flex gap-2">
                            <Button type="button" v-tooltip.bottom="'Exchange'" placeholder="bottom" icon="pi pi-arrows-h" class="flex-shrink-0" rounded outlined></Button>
                            <Button type="button" v-tooltip.bottom="'Withdraw'" placeholder="bottom" icon="pi pi-download" class="flex-shrink-0" rounded outlined></Button>
                            <Button type="button" v-tooltip.bottom="'Send'" placeholder="bottom" icon="pi pi-send" class="flex-shrink-0" rounded></Button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-span-12 xl:col-span-8">
                <div class="flex flex-col sm:flex-row gap-4 mx-2 mb-4">
                    <div class="w-full sm:w-1/3">
                        <div class="card bg-surface-0 dark:bg-surface-900 text-surface-700 dark:text-surface-100 flex justify-between pt-6 h-full">
                            <div class="overview-info">
                                <div class="m-0 mb-1 font-semibold text-lg">Total Balance</div>
                                <div class="m-0 text-4xl font-semibold">$3879.76</div>
                            </div>
                            <i class="pi pi-dollar !text-3xl"></i>
                        </div>
                    </div>
                    <div class="w-full sm:w-1/3">
                        <div class="card bg-orange-300 text-white flex justify-between pt-6 h-full">
                            <div class="overview-info">
                                <div class="m-0 mb-1 text-white font-semibold text-l">Total Spending</div>
                                <div class="m-0 text-white text-4xl font-semibold">$843.64</div>
                            </div>
                            <i class="pi pi-dollar !text-3xl"></i>
                        </div>
                    </div>
                    <div class="w-full sm:w-1/3">
                        <div class="card bg-blue-300 text-white flex justify-between pt-6 h-full">
                            <div class="overview-info">
                                <div class="m-0 mb-1 text-white font-semibold text-l">Subscriptions</div>
                                <div class="m-0 text-white text-4xl font-semibold">$126.82</div>
                            </div>
                            <i class="pi pi-dollar !text-3xl"></i>
                        </div>
                    </div>
                </div>
                <div>
                    <div class="col-span-12 p-2">
                        <div class="card">
                            <div class="card-header gap-4">
                                <div class="card-title">
                                    <div class="font-semibold mb-2">Money Flow</div>
                                    <p class="subtitle">
                                        Your <b>{{ selectedDate.name }}</b> Income & Expenses data.
                                    </p>
                                </div>
                                <Select :options="dateRanges" v-model="selectedDate" placeholder="Monthly" optionLabel="name" :showClear="false" class="w-36" @onChange="onDateChangeBarChart()"></Select>
                            </div>
                            <Chart type="bar" :height="540" :data="chartData" :options="chartOptions"></Chart>
                        </div>
                    </div>
                </div>
                <Menu ref="menu" :popup="true" :model="items"></Menu>
            </div>
            <div class="col-span-12 xl:col-span-4">
                <Dialog header="Add New Card" v-model:visible="displayBasic" modal :style="{ width: '50vw' }">
                    <template #header>
                        <div class="block w-8/12">
                            <h2 class="p-0 m-0 mb-4">Add your new card</h2>
                            <p class="p-0 m-0">Lorem ipsum dolor sit amet consectetur adipisicing elit. Tempore dolores quasi consequuntur eveniet iure perspiciatis.</p>
                        </div>
                    </template>
                    <div class="flex justify-between items-center gap-16">
                        <div class="card-form w-full">
                            <label for="name" class="block">Name on your card</label>
                            <InputText class="w-full mb-4" type="text" id="name" v-model="cardName" />
                            <label for="cardno" class="block">Your card number</label>
                            <InputMask class="w-full mb-4" id="cardno" v-model="cardno" mask="9999-9999-9999-9999" :autoClear="false"></InputMask>
                            <div class="flex gap-4">
                                <div class="mb-4 w-6/12">
                                    <label for="carddate" class="block">Your card's valid date</label>
                                    <InputMask class="w-full" id="carddate" v-model="cardDate" :autoClear="false" mask="99/99"></InputMask>
                                </div>
                                <div class="mb-4 w-6/12">
                                    <label for="cvv" class="block">CVV on your card</label>
                                    <InputMask class="w-full" id="cvv" v-model="cvv" :autoClear="false" mask="999"></InputMask>
                                </div>
                            </div>
                        </div>

                        <div class="px-4 xl:px-8 py-8 w-full rounded-2xl shadow-md" style="background: linear-gradient(to left bottom, var(--p-primary-100), var(--p-primary-400)); min-height: 19rem">
                            <div class="mb-8">
                                <img src="/demo/images/logo-freya-single.svg" class="w-8" alt="" />
                            </div>
                            <div class="mb-4">
                                <h3 class="text-surface-0 dark:text-surface-900" style="letter-spacing: -0.5px; min-height: 2.09rem">{{ cardno }}</h3>
                                <div class="text-surface-0 dark:text-surface-900 flex items-center justify-end">
                                    <span class="m-0 text-sm p-0 mr-2">Valid <br />thru</span>
                                    <h4 class="m-0 text-surface-0 dark:text-surface-900" style="min-width: 4.1rem">{{ cardDate }}</h4>
                                </div>
                                <h4 class="text-surface-0 dark:text-surface-900" style="min-height: 1.7rem">
                                    {{ cardName }}
                                </h4>
                            </div>
                        </div>
                    </div>
                    <template #footer>
                        <Button icon="pi pi-check" @click="addCreditCard()" label="Save Card" text></Button>
                    </template>
                </Dialog>
                <div class="grid grid-cols-12 gap-4">
                    <div class="col-span-12 md:col-span-6 xl:col-span-12">
                        <div class="card px-0" v-if="cards">
                            <div class="card-header gap-4" style="padding: 0 28px 16px">
                                <div class="card-title">
                                    <span class="font-semibold">My Cards ({{ cards.length }})</span>
                                    <p class="subtitle">You can always add more</p>
                                </div>
                                <Button type="button" icon="pi pi-plus" severity="secondary" text rounded @click="showBasicDialog()"></Button>
                            </div>
                            <Carousel :value="cards" :numVisible="1" :numScroll="1" circular>
                                <template #item="slotProps">
                                    <div class="px-2 pb-2">
                                        <div class="px-4 xl:px-8 py-8 w-full rounded-2xl shadow-md" style="background: linear-gradient(to left bottom, var(--p-primary-100), var(--p-primary-400))">
                                            <div class="mb-8">
                                                <img :src="slotProps.data.logo" class="w-8" alt="" />
                                            </div>
                                            <div class="mb-4">
                                                <h3 class="text-surface-0 dark:text-surface-900" style="letter-spacing: -0.5px">**** **** **** {{ slotProps.data.cardNo.split('-')[3] }}</h3>
                                                <div class="text-surface-0 dark:text-surface-900 flex items-center justify-end">
                                                    <span class="m-0 text-sm p-0 mr-2">Valid <br />thru</span>
                                                    <h4 class="m-0 text-surface-0 dark:text-surface-900">{{ slotProps.data.validDate }}</h4>
                                                </div>
                                                <h4 class="text-surface-0 dark:text-surface-900">
                                                    {{ slotProps.data.name }}
                                                </h4>
                                            </div>
                                        </div>
                                    </div>
                                </template>
                            </Carousel>
                        </div>
                    </div>
                    <div class="col-span-12 md:col-span-6 xl:col-span-12">
                        <div class="card" v-if="cards">
                            <div class="card-header gap-4">
                                <div class="card-title">
                                    <div class="font-semibold mb-2">Quick Actions</div>
                                    <p class="subtitle">Send money or pay your bills.</p>
                                </div>
                                <Select :options="cards" v-model="selectedCard" :placeholder="selectedCard.cardNo" optionLabel="cardNo" :showClear="false" class="w-32" panelclass="w-32">
                                    <template #option="slotProps">
                                        <span class="block">****{{ slotProps.option.cardNo.split('-')[3] }}</span>
                                    </template>
                                </Select>
                            </div>
                            <ConfirmDialog :style="{ width: '360px' }" :baseZIndex="10000"></ConfirmDialog>
                            <Tabs value="0">
                                <TabList>
                                    <Tab value="0">Send Money</Tab>
                                    <Tab value="1">Pay Subscriptions or Bills</Tab>
                                </TabList>
                                <TabPanels>
                                    <TabPanel value="0">
                                        <AutoComplete class="w-full p-0" v-model="selectedAccount" placeholder="Enter Name" :suggestions="filteredAccounts" @complete="filterAccounts" field="name" dropdown>
                                            <template #item="slotProps">
                                                <div class="name-item flex items-center justify-between">
                                                    <div class="flex items-center">
                                                        <img v-if="slotProps.item.photo.length > 0" class="w-8 h-8 rounded-full" :src="slotProps.item.photo" />
                                                        <div v-else class="w-8 h-8 rounded-full flex justify-center items-center uppercase font-medium bg-surface-100 dark:bg-surface-700">
                                                            {{ slotProps.item.name[0] }}
                                                        </div>
                                                        <div class="ml-2">{{ slotProps.item.name }}</div>
                                                    </div>
                                                    <div>{{ slotProps.item.accountNo }}</div>
                                                </div>
                                            </template>
                                        </AutoComplete>

                                        <div class="flex items-center my-4 gap-4">
                                            <InputText class="w-8/12" type="text" v-model="inputValue" placeholder="Enter Account No" />
                                            <InputNumber v-model="amount" inputId="currency-us" mode="currency" placeholder="$00.00" currency="USD" locale="en-US"></InputNumber>
                                        </div>
                                        <Button icon="pi pi-send" type="button" label="Send" class="w-full" outlined @click="confirm1(selectedAccount.name ? selectedAccount.name : selectedAccount, amount)"></Button>
                                    </TabPanel>
                                    <TabPanel value="1">
                                        <AutoComplete
                                            class="w-full"
                                            v-model="selectedSubscription"
                                            placeholder="Enter Subscription or Bill Company"
                                            :suggestions="filteredSubscriptions"
                                            @complete="filterSubscription($event)"
                                            field="name"
                                            :dropdown="true"
                                        >
                                            <template #option="subscription">
                                                <div class="name-item flex items-center justify-between">
                                                    <div class="flex items-center">
                                                        <img v-if="subscription.option.image && subscription.option.image.length > 0" class="w-8 h-8 rounded-full" :src="subscription.option.image" />
                                                        <span v-else class="w-8 h-8 rounded-full flex justify-center items-center uppercase font-medium bg-surface-100 dark:bg-surface-700">
                                                            {{ subscription.option.name[0] }}
                                                        </span>

                                                        <div class="ml-2">{{ subscription.option.name }}</div>
                                                    </div>
                                                    <div class="flex items-center gap-1">
                                                        <small :style="{ color: subscription.due === 'late' ? '#fe572c' : subscription.due === 'close' ? '#ffc800' : '' }">{{
                                                            subscription.due === 'late' ? 'late' : subscription.due === 'close' ? 'close' : ''
                                                        }}</small>
                                                        <div class="rounded-2xl p-1 text-center" :style="{ backgroundColor: subscription.due === 'late' ? '#ff6e493a' : subscription.due === 'close' ? '#ffd8493a' : '' }">
                                                            ${{ subscription.option.amount }}
                                                        </div>
                                                    </div>
                                                </div>
                                            </template>
                                        </AutoComplete>

                                        <div class="flex items-center my-4 gap-4">
                                            <InputText class="w-8/12" type="text" :v-model="selectedSubscription ? selectedSubscription.accountNo : accountNumber" placeholder="Enter User No" />
                                            <InputNumber :v-model="selectedSubscription ? selectedSubscription.amount : amount" inputId="currency-us" placeholder="$00.00" mode="currency" currency="USD" locale="en-US"></InputNumber>
                                        </div>
                                        <Button
                                            icon="pi pi-wallet"
                                            type="button"
                                            label="Pay"
                                            class="w-full"
                                            outlined
                                            @click="confirm2(selectedSubscription.name ? selectedSubscription.name : selectedSubscription, selectedSubscription ? selectedSubscription.amount : amount)"
                                        ></Button>
                                    </TabPanel>
                                </TabPanels>
                            </Tabs>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-span-12 xl:col-span-6 p-2">
                <div class="card h-full">
                    <div class="card-header gap-4 flex-wrap md:flex-nowrap">
                        <div class="card-title">
                            <div class="font-semibold mb-2">Transactions</div>
                            <p class="subtitle">
                                Your <b>{{ selectedDate.name }}</b> Income & Expenses data.
                            </p>
                        </div>
                        <div class="inline-flex items-center w-full md:w-auto">
                            <IconField class="flex-auto">
                                <InputIcon class="pi pi-search" />
                                <InputText type="text" v-model="filterSalesTable.global.value" placeholder="Search" class="w-full" style="border-radius: 2rem" />
                            </IconField>
                            <Button icon="pi pi-upload" class="mx-4 flex-shrink-0" rounded @click="dt.exportCSV()"></Button>
                        </div>
                    </div>
                    <DataTable :value="transactions" :paginator="true" :rows="3" v-model:filters="filterSalesTable">
                        <Column>
                            <template #body="slotProps">
                                <img v-if="slotProps.data.image.length > 0" class="w-8 h-8 rounded-full mt-2" :src="slotProps.data.image" />
                                <span v-else class="w-8 h-8 rounded-full flex justify-center items-center uppercase font-medium bg-surface-100 dark:bg-surface-700 mt-2">
                                    {{ slotProps.data.name[0] }}
                                </span>
                            </template>
                        </Column>

                        <Column field="name" header="Name"></Column>

                        <Column field="action" header="Action"></Column>

                        <Column field="accountNo" header="Account&User No"></Column>

                        <Column header="Amount">
                            <template #body="slotProps">
                                <span class="rounded-xl p-1 w-16 text-center block font-medium" :style="slotProps.data.amount > 0 ? 'background-color:#8fff493a;' : 'background-color:#ff6e493a;'">${{ slotProps.data.amount }}</span>
                            </template>
                        </Column>

                        <Column>
                            <template #body>
                                <Button icon="pi pi-ellipsis-v" rounded text @click="menu.toggle($event)"></Button>
                            </template>
                        </Column>
                    </DataTable>
                </div>
            </div>
            <div class="col-span-12 sm:col-span-6 xl:col-span-3">
                <div class="card h-full">
                    <div class="card-header gap-4">
                        <div class="card-title">
                            <div class="font-semibold mb-2">All Expenses</div>
                            <p class="subtitle">
                                Your <b>{{ selectedDate.name }}</b> Expenses data.
                            </p>
                        </div>
                        <Select :options="dateRanges2" v-model="selectedDate2" placeholder="Last 7 Days" optionLabel="name" :showClear="false" class="w-32" @onChange="onDateChangePieChart()"></Select>
                    </div>
                    <Chart type="doughnut" :data="pieData" :options="pieOptions" :height="300"></Chart>
                </div>
            </div>
            <div class="col-span-12 sm:col-span-6 xl:col-span-3 p-4">
                <div class="card h-full flex items-center justify-start p-8 bg-gradient-to-b from-primary-100 to-primary-400">
                    <div class="flex flex-col">
                        <span class="m-0 mb-6 w-9/12 font-bold text-3xl">Upgrade to premium banking!</span>
                        <span class="m-0 mb-12 w-10/12 font-semibold text-2xl">Lightning-fast transactions, no fees, and exclusive discounts from top brands!</span>
                        <Button icon="pi pi-wallet" type="button" label="Upgrade Now" size="large" class="w-full"></Button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
