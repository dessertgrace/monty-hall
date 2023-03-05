<script>

    let x = 0;
    let isChosen = false;
    let isOpen = false;


    function door1click() {
        isChosen = true;
        isOpen = true;
    }

    function resetDoors() {
        isChosen = false;
        isOpen = false;
    }


</script>


<div class="wrapper">

    <div class="probcap">
        <h3> Probability: </h3>
    </div>

    <div class="captions">
        <div class="prob">
            <h2> 1 / 3 </h2>
        </div>

        <div class="prob twoCase reduced" class:reduced={isChosen}>
            <h2> 2 / 3 </h2>
        </div>
    </div>

    <div id="doorWrapper">

    <div id="door1" class="door_outer" class:doorChosen={isChosen} on:click={door1click} on:keydown={()=>{x = x+1}}>
      <div class="door doorValidChoice">
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob1" class="knob"></div> 
        <!-- dummy door classes here for proper instantiation  -->
      </div>
    </div>

    <div id="door2" class="door_outer doorFrameGoat">
      <div class="door" class:doorGoat={isOpen}>
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob2" class="knob"></div>
      </div>
    </div>

    <div id="door3" class="door_outer doorFrameCar">
      <div class="door">
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob3" class="knob"></div>
      </div>
    </div>

    </div>

</div>

<div class=wrapRest>
    <button class="restartButton" on:click={resetDoors}>
        restart
    </button>
</div>

<style>
.prob {
    /* border: 2px black solid; */
    background-color: #9a0000;
    color: white;
    border-radius: 6px;
    width: 70%;
    /* margin: 0px 30px; */
    /* height: 60px; */
    min-width: 90px;
    justify-self: center;
}

h2 {
    font-size: 25px;
    margin: 0px;
}
.shape {
  border: 3px solid #262626;
  width: 80%;
  height: 45%;
}

.probcap {
    grid-row: 1 / 2;
    text-align: left;
    position: relative;
    top: 6px;
    left: 15px;
}

.twoCase {
    grid-column: 2 / 4;
    width: 90%;
    min-width: 230px;
    margin: 0 auto;
    margin-right: 14px;
    justify-self: center;
}
.reduced {
    /* grid-column: 3 / 4; */
    width: 28%;
    min-width: 90px;
    /* min-width: 100px; */
    /* width: 100%; */
    /* margin: 0px 0px; */
    margin-right: 2%; 
    justify-self: end; 
    /* transform-origin: left; */
    transition: width 2s;
    /* transform: scaleX(0.35) */
}

.captions {
    margin: 0px 8%;
    margin-left: 5%;
    /* margin-left: 0px; */
    grid-column: 1 / 4;
    grid-row: 2 / 3;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 12%;
}

.knob {
  top: 38%;
  right: 7px;
  width: 15%;
  height: 20%;
  background-color: #ffffff;
  position: absolute;
}

.door {
  grid-column: 1 / 4;
  background-color: rgb(129, 91, 62);
  height: 100%;
  width: 100%;
  position: absolute;
  /* inner layout */
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  top: 0;
  left: 0;
  /* transition */
  transform-origin: left;
  transition: transform 0.5s;
}

#doorWrapper {
    grid-column: 1 / 4;
    grid-row: 3 / 4;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    margin: 0 0;
    margin-bottom: 65px;
}

.wrapper {
  margin: 0px auto;
  /* margin-bottom: 5px; */
  padding: 0px 0px;
  max-width: 850px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  /* grid-template-rows: 1fr 1fr; */
  grid-auto-rows: 40px 40px 1fr;
  justify-content: center;
  align-items: center;
  gap: 4%;
  background-color: rgb(211, 180, 176);
  /* border: 4px rgb(177, 44, 29) solid; */
  font-family: 'National 2 Web',Helvetica,Arial,sans-serif;
}

.wrapRest {
  max-width: 850px;
  width: 100%;
  /* background-color: #383737; */
  display: grid;
  margin: 10px auto;
  margin-bottom: 20px;
}

button {
    border: 1px black solid;
}

.door_outer {
    background-image: none;
    /* height: 130px; */
    width: 60%;
    max-width: 120px;
    height: 55vw;
    max-height: 300px;
    border: 5px solid #61412D;
    position: relative;
    /* 3D */
    perspective: 1000px;
    justify-self: center;
    align-self: start;
  } 

.doorValidChoice:hover{
  /* transform: rotateY(-110deg); */
  outline: 3px solid #9a0000;
  outline-offset: 6px;
}

.doorFrameGoat{
    background-image: url("/src/assets/goat.jpg") !important;
    /* background-repeat: no-repeat; */
    background-size: contain;
}

.doorFrameCar{
    background-image: url("/src/assets/car.jpg") !important;
    /* background-repeat: no-repeat; */
    background-size: contain;
}

.doorGoat{
    transform: rotateY(-110deg) !important;
}

.doorCar{
    transform: rotateY(-110deg) !important;
}

.doorChosen {
    border: 5px outset #0090ab !important;
}


.restartButton {
    /* grid-column: 3 / 4;
    grid-row: 3 / 4; */
    /* margin: 20px; */
    font-size: 13px;
    justify-self: end;
    border-radius: 0px;
    background-color: #ffffff;
    /* border: 2px rgb(110, 110, 110) outset; */
}

.restartButton:hover{
  outline: 1px solid #9a0000;
}


</style>