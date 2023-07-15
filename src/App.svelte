<script>
    import { element } from 'svelte/internal';
    import Concentrated from './lib/Concentrated.svelte';

  // import svelteLogo from './assets/svelte.svg'
  import Counter from './lib/Counter.svelte'
  import GameInteractive from './lib/GameInteractive.svelte';

  import Scatterplot from './lib/ScatterPlot.svelte';
    import Table from './lib/Table.svelte';

  import { datSwitch, datStay, datSwitchV, datStayV} from './stores.js';


  let revealedTable = false;
  let points1 = [];
  let points2 = [];

  let datSwitchIn = [];
  datSwitch.subscribe(value => {
		datSwitchIn = value;
    updatePoints();
	});
  datSwitchIn = [];

  let datStayIn = [];
  datStay.subscribe(value => {
		datStayIn = value;
    updatePoints();
	});

  datStayIn = [];


  let datSwitchVI;
  datSwitchV.subscribe(value => {
		datSwitchVI = value;
	});

  let datStayVI;
  datStayV.subscribe(value => {
		datStayVI = value;
	});

  function updatePoints() {
    points1 = [];
    let count = 1;
    $datSwitch.forEach((element,index) => {
      if (index > 0) {
        points1.push({'x': count, 'y': element})
        count += 1;
      }
    });
    points2 = [];
    count = 1;
    $datStay.forEach((element,index) => {
      if (index > 0) {
        points2.push({'x': count, 'y': element})
        count += 1;
      }
    });
    console.log("Points length is" + points2.length)
  }

  function play1000() {
    console.log("play1000")
    points1 = switch1000(points1);
    console.log(points1)
    datSwitchVI[0] = Math.round(points1[points1.length-1].y*points1.length);
    datSwitchVI[1] = points1.length;

    points2 = stay1000(points2);
    console.log(points2)
    datStayVI[0] = Math.round(points2[points2.length-1].y*points2.length);
    datStayVI[1] = points2.length;
  }

  function stay1000(points2) {
    let count = points2.length+1;
    let successCount = points2[points2.length-1].y*count;
    let cumPerc;
    console.log("OOOOOO")
    console.log(points2.length%50)
    let start = 0;
    while (start != 1 || count%50 != 1) {
        start = 1;
        let outcome = Math.floor(Math.random()*3);
        if (outcome == 0) {
            successCount += 1;
        }
        count += 1;
        cumPerc = successCount/count;
        points2.push({'x': count, 'y': cumPerc})
    }
    return points2;
  } 

  function switch1000(points1) {
    let count = points1.length+1;
    let successCount = points1[points1.length-1].y*count;
    let cumPerc;
    let start = 0;
    while (start != 1 || count%50 != 1) {
      start = 1;
        let outcome = Math.floor(Math.random()*3);
        if (outcome < 2) {
            successCount += 1;
        }
        count += 1;
        cumPerc = successCount/count;
        points1.push({'x': count, 'y': cumPerc})
    }
    return points1;
  }




</script>

<body>

<header>
  <h1> This statistics problem is confusing. </h1>
</header>

<main>

  <div class=subtitle>
    <p style="margin:0px auto"> <strong> The Monty Hall problem </strong>has stumped generations.</p>
    <p style="margin-top:0px">Even if you know the answer, do you understand it?</p>
  </div>

  <GameInteractive/>

  <!-- <h2> {points.length} </h2> -->
  <p  style="margin:0px auto"> For your second choice of door, given the host will always reveal a goat, should we <strong>SWITCH</strong> or <strong>STAY</strong>?</p>
  <!-- <p> Which gives a higher change of winning?</p> -->
  <p  style="margin:0px auto"> Let's try to determine this emperically. Play the game ten times with each choice.</p>
  <!-- <br>  -->

  <div class="chart">  
    <!-- <h1> {points1.length} </h1> -->
      <!-- SWITCH CASE -->
      <Scatterplot points={points1} datCount={datSwitchVI} label="SWITCH"/> 
       <!-- STAY CASE --> 
      <Scatterplot points={points2} datCount={datStayVI} label="STAY"/>
  </div>

  <p  style="margin:0px auto"> In which case do we have a <strong>higher probability</strong> of winning? </p>
  <p  style="margin:0px auto"> What happens if we play the game hundreds of times? </p>

  <br>
  <button on:click={play1000} class="button1000"> Find out! </button>

  <br>

  <br>
  <hr>

  <br>

  <br>

  <p> As you might see, we are about twice as likely to win the car if we switch! </p>
  <p> But why is this true? Probability is just proportions.</p>

  <p  style="margin:0px auto"> Let's look at every possible scenario, <strong>assuming we choose Door 1 initially</strong>.</p>
  <br>

  <Table bind:revealedTable={revealedTable}/>
  <!-- <br> -->

  <p class:hideParag={!revealedTable}> This table shows us that if we switch, we win in 2 of 3 cases, while if we stay, we win in 1 of 3 cases. </p>
  <p class:hideParag={!revealedTable}> Since we assume that each case (each row) is equally likely, this means that there is a <strong>2/3 chance</strong> of winning if we switch, and a <strong>1/3 chance</strong> of winning if we stay.</p>

  <br>
  <br>
  <hr>
  <br>
  <br>
  <p style="margin-bottom:0px"> While the calculation might make sense, </p>
  <h2> how can we develop intuition? </h2>
  <br>
  <p style="margin:5px auto"> This problem has stuck with us for generations because it is so simple yet so easily defies our intuition. </p>
  <br>
  <p style="margin:5px auto"> We approach this problem with the understanding that each door has an equal chance of concealing the car, and this thinking is hard to break away from. But because a door is opened <strong>NOT randomly</strong> (a goat is revealed), the probabilities must be <strong> updated</strong>. The two remaining doors do not have the same probability of concealing the car.</p>
  <br>
  <p style="margin:5px auto"> To understand this problem more intuitively, we can reframe the key question:  </p>
  <p><strong><em>Do you want to keep your initial choice, or choose the other two doors?</em></strong></p>
  <p> When a goat is revealed, the 2/3 probability from the other two doors is concentrated on the third door.</p>

  <br>
  <br>
  <Concentrated/>
  <p> So you should always switch! </p>
  <br>
  <p> If you're still confused (it's okay!), think about what happens if there are 1000 doors. If the host reveals goats behind 998 (all except your initial choice and one other), should you switch or stay?</p>


</main>

</body>

<footer>
  <hr>
  <br>
  <p> <strong> by Grace Dessert </strong> </p>
  <p> <strong> March 2023 </strong> </p>
  <p style="margin: 0px">Check out my <a href="https://github.com/dessertgrace/monty-hall">code on GitHub</a>.</p>
  <br>
  <p> Thanks for engaging with this interactive lesson! </p>
  <br>
  <p> <strong>Learn more: </strong></p>
    <p>
      <li> <a href="https://www.youtube.com/watch?v=iSNsgj1OCLA" style="color: #9a0000">The math problem that stumped thousands of mansplainers, Vox</a> </li>
      <li> <a href="https://www.youtube.com/watch?v=ugbWqWCcxrg" style="color: #9a0000">A more-mathematical explanation using conditional probability and Bayes' Theorem, Numberphile2</a> </li>
    </p>
</footer>

<style>

  .hideParag {
    display:none;
  }


  em {
    line-height: 1.8em;
  }
  footer {
    padding: 1.5em;
    text-align: start;
    max-width: 900px;
    margin-bottom: 30px;
  }

  hr {
    max-width: 950px;
    margin: 10px auto;
  }
  h2 {
    font-size: 35px;
  }

  p {
    max-width: 850px;
    margin: 1em auto;
  }

.button1000 {
  width: 200px;
  background-color: white;
  border: 1px black solid;
  display: grid;
  margin: 15px auto;
  padding: 10px;
}

body {
  display: block;
  box-sizing: border-box;
  /* margin: 0; */
  /* width: 100%; */
  /* height: 3030px; */
}

:global(#app){
  padding: 0;
}

:global(body) {
		background-color: #f2eee2;
		color: #9a0000;
    width: 100%;
    height: 100%;
    font-size: 18px;
	}

/* If the screen size is 600px wide or less, set the font-size of <div> to 30px */
@media screen and (max-width: 600px) {
  body {
    font-size: 16px;
  }
}


/* @media only screen and (max-width: 1400px) */

.chart {
  padding-top: 40px;
  padding-bottom: 10px;
  display: flex;
  flex-wrap: wrap;
  max-width: 900px;
  margin: 0 auto;
  /* height: calc(100% - 4em);  */
  min-height: 280px;
  /* max-height: 480px; */
  gap: 5px;
}


* {
  margin: 0px;
  padding: 0px;
}

header {
  margin: 30px 0px;
  margin-bottom: 10px;
  background-color: #f2eee2;
  color: #9a0000;
  padding: 1.5em;
  /* margin: 50px; */
}
.card{
  margin-top: 60px;
}

main {
    text-align: center;
    padding: 1.5em;
    padding-top: 0.5em;
    /* max-width: 240px; */
    margin: 0 auto;
  }

#app {
  padding: 0;
}

.subtitle {
  width: 80%;
  margin: 0 auto;
}


</style>
