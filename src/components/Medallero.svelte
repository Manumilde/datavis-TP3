<script>
  import * as d3 from "d3"
  import {onMount} from "svelte"

  /* Propiedad donde guardaremos la data */
  export let peliculas = []

  /* 1. Escala para edades */
  let grosor = d3.scaleLinear().range([5, 20])

/* 3. Escala para continentes */
let colorPhase = d3
  .scaleOrdinal()
  .domain(["Marvel", "DC"])
  .range(["#ed334e", "#1434A4"])

/* 4. Escala para altura */
let radioBudget = d3.scaleRadial()

/* $: Data reactiva, cuando cambia data se ejecuta este bloque */
$: {
  
  /* Actualizamos dominio y rango con la data de Budget */
  let minMaxBudget = d3.extent(peliculas, d => d.Budget)
  radioBudget = radioBudget.domain(minMaxBudget).range([10000000, 400000000])
  
}
</script>


<div class="container">
  <!-- Iteramos la data para visualizar c/ entidad -->
  {#each peliculas as dep, index}
    <div class="person-container">
      
      <div
        class="person"
        style="border-width: 2px; 
        background-color: {colorPhase(dep.Franchise)}; 
        width: {2 * 10}px; 
        height: {2 * 10}px; 
        border-color: black ;
        "
      ></div>
      <p class="name">
        <b>{dep.Film}</b>
        <br />
        <!-- {dep.RottenTomatoesCriticScore} -->
      </p>
    </div>
  {/each}
</div>

<style>
  .container {
    display: flex;
    justify-content: center;
    align-items: end;
    margin: auto;
    flex-wrap: wrap;
    max-width: 1000px;
    gap: 30px;
    margin-bottom: 100px;
  }
  .person-container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    flex: 180px 0 0;
  }
  .person {
    width: 100px;
    height: 100px;
    border: 10px solid black;
    border-radius: 50%;
    box-sizing: border-box;
    background-color: pink;
    /* Transicion para el cambio de data */
    transition: all 0.5s ease-in-out;
  }
  .name {
    font-size: 14px;
    color: rgb(65, 65, 65);
    font-weight: normal;
    text-align: center;
    margin-top: 5px;
  }
</style>
