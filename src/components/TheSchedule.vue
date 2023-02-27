<template>
    <div class="schedule">
        <div class="schedule__sidebar">
            <div class="schedule__team">
                <p>Staff</p>
                <p>Role</p>
            </div>

            <div class="schedule__team-grid">
                <div class="schedule__member">
                    <h3>John Cena</h3>
                </div>

                <div class="schedule__member">
                    <h3>John Cena</h3>
                </div>

                <div class="schedule__member">
                    <h3>John Cena</h3>
                </div>

                <div class="schedule__member">
                    <h3>John Cena</h3>
                </div>
            </div>
        </div>

        <div class="schedule__main">
            <div class="schedule__day">
                <p>{{ currentDay }}</p>

                <div class="schedule__change-day prev" @click="prevDay">{{ '<' }}</div>
                <div class="schedule__change-day next" @click="nextDay">{{ '>' }}</div>
            </div>

            <div class="schedule__inner">
                <div class="schedule__hours-grid">
                    <div class="schedule__hour-item" v-for="(hour, i) in hours" :key="i">
                        {{ hour }}
                    </div>
                </div>

                <div class="schedule__inner-grid" ref="eventsGridRef">
                    <div class="schedule__row">
                        <span v-for="i in 16" :key="i"></span>

                        <ScheduleEvent v-if="parentWidth" :parentWidth="parentWidth" />
                    </div>

                    <div class="schedule__row">
                        <span v-for="i in 16" :key="i"></span>
                    </div>

                    <div class="schedule__row">
                        <span v-for="i in 16" :key="i"></span>
                    </div>

                    <div class="schedule__row">
                        <span v-for="i in 16" :key="i"></span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import moment from "moment";
import ScheduleEvent from "@/components/ScheduleEvent.vue";
export default {
    name: "TheSchedule",
    components: {ScheduleEvent},
    data() {
        return {
            hours: [],
            day: null,

            parentWidth: 0
        }
    },
    created() {
        this.day = moment().startOf('day');

        const startHour = moment().startOf('day').hour(8);
        const endHour = moment().endOf('day');

        let currentHour = startHour;
        while (currentHour <= endHour) {
            this.hours.push(currentHour.format('h A'));
            currentHour.add(1, 'hours');
        }
    },
    methods: {
        prevDay() {
            this.day = moment(this.day).subtract(1, 'days').format('YYYY-MM-DD');
        },
        nextDay() {
            this.day = moment(this.day).add(1, 'days').format('YYYY-MM-DD');
        },
    },
    computed: {
        currentDay() {
            return moment(this.day).format('ddd DD/MM');
        },
        gridOptions() {
            return this.$refs.eventsGridRef?.getBoundingClientRect();
        }
    },
    mounted() {
        if (this.$refs.eventsGridRef) {
            this.parentWidth = this.$refs.eventsGridRef.getBoundingClientRect().width;
        }
    }
}
</script>

<style lang="scss" scoped>
.schedule {
    display: flex;

    &__sidebar {
        width: 300px;
        background-color: red;
    }

    &__main {
        width: calc(100% - 300px);
        overflow-x: hidden;
    }

    &__team {
        display: grid;
        place-items: center;
        grid-template-columns: 1fr 1fr;
        height: 60px;
        border: 1px solid #d8d9da;
    }

    &__member {
        height: 50px;
        display: flex;
        align-items: center;
        padding: 5px;
    }

    &__day {
        text-align: center;
        font-size: 14px;
        border: 1px solid #d8d9da;
        position: relative;
        height: 30px;
        display: flex;
        align-items: center;
        justify-content: center;

        p {
            font-size: inherit;
        }
    }

    &__change-day {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 30px;
        background-color: red;

        &.prev {
            left: 0;
        }

        &.next {
            left: 40px;
        }
    }

    &__hours-grid {
        display: grid;
        grid-template-columns: repeat(16, 100px);
        width: max-content;
    }
    
    &__hour-item {
        text-align: center;
        font-size: 14px;
        border: 1px solid #d8d9da;
        height: 30px;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    &__inner {
        width: 100%;
        overflow-x: auto;
        padding-bottom: 30px;
    }

    &__inner-grid {
        width: max-content;
    }

    &__row {
        height: 50px;
        width: 100%;
        //border: 1px solid #d8d9da;
        display: grid;
        grid-template-columns: repeat(16, 100px);
        position: relative;

        span {
            border: 1px solid #d8d9da;
        }
    }
}
</style>