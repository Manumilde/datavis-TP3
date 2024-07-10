<script>
  import Scroller from "@sveltejs/svelte-scroller"
  import {onMount} from "svelte"
  import * as d3 from "d3"
  import { fade } from 'svelte/transition';

  import Medallero from "./components/Medallero.svelte"
  import DebugScroller from "./components/DebugScroller.svelte"
  import BarChart from "./components/BarChart.svelte"
  import RatingsChart from "./components/RatingsChart.svelte"
  import LineGraph from "./components/LineGraph.svelte"
  import LineGraph2 from "./components/LineGraph2.svelte"
  
  import LineChart from "./components/LineChart.svelte"
  import Text1 from "./components/Text1.svelte"
  import Text2 from "./components/Text2.svelte"
  import Text2o from "./components/Text2o.svelte"
  import Text3 from "./components/Text3.svelte"
  import Text4 from "./components/Text4.svelte"
  import Text5 from "./components/Text5.svelte"
  import Text6 from "./components/Text6.svelte"
  import Text7 from "./components/Text7.svelte"
  import TextCalidad from "./components/TextCalidad.svelte"
  import Conclusion from "./components/Conclusion.svelte"

  /* Variables para la data del medallero */
  let peliculas = []
  let filteredPeliculas = []
  let streamShow = []
  let filteredShow = []
  let peliculasYear = []
  let peliculasFlopSucc = []

  /* Variables para el scroller1 */
  let count
  let index
  let offset
  let progress
  let top = 0.1
  let threshold = 0.5
  let bottom = 0.9

  /* Variables para el scroller 2 */
  let count2
  let index2
  let offset2
  let progress2
  let top2 = 0.1
  let threshold2 = 0.5
  let bottom2 = 0.9

  /* Variables para el scroller 3 */
  let count3
  let index3
  let offset3
  let progress3
  let top3 = 0.1
  let threshold3 = 0.5
  let bottom3 = 0.9

  /* Variables para el scroller 4 */
  let count4
  let index4
  let offset4
  let progress4
  let top4 = 0.1
  let threshold4 = 0.5
  let bottom4 = 0.9

  /* Charts */
  let charts = {
    0: "ironman.jpg",
    1: "darkknight.jpg",
    2: "endgame.jpg",
    3: "marvel-logo.png",
  }

  let show = false;

  // Función para contar la cantidad de películas por año
  function contarPeliculasPorAno(dataset) {
        const conteoPorAno = {};

        dataset.forEach(show_id => {
            const ano = show_id.release_year;
            if (conteoPorAno[ano]) {
                conteoPorAno[ano]++;
            } else {
                conteoPorAno[ano] = 1;
            }
        });

        return Object.keys(conteoPorAno).map(ano => ({
            year: +ano,
            count: conteoPorAno[ano]
        }));
    }

  // Función para contar la cantidad de películas por año FLOP
  function contarFvS(dataset) {
    const conteoPorAno = {};

    dataset.forEach(film => {
        if (film.Franchise === 'Marvel' && film.Year > "2007") {
            const ano = film.Year;
            const resultado = film.breakeven;
            
            if (!conteoPorAno[ano]) {
                conteoPorAno[ano] = { Flop: 0, Success: 0 };
            }

            if (resultado === 'Flop') {
                conteoPorAno[ano].Flop++;
            } else if (resultado === 'Success') {
                conteoPorAno[ano].Success++;
            }
        }
    });

    return Object.keys(conteoPorAno).map(ano => ({
        year: +ano,
        flopCount: conteoPorAno[ano].Flop,
        successCount: conteoPorAno[ano].Success
    }));

  };

  // Función para contar la cantidad de películas por año teniendo en cuenta el rating
  function calcularRatingPromedioPorAno(dataset) {
    const ratingsPorAno = {};

    dataset.forEach(pelicula => {
        const ano = parseInt(pelicula.Year, 10);
        const rating = parseFloat(pelicula.RottenTomatoesCriticScore);
        

        if (!isNaN(ano) && !isNaN(rating) && (pelicula.Franchise == "Marvel")) { // Filtrar valores no numéricos
            if (ratingsPorAno[ano]) {
                ratingsPorAno[ano].totalRating += rating;
                ratingsPorAno[ano].count++;
            } else {
                ratingsPorAno[ano] = { totalRating: rating, count: 1 };
            }
        }
    });

    return Object.keys(ratingsPorAno).map(ano => ({
        year: +ano,
        averageRating: ratingsPorAno[ano].totalRating / ratingsPorAno[ano].count
        
    }));
  }


  onMount(() => {
    d3.csv("./data/dc_marvel_movie_performance.csv", d3.autoType).then(data => {
      
      peliculas = data
      filteredPeliculas = peliculas
      peliculasYear = calcularRatingPromedioPorAno(peliculas)
      peliculasFlopSucc = contarFvS (peliculas)
      console.log(peliculasFlopSucc)
    })
  })

  onMount(() => {
    d3.csv("./data/disney_plus_titles.csv", d3.autoType).then(data => {
      
      streamShow = contarPeliculasPorAno(data)
      filteredShow = streamShow
      
    })
  })


  $: {
    // Es un observer que se ejecuta cuando cambia el valor de index
    switch (index) {
      case 0:
        filteredPeliculas = peliculas.filter(d => d.Orden < "21" && d.Franchise === "Marvel" )
        break
      case 1:
        filteredPeliculas = peliculas.filter(d => d.Franchise === "DC" && d.Orden <  55)
        break
      case 2:
        filteredPeliculas = peliculas.filter(d => d.Orden <= "71" )
        break
      case 3:
        filteredPeliculas = peliculas.filter(
          d => d.Orden >= "68" && d.Franchise === "Marvel"
        )
        break
      default:
        filteredPeliculas = peliculas
    }
    console.log(filteredPeliculas)
  }

  $: {
    // Es un observer 3
    switch (index3) {
      case 0:
        filteredShow = peliculas.filter(d => d.breakeven === "Flop" &&  d.Year > "2007")
        break
      case 1:
        filteredShow = peliculas.filter(d => d.breakeven === "Flop" &&  d.RottenTomatoesCriticScore > "60")
        break
      default:
      streamShow 
    }
    
  }
</script>

<main>
  <div class="header">
    
    <h3 class="headline">
      <img src="./images/fatigaheroica.png" alt="logo">
      
    </h3>
    <p class="bajada">¿El fin de de los Superhéroes?</p>
    <div class="quote" style="
    margin: 50px auto;
    max-width: 740px;
    
    ">
      <p>Honestamente, a lo que más me recuerdan, pese a lo bien que están producidas, 
        con los actores dando lo mejor de sí dadas las circunstancias, es a los parques de diversiones. 
        No es el cine de humanos tratando de expresar experiencias emocionales y psicológicas a otros humanos</p>
      <p style="
      text-align: right;
      font-family: Satoshi-BoldItalic;"
      >Martin Scorsese</p>
    </div>
    
  </div>
  <div class="lorem_ipsum1" >
    <Text1 />
  </div>


  <!-- {#if progress < 1}
  <DebugScroller
    index={index}
    count={count}
    offset={offset}
    progress={progress}
  />
  {/if} -->

  

  <!-- <div class="flourish-embed flourish-bubble-chart" data-src="visualisation/18441472"><script src="https://public.flourish.studio/resources/embed.js"></script></div> -->

  

  <div class="lorem_ipsum" style="margin-top: 5%;">
    <Text2 />
  </div>

  
  
  <!-- scroller -->
  <Scroller
    top={top}
    threshold={threshold}
    bottom={bottom}
    bind:count={count}
    bind:index={index}
    bind:offset={offset}
    bind:progress={progress}
    
  >
    <div slot="background"  class="background-container" style="background-color: {progress > 0.99999 ? "rgba(18, 3, 29, 0)" : "rgba(18, 3, 29, 0.8)"};padding: 2cap;" transition:fade>
      <LineGraph peliculas={filteredPeliculas} {show} {progress} {index}/>
    </div>
    <div slot="foreground" class="foreground_container2">
      <section class="step_foreground2">
        <div class="epi_foreground2">
          <Text2o />
        </div>
      </section>
      <section class="step_foreground2">
        <div class="epi_foreground2">
          <Text3 />
        </div>
      </section>
      <section class="step_foreground2">
        <div class="epi_foreground2">
          <Text4 />
        </div>
      </section>
      <section class="step_foreground2">
        <div class="epi_foreground2">
          <Text5 />
        </div>
      </section>
    </div>
  </Scroller>

  <div class="lorem_ipsum" style="margin-top: 10%;margin-bottom:5%; z-index:7;">
    <Text6 />
  </div>
  
  <!-- Scroller2 -->
  <Scroller
  top={top3}
  threshold={threshold3}
  bottom={bottom3}
  bind:count={count3}
  bind:index={index3}
  bind:offset={offset3}
  bind:progress={progress3}
  >
  <div slot="background" style="background-color: #12031D;padding: 2cap; ">
    
    <div class="barchart">
      <BarChart {streamShow} {index3}/>
    </div>
  </div>
  <div slot="foreground" class="foreground_container2">
    <section class="step_foreground2">
      <div class="epi_foreground2">
  
        <p class = "epigrafe">Podemos ver que a partir del 2019 (lanzamiento de Disney+), 
          la oferta tanto de largometrajes como 
          TV aumentó drasticamente.</p>
      </div>
    </section>
    <section class="step_foreground2">
      <div class="epi_foreground2">
        
        <p class = "epigrafe">
          Especialmente luego del inicio de la pandemia.</p>
      </div>
    </section>
    
  </div>
  </Scroller>

  <div class="lorem_ipsum"
  style="margin-top: 10%;">
    <Text7 />
  </div>

  <!-- <div style="background-color: #12031D;padding: 2cap; ">
    <LineChart {peliculasFlopSucc}/>
  </div> -->

  <!-- Scroller4 -->
  <Scroller
  top={top4}
  threshold={threshold4}
  bottom={bottom4}
  bind:count={count4}
  bind:index={index4}
  bind:offset={offset4}
  bind:progress={progress4}
  >
  <div slot="background" style="background-color: #12031D;padding: 2cap; ">
    
    <div class="barchart">
      <LineChart {peliculasFlopSucc} {index4}/>
    </div>
  </div>
  <div slot="foreground" class="foreground_container2">
    <section class="step_foreground2">
      
    </section>
    <section class="step_foreground2">
      <div class="epi_foreground2">
        
        <p class = "epigrafe">
          Es interesante ver cómo los niveles Post-Pandemia bajan para parecerse a los previos de la "Era Dorada".</p>
      </div>
    </section>
    
  </div>
  </Scroller>

  
  <div class="lorem_ipsum"
  style="margin-top: 10%;">
    <TextCalidad />
  </div>

  <Scroller
  top={top2}
  threshold={threshold2}
  bottom={bottom2}
  bind:count={count2}
  bind:index={index2}
  bind:offset={offset2}
  bind:progress={progress2}
  >
  <div slot="background" style="background-color: #12031D;padding: 2cap; ">
    
    <div class="barchart">
      <RatingsChart {peliculasYear} {index2}/>
    </div>
  </div>
  <div slot="foreground" class="foreground_container2">
    <section class="step_foreground2">
      
    </section>
    <section class="step_foreground2">
      <div class="epi_foreground2">
        
        <p class = "epigrafe">
          No notamos un cambio significativo post-pandemia. Los puntos bajos del 2020 y 2024 son por la poca cantidad de largometrajes producidos.</p>
      </div>
    </section>
    
  </div>
  </Scroller>

  <div class="lorem_ipsum"
  style="margin-top: 10%;">
    <Conclusion />
  </div>

  <div class="lorem_ipsum1"
  style="margin-top: 5%;">

    <h2 style="fill">Autores</h2>
    <p>Manuel Milde</p>
    <p>Gonzalo García Vence</p>
    <p>Ezequiel Grinblat</p>

  </div>
</main>

<!-- <div class="lorem_ipsum">
  <p style="font-weight: bold;">Al día de hoy, el desempeño de ambas franquicias en la taquilla se ve de la siguiente manera:</p>
  <p>El punto más alto de recaudación de Marvel se da en "Endgame", seguida de "Infinity War", 
    mientras que en DC las más taquilleras fueron "The Dark Knight" y "The Dark Knight Rises".</p>
</div>
<div class="flourish-embed flourish-chart" data-src="visualisation/18441838"><script src="https://public.flourish.studio/resources/embed.js"></script></div>
<div class="lorem_ipsum">
  
  <p>Es interesante cómo las dos películas de DC que más recaudaron, no alcanzan ni siquiera a la cuarta de Marvel: "The Avengers" de 2012. </p>
  <p style="font-style: italic;">Sin embargo, después de "Endgame" los números bajan drásticamente. ¿A qué se debe esto?</p>
</div> -->





<style>
  :global(body){
            background: #12031D;
            background-image: url('./images/PerspectiveGrid.png');
            background-repeat: no-repeat;
            background-attachment: fixed;
            background-size: cover;
        }

  @font-face {
    font-family: 'Satoshi-Light';
    src: url('../fonts/Satoshi-Light.woff2') format('woff2'),
        url('../fonts/Satoshi-Light.woff') format('woff'),
        url('../fonts/Satoshi-Light.ttf') format('truetype');
    font-weight: 300;
    font-display: swap;
    font-style: normal;
  }
  @font-face {
    font-family: 'Satoshi-LightItalic';
    src: url('../fonts/Satoshi-LightItalic.woff2') format('woff2'),
        url('../fonts/Satoshi-LightItalic.woff') format('woff'),
        url('../fonts/Satoshi-LightItalic.ttf') format('truetype');
    font-weight: 300;
    font-display: swap;
    font-style: italic;
  }
  @font-face {
    font-family: 'Satoshi-Regular';
    src: url('../fonts/Satoshi-Regular.woff2') format('woff2'),
        url('../fonts/Satoshi-Regular.woff') format('woff'),
        url('../fonts/Satoshi-Regular.ttf') format('truetype');
    font-weight: 400;
    font-display: swap;
    font-style: normal;
  }
  @font-face {
    font-family: 'Satoshi-Italic';
    src: url('../fonts/Satoshi-Italic.woff2') format('woff2'),
        url('../fonts/Satoshi-Italic.woff') format('woff'),
        url('../fonts/Satoshi-Italic.ttf') format('truetype');
    font-weight: 400;
    font-display: swap;
    font-style: italic;
  }
  @font-face {
    font-family: 'Satoshi-Medium';
    src: url('../fonts/Satoshi-Medium.woff2') format('woff2'),
        url('../fonts/Satoshi-Medium.woff') format('woff'),
        url('../fonts/Satoshi-Medium.ttf') format('truetype');
    font-weight: 500;
    font-display: swap;
    font-style: normal;
  }
  @font-face {
    font-family: 'Satoshi-MediumItalic';
    src: url('../fonts/Satoshi-MediumItalic.woff2') format('woff2'),
        url('../fonts/Satoshi-MediumItalic.woff') format('woff'),
        url('../fonts/Satoshi-MediumItalic.ttf') format('truetype');
    font-weight: 500;
    font-display: swap;
    font-style: italic;
  }
  @font-face {
    font-family: 'Satoshi-Bold';
    src: url('../fonts/Satoshi-Bold.woff2') format('woff2'),
        url('../fonts/Satoshi-Bold.woff') format('woff'),
        url('../fonts/Satoshi-Bold.ttf') format('truetype');
    font-weight: 700;
    font-display: swap;
    font-style: normal;
  }
  @font-face {
    font-family: 'Satoshi-BoldItalic';
    src: url('../fonts/Satoshi-BoldItalic.woff2') format('woff2'),
        url('../fonts/Satoshi-BoldItalic.woff') format('woff'),
        url('../fonts/Satoshi-BoldItalic.ttf') format('truetype');
    font-weight: 700;
    font-display: swap;
    font-style: italic;
  }
  @font-face {
    font-family: 'Satoshi-Black';
    src: url('../fonts/Satoshi-Black.woff2') format('woff2'),
        url('../fonts/Satoshi-Black.woff') format('woff'),
        url('../fonts/Satoshi-Black.ttf') format('truetype');
    font-weight: 900;
    font-display: swap;
    font-style: normal;
  }
  @font-face {
    font-family: 'Satoshi-BlackItalic';
    src: url('../fonts/Satoshi-BlackItalic.woff2') format('woff2'),
        url('../fonts/Satoshi-BlackItalic.woff') format('woff'),
        url('../fonts/Satoshi-BlackItalic.ttf') format('truetype');
    font-weight: 900;
    font-display: swap;
    font-style: italic;
  }
  /**
  * This is a variable font
  * You can control variable axes as shown below:
  * font-variation-settings: wght 900.0;
  *
  * available axes:
  'wght' (range from 300.0 to 900.0
  */
  @font-face {
    font-family: 'Satoshi-Variable';
    src: url('../fonts/Satoshi-Variable.woff2') format('woff2'),
        url('../fonts/Satoshi-Variable.woff') format('woff'),
        url('../fonts/Satoshi-Variable.ttf') format('truetype');
    font-weight: 300 900;
    font-display: swap;
    font-style: normal;
  }
  /**
  * This is a variable font
  * You can control variable axes as shown below:
  * font-variation-settings: wght 900.0;
  *
  * available axes:
  'wght' (range from 300.0 to 900.0
  */
  @font-face {
    font-family: 'Satoshi-VariableItalic';
    src: url('../fonts/Satoshi-VariableItalic.woff2') format('woff2'),
        url('../fonts/Satoshi-VariableItalic.woff') format('woff'),
        url('../fonts/Satoshi-VariableItalic.ttf') format('truetype');
    font-weight: 300 900;
    font-display: swap;
    font-style: italic;
  }

  .header {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin-top: 50px;
    margin-bottom: 80px;
    color:white;
  }
  .headline {
    font-size: 30px;
    line-height: 1.2;
    font-weight: normal;
    text-align: center;
    margin: 20px;
  }
  .bajada {
    font-size: 25px;
    font-weight: normal;
    text-align: center;
    margin: 10px;
    
    font-family: 'Satoshi-Bold';
    color: #73FFF7;
  }
  .quote{
    font-family: "Satoshi-MediumItalic";
    
    box-shadow: 0px 0px 18px #EF33FF;
    padding: 2cap;
    border-style: solid;
    border-color: #EF33FF;
    background-color: #12031D;
    
  }

  .foreground_container2 {
    pointer-events: none;
    padding-left: 0%;
  }
  .step_foreground2 {
    display: flex;
    flex-flow: column;
    justify-content: center;
    align-content: baseline;
    align-items: bottom;
    height: 100vh;
    border: 1px solid rgba(0, 0, 0, 0);
    color: white;
    padding: 1em;
    margin: 0 0 2em 0;
  }
  .epi_foreground2 {
    padding: 20px;
    max-width: 1000px;
    max-height: 200px;
    background-color:rgba(18, 3, 29, 0.8);
    font-family: Satoshi-Regular;
    font-size: 18px;
  }
  .epigrafe{
    color:white;
  }
  .lorem_ipsum {
    margin-bottom: 100px auto;
    margin-top: 100px auto;
    max-width: 1250px;
    font-family: Satoshi-Regular;
    justify-content: center;
    text-align: left;
    margin: auto;
    color:white;
    font-size: 18px;
    background-color: #12031D;
    padding: 2cap;
    border-style: solid;
    border-color: #73FFF7;
    box-shadow: 0px 0px 5px #73FFF7;
    
  }
  .lorem_ipsum1 {
    margin-bottom: 100px auto;
    margin-top: 100px auto;
    max-width: 1250px;
    font-family: Satoshi-Regular;
    justify-content: center;
    text-align: left;
    margin: auto;
    color:white;
    font-size: 18px;
    background-color: #12031D;
    padding: 2cap;
    border-style: solid;
    border-color: #EF33FF;
    box-shadow: 0px 0px 5px #EF33FF;
    
  }
 
  
</style>
