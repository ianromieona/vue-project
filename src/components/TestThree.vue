<script setup>
import WelcomeItem from "./WelcomeItem.vue";

import EcosystemIcon from "./icons/IconEcosystem.vue";
</script>

<template>
    <WelcomeItem>
        <template #icon>
            <EcosystemIcon />
        </template>
        <template #heading>Task 3</template>
        Create a program that will accept a set of 3 unique cards. Arrange and
        print the cards from highest to lowest.
    </WelcomeItem>

    <p>Pick 3 unique cards:</p>
    <div>
        <button
            v-for="n in getCards()"
            :key="n"
            :disabled="selectedCards.includes(n)"
            @click="selectCard(n)"
        >
            {{ n }}
        </button>
    </div>
    <p>Your selections: {{ selectedCards.join(", ") }}</p>
    <p v-if="gameOver">Result: {{ result.join(", ") }}</p>
    <button
        v-if="!gameOver"
        class="submitButton"
        :disabled="selectedCards.length !== 3"
        @click="submit"
    >
        Submit
    </button>
    <button class="submitButton" v-if="gameOver" @click="clear">Retry</button>
</template>

<script>
export default {
    data() {
        return {
            suitOrder: { D: 4, H: 3, S: 2, C: 1 },
            gameOver: false,
            selectedCards: [],
            rankOrder: {
                A: 13,
                K: 12,
                Q: 11,
                J: 10,
                10: 9,
                9: 8,
                8: 7,
                7: 6,
                6: 5,
                5: 4,
                4: 3,
                3: 2,
                2: 1,
            },
            result: [],
        };
    },
    methods: {
        selectCard(number) {
            if (this.selectedCards.length < 3) {
                this.selectedCards.push(number);
            }
        },
        submit() {
            this.result = this.sortCards();
            this.gameOver = true;
        },
        sortCards() {
            const parsedCards = this.selectedCards.map((card) => ({
                suit: card[0],
                rank: card.slice(1),
            }));

            const rankCounts = parsedCards.reduce((acc, card) => {
                acc[card.rank] = (acc[card.rank] || 0) + 1;
                return acc;
            }, {});

            parsedCards.sort((a, b) => {
                if (rankCounts[a.rank] === rankCounts[b.rank]) {
                    if (this.rankOrder[a.rank] === this.rankOrder[b.rank]) {
                        return this.suitOrder[b.suit] - this.suitOrder[a.suit];
                    }
                    return this.rankOrder[b.rank] - this.rankOrder[a.rank];
                }
                return rankCounts[b.rank] - rankCounts[a.rank];
            });

            return parsedCards.map((card) => card.suit + card.rank);
        },

        getCards() {
            const cArray = [];

            for (const i in this.suitOrder) {
                for (const a in this.rankOrder) {
                    cArray.push(i + "" + a);
                }
            }

            return cArray;
        },
        clear() {
            this.gameOver = false;
            this.selectedCards = [];
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
