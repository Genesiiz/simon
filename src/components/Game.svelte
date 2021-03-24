<script>
  import {getRandomInt} from "@utils/randomize.js";
  import { onMount } from "svelte";
  import gsap from "gsap";

  export let colors = ['red', 'green', 'yellow', 'blue', 'pink', 'orange', 'royalblue', 'purple', 'darkgreen'];

  let buttons;
  let start = false;
  let lvl = 1;
  let lvlColors = [];
  let lvlIndex = 0; // the current turn index lvlColors table
  let ready = false;

  onMount(() => {
    buttons = document.querySelectorAll('[data-color]');
	});

  function reset () {
    start = false;
    lvl = 1;
    lvlColors = [];
    lvlIndex = 0;
    ready = false;
  }

  function incColors () {
    lvlIndex = 0;
    // for (let index = 1; index <= lvl; index++) {
    const colorIndex = getRandomInt(0, (colors.length - 1));
    const randomColor = colors[colorIndex];
    lvlColors = [...lvlColors, randomColor];
    // }
    showColors(0);
  }

  function showColors (counter){
    if(counter < lvlColors.length){
      setTimeout(() => {
        // show current index lvlColors table
        const index = colors.indexOf(lvlColors[counter]); // colors table index
        const button = buttons[index]; // button color
        lightColor(button, index);
        
        counter++;
        showColors(counter);
      }, 1000);
    }
  }
  function lightColor (button, index) {
    gsap.set(button, {
      opacity: 1,
      boxShadow: `0px 0px 0 0 ${colors[index]}`}); 
    gsap.to(button, 0.42, {
      opacity: 1,
      boxShadow: `0px 0px 20px 20px ${colors[index]}`,
      yoyo: true,
      repeat: 1,
      ease: "back.Out"
    });
  }
</script>

<div class="container">
  <h1>Simon Game!</h1>

  <button on:click={() => {
    if (start) {
      reset ();

    } else { // start game
      if (!buttons) return;
      start = true;
      incColors();
    }
  }}>{start ? 'Stop' : 'Play'}</button>

  <p>{lvl}</p>

  <ul class="box row center-xs">
    {#each colors as color, index}
      <li class="col-xs-4
                col-sm-4
                col-lg-2">
        <button
          style="background:{color};"
          data-color="{index}"
          on:click={() => {
            if (colors[index] === lvlColors[lvlIndex]) {
              if ((lvlColors.length - 1) > lvlIndex) { // continue lvl
                lvlIndex++; // inc the index lvl

              } else { // lvl complete go next
                lvl++;
                incColors();
              }
            } else {
              alert('lose');
              reset ();
            }
          }}
        >
          <span><span class="hidden">{index + 1} : </span>{color}</span>
        </button>
      </li>
    {/each}
  </ul>
</div>

<style> /* Styles */
.box {
  list-style: none;
  padding: 0;
  margin: 0;
  max-height: 96vh;
}

.box li {
  list-style: none;
  position: relative;
  margin-bottom: 3px;
}
button[data-color] {
  position: relative;
  width: 100%; height: 0;
  padding-top: 50%;

  border: none;
  cursor: pointer;
  border-radius: 10px;
}
button[data-color] span {
  position: absolute;
  top: 50%; left: 50%;
  transform: translateX(-50%) translateY(-50%);
}
.hidden {
  visibility: hidden;
  opacity: 0;
  text-indent: 999px;
  white-space: nowrap;
  width: 0; height: 0;
}
</style>