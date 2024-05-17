<script lang="ts" setup>
import { ref } from 'vue';
import PokemonOptions from '../components/PokemonOptions.vue';
import PokemonPicture from '../components/PokemonPicture.vue';
import getPokemonOptions from '../helpers/getPokemonOptions';
import { Pokemon } from '../interfaces/pokemon';


const showPokemon = ref(false);
const showAnswer = ref(false);
const message = ref('');
const pokemonArr = ref<Pokemon[]>([]);
const pokemon = ref<Pokemon>();

const mixPokemonArray = async () => {
    pokemonArr.value = await getPokemonOptions();
    const randomInt = Math.floor(Math.random() * 4);
    pokemon.value = pokemonArr.value[randomInt];
};

const checkAnswer = (selectedId: number) => {
    if (!pokemon.value) return;
    
    showPokemon.value = true;
    showAnswer.value = true;
    
    if (selectedId === pokemon.value.id) {
      
        message.value = `Correcto, ${pokemon.value.name}`;
        return; // Evitar que el segundo condicional se active
    }


    const motivationalMessages = [
           `Maldito, eso es un ${pokemon.value.name}`,
           `Desgraciado, eso es un ${pokemon.value.name}`,
           `Por eso te montan cacho, porque no sabes que eso es un ${pokemon.value.name}`,

        ]
        const randomInt = Math.floor(Math.random() * motivationalMessages.length);
    message.value = `${motivationalMessages[randomInt]}`;
};

const newGame = () => {
    showPokemon.value = false;
    showAnswer.value = false;
    pokemonArr.value = [];
    pokemon.value = undefined;
    mixPokemonArray();
};

mixPokemonArray();
</script>



<template>
    <h1 v-if="!pokemon">Espere por favor...</h1>

    <div v-else>
        <h1>¿Quién es este pokémon?</h1>
        
        <PokemonPicture 
            :pokemon-id="pokemon.id" 
            :show-pokemon="showPokemon" 
        />

        <PokemonOptions 
            :pokemons="pokemonArr"
            @selection-pokemon="checkAnswer"
        />

        <template v-if="showAnswer">
            <h2 class="fade-in">{{ message }}</h2>
            <button @click="newGame">
                Nuevo Juego
            </button>
        </template>

    </div>
    
</template>

