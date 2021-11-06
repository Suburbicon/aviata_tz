<template>
    <div class="ticket">
        <div class="ticket__col1">
            <div class="ticket__wrap-departure-info">
                <div class="ticket__wrap-title">
                    <img
                        width="20"
                        height="20"
                        :src="getURL(flight.validating_carrier)"
                        alt="icon-carrier"
                    />
                    <h4 class="ticket__title">{{ flight.itineraries[0][0].carrier_name }}</h4>
                </div>
                <div class="ticket__wrap-departure-date">
                    <span class="ticket__departure-day">{{ getDepDate }}</span>
                    <p class="ticket__departure-time">{{ getDepTime }}</p>
                </div>
                <div class="ticket__wrap-departure-destination">
                    <div class="ticket__cities">
                        <span class="ticket__cities-name">{{
                            flight.itineraries[0][0].segments[0].origin_code
                        }}</span>
                        <span class="ticket__cities-time">{{ getFlightTime }}</span>
                        <span class="ticket__cities-name">{{ getAirportDest }}</span>
                    </div>
                    <img src="../assets/gap.png" alt="" class="ticket__gap" />
                    <p v-if="flight.itineraries[0][0].segments.length > 1" class="ticket__stopping">
                        через {{ getSegmentCity }}, {{ getSegmentTime }}
                    </p>
                </div>
                <div class="ticket__wrap-departure-date">
                    <span class="ticket__departure-day">{{ getArrDate }}</span>
                    <p class="ticket__departure-time">{{ getArrTime }}</p>
                </div>
            </div>
            <div class="ticket__additions">
                <router-link class="ticket__additions-btn"> Детали перелета </router-link>
                <router-link class="ticket__additions-btn"> Условия тарифа </router-link>
                <div v-if="flight.refundable" class="ticket__wrap-additions-icon">
                    <IrrevocableIcon />
                    <p class="ticket__additions-text">невозвратный</p>
                </div>
            </div>
        </div>
        <div class="ticket__col2">
            <div class="ticket__wrap-price">
                <h2 class="ticket__price">
                    {{ flight.itineraries[0][0].price.amount }}
                    <span class="ticket__price-symbol">{{
                        flight.itineraries[0][0].price.currency
                    }}</span>
                </h2>
            </div>
            <button class="ticket__choose-btn">Выбрать</button>
            <span class="ticket__price-text">Цена за всех пассажиров</span>
            <div class="ticket__wrap-baggage-info">
                <span class="ticket__baggage-info">{{ getWeight }}</span>
                <button class="ticket__baggage-btn">+ Добавить багаж</button>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, computed } from 'vue';
import IrrevocableIcon from './Icons/IrrevocableIcon.vue';
import moment from 'moment';
export default {
    name: 'AppTicket',
    components: {
        IrrevocableIcon
    },
    props: {
        flight: {
            type: Object,
            default: {}
        }
    },
    setup(props) {
        const getURL = (symbol) => {
            return 'https://aviata.kz/static/airline-logos/80x80/' + symbol + '.png';
        };

        const getWeight = computed(() => {
            let weight = props.flight.services;
            let key = Object.keys(weight)[0];
            return weight[key].alt_text;
        });

        const getDepDate = computed(() => {
            return props.flight.itineraries[0][0].segments[0].dep_time.slice(0, 11).toLowerCase();
        });

        const getDepTime = computed(() => {
            return props.flight.itineraries[0][0].segments[0].dep_time.slice(11, 16);
        });

        const getArrDate = computed(() => {
            let length = props.flight.itineraries[0][0].segments.length;
            if (length > 1) {
                return props.flight.itineraries[0][0].segments[length - 1].arr_time
                    .slice(0, 11)
                    .toLowerCase();
            } else {
                return props.flight.itineraries[0][0].segments[0].arr_time
                    .slice(0, 11)
                    .toLowerCase();
            }
        });

        const getArrTime = computed(() => {
            let length = props.flight.itineraries[0][0].segments.length;
            if (length > 1) {
                return props.flight.itineraries[0][0].segments[length - 1].arr_time.slice(11, 16);
            } else {
                return props.flight.itineraries[0][0].segments[0].arr_time.slice(11, 16);
            }
        });

        const getAirportDest = computed(() => {
            let length = props.flight.itineraries[0][0].segments.length;
            if (length > 1) {
                return props.flight.itineraries[0][0].segments[length - 1].dest_code;
            } else {
                return props.flight.itineraries[0][0].segments[0].dest_code;
            }
        });

        const getSegmentCity = computed(() => {
            let length = props.flight.itineraries[0][0].segments.length;
            if (length > 1) {
                return props.flight.itineraries[0][0].segments[0].dest;
            }
        });

        const getSegmentTime = computed(() => {
            let length = props.flight.itineraries[0][0].segments.length;
            if (length > 1) {
                let a = moment(props.flight.itineraries[0][0].segments[0].dep_time_iso);
                let b = moment(props.flight.itineraries[0][0].segments[0].arr_time_iso);
                let duration = moment.duration(b.diff(a));
                return duration.hours() + ' ч ' + duration.minutes() + ' м';
            }
        });

        const getFlightTime = computed(() => {
            let dep_date = moment(props.flight.itineraries[0][0].dep_date);
            let arr_date = moment(props.flight.itineraries[0][0].arr_date);
            let difference = moment.duration(arr_date.diff(dep_date));
            return difference.hours() + ' ч ' + difference.minutes() + ' м';
        });

        return {
            getWeight,
            getURL,
            getDepDate,
            getArrDate,
            getDepTime,
            getArrTime,
            getAirportDest,
            getSegmentCity,
            getSegmentTime,
            getFlightTime
        };
    }
};
</script>

<style lang="scss" scope>
.ticket {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: #fff;
    border-radius: 4px;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.15);
    margin-left: 20px;
    margin-bottom: 12px;
    width: 100%;
    height: 170px;

    &__col1 {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        width: 72%;
        height: 100%;
    }

    &__wrap-departure-info {
        margin-top: 20px;
        display: flex;
    }

    &__wrap-title {
        display: flex;
        align-items: center;
        margin-right: 35px;
        margin-left: 10px;
    }

    &__title {
        font-size: 14px;
        font-weight: 600;
        margin-left: 12px;
        padding-top: 2px;
    }

    &__wrap-departure-date {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        margin-right: 28px;
    }

    &__departure-day {
        font-size: 12px;
        font-weight: 400;
    }

    &__departure-time {
        font-size: 24px;
        font-weight: 400;
    }

    &__wrap-departure-destination {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        margin-right: 28px;
    }

    &__cities {
        display: flex;
        justify-content: space-between;
        width: 100%;
    }

    &__cities-name {
        font-size: 10px;
        font-weight: 400;
        color: #b9b9b9;
        line-height: 10px;
    }

    &__cities-time {
        font-size: 12px;
        font-weight: 400;
    }

    &__gap {
        width: 100%;
    }

    &__stopping {
        font-size: 12px;
        font-weight: 400;
        color: #ff9900;
        margin-top: 3px;
    }

    &__additions {
        display: flex;
        align-items: center;
        justify-content: start;
        width: 100%;
        padding-left: 40px;
        margin-top: 45px;
    }

    &__additions-btn {
        font-size: 12px;
        color: #7284e4;
        margin-right: 25px;
        border-bottom: 1px dashed #7284e4;
    }

    &__wrap-additions-icon {
        display: flex;
        align-items: center;
        margin-left: 12px;
    }

    &__additions-text {
        font-size: 12px;
        color: #707276;
        margin-left: 8px;
    }

    &__col2 {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 12px 20px;
        height: 100%;
        width: 29%;
        background-color: #f5f5f5;
        border-bottom-right-radius: 4px;
        border-top-right-radius: 4px;
    }

    &__wrap-price {
        display: flex;
        justify-content: center;
        align-items: center;
    }

    &__price {
        font-size: 26px;
        font-weight: 400;
    }

    &__price-symbol {
        font-size: 18px;
    }

    &__choose-btn {
        border: none;
        background-color: #55bb06;
        color: white;
        font-size: 18px;
        font-weight: 500;
        padding: 8px 0;
        border-radius: 4px;
        font-family: 'Open Sans', sans-serif;
        width: 100%;
        margin-top: 10px;
        margin-bottom: 8px;
    }

    &__price-text {
        font-size: 12px;
        font-weight: 400;
        color: #707276;
        margin-bottom: 12px;
    }

    &__wrap-baggage-info {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 191px;
    }

    &__baggage-info {
        font-size: 12px;
        font-weight: 400;
        margin-right: 6px;
    }

    &__baggage-btn {
        border: none;
        background-color: #eaf0fa;
        color: #5763b3;
        font-size: 12px;
        padding: 5px 10px;
        border-radius: 2px;
    }
}
</style>
