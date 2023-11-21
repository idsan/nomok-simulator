<script setup lang="ts">
import { ref } from "vue";

const getRandomIntInclusive = (min: number, max: number): number => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

// 업그레이드 횟수
const upgrades = ref(0);
// 공격력
const attack = ref(0);
// 업그레이드 가능 횟수
const upgradesAvailable = ref(5);
// 시스템 메시지
const systemMessage = ref("");

const scrolling = (option: number) => {
  if (upgradesAvailable.value === 0) {
    return;
  }

  const rndNum = getRandomIntInclusive(1, 100);
  let isSucceed = false;

  if (option === 10 || option === 30 || option === 60 || option === 70) {
    if (rndNum <= option) {
      attack.value += option === 10 || option === 30 ? 3 : 2;
      isSucceed = true;
    } else if (option === 30 || option === 70) {
      isSucceed = getRandomIntInclusive(0, 1) === 1;
      if (!isSucceed) {
        destroyed();
        return;
      }
    }
  } else if (option === 100) {
    attack.value += 1;
    isSucceed = true;
  }

  if (isSucceed) {
    upgrades.value++;
  }

  const successMessage = `장갑 공격력 주문서 ${option}%가 한 순간 빛나더니 신비로운 힘이 그대로 노가다 목장갑에 전해졌습니다.`;
  const failureMessage = `장갑 공격력 주문서 ${option}%가 한 순간 빛났지만 노가다 목장갑에는 아무런 변화도 일어나지 않았습니다.`;
  systemMessage.value = isSucceed ? successMessage : failureMessage;
  upgradesAvailable.value--;
  setTimeout(() => {
    systemMessage.value = "";
  }, 1500);
};

const destroyed = () => {
  systemMessage.value = "장비가 파괴되었습니다.";
  upgradesAvailable.value = 0;
};

const reset = () => {
  upgrades.value = 0;
  attack.value = 0;
  upgradesAvailable.value = 5;
};
</script>

<template>
  <div id="nomok">
    <p>
      노가다 목장갑 <span v-if="upgrades">+{{ upgrades }}</span>
    </p>
    <p>
      <span v-if="attack">공격력 : +{{ attack }}</span
      ><br />
      물리방어력 : +2
    </p>
    <p>업그레이드 가능 횟수 : {{ upgradesAvailable }}</p>
  </div>
  <div class="scroll" v-for="percent in [10, 30, 60, 70, 100]" :key="percent" @click="scrolling(percent)">
    장갑 공격력 주문서 {{ percent }}%
  </div>
  <div id="systemMessage">{{ systemMessage }}</div>
  <button v-if="!upgradesAvailable" @click="reset">재시도</button>
</template>

<style scoped>
.scroll {
  padding: 5px;
  margin-top: 3px;
  display: block;
  cursor: pointer;
}

.scroll:hover {
  background: #333333;
}

#systemMessage {
  top: 0;
  position: absolute;
}
</style>
