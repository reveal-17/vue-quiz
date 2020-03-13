<template>
    <div id="app">
        <div class="tmgeNav">
            <el-steps :space="200" :active="active" simple>
                <el-step title="ホーム" icon="el-icon-s-home"></el-step>
                <el-step title="クイズを解く" icon="el-icon-edit"></el-step>
                <el-step title="結果発表！" icon="el-icon-document-checked"></el-step>
            </el-steps>
        </div>

        <div class="tmgeStart" v-if="page == 1">
            <p>ミッシェルガンエレファントにまつわるクイズ！<br>Twitterでログインすると結果をみんなにシェアすることができます！</p>
            <el-row>
                <el-button type="success" plain @click="nextPage">クイズに挑戦してみる</el-button>
            </el-row>
        </div>

        <div class="tmgeMain" v-if="page == 2">
            <el-row :gutter="20" class="tmgeMain__nav">
                <el-col :span="12"><div class="grid-content bg-purple">{{ counter }}問目</div></el-col>
                <el-col :span="12"><div class="grid-content bg-purple">{{ score }}点</div></el-col>
            </el-row>

            <div class="tmgeMain__question">
                {{ currentQuestion.question }}
                <div class="tmgeMain__question--success">

                </div>
                <div class="tmgeMain__question--alert">

                </div>
            </div>

            <el-radio-group v-model="radio1" class="tmgeMain__form">
                <el-radio-button :label="1">{{ currentQuestion.selections[0] }}</el-radio-button>
                <el-radio-button :label="2">{{ currentQuestion.selections[1] }}</el-radio-button>
                <el-radio-button :label="3">{{ currentQuestion.selections[2] }}</el-radio-button>
                <el-radio-button :label="4">{{ currentQuestion.selections[3] }}</el-radio-button>
            </el-radio-group>

            <el-row class="tmgeMain__send">
                <el-button @click="counter += 1; isTrue(); nextQuestion()" type="success">回答する</el-button>
            </el-row>
        </div>

        <div class="tmgeOutput" v-if="page == 3">
            <el-dialog
            title="結果発表！"
            :visible.sync="centerDialogVisible"
            width="30%"
            center>
            <span >あなたの得点は<span class="tmgeOutput__score">{{ score }}</span>点でした！結果をTwitterで共有しよう！</span>
            <span slot="footer" class="dialog-footer">
                <el-button @click="centerDialogVisible = false">キャンセル</el-button>
                <el-button type="primary" @click="centerDialogVisible = false">Twitterで共有する</el-button>
            </span>
            </el-dialog>

            <div class="tmgeOutput__message">
                <p>あなたは<span class="tmgeOutput__reception">{{ resultMessage() }}</span>です！<br>クイズの結果をTwitterで共有してみましょう！</p>
                <el-row>
                    <el-button type="success" plain @click="resetPage()">もう一度挑戦してみる！</el-button>
                    <el-button type="primary" plain>Twitterで結果を共有する！</el-button>
                </el-row>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'app',
    methods: {
        nextPage: function() {
            this.page++;
            this.active++;
        },
        resetPage: function() {
            this.page = 1;
            this.active = 0;
        },
        isTrue: function() {
            if (this.radio1 === this.currentQuestion.answer+1) {
                this.score += 10;
                this.$notify({
                    title: '正解',
                    message: 'おめでとうございます！',
                    type: 'success',
                    duration: 1500,
                });
            } else {
                this.score += 0;
                this.$notify({
                title: '不正解',
                message: '残念...',
                type: 'warning',
                duration: 1500,
                });
            }
            this.radio1 = 1;
        },
        nextQuestion: function() {
            if (this.currentQuestionIndex < this.questions.length-1-9) {
                this.currentQuestionIndex += 1;
            } else {
                this.nextPage();
                this.centerDialogVisible = true;
            }
        },
        resultMessage: function() {
            const totalScore = this.questions.length*10;
            if (this.score === totalScore) {
                return 'ミッシェルガンエレファントマスター';
            } else if (this.score < totalScore/2) {
                return 'ミッシェルガンエレファントに興味がない人';
            } else {
                return 'ミッシェルガンエレファントにわか';
            }
        },
    },
    computed: {
        currentQuestion: function() {
            return this.questions[this.currentQuestionIndex];
        },
    },
    data () {
        return {
            page: 1,
            active: 0,
            radio1: 1,
            counter: 1,
            score: 0,
            currentQuestionIndex: 0,
            centerDialogVisible: false,
            questions: [
                {
                    question: '以下人物の中で、ミッシェルガンエレファントへの加入が遅い人物は？',
                    selections: [
                            'チバユウスケ',
                            'アベフトシ',
                            'ウエノコウジ',
                            'クハラカズユキ',
                    ],
                    answer: 1
                },
                {
                    question: '「夢のマイアミ」の歌詞に出てくる、チバユウスケが飼っていたとされる犬の名前は？',
                    selections: [
                            'りょうたろう',
                            'そうたろう',
                            'しょうたろう',
                            'こうたろう',
                    ],
                    answer: 2
                },
                {
                    question: '4thアルバム「ギヤ・ブルーズ」の発売日は？',
                    selections: [
                            '1997/11/25',
                            '1998/11/25',
                            '1999/11/25',
                            '2000/11/25',
                    ],
                    answer: 1
                },
                {
                    question: 'チバユウスケの出身大学・学部・学科は？',
                    selections: [
                            '明治学院大学社会学部社会学科',
                            '明治学院大学文学部芸術学科',
                            '明治学院大学経済学部経済学科',
                            '明治学院大学経済学部商学科',
                    ],
                    answer: 3
                },
                {
                    question: '「ホテル・ブロンコ」はインスト曲であるが、その中でチバユウスケが叫んでいるのは？',
                    selections: [
                            'Samoa beach',
                            'Son of a bitch',
                            'Saloa beach',
                            'Sanoah reach',
                    ],
                    answer: 1
                },
                {
                    question: 'World Gear Blues Tourの振り返りとして、「カサノバ・スネイク」リリース後にTMGEの４人がインタビューを受けた場所は？',
                    selections: [
                            '江知勝',
                            '秋与し',
                            '喜久屋',
                            '瀬里奈',
                    ],
                    answer: 3
                },
                {
                    question: '1stシングル「世界の終わり」のMVが撮影された場所は？',
                    selections: [
                            '御殿場',
                            '長浜',
                            '函館',
                            '出雲',
                    ],
                    answer: 0
                },
                {
                    question: 'FUJI ROCK FESTIVAL 98 において、TMGEが最初に演奏した曲は「CISCO」であるが、最後に演奏した曲は？',
                    selections: [
                            'ロシアン・ハスキー',
                            'バードメン',
                            'VIBE ON!',
                            'ゲット・アップ・ルーシー',
                    ],
                    answer: 2
                },
                {
                    question: 'チバユウスケが「MUSIC SOUP -45r.p.m.（revolution per man）-」に出演した際に紹介した、彼の人生に影響を与えた45曲に含まれないものは？',
                    selections: [
                            'CROSSTOWN TRAFFIC (JIMI HENDRIX EXPERIENCE)',
                            'DAZED AND CONFUSED (LED ZEPPELIN)',
                            'TOMORROW NEVER KNOWS (THE BEATLES)',
                            'MARQUEE MOON (TELEVISION)',
                    ],
                    answer: 3
                },
                {
                    question: 'TMGEがリリースしたシングルの中で、最も多く売り上げたものは？',
                    selections: [
                            '世界の終わり',
                            'バードメン',
                            'G.W.D',
                            'スモーキン・ビリー',
                    ],
                    answer: 2
                },
                // {
                //     question: '15thシングル「Girl Friend」の収録曲として正しいものは？',
                //     selections: [
                //             'NIGHT IS OVER',
                //             '夜が終わる',
                //             'ヴァレンタイン',
                //             '解答なし',
                //     ],
                //     answer: 3
                // },
                // {
                //     question: 'チバユウスケが所有する機材の中で、Ekoというイタリアのメーカーのギターがあるが、彼はこれをどこで手に入れた？',
                //     selections: [
                //             '新大久保',
                //             '下北沢',
                //             '吉祥寺',
                //             '御茶ノ水',
                //     ],
                //     answer: 0
                // },
            ],
        }
    },
}
</script>

<style lang="scss">
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 1080px;
    margin: 60px auto 0;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.el-row {
    margin-bottom: 20px;
    &:last-child {
    margin-bottom: 0;
    }
}
.el-col {
    border-radius: 4px;
}

.bg-purple {
    background-color: rgba($color: #409EFF, $alpha: .2);
    border: 1px solid #DCDFE6;
}

.el-steps {
    background-color: #fff;
}
</style>
