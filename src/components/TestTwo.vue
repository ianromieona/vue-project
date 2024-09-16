<script setup>
import WelcomeItem from "./WelcomeItem.vue";

import EcosystemIcon from "./icons/IconEcosystem.vue";
</script>

<template>
    <WelcomeItem>
        <template #icon>
            <EcosystemIcon />
        </template>
        <template #heading>Task 2</template>
        Create a page that accepts a deposit amount and automatically computes
        additional bonus amount based on predefined promo settings.
    </WelcomeItem>

    <table>
        <thead>
            <tr>
                <td align="center" colspan="3">
                    Get <span>{{ this.getBonus(1000)?.label }}</span> bonus up
                    to $2000 when you deposit at least 1,000!
                </td>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td colspan="3" align="center">OTHER BONUS OFFERS</td>
            </tr>
            <tr>
                <td align="center">
                    Deposit $1.00 to $99.99 Get Instant <br />
                    <span>{{ this.getBonus(1)?.label }}</span> Bonus Credit
                </td>
                <td align="center">
                    Deposit $1.00 to $99.99 <br />
                    Get Instant
                    <span>{{ this.getBonus(100)?.label }}</span> Bonus Credit
                </td>
                <td align="center">
                    Deposit $1.00 to $99.99 <br />
                    Get Instant
                    <span>{{ this.getBonus(500)?.label }}</span> Bonus Credit
                </td>
            </tr>
        </tbody>
    </table>
    <br />
    <br />

    Deposit Amount: <input type="number" v-model="depositAmount" />
    <br />
    <button class="submitButton" :disabled="!depositAmount" @click="submit">
        Submit
    </button>
    <div v-if="submitted">
        <h3>Transaction Summary</h3>
        <hr />
        Deposit Amount: ${{ depositAmount }}<br />
        Bonus Amount: ${{ bonusAmount }} <br />
        Total Amount:${{ depositAmount + bonusAmount }}
        <br />
        <button @click="clear">Clear</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            depositAmount: "",
            bonusAmount: 0,
            bonusBase: {
                max_bonus_amount: 2000.0,
                bonus_matrix: [
                    { min_amount: 1, bonus_type: "F", bonus_value: 50 },
                    { min_amount: 100, bonus_type: "F", bonus_value: 125 },
                    { min_amount: 500, bonus_type: "P", bonus_value: 0.5 },
                    { min_amount: 1000, bonus_type: "P", bonus_value: 1 },
                ],
            },
            submitted: false,
        };
    },
    methods: {
        submit() {
            const bonusArr = this.bonusBase.bonus_matrix.sort(
                (a, b) => b.min_amount - a.min_amount
            );

            for (const i of bonusArr) {
                if (this.depositAmount >= i.min_amount) {
                    this.bonusAmount = this.calculateBonus(i);
                    break;
                }
            }

            this.submitted = true;
        },
        calculateBonus({ bonus_type, bonus_value }) {
            switch (bonus_type) {
                case "F":
                    return bonus_value;
                    break;
                case "P":
                    const caculatedP = this.depositAmount * bonus_value;

                    return caculatedP > 2000 ? 2000 : caculatedP;
                    break;
            }

            return 0;
        },

        getBonus(amount) {
            const bonus = this.bonusBase.bonus_matrix
                .filter((f) => f.min_amount === amount)
                .map((m) => ({
                    ...m,
                    label:
                        m.bonus_type === "F"
                            ? "$" + m.bonus_value
                            : m.bonus_value * 100 + "%",
                }))[0];
            return bonus;
        },
        clear() {
            this.depositAmount = "";
            this.bonusAmount = 0;
            this.submitted = false;
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
table {
    table-layout: fixed;
    width: 100%;
}
table,
th,
td {
    border: 1px solid black;
    border-collapse: collapse;
    font-weight: 100;
    padding: 10px;
}
table td span {
    font-weight: bold;
}
</style>
