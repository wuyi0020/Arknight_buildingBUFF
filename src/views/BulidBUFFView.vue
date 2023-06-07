<template>
    <div id="Bulid" class="h1">基建技能</div>
    <div class="row">
        <label class="col pe-0 w-10  .flex-wrap">
            <input type="checkbox" value="MANUFACTURE" v-model="room_filter" />
            製造站
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="POWER" v-model="room_filter" />
            發電站
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="CONTROL" v-model="room_filter" />
            訓練室
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="MEETING" v-model="room_filter" />
            會客室
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="DORMITORY" v-model="room_filter" />
            宿舍
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="WORKSHOP" v-model="room_filter" />
            加工站
        </label>
        <label class="col pe-0 w-10 .flex-wrap">
            <input type="checkbox" value="TRADING" v-model="room_filter" />
            貿易站
        </label>
    </div>
    <div class="container-fulid text-white bg-dark">
        <div class="row m-0">
            <div class="row border-bottom mx-0">
                <div class="col-1 text-center w-10 pe-0">幹員</div>
                <div class="col">
                    <div class="row ps-0 ms-0 justify-items-center">
                        <div class="col-1 text-center w-10 collapse-expand-lg">建築</div>
                        <div class="col-2 text-center w-15 p-0">技能</div>
                        <div class="col pe-0">描述</div>
                    </div>
                </div>
            </div>

            <div class="row char mx-0" v-for="(chars, key, index) in filteredBuffChars" :key="key" :id="key">
                <div class="col-1 charname center w-10 pe-0">{{ character_table[key]["name"] }}</div>
                <div class="col">
                    <div :class="`row ps-0 ms-0 buffs buffrow buff-${index}`" v-for="(buffChar, index) in chars['buffChar']" :key="`${key}-${index}`" :id="`${key}-buff-${index}`">
                        <div class="row w-100 ps-0 ms-0 align-items-center buffdata" v-for="(buffdata, index) in buffChar['buffData']" :key="`${buffdata['buffId']}`" :id="`${buffdata['buffId']}`">
                            <div class="col-1 text-center collapse-expand-lg w-10">
                                <div class="p-1 roomType">
                                    <div :class="`room${buffs[buffdata['buffId']]['roomType']}`">
                                        {{ building_data["rooms"][buffs[buffdata["buffId"]]["roomType"]]["name"] }}
                                    </div>
                                </div>
                            </div>
                            <div class="col-2 text-center w-15 p-0 buffName">
                                <div :class="`rounded ${buffs[buffdata['buffId']]['roomType']} `">
                                    {{ buffs[buffdata["buffId"]]["buffName"] }}
                                </div>
                            </div>
                            <div class="col pe-0 description" v-html="buffs[buffdata['buffId']]['description']" :id="`${buffdata['buffId']}-d`"></div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
import { ref, reactive, onMounted, computed } from "vue";
import building_data from "@/assets/building_data.json";
import character_table from "@/assets/character_table.json";
const buffs = ref(JSON.parse(JSON.stringify(building_data["buffs"])));
const buffchars = ref(JSON.parse(JSON.stringify(building_data["chars"])));

const reg = /(?<=cc.).*?(?=>)/g;
const room_filter = ref([]);

onMounted(() => {
    for (const key in buffs.value) {
        const element = buffs.value[key].description;
        if (element.includes("@cc")) {
            let rp_element = element
                .replace(/<\/?>/g, (match) => {
                    const tag = match.replace(/[<\/>]/g, "");
                    return `</${tag}>`;
                })
                .replace(/cc\.|@|\$/g, "");

            buffs.value[key].description = rp_element;
        }
    }

    buffchars_reset();
    buffchars_filter();
});
function buffchars_reset() {
    buffchars.value = JSON.parse(JSON.stringify(building_data["chars"]));
    for (const key in buffchars.value) {
        buffchars.value[key]["buffChar"] = buffchars.value[key]["buffChar"].filter((buff) => buff.buffData && buff.buffData.length > 0);
    }
}

function buffchars_filter() {
    if (room_filter.value.length <= 0) {
        return;
    }
    Object.keys(buffchars.value).forEach((buffId) => {
        const buffData = buffchars.value[buffId];

        if (buffData && Array.isArray(buffData.buffChar)) {
            const hasControlBuff = buffData.buffChar.some((buffChar) => {
                return buffChar.buffData.some((data) => {
                    const buffIdToCompare = data.buffId;
                    const buffInBuffs = buffs.value[buffIdToCompare];

                    return buffInBuffs && room_filter.value.includes(buffInBuffs.roomType);
                });
            });

            if (!hasControlBuff) {
                delete buffchars.value[buffId];
            }
        }
    });
}
const filteredBuffChars = computed(() => {
    if (room_filter.value.length <= 0) {
        return buffchars.value;
    }

    const filteredChars = {};
    Object.keys(buffchars.value).forEach((buffId) => {
        const buffData = buffchars.value[buffId];

        if (buffData && Array.isArray(buffData.buffChar)) {
            const hasControlBuff = buffData.buffChar.some((buffChar) => {
                return buffChar.buffData.some((data) => {
                    const buffIdToCompare = data.buffId;
                    const buffInBuffs = buffs.value[buffIdToCompare];

                    return buffInBuffs && room_filter.value.includes(buffInBuffs.roomType);
                });
            });

            if (hasControlBuff) {
                filteredChars[buffId] = buffData;
            }
        }
    });

    return filteredChars;
});

function room_filter_cracert(str) {
    room_filter.value.push(str);
}
</script>
<style>
vup {
    color: orange;
}
vdown {
    color: red;
}
kw {
    color: aqua;
}
rem {
    color: orange;
    text-decoration: underline;
}
.POWER {
    background-color: #43e44b;
    border: #43e44b 3px solid;
    color: black;
}
.DORMITORY {
    background-color: #39d1d6;
    border: #39d1d6 3px solid;
    color: black;
}
.MANUFACTURE {
    background-color: #d5a132;
    border: #d5a132 3px solid;
    color: black;
}
.MEETING {
    background-color: #d54532;
    border: #d54532 3px solid;
    color: black;
}
.WORKSHOP {
    background-color: #bad532;
    border: #bad532 3px solid;
    color: black;
}
.TRADING {
    background-color: #08868a;
    border: #08868a 3px solid;
    color: black;
}
.HIRE {
    background-color: #b4b4b4;
    border: #b4b4b4 2px solid;
    color: black;
}
.TRAINING {
    background-color: #e65a5a;
    border: #e65a5a 2px solid;
    color: black;
}
.CONTROL {
    background-color: #0fa117;
    border: #0fa117 2px solid;
    color: black;
}

.buffdata,
.buffrow {
    border-bottom: gray solid 1px;
}

.buffdata:nth-last-child(1),
.buffrow:nth-last-child(1) {
    border-bottom: none;
}

.char {
    padding-bottom: 1px;
    border-bottom: gray solid 1px;
}

.center {
    display: flex;
    justify-content: center;
    align-items: center;
}

.w-15 {
    min-width: 125px;
    width: 15%;
}
.w-10 {
    min-width: 100px;
    width: 10%;
}
.collapse-expand-lg {
    display: none;
}
.collapse-expand-md {
    display: none;
}
@media (min-width: 992px) {
    .collapse-expand-lg {
        display: block;
    }
}
@media (min-width: 768px) {
    .collapse-expand-md {
        display: block;
    }
}
</style>
