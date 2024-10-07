<template>
    <div class="diceRoller-component">
        <h1>D&D Dice Roller</h1>
        <div>
            <input type="number" v-model.number="diceCount" min="1" placeholder="Number of dice" />
        </div>
        <div class="dice-buttons">
            <button v-for="die in dice" :key="die" @click="rollDice(die)">
                Roll {{ die }}
            </button>
        </div>
        <div v-if="rolls.length > 0">
            <h2>Results</h2>
            <ul>
                <li v-for="(roll, index) in rolls" :key="index">
                    Rolled a {{ roll.die }}: {{ roll.result }}
                </li>
            </ul>
            <h3>Total: {{ total }}</h3>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                dice: ['d4', 'd6', 'd8', 'd10', 'd12', 'd20', 'd100'],
                diceCount: 1,
                rolls: [],
            };
        },
        computed: {
            total() {
                return this.rolls.reduce((sum, roll) => sum + roll.result, 0);
            },
        },
        methods: {
            rollDice(die) {
                const sides = parseInt(die.slice(1));
                for (let i = 0; i < this.diceCount; i++) {
                    const result = Math.floor(Math.random() * sides) + 1;
                    this.rolls.push({ die, result });
                }
            },
        },
    };
</script>

<style scoped>
    /* Parchment background */
    .diceRoller-component {
        background-color: #f4e9d8;
        padding: 2rem;
        border: 2px solid #8b4513;
        border-radius: 15px;
        box-shadow: 0px 0px 20px rgba(0, 0, 0, 0.5);
        font-family: 'MedievalSharp', serif; /* Use a D&D themed font */
        color: #5e3a1a;
        max-width: 700px;
        margin: 0 auto;
    }

    /* Headings styled like a medieval title */
    h1 {
        font-family: 'Cinzel Decorative', serif;
        color: #5e3a1a;
        font-size: 2.5rem;
        text-align: center;
        text-shadow: 2px 2px #8b4513;
    }

    h2, h3 {
        font-family: 'Cinzel', serif;
        color: #8b4513;
        text-align: center;
        text-shadow: 1px 1px #5e3a1a;
        margin-bottom: 1rem;
    }

    /* Input field styled like an ancient scroll */
    input[type="number"] {
        font-family: 'MedievalSharp', serif;
        border: 2px solid #8b4513;
        padding: 0.5rem;
        font-size: 1.2rem;
        background-color: #f9f4e3;
        color: #5e3a1a;
        border-radius: 5px;
        box-shadow: inset 0px 0px 10px rgba(0, 0, 0, 0.3);
        width: 100%;
        max-width: 120px;
        margin: 1rem auto;
        display: block;
    }

    /* Buttons styled like scrolls */
    button {
        background-color: #8b4513;
        color: #f9f4e3;
        border: none;
        padding: 0.7rem 1.2rem;
        margin: 0.5rem;
        font-family: 'Cinzel', serif;
        font-size: 1.2rem;
        border-radius: 10px;
        cursor: pointer;
        box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
        transition: all 0.3s ease;
    }

        button:hover {
            background-color: #5e3a1a;
            color: #fff;
            transform: translateY(-3px);
        }

    /* Dice buttons arranged in a row */
    .dice-buttons {
        display: flex;
        justify-content: space-evenly;
        margin-bottom: 1.5rem;
    }

    /* Results styled with medieval flair */
    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        font-family: 'MedievalSharp', serif;
        color: #5e3a1a;
        font-size: 1.3rem;
        margin: 0.5rem 0;
    }

    /* Total section stands out */
    h3 {
        font-size: 1.7rem;
        color: #8b0000;
        text-shadow: 1px 1px #5e3a1a;
    }

    /* Fonts to import */
    @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Cinzel+Decorative:wght@700&family=MedievalSharp&display=swap');
</style>