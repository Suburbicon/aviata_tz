<template>
    <div class="main-page">
        <div class="main-page__col1">
            <AppFilter
                @clear="clearTariffSearch"
                title="Опции тарифа"
                :content="tariffOptions"
                :showClearIconToggle="showClearIcon.tariff"
                class="main-page__col-item"
                v-model:valueCheckbox="tariffCheckbox"
            />
            <AppFilter
                @clear="clearAirlinesSearch"
                title="Авиакомпании"
                :content="airlines"
                :showClearIconToggle="showClearIcon.airlines"
                v-model:valueCheckbox="airlinesCheckbox"
            />
            <div class="main-page__wrap-btn">
                <button @click="clearFilters" class="main-page__clear-btn">
                    Сбросить все фильтры
                </button>
            </div>
        </div>
        <div class="main-page__col2">
            <template v-for="flight in flights" :key="flight.id">
                <AppTicket :flight="flight" />
            </template>
        </div>
    </div>
</template>

<script>
import { reactive, ref, watch, onMounted } from 'vue';
import AppFilter from '../components/AppFilter.vue';
import AppTicket from '../components/AppTicket.vue';
import { airlines, tariffOptions } from '../enums.js';
import apiJson from '../results.json';

export default {
    name: 'MainPage',
    components: {
        AppFilter,
        AppTicket
    },

    setup() {
        const showClearIcon = reactive({
            tariff: false,
            airlines: false
        });
        const airlinesCheckbox = ref([]);
        const tariffCheckbox = ref([]);
        const apiData = ref(apiJson);
        const flights = ref([]);

        const clearTariffSearch = () => {
            tariffCheckbox.value = [];
            flights.value = apiData.value.flights;
        };

        const clearAirlinesSearch = () => {
            airlinesCheckbox.value = [];
            flights.value = apiData.value.flights;
        };

        const clearFilters = () => {
            tariffCheckbox.value = [];
            airlinesCheckbox.value = [];
            flights.value = apiData.value.flights;
        };

        watch(
            tariffCheckbox,
            () => {
                if (tariffCheckbox.value.length > 0) showClearIcon.tariff = true;
                else showClearIcon.tariff = false;

                flights.value = apiData.value.flights;

                if (tariffCheckbox.value.indexOf('refundable') != -1) {
                    flights.value = flights.value.filter((item) => item.refundable);
                }
                if (tariffCheckbox.value.indexOf('baggage') != -1) {
                    flights.value = flights.value.filter(
                        (item) => Object.keys(item.services)[0] != '0PC'
                    );
                }
                if (tariffCheckbox.value.indexOf('straight') != -1) {
                    flights.value = flights.value.filter(
                        (item) => item.itineraries[0][0].segments.length === 1
                    );
                }
            },
            {
                deep: true
            }
        );

        watch(
            airlinesCheckbox,
            () => {
                if (airlinesCheckbox.value.length > 0) showClearIcon.airlines = true;
                else showClearIcon.airlines = false;

                flights.value = apiData.value.flights;

                if (airlinesCheckbox.value.length) {
                    flights.value = flights.value.filter((item) =>
                        airlinesCheckbox.value.includes(item.validating_carrier)
                    );
                }

                if (airlinesCheckbox.value.indexOf('all') != -1) {
                    flights.value = apiData.value.flights;
                }
            },
            {
                deep: true
            }
        );

        onMounted(() => {
            flights.value = apiData.value.flights;
        });

        return {
            tariffOptions,
            airlines,
            showClearIcon,
            clearAirlinesSearch,
            clearTariffSearch,
            clearFilters,
            airlinesCheckbox,
            tariffCheckbox,
            flights
        };
    }
};
</script>

<style lang="scss" scope>
.main-page {
    display: flex;
    align-items: flex-start;

    &__col1 {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        width: 240px;
    }

    &__col2 {
        width: 80%;
    }

    &__col-item {
        margin-bottom: 12px;
    }

    &__wrap-btn {
        display: flex;
        align-items: center;
        justify-content: flex-start;
        width: 100%;
    }

    &__clear-btn {
        display: flex;
        justify-content: start;
        border: none;
        margin-top: 12px;
        color: #7284e4;
        background-color: #d7d7d7;
        font-size: 12px;
        border-bottom: 1px dashed #7284e4;
        cursor: pointer;

        &:hover {
            color: darken($color: #7284e4, $amount: 15);
        }
    }
}
</style>
