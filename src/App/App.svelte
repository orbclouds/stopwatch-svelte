<script lang="ts" context="module">
  const DEFAULT_TIME = {
    ms: 0,
    s: 0,
    m: 0,
  };
</script>

<script lang="ts">
  import { GoogleAnalytics } from '@beyonk/svelte-google-analytics';

  import Orb from '@app/Orb';

  let running = false;
  let time = DEFAULT_TIME;
  let interval: ReturnType<
    typeof setInterval
  > | null = null;

  $: isZero =
    time.m === 0 &&
    time.s === 0 &&
    time.ms === 0;

  const clearTime = () => {
    time = DEFAULT_TIME;
  };

  const stopTime = () => {
    running = false;
    if (interval)
      clearInterval(interval);
  };

  const startTime = () => {
    running = true;
    interval = setInterval(() => {
      let { m, s, ms } = time;
      if (m >= 60) {
        stop();
        return;
      }
      ms += 4;
      if (ms >= 1000) {
        s++;
        ms = 0;
      }
      if (s >= 60) {
        m++;
        s = 0;
      }
      time = { m, s, ms };
    }, 4);
  };
</script>

<GoogleAnalytics
  properties={[
    import.meta.env
      .SNOWPACK_PUBLIC_GOOGLE_ANALYTICS_ID,
  ]}
/>

<Orb />

<main>
  <div class="stopwatch">
    <div class="time">
      {('0' + time.m).slice(-2)}:{(
        '0' + time.s
      ).slice(-2)}:{(
        '00' + time.ms
      ).slice(-3)}
    </div>
    <div class="buttons">
      <button
        class="start"
        type="button"
        on:click={startTime}
      >
        start
      </button>
      <button
        class="stop"
        type="button"
        on:click={isZero || running
          ? stopTime
          : clearTime}
      >
        {isZero || running
          ? 'stop'
          : 'clear'}
      </button>
    </div>
  </div>
</main>

<style>
  main {
    width: 90%;
    margin: 64px auto;
    max-width: 600px;
  }
  div.stopwatch {
    height: 400px;
    padding: 64px;
    display: flex;
    min-width: 500px;
    background: #ccc;
    border-radius: 80px;
    flex-direction: column;
    border: solid 16px black;
    justify-content: space-around;
    box-shadow: 8px 8px 16px
      rgba(0, 0, 0, 0.3);
  }
  div.time {
    width: 352px;
    margin: 0 auto;
    font-size: 3rem;
    font-weight: 500;
    padding: 16px 32px;
    background: white;
    text-align: center;
    border-radius: 8px;
    border: solid 8px black;
    font-family: 'courier new';
  }
  div.buttons {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  div.buttons button {
    margin: 0 16px;
    min-width: 152px;
  }

  button {
    opacity: 0.8;
    cursor: pointer;
    font-size: 2rem;
    appearance: none;
    font-weight: 500;
    padding: 8px 32px;
    border-radius: 8px;
    border: solid 4px black;
  }
  button:hover {
    opacity: 1;
  }
  button.start {
    background: green;
  }
  button.stop {
    background: crimson;
  }
</style>
