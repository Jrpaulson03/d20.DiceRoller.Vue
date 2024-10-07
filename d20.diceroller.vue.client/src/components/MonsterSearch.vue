<template>
    <div class="monster-search-component">
        <h1>Monster Search</h1>
        <input type="text"
               v-model="searchQuery"
               placeholder="Type monster name..." />
        <ul v-if="searchResults.length > 0">
            <li v-for="monster in searchResults"
                :key="monster.index"
                @click="selectMonster(monster)">
                {{ monster.name }}
            </li>
        </ul>
        <div v-if="monsterDetails" class="monster-details stat-block wide">
            <h2>{{ monsterDetails.name ? monsterDetails.name : 'Unknown Monster' }}</h2>
            <img v-if="monsterDetails.image"
                 :src="`https://www.dnd5eapi.co${monsterDetails.image}`"
                 :alt="monsterDetails.name ? monsterDetails.name : 'Monster Image'" />
            <div class="stats">
                <p><strong>Size:</strong> {{ monsterDetails.size ? monsterDetails.size : 'Unknown' }}</p>
                <p><strong>Type:</strong> {{ monsterDetails.type ? monsterDetails.type : 'Unknown' }}</p>
                <p><strong>Alignment:</strong> {{ monsterDetails.alignment ? monsterDetails.alignment : 'Unknown' }}</p>
                <p><strong>Armor Class:</strong> {{ (monsterDetails.armor_class && monsterDetails.armor_class[0] && monsterDetails.armor_class[0].value) ? monsterDetails.armor_class[0].value : 'Unknown' }}</p>
                <p><strong>Hit Points:</strong> {{ monsterDetails.hit_points ? monsterDetails.hit_points : 'Unknown' }}</p>
                <p><strong>Speed:</strong> {{ monsterDetails.speed ? formatSpeed(monsterDetails.speed) : 'Unknown' }}</p>

                <h3>Abilities</h3>
                <ul>
                    <li><strong>STR:</strong> {{ monsterDetails.strength ? monsterDetails.strength : 'N/A' }}</li>
                    <li><strong>DEX:</strong> {{ monsterDetails.dexterity ? monsterDetails.dexterity : 'N/A' }}</li>
                    <li><strong>CON:</strong> {{ monsterDetails.constitution ? monsterDetails.constitution : 'N/A' }}</li>
                    <li><strong>INT:</strong> {{ monsterDetails.intelligence ? monsterDetails.intelligence : 'N/A' }}</li>
                    <li><strong>WIS:</strong> {{ monsterDetails.wisdom ? monsterDetails.wisdom : 'N/A' }}</li>
                    <li><strong>CHA:</strong> {{ monsterDetails.charisma ? monsterDetails.charisma : 'N/A' }}</li>
                </ul>

                <h3 v-if="monsterDetails.saving_throws && monsterDetails.saving_throws.length">Saving Throws</h3>
                <ul v-if="monsterDetails.saving_throws && monsterDetails.saving_throws.length">
                    <li v-for="savingThrow in monsterDetails.saving_throws" :key="savingThrow.name">
                        <strong>{{ savingThrow.name }}:</strong> {{ savingThrow.value }}
                    </li>
                </ul>

                <h3 v-if="monsterDetails.actions && monsterDetails.actions.length">Actions</h3>
                <ul v-if="monsterDetails.actions && monsterDetails.actions.length">
                    <li v-for="action in monsterDetails.actions" :key="action.name">
                        <strong>{{ action.name }}:</strong> {{ action.desc }}
                    </li>
                </ul>

                <h3 v-if="monsterDetails.special_abilities && monsterDetails.special_abilities.length">Special Abilities</h3>
                <ul v-if="monsterDetails.special_abilities && monsterDetails.special_abilities.length">
                    <li v-for="ability in monsterDetails.special_abilities" :key="ability.name">
                        <strong>{{ ability.name }}:</strong> {{ ability.desc }}
                    </li>
                </ul>

                <h3 v-if="monsterDetails.legendary_actions && monsterDetails.legendary_actions.length">Legendary Actions</h3>
                <ul v-if="monsterDetails.legendary_actions && monsterDetails.legendary_actions.length">
                    <li v-for="legendary in monsterDetails.legendary_actions" :key="legendary.name">
                        <strong>{{ legendary.name }}:</strong> {{ legendary.desc }}
                    </li>
                </ul>
            </div>
        </div>


    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                searchQuery: '',
                searchResults: [],
                monsterDetails: null,
                timeout: null,
            };
        },
        watch: {
            searchQuery(newQuery) {
                if (newQuery.length >= 3) {
                    clearTimeout(this.timeout);
                    this.timeout = setTimeout(() => {
                        this.searchMonsters();
                    }, 500);
                } else {
                    this.searchResults = [];
                    this.monsterDetails = null;
                }
            },
        },
        methods: {
            async searchMonsters() {
                try {
                    const response = await axios.get(
                        'https://www.dnd5eapi.co/api/monsters'
                    );
                    this.searchResults = response.data.results.filter((monster) =>
                        monster.name.toLowerCase().includes(this.searchQuery.toLowerCase())
                    );
                } catch (error) {
                    console.error('Error fetching monster list:', error);
                }
            },
            async selectMonster(monster) {
                try {
                    const response = await axios.get(
                        `https://www.dnd5eapi.co${monster.url}`
                    );
                    this.monsterDetails = response.data;
                    this.searchResults = []; // Clear search results
                    this.searchQuery = monster.name; // Set search query to selected monster
                } catch (error) {
                    console.error('Error fetching monster details:', error);
                }
            },
            formatSpeed(speed) {
                return Object.entries(speed)
                    .map(([type, value]) => `${type}: ${value}`)
                    .join(', ');
            },
        },
    };
</script>
<style src="../assets/StatBlock.css" scoped></style>
