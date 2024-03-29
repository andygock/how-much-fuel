<script>
  import { onMount } from "svelte";

  let fuelRate = 0; // litres per lap
  let lapTime = 0; // seconds
  let raceDuration = 0; // minutes

  let lastSelectedInput = null;

  // default user inputs
  const defaults = {
    fuelRate: 3,
    lapTime: 120,
    raceDuration: 60,
  };

  // output calculations
  $: estimatedLaps = (raceDuration * 60) / lapTime;
  $: fuelRequired = estimatedLaps * fuelRate;

  // select all text when user clicks on control, but only if it is a different input
  const handleInputClick = (e) => {
    if (e.target !== lastSelectedInput) {
      e.target.select();
    }
    lastSelectedInput = e.target;
  };

  // save to local storage each time user changes inputs
  const handleInputChange = (e) =>
    localSave({ fuelRate, lapTime, raceDuration: raceDuration });

  // save vars to local storage
  const localSave = (data) => {
    localStorage.howMuchFuel = JSON.stringify(data);
  };

  // load inputs with default values
  const loadDefault = () => {
    ({ fuelRate, lapTime, raceDuration: raceDuration } = defaults);
  };

  // load inputs from localStorage
  const localLoad = () => {
    const saved = localStorage.getItem("howMuchFuel");
    if (saved) {
      try {
        const data = JSON.parse(saved);

        // update state with loaded data
        ({ fuelRate, lapTime, raceDuration: raceDuration } = data);
      } catch (err) {
        // unparsable JSON data in local storage
        localStorage.removeItem("howMuchFuel");
        loadDefault();
      }
    } else {
      loadDefault();
    }
  };

  onMount(() => {
    localLoad();
  });

  const secondsToPrettyString = (secs) => {
    const minutes = String(Math.floor(secs / 60));
    const seconds = String(Math.floor(secs % 60)).padStart(2, "0");
    return `${minutes}:${seconds}`;
  };
</script>

<main>
  <div class="container">
    <h1>How much fuel?</h1>
    <label
      ><input
        type="number"
        min="0"
        max="100"
        bind:value={fuelRate}
        on:click={handleInputClick}
        on:change={handleInputChange}
      /> Fuel rate (L/lap)</label
    >
    <label
      ><input
        type="number"
        min="0"
        max="1000"
        bind:value={lapTime}
        on:click={handleInputClick}
        on:change={handleInputChange}
      />
      Lap time (s) = {secondsToPrettyString(lapTime)}</label
    >
    <label
      ><input
        type="number"
        min="0"
        max="6000"
        bind:value={raceDuration}
        on:click={handleInputClick}
        on:change={handleInputChange}
      /> Race duration (m)</label
    >

    <p>Minimum fuel required: {fuelRequired.toFixed(1)} L</p>
    <p>Estimated laps: {estimatedLaps.toFixed(1)}</p>
    <p>Lap time: {secondsToPrettyString(lapTime)}</p>
    <div class="footer">
      <a href="https://github.com/andygock/how-much-fuel">GitHub</a>
    </div>
  </div>
</main>

<style>
  main {
    font-family: sans-serif;
    color: #333;
    display: flex;
    justify-content: center;
  }
  .container {
    border: solid 1px #ccc;
    padding: 1rem;
    width: 450px;
    background-color: white;
  }
  label {
    display: block;
    padding: 0.3rem;
  }
  input {
    width: 80px;
    padding: 0.3rem;
    font-size: 120%;
  }
  .footer {
    font-size: smaller;
    text-align: center;
  }
</style>
