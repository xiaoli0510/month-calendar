<script setup lang='ts'>
import { computed, ref } from 'vue'
import { Solar, HolidayUtil, Holiday } from 'lunar-typescript';

const week = [
    { id: 0, day: '日' },
    { id: 1, day: '一' },
    { id: 2, day: '二' },
    { id: 3, day: '三' },
    { id: 4, day: '四' },
    { id: 5, day: '五' },
    { id: 6, day: '六' },
]

// 一个月的所有日期和对应的星期几
type DayObj = {
    id: number
    formatDate: string
    day: number
    week: number
    solarDay: string
    holiday?: Holiday | null
}
type DaysMonth = DayObj | number
const daysMonth = ref<DaysMonth[][]>([])
const year = new Date().getFullYear()
const month = new Date().getMonth() + 1
//格式化一个月的日期
const formatMonthDays = (year: number, month: number) => {
    let days: number = new Date(year, month, 0).getDate()
    let daysItem: DaysMonth[] = new Array(7).fill(-1)
    for (let i = 0; i < days; i++) {
        const day = new Date(year, month - 1, i + 1).getDay()
        const holiday: Holiday | null = HolidayUtil.getHoliday(year, month, i + 1);
        console.log(holiday);

        const solarObj = Solar.fromYmd(year, month, i + 1)
        const solarDay = solarObj.getLunar().getDayInChinese()
        const dayObj = {
            id: i,
            formatDate: year + '-' + month + '-' + (i + 1),
            day: i + 1,
            week: day,
            solarDay,
            holiday
        }
        daysItem[day] = dayObj
        if (day === 6) {
            daysMonth.value.push(daysItem)
            daysItem = new Array(7).fill(-1)
        }
    }
}
formatMonthDays(year, month)

const getToday = computed(() => {
    const today = new Date()
    const year = today.getFullYear()
    const month = today.getMonth() + 1
    const day = today.getDate()
    return year + '-' + month + '-' + day
})

</script>
<template>
    <div class="main border-2 border-gray-300 p-4 flex flex-col w-full overflow-y-auto">
        <div class="font-bold">5月</div>
        <div>
            <span v-for="item in week" :key="item.id" class="text-center text-lg inline-block p-1 m-1.5 w-10 h-10">{{
                item.day }}</span>
        </div>
        <div>
            <template v-for="(item, index) in daysMonth" :key="index">
                <div class="flex flex-row">
                    <template v-for="item1 in item">
                        <div class="inline-block text-xs my-2">
                            <span class="leading-[40px] text-center text-xs relative inline-block mx-1.5 w-10 h-10"                                                                                                                                             
                                :class="{ 'active': typeof item1 === 'object' && getToday === item1.formatDate }">{{
                                    typeof
                                        item1 === 'object' ? item1.day : '' }}
                                <span class="absolute -right-1 -top-2  text-purple-400"
                                    v-if="typeof item1 === 'object' && item1.holiday && !item1.holiday.isWork()">休</span>
                            </span>
                            <br />
                            <span class="text-xs inline-block mx-2 w-8">{{ typeof item1 === 'object' ? item1.solarDay :
                                '' }}</span>
                            <br />
                            <span class="tracking-tighter text-xs inline-block mx-2 text-purple-400 bg-purple-200 p-0.5"
                                v-if="typeof item1 === 'object' && item1.holiday && !item1.holiday.isWork()">{{ typeof
                                    item1 === 'object' ? item1.holiday.getName() :
                                '' }}</span>
                        </div>
                    </template>
                </div>
            </template>
        </div>
    </div>

</template>
<style scoped>
.active {
    background: blue;
    color: #fff;
    border-radius: 50%;
}
</style>