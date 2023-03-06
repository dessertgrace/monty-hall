<script>
  let isCorrect = [false,false,false,false,false,false];

function selectWD(evt) {
    const choice = String(evt.target.value).toUpperCase();
    const id = evt.target.id;
    const idN = id.charAt(id.length-1);
    if (choice=="WIN"){
      isCorrect[idN] = true;
    } else {
      isCorrect[idN] = false;
    }
  }

  function selectLD(evt) {
    const choice = String(evt.target.value).toUpperCase();
    const id = evt.target.id;
    const idN = id.charAt(id.length-1);
    if (choice=="LOSE"){
      isCorrect[idN] = true;
    } else {
      isCorrect[idN] = false;
    }
  }

  function revealAll() {
    // select correct value
    const selects = document.querySelectorAll('select');
    selects.forEach( element => {
      if (element.classList.contains("option")) {
        if (element.classList.contains("win")) {
          element.value = "win";
        } else {
          element.value = "lose";
      }
      }
    });
    for (let i=0;i<isCorrect.length;i++){
      isCorrect[i]=true;
    }
  }
</script>


<table>
    <tr>
      <th>Car is behind:</th>
      <th>Goat revealed at:</th>
      <th>Result if SWITCH:</th>
      <th>Result if STAY:</th>
    </tr>
    <tr>
      <td>Door 1</td>
      <td>Door 2 or 3</td>
      <td class="selectCell">
        <select id='select1' class="option lose" on:change={selectLD} class:correct={isCorrect[1]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
      <td class="selectCell">
        <select id='select0' class="option win" on:change={selectWD} class:correct={isCorrect[0]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
      
    </tr>
    <tr>
      <td>Door 2</td>
      <td>Door 3</td>
      <td class="selectCell">
        <select id='select3' class="option win" on:change={selectWD} class:correct={isCorrect[3]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
      <td class="selectCell">
        <select id='select2' class="option lose" on:change={selectLD} class:correct={isCorrect[2]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
      
    </tr>
    <tr>
      <td>Door 3</td>
      <td>Door 2</td>
      <td class="selectCell">
        <select id='select5' class="option win" on:change={selectWD} class:correct={isCorrect[5]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
      <td class="selectCell">
        <select id='select4' class="option lose" on:change={selectLD} class:correct={isCorrect[4]}>
          <option value="select" selected>select</option>
          <option value="win">WIN</option>
          <option value="lose">LOSE</option>
        </select>
      </td>
    </tr>

  </table>

  <div class=buttonWrap on:click={revealAll} on:keypress={revealAll}>
    <button> reveal </button>
  </div>

<style>
  
  button {
    padding: 6px;
    min-width: 100px;
    border: 1px black solid;
    border-radius: 0px;
    font-size: 13px;
    margin-bottom: 20px;
  }

  .buttonWrap {
    max-width: 690px;
    /* background-color: #383737; */
    display: grid;
    margin: 5px auto;
    justify-content: end;
  }

    select {

        appearance: none;
        width: 100%;
        height: 100%;
        font-family: inherit;
        font-size: inherit;
        cursor: pointer;
        background-color: #712e2e;
        color: white;
        min-width: 7ch;
        max-width: 30ch;
        border: 1px white solid;  
        padding: 5px;  
        text-align: center;
    }

  .correct {
    background-color: #0090ab;
  }
  table, th, td {
  border: 1px black solid;
  border-collapse: collapse;
  padding: 1.5ch;
}

  table {
    background-color: #800808;
    color: white;
    /* border: 2px black solid; */
    margin: 5px auto;
    margin-top: 20px;
    /* max-width: 100%; */
    /* max-width: 100px; */
  }

  @media screen and (max-width: 600px) {
  table {
    font-size: 12px;
  }
}



  </style>