<script>

    import { datSwitch, datStay, datSwitchV, datStayV } from '../stores.js';

    let x = 1;

    let captions = [];
    let doorClicks = [0,0];
    let carPos = Math.floor(Math.random()*3)+1; // car is behind door 1,2 or 3
    let goatPos = [((carPos-1)+1)%3+1,((carPos-1)+2)%3+1];
    let revealPos = null;
    let trialWins = [0,0]; // with switching and with staying
    let trials = [0,0]; // total completed trials with switching and staying

    let gameStep = 1;
    let choices = [1,2,3]

    window.onload = init;

    let door1,door2,door3,caps,capsH

    function init() {
        caps = document.getElementById("innercap")
        console.log("ok")
        door1 = document.getElementById("door1")
        door2 = document.getElementById("door2")
        door3 = document.getElementById("door3")
        removeDummyClasses() 
    }
    
    function door1click() {
        console.log("door1 click")
        if (gameStep === 1) {
            gameStep += 1;
            doorClicks[0]=1;
        } else if (gameStep === 2 && revealPos!=1){
            gameStep += 1;
            doorClicks[1]=1;
        }
    }
    function door2click() {
        console.log("door2 click")
        if (gameStep === 1) {
            gameStep += 1;
            doorClicks[0]=2;
        } else if (gameStep === 2 && revealPos!=2){
            gameStep += 1;
            doorClicks[1]=2;
        }
    }
    function door3click() {
        console.log("door3 click")
        if (gameStep === 1) {
            gameStep += 1;
            doorClicks[0]=3;
        } else if (gameStep === 2 && revealPos!=3){
            gameStep += 1;
            doorClicks[1]=3;
        }
    }

    function step2() {
        // removeDoorChoices()
        console.log("STEP 2 BABY")
        let caps = document.getElementById("innercap")
        let message = ""; // "You picked door " + doorClicks[0] + ". ";
        activateChoice(doorClicks[0])
        if (doorClicks[0] == carPos) {
            // if picked car, reveal random goat
            revealPos = goatPos[Math.floor((Math.random()*2))];
        } else {
            // else, we picked a goat, so reveal the other goat
            revealPos = 6 - carPos - doorClicks[0];
        }
        message += "Now, Door " + revealPos + " has a goat. Do you want to switch or stay??"
        caps.textContent = message;
        console.log(caps)
        removeDoorChoices([revealPos])
        revealGoat(revealPos)
    }

    function revealGoat(revealPos) {
        let elem = document.getElementById("door"+revealPos);
        elem.classList.add("doorFrameGoat")
        let child = elem.children[0];
        child.classList.add("doorGoat");
        console.log(elem)
    }
    
    function revealCar(revealPos){
        let elem = document.getElementById("door"+revealPos);
        elem.classList.add("doorFrameCar")
        let child = elem.children[0];
        child.classList.add("doorCar");
        console.log(elem)
        // TODO add goat image
    }

    function step3() {
        console.log("STEP 3 BABY")
        let message = '';
        activateChoice(doorClicks[1])
        if (doorClicks[1] == doorClicks[0]) {
            message += "You STAYED and picked Door " + doorClicks[1] + ".";
            trials[1] += 1;
        } else {
            message += "You SWITCHED and picked Door " + doorClicks[1] + ".";
            trials[0] += 1;
        }
        let caps = document.getElementById("innercap")
        removeDoorChoices([1,2,3])
        // open door
        if (carPos == doorClicks[1]) {
            message += " Nice! You won the car!"
            revealCar(doorClicks[1])
            if (doorClicks[1] == doorClicks[0]) { // stayed
                trialWins[1] += 1;
            } else { // switched
                trialWins[0] += 1;
            }
        } else {
            message += " Sad... you chose a goat."
            revealGoat(doorClicks[1])
        }
        if (doorClicks[1] == doorClicks[0]) {
            updateGraphStay(trialWins, trials)
        } else {
            updateGraphSwitch(trialWins, trials)
        }
        caps.textContent = message;
        // reveal other goat
        let otherGoat = 6 - doorClicks[1] - revealPos;
        revealGoat(otherGoat);
        revealCar(carPos)
    }

    function updateGraphSwitch(trialWins, trials){
        let switchWinPerc = trialWins[0]/trials[0];
        console.log("switch win perc is " + switchWinPerc)
        datSwitch.update(contents => [...contents, switchWinPerc])
        datSwitchV.update(contents => [trialWins[0], trials[0]])
    }

    function updateGraphStay(trialWins, trials){
        let stayWinPerc = trialWins[1]/trials[1];
        console.log("stay win perc is " + stayWinPerc)
        datStay.update(contents => [...contents, stayWinPerc])
        datStayV.update(contents => [trialWins[1], trials[1]])
    }

    function resetDoors() {
        // upon clicking reset button, 

        // reset global vars. pick new car door and goat doors
        doorClicks = [0,0];
        carPos = Math.floor(Math.random()*3)+1; // car is behind door 1,2 or 3
        goatPos = [((carPos-1)+1)%3+1,((carPos-1)+2)%3+1];
        revealPos = null;
        gameStep = 1;
        choices = [1,2,3];

        // close doors and reset highlighting
        resetDoorClasses();

        // reset caption
        let caps = document.getElementById("innercap");
        caps.textContent = "Behind one door is a car; behind the others, goats. Pick one!";

        console.log("RESET ALL");

    }

    function activateChoice(choice){
        console.log("updating choices")
        // activate this choice and remove all others
        let x = [1,2,3]
        x.forEach(element => {
            let elem = document.getElementById("door"+element);
            elem.classList.remove("doorChosen");
        });
        let elem = document.getElementById("door"+choice);
        elem.classList.add("doorChosen");
    }

    function removeDoorChoices(choices){
        choices.forEach(element => {
            let elem = document.getElementById("door"+element);
            let child = elem.children[0];
            child.classList.remove("doorValidChoice");
        });
    }

    // reactive things! Svelte is cool!
    $: {
        console.log("GAME STEP IS: "+ gameStep)
        if (gameStep === 2) {
            step2()
        } else if (gameStep === 3){
            step3()
        }
    }
    $: console.log(datStay)
    $: console.log(datSwitch)

    function resetDoorClasses() {
        let x = [1,2,3];
        x.forEach(element => {
            let ele = document.getElementById("door"+element)
            ele.classList.remove("doorChosen")
            ele.classList.remove("doorFrameGoat")
            ele.classList.remove("doorFrameCar")
            let child = ele.children[0];
            child.classList.remove("doorChosen")
            child.classList.remove("doorGoat")
            child.classList.remove("doorCar")
            child.classList.remove("doorFrameGoat")
            child.classList.remove("doorFrameCar")
            child.classList.add("doorValidChoice")
        })
    }


    function removeDummyClasses() {
        let ele = document.getElementById("left-knob1")
        ele.classList.remove("doorChosen")
        ele.classList.remove("doorGoat")
        ele.classList.remove("doorCar")
        ele.classList.remove("doorFrameGoat")
        ele.classList.remove("doorFrameCar")
    }

</script>


<div class="wrapper">

    <div id="doorWrapper">

    <div id="door1" class="door_outer" on:click={door1click} on:keydown={()=>{x = x+1}}>
      <div class="door doorValidChoice">
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob1" class="knob doorGoat doorChosen doorCar doorFrameGoat doorFrameCar"></div> 
        <!-- dummy door classes here for proper instantiation  -->
      </div>
    </div>

    <div id="door2" class="door_outer" on:click={door2click} on:keydown={()=>{x = x+1}}>
      <div class="door doorValidChoice">
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob2" class="knob"></div>
      </div>
    </div>

    <div id="door3" class="door_outer" on:click={door3click} on:keydown={()=>{x = x+1}}>
      <div class="door doorValidChoice">
        <div class="shape"></div>
        <div class="shape"></div>
        <div id="left-knob3" class="knob"></div>
      </div>
    </div>

    </div>

    <div id="captions">
        <div class="box sb1">
            <h3 id="innercap"> Behind one door is a car; behind the others, goats. Pick one! </h3>
        </div>
    </div>

</div>

<div class=wrapRest>
    <button class="restartButton" on:click={resetDoors}>
        restart
    </button>
</div>

<style>

button {
    border: 1px black solid;
}

#captions {
    /* background-color: #61412D; */
    width: 100%;
    grid-column: 1 / 4;
    grid-row: 2 / 3;
    justify-self: center;
    align-self: center;
    justify-content: center;
    align-content: center;
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

.shape {
  border: 3px solid #262626;
  width: 80%;
  height: 43%;
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
    grid-row: 1 / 2;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    margin: 2%;
}

.wrapper {
  margin: 40px auto;
  margin-bottom: 5px;
  width: 100%;
  max-width: 850px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  /* grid-template-rows: 1fr 1fr; */
  grid-auto-rows: max-content;
  justify-content: center;
  align-items: center;
  /* gap: 9%; */
  background-color: rgb(211, 180, 176);
  /* border: 4px rgb(177, 44, 29) solid; */
  font-family: 'National 2 Web',Helvetica,Arial,sans-serif;
}

.wrapRest {
  max-width: 850px;
  width: 100%;
  /* background-color: #383737; */
  display: grid;
  margin: 0px auto;
  margin-bottom: 20px;
}

.door_outer {
    background-image: none;
    margin-top: 15px;
    /* height: 130px; */
    width: 65%;
    max-width: 120px;
    height: 40vw;
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

.box {
  /* width: 90%; */
  height: auto;
  background: #262626;
  padding: 1px 10px; 
  text-align: center;
  font-weight: 400;
  color: #ffffff;
  position:relative;
  width: 80%;
  max-width: 600px;
  margin: 1rem auto;
  align-self: center;
  align-items: center;
  align-content: center;
  font-size: 13px;
}

.sb1:before {
  content: "";
  width: 0px;
  height: 0px;
  position: absolute;
  border-left: 10px solid #262626;
  border-right: 10px solid transparent;
  border-top: 10px solid #262626;
  border-bottom: 10px solid transparent;
  right: -20px;
  top: 5px;
}



</style>

  