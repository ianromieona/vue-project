<script setup>
import WelcomeItem from "./WelcomeItem.vue";

import EcosystemIcon from "./icons/IconEcosystem.vue";
</script>

<template>
    <WelcomeItem>
        <template #icon>
            <EcosystemIcon />
        </template>
        <template #heading>Task 1</template>

        Create a simple game page that allows a user to pick 8 unique numbers
        from 1 to 80. After submitting the 8 numbers, your program should
        randomly draw 20 numbers from 1 to 80.
    </WelcomeItem>
    <div v-if="!gameOver">
        <p>Pick 8 unique numbers between 1 and 80:</p>
        <div>
            <button
                v-for="n in 80"
                :key="n"
                :disabled="selectedNumbers.includes(n)"
                @click="selectNumber(n)"
            >
                {{ n }}
            </button>
        </div>
        <p>Selected numbers: {{ selectedNumbers.join(", ") }}</p>
        <button
            :disabled="selectedNumbers.length < 8"
            @click="startGame"
            class="submitButton"
        >
            Submit Numbers
        </button>
    </div>

    <div v-else>
        <p>Your numbers: {{ selectedNumbers.join(", ") }}</p>
        <p>Drawn numbers: {{ drawnNumbers.join(", ") }}</p>
        <p>Hits: {{ hits }}</p>
        <p v-if="winnings > 0">Your prize: {{ winnings }}</p>
        <p v-if="specialPrize">
            Special Prize! Prize doubled to: {{ winnings * 2 }}
        </p>
        <p v-else>Your prize: {{ winnings }}</p>
        <button @click="resetGame">Retry</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            selectedNumbers: [],
            drawnNumbers: [],
            hits: 0,
            winnings: 0,
            specialPrize: false,
            gameOver: false,
            payTable: {
                8: 10000,
                7: 1650,
                6: 100,
                5: 12,
                4: 2,
                3: 0,
                2: 0,
                1: 0,
            },
        };
    },
    methods: {
        selectNumber(number) {
            if (this.selectedNumbers.length < 8) {
                this.selectedNumbers.push(number);
            }
        },
        startGame() {
            this.drawnNumbers = this.getRandomNumbers(20, 1, 80);
            this.calculateHits();
            this.calculateWinnings();
            this.gameOver = true;
        },
        resetGame() {
            this.selectedNumbers = [];
            this.drawnNumbers = [];
            this.hits = 0;
            this.winnings = 0;
            this.specialPrize = false;
            this.gameOver = false;
        },
        getRandomNumbers(count, min, max) {
            const numbers = [];
            while (numbers.length < count) {
                const random =
                    Math.floor(Math.random() * (max - min + 1)) + min;
                if (!numbers.includes(random)) {
                    numbers.push(random);
                }
            }
            return numbers;
        },
        calculateHits() {
            this.hits = this.selectedNumbers.filter((num) =>
                this.drawnNumbers.includes(num)
            ).length;
        },
        calculateWinnings() {
            this.winnings = this.payTable[this.hits] || 0;
            if (
                this.drawnNumbers[this.drawnNumbers.length - 1] &&
                this.selectedNumbers.includes(
                    this.drawnNumbers[this.drawnNumbers.length - 1]
                )
            ) {
                this.specialPrize = true;
                this.winnings *= 2;
            }
        },
    },
};
</script>

<style scoped>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    text-align: center;
    margin-top: 40px;
}

button {
    margin: 5px;
    padding: 10px;
    cursor: pointer;
}

button:not(.submitButton) {
    width: 50px;
}

button:disabled {
    background-color: lightgray;
    cursor: not-allowed;
}
</style>
