<template>
  <div class="contenedorPrincipal">
    <div class="encabezado">
      <div></div>
      <div class="imagen-encabezado">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/640px-International_Pok%C3%A9mon_logo.svg.png" alt="">
      </div>
      <div>
        <input type="text" v-model="pokemon" class="inpb">
        <button @click="listarPokemones()" class="boton-busqueda">🔎</button>
      </div>
      <div></div>
    </div>

    <div class="cuadres">
      <div>
        <div class="contenedor2" :style="{ borderColor: colores(tipo[0]) }"> 
          <div class="carta" v-if="nombre">
            <h1>{{ nombre }} {{ nmr }}</h1>
            <div class="imagen">
              <img :src="image" alt="">
              <div class="susp">
                <div><img :src="imagenf" alt=""></div>
                <div><img :src="imagend" alt=""></div>
              </div>
            </div>
            <br>
            <div class="datos">
              <h3>Peso: {{ peso }} kg</h3>
              <h3>Altura: {{ altura }} m</h3>
            </div>
          </div>

          <div class="datos-pokemon">
            <br><h3>Tipo: {{ tipo.join(", ") }}</h3><br>
            <h3>Debilidades: {{ devilidad.join(", ") }}</h3>
          </div>
        </div>
      </div>

      <div>
        <div class="estadisticas-del-personaje">
          <h1>Estadísticas</h1>
          <div class="estadisticas-pokemon">
            <h3>Ataque: {{ Ataque }}</h3>
            <h3>Defensa: {{ Defensa }}</h3>
            <h3>Velocidad: {{ Velocidad }}</h3>
            <h3>HP: {{ HP }}</h3>
          </div>
        </div>
      </div>
      <div></div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";

let nombre = ref("");
let image = ref("");
let pokemon = ref("");
let peso = ref("");
let altura = ref("");
let tipo = ref([]);
let devilidad = ref([]);
let nmr = ref("");
let Ataque = ref("");
let Defensa = ref("");
let Velocidad = ref("");
let HP = ref("");
let imagenf = ref("");
let imagend = ref("");

async function listarPokemones() {
  let url = `https://pokeapi.co/api/v2/pokemon/${pokemon.value.toLowerCase()}`;
  try {
    let { data } = await axios.get(url);
    console.log(data);

    nombre.value = data.name;
    image.value = data.sprites.other['official-artwork'].front_default;
    peso.value = data.weight / 10;
    altura.value = data.height / 10;
    tipo.value = data.types.map(typeinfo => typeinfo.type.name);
    nmr.value = `#${data.id}`;
    imagenf.value = data.sprites.front_default;
    imagend.value = data.sprites.back_default;

    Ataque.value = data.stats[1].base_stat;
    Defensa.value = data.stats[2].base_stat;
    Velocidad.value = data.stats[5].base_stat;
    HP.value = data.stats[0].base_stat;

    let debilidades = new Set();
    for (const typeinfo of data.types) {
      let { data: typeData } = await axios.get(typeinfo.type.url);
      let weaknesses = typeData.damage_relations.double_damage_from.map(damageType => damageType.name);
      weaknesses.forEach(weakness => debilidades.add(weakness));
    }
    devilidad.value = Array.from(debilidades);
  } catch (error) {
    console.error("Error: ", error);
    nombre.value = "Pokémon no encontrado";
    image.value = "https://cdn-icons-png.flaticon.com/256/5726/5726429.png";
    peso.value = 0;
    altura.value = 0;
    tipo.value = ["no hay tipo"];
    devilidad.value = ["ninguna"];
    nmr.value = "# ?";
    Ataque.value = Defensa.value = Velocidad.value = HP.value = 0;
    imagenf.value = "";
    imagend.value = "";
  }
}

function colores(tipo) {
  const typeColors = {
    fire: '#f08030',
    water: '#6890f0',
    grass: '#78c850',
    electric: '#f8d030',
    ice: '#98d8d8',
    fighting: '#c03028',
    poison: '#a040a0',
    ground: '#e0c068',
    flying: '#a890f0',
    psychic: '#f85888',
    bug: '#a8b820',
    rock: '#b8a038',
    ghost: '#705898',
    dragon: '#7038f8',
    dark: '#705848',
    steel: '#b8b8d0',
    fairy: '#f0b6bc',
    normal: '#a8a878'
  };
  return typeColors[tipo] || '#000';
}
</script>



<style>
* {
  margin: 0;
  padding: 0;
}

body, html {
  height: 100%;
}
.susp{
  background-color: #fff;
  display: grid;
  grid-template-columns:50% 50% ;
  max-height: 130px;

}

.contenedorPrincipal {
  align-items: center;
  justify-content: center;
  background-image: url(https://i.pinimg.com/564x/6f/7b/97/6f7b97f9c499abbae13db16ae9776918.jpg);
  background-size: cover;
  min-height: 100vh;
}

.encabezado {
  display: grid;
  grid-template-columns: 1fr 2fr 2fr 1fr; 
  width: 100%;
  align-items: center;
  margin-bottom: 20px;
}

.imagen-encabezado {
  width: 80%;
  display: flex;
  justify-content: center;
}

.cuadres{
  display: grid;
  grid-template-columns: 33% 33% 33%;
}

.contenedor2{
  text-align: center;
  max-width: 60%;
  margin: 20px 5%;
  background: rgb(158,153,146);
  background: linear-gradient(0deg, rgba(158,153,146,1) 23%, rgba(218,215,199,1) 91%);
  border: 20px solid #fff;
  border-radius: 5px;
  padding: 10px;
}
.estadisticas-del-personaje {
  text-align: center;
  max-width: 60%;
  background: rgb(158,153,146);
  background: linear-gradient(0deg, rgba(158,153,146,1) 23%, rgba(218,215,199,1) 91%);
  border: 1px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 30%;
  margin-top: 4%;

}

.datos {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.imagen {
  background-color: rgb(255, 255, 255);
  border-radius: 5px;
  width: 90%;
  margin: 0 auto;
  border: 10px solid #000;
}


img {
  width: 100%;
}

img:hover {
  width: 100%;
}

.inpb {
  margin-top: 5%;
  width: 100%;
  height: 30px;
  border: none;
  padding: 5px;
  border-radius: 5px;
}

.boton-busqueda {
  background-color: transparent;
  position: absolute;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 35%;
  margin-top: -2.2%;
  border-radius: 25px;
  background-color: #fff;
}


.estadisticas-del-personaje {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}




@media (max-width:1024px){
  .contenedor2{
  text-align: center;
  max-width: 70%;
  margin: 20px 5%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
}
.estadisticas-del-personaje {
  text-align: center;
  max-width: 70%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 30%;

}
.boton-busqueda {
  background-color: transparent;
  position: fixed;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 35%;
  margin-top: -3.4%;
  border-radius: 25px;
  background-color: #fff;
}


}

@media (max-width:768px){

  .cuadres{
  display: grid;
  grid-template-columns: 45% 45% 10%;
}

  .contenedor2{
  text-align: center;
  max-width: 70%;
  margin: 20px 5%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
}
.estadisticas-del-personaje {
  text-align: center;
  max-width: 70%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 30%;

}
.boton-busqueda {
  background-color: transparent;
  position: fixed;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 35%;
  margin-top: -5%;
  border-radius: 25px;
  background-color: #fff;
}

.encabezado {
  display: grid;
  grid-template-columns: 0fr 2fr 2fr 2fr; 
  width: 100%;
  align-items: center;
  margin-bottom: 20px;
}

.inpb {
  margin-top: 5%;
  width: 100%;
  height: 30px;
  border: none;
  padding: 5px;
  border-radius: 5px;
}

}

@media (max-width:426px) {
  .cuadres{
  display: grid;
  grid-template-columns: 100%;

  }
  .contenedor2{
  text-align: center;
  max-width: 70%;
  margin: 20px 5%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 10%;
}
  .estadisticas-del-personaje{
  text-align: center;
  max-width: 70%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 10%;
  }

  .inpb {
  width: 90%;
  height: 30px;
  border: none;
  padding: 5px;
  border-radius: 5px;
}
.boton-busqueda {
  background-color: transparent;
  position: absolute;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 5%;
  margin-top: 3%;
  border-radius: 25px;
  background-color: #fff;
}

}

@media (max-width:321px) {
  .cuadres{
  display: grid;
  grid-template-columns: 100%;

  }
  .contenedor2{
  text-align: center;
  max-width: 80%;
  margin: 20px 5%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
}
  .estadisticas-del-personaje{
  text-align: center;
  max-width: 70%;
  background: linear-gradient(0deg, rgba(241, 148, 25, 1) 23%, rgba(228, 197, 17, 1) 82%);
  border: 7px solid #fff;
  border-radius: 20px;
  padding: 10px;
  margin-left: 10%;
  }

  .inpb {
  width: 90%;
  height: 30px;
  border: none;
  padding: 5px;
  border-radius: 5px;
}
.boton-busqueda {
  background-color: transparent;
  position: absolute;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 5%;
  margin-top: 3%;
  border-radius: 25px;
  background-color: #fff;
}

}




</style>
