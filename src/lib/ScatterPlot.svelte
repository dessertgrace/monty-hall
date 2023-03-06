<script>
	import { scaleLinear } from 'd3-scale';
	import { tweened } from 'svelte/motion'
	import * as d3 from 'd3-interpolate'
	import { circInOut as easing } from 'svelte/easing'

    export let points;
	export let datCount;
	export let label;

	const yTicks = [0, 0.5, 1];
	let xTicks = [1, 2, 3, 4, 5, 10,20,100,250,500,750,1000];
	const padding = { top: 20, right: 15, bottom: 20, left: 25 };

    let svg;
	let width = 300;
	let height = 200;

	// let pointsPlot = points;// points;

	$: { console.log(points)
		console.log("iii")
		if (points.length==0) {
			points.push({'x': 0, 'y': 0})
			console.log(points)
		}
		// pointsPlot.splice(0,1);
		// console.log("pointsPlot")
		// console.log(pointsPlot) 
	}

	let animatedPoints = tweened(points, {
		interpolate: d3.interpolateArray,
		duration: 700,
		easing
	})

	$: {
		animatedPoints.set(points)
		// if (points.length > 20) {
		// 	let elem = document.querySelectorAll("circle");
		// 	elem.forEach(element => {
		// 	element.classList.remove('invisible')
		// 	console.log("ele")
		// });
		// }
	}

	// ?let elem = document.querySelectorAll("circle");
	// 		elem.forEach(element => {
	// 		element.classList.add('invisible')
	// 		console.log("ele")

	$: xScale = scaleLinear()
		.domain([minX, maxX])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([Math.min.apply(null, yTicks), Math.max.apply(null, yTicks)])
		.range([height - padding.bottom, padding.top]);
	
	let minX = 1;
	let maxX = 5;
	$: if (points[points.length - 1].x < 5){
		maxX = 5 ;
	} else {
		maxX = points[points.length - 1].x ;
	}

	$: currY = points[points.length - 1].y;

	// $: if (points[0].x >= 1) {
	// 	let elem = document.querySelectorAll("circle");
	// 	elem.forEach(element => {
	// 		element.classList.remove('invisible')
	// 		console.log("ele")
	// 	})
	// }
	// $: {
	// 	if (minX == maxX) {
	// 		xTicks = [0]	
	// 	} //else {
	// 		// xTicks.forEach((element,index) => {
	// 		// if ((element > maxX) || (element < minX)) {
	// 		// 	console.log(element)
	// 		// 	xTicks.splice(index,1)
	// 		// }
	// 	//})
	// 	//}
	// }
    
	$: path = `M${$animatedPoints.map(p => `${xScale(p.x)},${yScale(p.y)}`).join('L')}`;
	$: area = `${path}L${xScale($animatedPoints[$animatedPoints.length - 1].x)},${yScale(0)}L${xScale(minX)},${yScale(0)}Z`;

</script>

<!-- <svelte:window on:resize='{resize}'/> -->

<div class="wrapper">

	<div class="profile profile1">
		<div class="logo">

		</div>
		<div class="graphText"> 
			<strong> {label} Percent Success</strong> 
			<br>
			{datCount[0]} of {datCount[1]}  ({Math.round(currY*1000)/10}%)

			
		</div>

	</div>

	<div class="chart" bind:clientWidth={width} bind:clientHeight={height}>
		<svg>
			<!-- y axis -->
			<g class="axis y-axis" transform="translate(0, {padding.top})">
				{#each yTicks as tick}
					<g class="tick tick-{tick}" transform="translate(0, {yScale(tick) - padding.bottom})">
						<line x2="100%"></line>
						<text y="-4">{tick} {tick === 100 ? ' percent success' : ''}</text>
					</g>
				{/each}
			</g>

			<!-- x axis -->
			<g class="axis x-axis">
				{#each xTicks as tick}
					<g class="tick tick-{ tick }" transform="translate({xScale(tick)},{height})">
						<line y1="-{height}" y2="-{padding.bottom}" x1="0" x2="0"></line>
						<text y="-2">{tick}</text>
					</g>
				{/each}
			</g>

			<!-- data -->
			<path class="path-area" d={area}></path>
			<path class="path-line" d={path}></path>
			<!-- add scatter points -->
			{#each points as p}
				<circle class="" cx='{xScale(p.x)}' cy='{yScale(p.y)}' r='2'/>
			{/each}
		</svg>

	</div>

</div>

<style>

	.graphText {
		position: absolute;
		/* width: calc(100% - 0px); */
		/* width: 200px; */
		/* width: 100%; */
		top: 0px;
		left: 77px;
		margin: 0 0;
	}

	.profile {
		text-align: left;
		position: absolute;
		top: 10px;
		left: 10px;
		line-height: 1.5em;
		width: 95%;
	}

	.logo {
		background-size: 120% auto;
		background-repeat: no-repeat;
		background-position: 30% 2px;
		width: 65px;
		height: 50px;
		border: 1px black solid;
		background-color: #dadada;
	}

	.wrapper {
		margin: 0 auto;
		position: relative;
		box-sizing: border-box;
		vertical-align: baseline;
		/* display: flex; */
		width: 100%;
		max-width: 360px;
		/* grid-row: 1 / 2; */
		padding: 70px 5px 5px;
		border: 1px black solid;
		background-color: white;
		height: 238px;
	}

	.chart {
		width: 90%;
		/* height: 90% */
		height: 150px;
		/* max-width: 90%; */
		margin: 5px auto;
		vertical-align: baseline;
	}

	svg {
		position: relative;
		width: 100%;
		height: 170px;
		overflow: visible;
	}

	.tick {
		font-size: .725em;
		font-weight: 200;
	}

	.tick line {
		stroke: #aaa;
		stroke-dasharray: 2;
	}

	.tick text {
		fill: #666;
		text-anchor: start;
	}

	.tick.tick-0 line {
		stroke-dasharray: 0;
		background-color: white;
	}

	.x-axis .tick text {
		text-anchor: middle;
	}

	circle {
		fill: #262626;
		fill-opacity: 0.3;
		stroke: #262626;
	}

	.invisible {
		fill: red !important;
		fill-opacity: 0 !important;
		stroke-opacity: 0 !important;
	}

	.path-line {
		fill: none;
		stroke: #9a0000;
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 2;
	}

	.path-area {
		fill: rgba(211, 180, 176, 0.451);
	}
</style>