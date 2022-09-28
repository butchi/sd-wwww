<template>
    <v-row>
        <v-col cols="12" md="3">
            <client-only placeholder="Loading...">
                <v-row>
                    <v-col>
                        <v-select v-model="selectWho" :items="selectObj.who" item-title="ja" item-value="en" label="誰が"
                            return-value>
                        </v-select>
                        <v-select v-model="selectWhen" :items="selectObj.when" item-title="ja" item-value="en"
                            label="いつ" return-value>
                        </v-select>
                        <v-select v-model="selectWhere" :items="selectObj.where" item-title="ja" item-value="en"
                            label="どこで" return-value>
                        </v-select>
                        <v-select v-model="selectWhat" :items="selectObj.what" item-title="ja" item-value="en"
                            label="何をした" return-value>
                        </v-select>
                    </v-col>
                </v-row>
            </client-only>

            <v-row>
                <v-col class="d-flex justify-end">
                    <v-btn class="ma-2" color="green" @click.stop="selectRandom">
                        <v-icon icon="mdi-dice-5"></v-icon>
                        ランダム
                    </v-btn>

                    <v-btn :disabled="!selectWho || !selectWhat || !selectWhere || !selectWhen" class="ma-2"
                        color="primary" @click.stop="generateImage">
                        <v-icon icon="mdi-palette"></v-icon>
                        生成
                    </v-btn>
                </v-col>
            </v-row>
        </v-col>
        <v-col cols="12" md="5">
            <v-card v-if="isTileMode" :loading="isInLoad">
                <v-card-title>結果表示</v-card-title>
                <v-card-text v-if="isResult && txtWho && txtWhat && txtWhere && txtWhen">
                    <v-row v-for="y in [0, 1]">
                        <v-col v-for="x in [0, 1]">
                            <v-img
                                :src="`/sd-img/${txtWho},_${txtWhat}_in_${txtWhere}_in_${txtWhen}/seed_${1 + x + 2 * y}_${('00000' +(1 + x + 2 * y)).slice(-5)}.png`"
                                class="grey lighten-2">
                            </v-img>
                        </v-col>
                    </v-row>
                </v-card-text>
                <v-card-text v-else-if="txtWho || txtWhat || txtWhere || txtWhen">
                    <v-row v-for="y in [0, 1]">
                        <v-col v-for="x in [0, 1]">
                            <v-img max-width="512" max-height="512" aspect-ratio="1"
                                class="d-flex align-center text-center">
                                <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                            </v-img>
                        </v-col>
                    </v-row>
                </v-card-text>
            </v-card>
            <v-card v-else :loading="isInLoad">
                <v-card-title>結果表示</v-card-title>
                <v-card-text v-if="isResult && txtWho && txtWhat && txtWhere && txtWhen">
                    <v-img
                        :src="`/sd-img/${txtWho},_${txtWhat}_in_${txtWhere}_in_${txtWhen}/seed_${curIdx}_${('00000' + curIdx).slice(-5)}.png`"
                        class="grey lighten-2">
                    </v-img>
                </v-card-text>
                <v-card-text v-else-if="txtWho || txtWhat || txtWhere || txtWhen">
                    <v-img width="512" height="512" max-width="512" max-height="512" aspect-ratio="1"
                        class="d-flex align-center text-center">
                        <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                    </v-img>
                </v-card-text>
                <v-card-actions class="d-flex justify-end">
                    <v-btn v-if="isResult && !isTileMode" :disabled="curIdx >= 4" color="primary"
                        @click.stop="regenerateImage">
                        <v-icon icon="mdi-palette"></v-icon>
                        再生成
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-col>
        <v-col cols="12" md="4">
            <client-only placeholder="Loading...">
                <v-select v-model="waitSec" :items="[0, 1, 15, 25, 45]" label="待ち時間(秒) デモ" return-value variant="solo">
                </v-select>

                <v-checkbox v-model="isTileMode" @click.stop="isTileMode = !isTileMode" label="タイルモード"></v-checkbox>
            </client-only>
        </v-col>
    </v-row>
</template>

<script>
export default {
    data: () => ({
        waitSec: 5,
        isInLoad: false,
        isResult: false,
        isTileMode: false,
        curIdx: 0,
        txtWho: "",
        txtWhat: "",
        txtWhere: "",
        txtWhen: "",
        selectWho: null,
        selectWhat: null,
        selectWhere: null,
        selectWhen: null,
        selectObj: {
            "who": [{
                "ja": "女子高生",
                "en": "a_cutest_japanese_student_kawaii_girl",
            }, {
                "ja": "宇宙飛行士",
                "en": "an_astronought",
            }, {
                "ja": "侍",
                "en": "a_coolest_samurai",
            }, {
                "ja": "シェフ",
                "en": "a_coolest_cheff",
            }, {
                "ja": "エイリアン",
                "en": "an_alien",
            }, {
                "ja": "アインシュタイン",
                "en": "einstein",
            }],
            "what": [{
                "ja": "ラクロスする",
                "en": "playing_lacrosse",
            }, {
                "ja": "バスケットボールする",
                "en": "playing_basketball",
            }, {
                "ja": "空を飛ぶ",
                "en": "flying_the_sky",
            }, {
                "ja": "木を登る",
                "en": "climbing_a_tree",
            }, {
                "ja": "泳ぐ",
                "en": "swimming",
            }, {
                "ja": "ゲームする",
                "en": "playing_a_game",
            }],
            "where": [{
                "ja": "学校",
                "en": "a_school",
            }, {
                "ja": "森の中",
                "en": "the_forest",
            }, {
                "ja": "山頂",
                "en": "the_summit_of_a_mountain",
            }, {
                "ja": "火星",
                "en": "mars",
            }, {
                "ja": "砂漠の真ん中",
                "en": "middle_of_the_desert",
            }, {
                "ja": "テニスコート",
                "en": "tennis_court",
            }],
            "when": [{
                "ja": "真夏",
                "en": "midsummer",
            }, {
                "ja": "真夜中",
                "en": "the_middle_of_the_night",
            }, {
                "ja": "クリスマス",
                "en": "christmas",
            }, {
                "ja": "中世",
                "en": "middle_ages",
            }, {
                "ja": "誕生日",
                "en": "birthday",
            }, {
                "ja": "結婚式中",
                "en": "the_wedding_ceremony",
            }],
        },
    }),
    watch: {
        selectWho() {
            if (this.waitSec === 0) {
                this.generateImage()
            }
        },
        selectWhat() {
            if (this.waitSec === 0) {
                this.generateImage()
            }
        },
        selectWhere() {
            if (this.waitSec === 0) {
                this.generateImage()
            }
        },
        selectWhen() {
            if (this.waitSec === 0) {
                this.generateImage()
            }
        },
    },
    methods: {
        selectRandom() {
            this.selectWho = this.selectObj.who[Math.floor(Math.random() * 6)].en
            this.selectWhat = this.selectObj.what[Math.floor(Math.random() * 6)].en
            this.selectWhere = this.selectObj.where[Math.floor(Math.random() * 6)].en
            this.selectWhen = this.selectObj.when[Math.floor(Math.random() * 6)].en
        },
        generateImage() {
            this.curIdx = 0

            this.txtWho = this.selectWho
            this.txtWhat = this.selectWhat
            this.txtWhere = this.selectWhere
            this.txtWhen = this.selectWhen

            if (this.waitSec === 0) {
                this.isResult = true

                return
            }

            this.isResult = false

            setTimeout(_ => {
                this.isResult = true

                this.isInLoad = false
            }, this.waitSec * 1000)

            this.isInLoad = true
        },
        regenerateImage() {
            if (this.curIdx < 4) {
                this.curIdx++
            }

            this.txtWho = this.selectWho
            this.txtWhat = this.selectWhat
            this.txtWhere = this.selectWhere
            this.txtWhen = this.selectWhen

            if (this.waitSec === 0) {
                this.isResult = true

                return
            }

            this.isResult = false

            setTimeout(_ => {
                this.isResult = true

                this.isInLoad = false
            }, this.waitSec * 1000)

            this.isInLoad = true
        },
    },
}
</script>