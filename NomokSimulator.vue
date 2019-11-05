<template>
    <div>
        <div id="nomok">
            <p>노가다 목장갑 <span v-if="numOfUpgrades">+{{ numOfUpgrades }}</span></p>
            <p>
                <span v-if="weaponAttack">공격력 : +{{ weaponAttack }}</span><br>
                물리방어력 : +2
            </p>
            <p>업그레이드 가능 횟수 : {{ numOfUpgradesAvailable }}</p>
        </div>
        <div class="scroll" @click="scrolling(10)">
            <img src="./assets/images/scroll_10.png">장갑 공격력 주문서 10%
        </div>
        <div class="scroll" @click="scrolling(30)">
            <img src="./assets/images/scroll_30.png">장갑 공격력 주문서 30%
        </div>
        <div class="scroll" @click="scrolling(60)">
            <img src="./assets/images/scroll_60.png">장갑 공격력 주문서 60%
        </div>
        <div class="scroll" @click="scrolling(70)">
            <img src="./assets/images/scroll_70.png">장갑 공격력 주문서 70%
        </div>
        <div class="scroll" @click="scrolling(100)">
            <img src="./assets/images/scroll_100.png">장갑 공격력 주문서 100%
        </div>
        <div id="systemMessage">{{ systemMessage }}</div>
        <button v-if="!numOfUpgradesAvailable" @click="reset">재시도</button>
    </div>
</template>

<script>

    function playEnchantSound(isSucceed) {
        const audio = new Audio();
        const audioPath = "./assets/audio";
        audio.src = (isSucceed) ? audioPath + "/EnchantSuccess.mp3" :  audioPath + "/EnchantFailure.mp3";
        audio.play();
    }

    function getRandomIntInclusive(min, max) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min + 1)) + min; //최댓값도 포함, 최솟값도 포함
    }

    export default {
        data() {
            return {
                numOfUpgrades: 0,
                weaponAttack: 0,
                numOfUpgradesAvailable: 5,
                systemMessage: ""
            };
        },
        methods: {
            scrolling(option) {
                if (this.numOfUpgradesAvailable === 0) { // 업그레이드 가능 횟수가 0일때
                    return;
                }

                const rndNum = getRandomIntInclusive(1, 100);
                let isSucceed = true;

                if (option === 10 && rndNum <= option) {
                    this.weaponAttack += 3;
                } else if (option === 30) {
                    if (rndNum <= option) {
                        this.weaponAttack += 3;
                    } else {
                        if (getRandomIntInclusive(0, 1) === 1) {
                            this.destroyed();
                            return;
                        }
                        isSucceed = false;
                    }
                } else if (option === 60 && rndNum <= option) {
                    this.weaponAttack += 2;
                } else if (option === 70) {
                    if (rndNum <= option) {
                        this.weaponAttack += 2;
                    } else {
                        if (getRandomIntInclusive(0, 1) === 1) {
                            this.destroyed();
                            return;
                        }
                        isSucceed = false;
                    }
                } else if (option === 100) {
                    this.weaponAttack += 1;
                } else {
                    isSucceed = false;
                }

                if(isSucceed === true) { // 성공 할 경우 성공 횟수 증가 
                    this.numOfUpgrades++;
                }

                playEnchantSound(isSucceed);

                const successMessage = `장갑 공격력 주문서 ${option}%가 한 순간 빛나더니 신비로운 힘이 그대로 노가다 목장갑에 전해졌습니다.`;
                const failureMessage = `장갑 공격력 주문서 ${option}%가 한 순간 빛났지만 노가다 목장갑에는 아무런 변화도 일어나지 않았습니다.`;
                this.systemMessage = (isSucceed) ? successMessage : failureMessage;
                this.numOfUpgradesAvailable--; // 
                setTimeout(() => {
                    this.systemMessage = "";
                }, 1500)
            },
            destroyed() {
                playEnchantSound(false);
                this.systemMessage = "장비가 파괴되었습니다.";
                this.numOfUpgradesAvailable = 0;
            },
            reset() {
                this.numOfUpgrades = 0;
                this.weaponAttack = 0;
                this.numOfUpgradesAvailable = 5;
            }
        }
    };
</script>

<style>
    .scroll {
        padding: 5px;
        margin-top: 3px;
        display: inline-block;
        cursor:pointer;
    }

    .scroll:hover {
        background: #DADADA;
    }
</style>
