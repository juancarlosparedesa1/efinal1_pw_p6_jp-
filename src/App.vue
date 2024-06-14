<template>
  <div class="container">
    <div v-if="juego">
      <div class="header">
        <label>Puntaje: {{ puntaje }}</label>
        <label>Intento: {{ intento }}</label>
      </div>
      <div class="rectangle">
        <Pokemon v-for="(pokemon, index) in pokemons" :key="index" :imagen="pokemon.imagen" :nombre="pokemon.nombre" />
      </div>
      <div class="button">
        <button @click="jugar">Jugar</button>
      </div>
    </div>
    <div v-else>
      <div v-if="juegoTerminado && puntaje < 10 && intento === maxIntentos" style="color: red">
        <p>Has utilizado tus 5 intentos. El juego ha terminado, inténtalo nuevamente.</p>
      </div>
      <div v-if="juegoTerminado && puntaje >= 10" style="color: blue">
        <p>Puntaje: {{ puntaje }}. ¡Felicitaciones! Has ganado un premio de $10,000.00.</p>
      </div>

      <div v-if="juegoTerminado">
        <button @click="nuevoJuego">Nuevo Juego</button>
      </div>
    </div>
  </div>
</template>

<script>
import Pokemon from "./components/Pokemon.vue";

export default {
  name: "App",
  components: {
    Pokemon,
  },
  data() {
    return {
      puntaje: 0,
      intento: 0,
      pokemons: [],
      maxIntentos: 5,
      juegoTerminado: false,
      juego: true,
      pokemonIDs: [12, 10, 14, 21, 11],
    };
  },
  methods: {
    async jugar() {
      this.intento++;
      const pokemonElegidos = this.obtenerPokemonAleatorio();
      this.pokemons = await this.obtenerDetallesPokemon(pokemonElegidos);
      this.calcularPuntaje();
      if (this.intento >= this.maxIntentos || this.puntaje >= 10) {
        this.juegoTerminado = true;
        this.juego = false;
      }
    },
    async obtenerDetallesPokemon(pokemonIDs) {
      const pokemons = [];
      for (const id of pokemonIDs) {
        const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${id}`);
        if (!response.ok) {
          throw new Error(`No se pudo obtener los detalles del pokemon con ID ${id}`);
        }
        const data = await response.json();
        const nombre = data.name;
        const imagen = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${id}.svg`;
        pokemons.push({ nombre, imagen });
      }
      return pokemons;
    },
    obtenerPokemonAleatorio() {
      const pokemonElegidos = [];
      while (pokemonElegidos.length < 3) {
        const randomIndex = Math.floor(Math.random() * this.pokemonIDs.length);
        const randomPokemonID = this.pokemonIDs[randomIndex];
        if (!pokemonElegidos.includes(randomPokemonID)) {
          pokemonElegidos.push(randomPokemonID);
        }
      }
      return pokemonElegidos;
    },
    calcularPuntaje() {
      const pokemonIDs = this.pokemons.map(pokemon => pokemon.id);
      const intersection = this.pokemonIDs.filter(value => pokemonIDs.includes(value));
      const cantidadCoincidente = intersection.length;

      if (cantidadCoincidente === 3) {
        this.puntaje += 5;
      } else if (cantidadCoincidente === 2) {
        this.puntaje += 2;
      }
    },
    nuevoJuego() {
      this.puntaje = 0;
      this.intento = 0;
      this.pokemons = [];
      this.juegoTerminado = false;
      this.juego = true;
    },
  },
};
</script>

<style>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  width: 700px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
  border: solid 1px black;
}

.header {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  width: 700px;
  height: 50px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
}

.header label {
  margin: 0 90px;
  font-weight: bold;
}

.rectangle {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  width: 700px;
  height: 450px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
}

button {
  padding: 10px 40px;
  border: 4px solid black;
  margin: 10px;
  font-weight: bold;
}
</style>
