<template>
  <main class="generator">
    <div class="generator__tabs generator-tabs">
      <div class="generator-tabs__item" :class="{'generator-tabs__item--active': activeTabIndex === 0}" @click="activeTabIndex = 0">Generate Win Table</div>
      <div class="generator-tabs__item" :class="{'generator-tabs__item--active': activeTabIndex === 1}" @click="activeTabIndex = 1">Generate Tickets</div>
    </div>

    <template v-if="activeTabIndex == 0">
      <div class="generator__form generator-form" >
        <input class="generator-form__field" v-model="dataTime" type="number" placeholder="Data time">
        <input class="generator-form__field" v-model="prizeName" type="text" placeholder="Prize name">
        <input class="generator-form__field" v-model="winnersCount" type="number" placeholder="Winners count">
        <button class="generator-form__btn" type="button" @click="generateWinTable">Generate</button>
      </div>

      <vue-json-pretty class="generator__result" :data="winnersTable" />
      <button :disabled="isDisabledCopy" class="generator__copy" @click="copyToClipboard(winnersTable)">Copy json</button>
    </template>

    <template v-if="activeTabIndex == 1">
      <div class="generator__form generator-form">
        <input class="generator-form__field" v-model="ticketsCount" type="number" placeholder="Tickets count">
        <button class="generator-form__btn" type="button" @click="generateTickets">Generate</button>
      </div>
  
      <vue-json-pretty class="generator__result" :data="ticketsData" />
      <button :disabled="isDisabledCopy" class="generator__copy" @click="copyToClipboard(ticketsData)">Copy json</button>
    </template>

    <p v-if="message" class="generator__message">{{message}}</p>
  </main>
</template>

<script>
import {ref, unref} from 'vue';
import VueJsonPretty from 'vue-json-pretty';
import 'vue-json-pretty/lib/styles.css';

export default {
  name: 'App',
  components: {
    VueJsonPretty
  },
  setup() {
      const activeTabIndex = ref(0);
      const dataTime = ref(111111);
      const prizeName = ref('');
      const prizeType = ref(1);
      const winnersCount = ref(0);
      const ticketsCount = ref(0);
      const winnersTable = ref([]);
      const ticketsData = ref([]);

      const isDisabledCopy = ref(false);
      const message = ref('');

      const getRandomInt = (min, max) => {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min) + min);
      }

      const generateWinTable = () => {
        for(let i = 0; i < unref(winnersCount); i++) {
          winnersTable.value.push({
            "DT": unref(dataTime),
            "P": unref(prizeName),
            "T": unref(prizeType),
            "TI": getRandomInt(10000000, 99999999),
            "UI": 1
          })
        }

        prizeType.value += 1;
      };

      const generateTickets = () => {
        for(let i = 0; i < unref(ticketsCount); i++) {
          ticketsData.value.push(getRandomInt(10000000, 99999999))
        }
      }
      
        
      const copyToClipboard = (value) => {
        isDisabledCopy.value = true;
        let copiedValue = JSON.stringify(unref(value), null, 2);

        navigator.clipboard.writeText(copiedValue).then(() => {
          isDisabledCopy.value = false;
          message.value = 'Copying to clipboard was successful!';
        }, (err) => {
          isDisabledCopy.value = false;
          message.value =  `Could not copy text: ', ${err}`;

        });

        setTimeout(() => {
          message.value = '';
        }, 2000);
      };

      return {
        dataTime,
        prizeName,
        winnersCount,
        ticketsCount,
        winnersTable,
        ticketsData,
        activeTabIndex,
        message,
        generateWinTable,
        generateTickets,
        copyToClipboard
      }
  }
}
</script>

<style>
.generator {
  width: 1024px;
  margin-top: 15px;
  margin-left: auto;
  margin-right: auto;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

.generator__form {
  margin-bottom: 30px;
}

.generator-tabs {
  display: flex;
  margin-bottom: 15px;
}

.generator-tabs__item {
  background: #f0f0f0;
  padding: 10px 15px;
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
  color: #282828;
  cursor: pointer;
}

.generator-tabs__item--active {
  background: #c4c3c3;
}

.generator-form__field {
  display: block;
  width: 100%;
  height: 40px;
  margin-bottom: 10px;
  padding: 0 10px;
}

.generator-form__btn {
  display: block;
  height: 40px;
  padding: 10px;
  cursor: pointer;
}

.generator__result {
  position: relative;
  height: 500px;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #f0f0f0;
  overflow-y: auto;
}

.generator__copy {
  display: block;
  height: 40px;
  padding: 10px;
  cursor: pointer;
}


.generator__message {
  padding: 10px;
  background: #f0f0f0;
  font-size: 14px;
}
</style>
