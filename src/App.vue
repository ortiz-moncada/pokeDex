<template>
  <div class="contenedorPrincipal">
    <div class="encabezado">
      <div></div>
      <div class="imagen-encabezado">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/640px-International_Pok%C3%A9mon_logo.svg.png" alt="">
      </div>
      <div>
        <input type="text" v-model="pokemon" class="inpb" placeholder="ESCRIBE EL NOMBRE O NUMERO DEL POKEMON">
        <button @click="listarPokemones()" class="boton-busqueda">🔎</button>
        <a class="BotonBatallas" href="http://localhost:5174/" target="_blank">⚔️</a>
      </div>
      <div></div>
    </div>

    <div class="cuadres" v-if="nombre">
      <div>
        <div class="contenedor2"> 
          <div class="carta" v-if="nombre">
            <h1>{{ nombre }} {{ nmr }}</h1>
            <div class="imagen" >
              <img :src="image" alt="" :style="{ backgroundColor: colores(tipo[0]) }">
              <div class="susp" :style="{ backgroundColor: colores(tipo[0]) }">
                <div><img :src="imagenf" alt=""></div>
                <div><img :src="imagend" alt=""></div>
              </div>
            </div>
        </div>
      </div>

     
    </div>
    <div>
      <div class="Estadisticas2">
            <div class="datos">
              <h3>Peso: {{ peso }} kg</h3>
              <h3>Altura: {{ altura }} m</h3>
            </div>
            <br>
  <h3>Tipo:<span v-for="(t, index) in tipo" :key="index" :style="{ color: colores(t) }">
      {{ t }}<span v-if="index < tipo.length - 1">, </span>
    </span></h3>
  <br>
  <h3>Debilidades:<span v-for="(d, index) in devilidad" :key="index" :style="{ color: colores(d) }">
      {{ d }}<span v-if="index < devilidad.length - 1">, </span>
    </span> </h3>
          </div>
        <div class="estadisticas-del-personaje">
          <h1>Estadísticas</h1>
          <div class="estadisticas-pokemon">

            <h3>Ataque: {{ Ataque }}</h3>

            <div class="divbar">
              <div class="bar" :style="{width: Ataque + 'px'}"></div>
            </div>

            <h3>Ataque Especial: {{ AtaqueEspecial }}</h3>
            <div class="divbar">
              <div class="bar5" :style="{width: AtaqueEspecial + 'px'}"></div>
            </div>

            <h3>Defensa: {{ Defensa }}</h3>

            <div class="divbar">
              <div class="bar2" :style="{width: Defensa + 'px'}"></div>
            </div>

            <h3>Velocidad: {{ Velocidad }}</h3>

            <div class="divbar">
              <div class="bar3" :style="{width: Velocidad + 'px'}"></div>
            </div>

            <h3>HP: {{ HP }}</h3>

            <div class="divbar">
              <div class="bar4" :style="{width: HP + 'px'}"></div>
            </div>

            

          </div>
        </div>
            </div>
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
let AtaqueEspecial = ref("");


window.addEventListener("keydown",(e)=>{
  if(e.key === "Enter"){
    listarPokemones();
  }
})

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
    AtaqueEspecial.value = data.stats[4].base_stat;


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
    AtaqueEspecial.value = 0;
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
.BotonBatallas {
  position: absolute;
    font-size: 35px; 
    margin-left:10%;
    margin-top: -10px;
    cursor: pointer;

  }

 
.divbar {
    background-color: white;
    height: 40px;
    border-radius: 50px;
    width: 190%;
    margin-left: -45%;
    border:1px black solid;

}
.bar {
    width: 90%;
    background-color: #dd5e3e;
    height: 40px;
    border-radius:50px;
}

.bar2{
    width: 90%;
    background-color: rgb(81, 162, 219);
    height: 40px;
    border-radius:50px;
}

.bar3{
    width: 90%;
    background-color: rgb(114, 206, 234);
    height: 40px;
    border-radius:50px;
}

.bar4 {
    width: 90%;
    background-color: rgb(79, 200, 15);
    height: 40px;
    border-radius:50px;
}
.bar5 {
    width: 90%;
    background-color: rgb(255, 0, 0);
    height: 40px;
    border-radius:50px;
}

body, html {
  height: 100%;
}
.susp{
  display: grid;
  grid-template-columns:50% 50% ;
  max-height: 245px;

}

.contenedorPrincipal {
  align-items: center;
  justify-content: center;
  background-image: url(https://i.pinimg.com/736x/b6/40/c5/b640c58cfb5968c64b87c0334565a9fa.jpg);
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
  grid-template-columns:50% 50% ;
}

.contenedor2{
  text-align: center;
  max-width: 90%;
  margin:  5%;
  background: rgb(140,104,19);
  background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid rgb(0, 0, 0);
  border-radius: 5px;
  padding: 10px;
}
.estadisticas-del-personaje {
  text-align: center;
  max-width: 60%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left: 20%;
  margin-top: 4%;

}

.Estadisticas2{
  text-align: center;
  max-width: 60%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left:20%;
  margin-top: 4%;
}


.datos {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.imagen {
  border-radius: 5px;
  width: 80%;
  border: 5px solid #000;
  margin-left: 9%;
}


img {
  width: 100%;
}


.inpb {
  width: 100%;
  height: 30px;
  border: none;
  border-radius: 5px;
}

.boton-busqueda {
  background-color: transparent;
  position: absolute;
  border: none;
  padding: 5px 20px;
  cursor: pointer;
  margin-left: 2%;
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
 
  .divbar {
    background-color: white;
    height: 40px;
    border-radius: 50px;
    width: 120%;
    margin-left: -5%;

}

}

@media (max-width:768px){
  .divbar {
    background-color: white;
    height: 40px;
    border-radius: 50px;
    width: 120%;
    margin-left: -5%;    
}

.estadisticas-del-personaje {
  text-align: center;
  max-width: 80%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left: 0%;
  margin-top: 4%;

}

.Estadisticas2{
  text-align: center;
  max-width: 80%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left:0%;
  margin-top: 4%;
}

}

@media (max-width:426px) {

  .cuadres{
  display: grid;
  grid-template-columns:100%  ;
}
.estadisticas-del-personaje {
  text-align: center;
  max-width: 80%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left: 5%;
  margin-top: 4%;

}

.Estadisticas2{
  text-align: center;
  max-width: 80%;
  background: rgb(140,104,19);
background: linear-gradient(0deg, rgba(140,104,19,1) 33%, rgba(255,252,14,1) 71%);
  border: 10px solid #000000;
  border-radius: 20px;
  padding: 10px;
  margin-left:5%;
  margin-top: 4%;
}

}

@media (max-width:321px) {

  .divbar {
    background-color: white;
    height: 40px;
    border-radius: 50px;
    width: 100%;
    margin-left: -5%;
}

.encabezado {
  display: grid;
  grid-template-columns: 0fr 2fr 2fr 1fr; 
  width: 100%;
  align-items: center;
  margin-bottom: 20px;
}

}




</style>
