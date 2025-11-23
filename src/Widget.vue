<template>
  <!-- Reject IE9 entirely (better to display nothing than something broken) -->
  <div v-if="!($browserDetect.isIE && $browserDetect.meta.version < 10)">
    <!-- Main Widget -->
    <div class="cleanslate">
      <ccw-w :class="[flatten ? 'flatten' : '', lifeline]" :id="`ccw-container-${_uid}`" :size="size" :dark="dark"
        @click="handleClick">
        <ccw-flexwrap>
          <ccw-panel deadline>
            <ccw-div>
              <ccw-span>DATE LIMITE</ccw-span>
              <ccw-span>TEMPS RESTANT POUR LIMITER LE RÉCHAUFFEMENT CLIMATIQUE À 1,5 °C</ccw-span>
            </ccw-div>
            <ccw-readout>
              {{ remaining.years }}<ccw-span>ANS</ccw-span>{{ pad(remaining.days, 3) }}<ccw-span>JOURS</ccw-span>{{
                pad(remaining.hours, 2) }}<ccw-span>:</ccw-span>{{ pad(remaining.minutes, 2) }}<ccw-span>:</ccw-span>{{
                pad(remaining.seconds, 2) }}
            </ccw-readout>
          </ccw-panel>
        </ccw-flexwrap>
      </ccw-w>
    </div>

    <!-- Chart portion (component is lazy loaded) -->
    <transition name="slide">
      <ccw-div v-if="showChart">
        <ccw-flatten-header :size="size" v-if="flatten">
          <div>
            #FlattenThe<span>Climate</span>Curve
            <img v-if="/xl|lg/.test(size)" svg-inline src="./flatten_logo.svg" />
          </div>
          <div>
            <span v-if="/xl|lg/.test(size)">A project of</span>
            <a href="https://climateclock.world" target="_blank">
              <img svg-inline src="./climateclock_logo.svg" />
            </a>
          </div>
        </ccw-flatten-header>

        <ccw-chart-wrapper :class="{ flatten: flatten }" id="ccw-chart-wrapper" :size="size">
          <ccw-div>
            <ccw-chart :height="650" @newK="k = $event" :factorA="parseInt(A)" :factorB="parseInt(B)"
              :size="size"></ccw-chart>
          </ccw-div>
          <ccw-control-panel id="ccw-slider">
            <ccw-div>SIZE OF INVESTMENT</ccw-div>
            <ccw-slider>
              <input type="range" v-model="A" min="1" max="5" />
              <ccw-div>
                <ccw-span v-for="(v, k) in action" :key="k">{{ v }}</ccw-span>
              </ccw-div>
            </ccw-slider>
            <ccw-div>SPEED OF ACTION</ccw-div>
            <ccw-slider>
              <input type="range" v-model="B" min="1" max="5" />
              <ccw-div>
                <ccw-span v-for="(v, k) in investment" :key="k">{{ v }}</ccw-span>
              </ccw-div>
            </ccw-slider>
            <ccw-hr>Scenario</ccw-hr>
            <ccw-scenario>
              <ccw-div>
                <ccw-radio @click="setPreset('bad')">
                  <img svg-inline v-if="preset == 'bad'" src="./checked.svg" />
                  <img svg-inline v-else src="./unchecked.svg" />
                  <ccw-span class="ccw-bad" @click="setPreset('bad')">BUSINESS AS USUAL</ccw-span>
                </ccw-radio>
                <ccw-radio @click="setPreset('middle')">
                  <img svg-inline v-if="preset == 'middle'" src="./checked.svg" />
                  <img svg-inline v-else src="./unchecked.svg" />
                  <ccw-span class="ccw-middle" @click="setPreset('middle')">"MIDDLE GROUND"</ccw-span>
                </ccw-radio>
                <ccw-radio @click="setPreset('good')">
                  <img svg-inline v-if="preset == 'good'" src="./checked.svg" />
                  <img svg-inline v-else src="./unchecked.svg" />
                  <ccw-span class="ccw-good" @click="setPreset('good')">GREEN NEW DEAL</ccw-span>
                </ccw-radio>
              </ccw-div>

              <ccw-div class="ccw-text">
                <ccw-span>With the level of climate action you chose (<ccw-span>{{ action[A] }}</ccw-span> investment;
                  with
                  <ccw-span>{{ investment[B] }}</ccw-span> speed of action), the model suggests that
                  {{ scenarios[preset] }}. If we shift our priorities now, we can change the future.</ccw-span>
                <ccw-span v-if="!flatten">Model derived from peer-reviewed science, including:
                  <a href="https://www.ipcc.ch/sr15/chapter/spm/" target="_blank">IPCC 2018 special report on the
                    impacts of global warming of 1.5 °C</a>; and “Emissions – the ‘business as usual’ story is
                  misleading” in
                  <a href="https://www.nature.com/articles/d41586-020-00177-3" target="_blank"><i>Nature</i>, Issue
                    577</a>, 618-620 (2020); Zeke Hausfather & Glen P. Peters.</ccw-span>
              </ccw-div>
            </ccw-scenario>
          </ccw-control-panel>
        </ccw-chart-wrapper>

        <ccw-flatten-footer v-if="flatten">
          <p>
            Conceptual model derived from peer-reviewed science, including:
            <a href="https://www.ipcc.ch/sr15/chapter/spm/" target="_blank">IPCC 2018 special report on the impacts of
              global warming of 1.5 °C</a>; and “Emissions – the ‘business as usual’ story is misleading” in
            <a href="https://www.nature.com/articles/d41586-020-00177-3" target="_blank"><i>Nature</i>, Issue 577</a>,
            618-620 (2020); Zeke Hausfather & Glen P. Peters. Curves are smoothed to reflect broad trends in available
            data.
          </p>
          <p>
            This is a beta version of #FlattenTheClimateCurve ©2020. Tool designed by Gan Golan and Andrew Boyd;
            programming by Adrian Carpentər; video-explainer by Alex Cequea; science advising by Richard Heinberg
            (Senior Policy Analyst, Post-Carbon Institute) and Bill Becker (Executive Director, Presidential Climate
            Action Project); a project of
            <a href="https://climateclock.world" target="_blank">ClimateClock.world</a> and
            <a href="https://beautifultrouble.org" target="_blank">Beautiful Trouble</a>.
          </p>
          <p>This tool is free and available to the public under a creative commons license.</p>
          <iframe src="https://drive.google.com/file/d/1uXboC4JZHDLWqNE4zmGmmeGq7AoCbBjF/preview" width="400"
            height="225"></iframe>
        </ccw-flatten-footer>
      </ccw-div>
    </transition>
  </div>
</template>

<script>
import debounce from "lodash.debounce"
import { DateTime, Settings } from "luxon"



Settings.defaultZone = "utc"

export default {
  props: {
    bottom: { type: Boolean, default: false },
    flatten: { type: Boolean, default: false },
  },
  components: {
    // Lazy-load this component
    "ccw-chart": () => import(/* webpackChunkName: "flatten" */ "./Chart.vue"),
  },
  data: () => ({
    // Component loading
    ready: false,

    // All clock action is invoked by changing this.now in an interval function
    now: null,
    deadline: null,



    // Modules
    carbon: {},

    // Chart
    A: 2,
    B: 2,
    k: 0,
    preset: "bad",
    action: { 1: "zero", 2: "low", 3: "medium", 4: "serious", 5: "maximum" },
    investment: { 1: "zero", 2: "small", 3: "medium", 4: "high", 5: "maximum" },
    showChart: false,
    scenarios: {
      good: "average global surface temperature could skirt just under 1.5°C around 2040 and level off for the rest of the century, avoiding the worst climate impacts, and preserving a habitable planet for future generations.",
      middle:
        "average global surface temperature would likely reach ~2°C by 2100 with devastating (and permanent) impacts on humanity and the biosphere, including: floods, droughts, mass extinctions, 100s of millions of climate refugees, and millions dead. Crossing 1.5°C, we also risk triggering a series of catastrophic feedback loops that could spiral beyond our ability to ever remedy.",
      bad: "average global surface temperature would likely reach 3-4°C by 2100 with catastrophic (and permanent) impacts on humanity and the biosphere, including: floods, droughts, mass extinctions, permanently uninhabitable regions, billions of climate refugees, and 100s of millions dead. Civilization as we know it will no longer be possible.",
    },

    // To become a prop when the mockup is done
    dark: false,

    // Items below are skin/theme-specific (TODO: settle on defaults for all skins/themes)
    // Ascending sizes work like breakpoints, adding an html attribute to the container
    size: "ultra-stretch",
    sizes: [
      [0, "ultra-stretch"],
    ],
    lastSize: 0,
  }),
  computed: {
    remaining() {
      if (!this.deadline) return {}
      return this.deadline.diff(DateTime.fromJSDate(this.now), ["years", "days", "hours", "minutes", "seconds"])
    },




    // Chart thing
    scenarioText() {
      return this.scenarios[this.preset]
    },
  },
  methods: {
    setSize() {
      // Always use ultra-stretch mode for signage display
      let chartShouldBeShown = this.showChart
      this.size = "ultra-stretch"
      this.ready = true
      this.showChart = chartShouldBeShown
    },
    // Items below are skin/theme-specific
    pad(n, length = 2) {
      return `${"0".repeat(length)}${Math.trunc(n)}`.slice(-length)
    },
    plural(n, pre, suf) {
      return n == 0 || n > 1 ? `${n} ${pre + suf}` : `${n} ${pre}`
    },
    handleClick() {
      if (this.lifeline == "cleancreatives") {
        //window.open("https://cleancreatives.org")
      } else if (!window.location.hostname.includes("climateclock.world")) {
        //window.open("https://climateclock.world")
      } else {
        this.showChart = !this.showChart
      }
    },
    updatePreset(preset) {
      this.preset = preset
    },
    setPreset(preset) {
      switch (preset) {
        case "bad":
          this.A = 2
          this.B = 2
          break
        case "middle":
          this.A = 3
          this.B = 3
          break
        case "good":
          this.A = 5
          this.B = 5
          break
      }
      this.preset = preset
    },
  },
  created() {
    this.$http
      .get("https://api.climateclock.world/v2/widget/clock.json")
      .then((res) => {
        let modules = res.data.data.modules
        this.carbon = modules.carbon_deadline_1
        this.deadline = DateTime.fromISO(this.carbon.timestamp)
      })
      .catch((err) => {
        // eslint-disable-next-line
        console.log(err)
      })

    // Watch for container size changes and update sizing classes
    let resizeInterval = 100,
      tickInterval = 250
    if (this.$browserDetect.isEdge) {
      // Slow down for the special browser
      resizeInterval = 250
      tickInterval = 997
    }
    window.addEventListener("load", this.setSize)
    window.addEventListener("resize", this.resizeInterval ? debounce(this.setSize, resizeInterval) : this.setSize)
    setInterval(() => {
      this.now = new Date()
    }, tickInterval)

    // Removed lifeline cycling since we only show the deadline now

    if (this.flatten || new URLSearchParams(window.location.search).has("flatten")) {
      this.showChart = true
    }
  },
  watch: {
    k(newK) {
      this.preset = newK < 20 ? "good" : newK < 60 ? "middle" : "bad"
    },
    ready() {
      if (!this.bottom || window.climateClockWidgetPaddingAdded) return
      window.climateClockWidgetPaddingAdded = true
      this.$nextTick(() => {
        let pb = window.getComputedStyle(document.body).getPropertyValue("padding-bottom"),
          ch = document.getElementById(`ccw-container-${this._uid}`).clientHeight
        document.body.style.paddingBottom = `calc(${pb} + ${ch}px)`
      })
    },
  },
}
</script>

<style lang="scss">


@import "cleanslate";
@import "matthewha";

$accent: #eb1c23;
$accentDark: #940000;
$secondary: #4aa1cc;
$secondaryDark: #1362a1;

@mixin glow($color) {
  font-weight: 400;
  color: lighten($color, 20%);
  text-shadow: 0.5px 0.5px 0.1em $color, -0.5px -0.5px 0.1em $color, -0.5px 0.5px 0.1em $color,
    0.5px -0.5px 0.1em $color;
}

// <ccw-w>
ccw-fixed {
  // For "fixed" prop ;not yet used
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  box-shadow: 0 20px 30px rgba(black, 50%);
}

ccw-div {
  display: block;
}

ccw-span {
  display: inline-block;
}

// An arbitrary measure which nevertheless gets used a lot (16 * 7 = 112)
//$cubit: 7rem;
$cubit: 112px;

ccw-w {
  cursor: pointer;
  user-select: none;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
  display: flex;
  flex-direction: row-reverse;
  justify-content: space-between;
  font-weight: normal;
  font-size: 28px;
  position: relative;
  width: 3840px;
  height: 600px;
  white-space: nowrap;
  overflow: hidden;

  &.flatten,
  &[size="xl"].flatten,
  &[size="lg"].flatten,
  &[size="md"].flatten,
  &[size="sm"].flatten,
  &[size="xs"].flatten {
    height: 1px;
    opacity: 0;
  }

  *,
  *:before,
  *:after {
    box-sizing: border-box;
    font-family: "katwijk_monoblack", "Lucida Console", Monaco, monospace;
  }

  // Removed height settings - now fixed at 600px above
}

// Custom classes: Clean Creatives
ccw-w.cleancreatives {
  $cltext: #d7d7d7;

  ccw-brand {
    display: none;
  }

  ccw-panel[lifeline] {
    background-image: url("https://climateclock.world/s/lifeline_cleancreatives.jpg");
  }

  ccw-panel[deadline] {
    background-image: url("https://climateclock.world/s/deadline_cleancreatives.jpg");
  }

  ccw-readout {
    color: $cltext;
  }

  ccw-panel[lifeline],
  ccw-panel[deadline] {
    background-position: center;
    background-size: cover;

    >ccw-div {
      background: black;
      color: white;

      ccw-span:first-of-type {
        background: black;
        color: white;
      }
    }

    ccw-span:first-of-type {
      background: none;
      color: $cltext;
    }
  }

  ccw-ticker {
    color: white;
  }
}

ccw-flexwrap {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  position: relative; // <ccw-ticker> needs this, yeah?
  width: 100%;

  flex: 10 0 0;
}

// Used in deadline/lifeline headings and ticker
$txtPad: 5px;

ccw-panel {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  color: black;
  letter-spacing: -1px;

  flex: 1 0 100%; // Take full width since we removed the lifeline panel
  overflow: hidden;
  height: 600px;

  ccw-span {
    padding: $txtPad $txtPad * 5;
  }

  >ccw-div>ccw-span:nth-of-type(1) {
    font-size: 120px;
  }

  >ccw-div>ccw-span:nth-of-type(2) {
    font-size: 100px;
  }

  ccw-span {
    padding: $txtPad $txtPad * 2;
    text-transform: uppercase;
  }

  >ccw-div>ccw-span:nth-of-type(2) {
    font-family: "katwijk_monolight", "Lucida Console", Monaco, monospace;
    font-weight: bold;
  }

  &[deadline] {
    background: $accent;

    ccw-div {
      background: black;
      color: $accent;
    }

    ccw-span:first-of-type {
      background: $accent;
      color: black;
    }

    ccw-w[dark] & {
      color: $accent;
      background: $accentDark;

      ccw-span:first-of-type {
        background: $accentDark;
        color: $accent;
      }
    }


  }


}

$ccwFont: 70px;

ccw-readout {
  flex: 2 0 0;
  font-size: 400px;
  letter-spacing: -2px;
  line-height: 1.0;
  text-align: left;
  margin: 0 100px;
  position: relative;
  overflow: hidden;

  ccw-span {
    // Smaller labels
    line-height: 1;
    margin-bottom: -40px;
    margin-right: 5px;
    font-size: 160px;
    padding: 0;
    background: transparent;
  }
}

ccw-ticker {
  position: relative;
  height: 32px;
  text-transform: uppercase;
  text-align: left;
  overflow: hidden;
  background: black;
  color: $secondary;
  flex: 2 0 100%;

  ccw-w[size="md"] & {
    flex: 1 0 100%; // also ccw-panel
  }

  ccw-w[size="sm"] & {
    flex: 1 0 100%; // also ccw-panel
  }

  ccw-w[size="xs"] & {
    flex: 1 0 100%; // also ccw-panel
  }

  ccw-div {
    font-family: "katwijk_monolight", "Lucida Console", Monaco, monospace;
    font-weight: bold;
    position: absolute;
    top: 1px;

    ccw-w[size="lg"] & {
      top: 3px;
    }

    ccw-w[size="md"] & {
      top: 2px;
    }

    ccw-w[size="sm"] & {
      top: 4px;
    }

    ccw-w[size="xs"] & {
      top: 7px;
    }

    animation-timing-function: linear;
    animation-iteration-count: infinite;
    padding: 2.4px 8px;

    &[one] {
      animation-name: widget-feed-one;
    }

    &[two] {
      animation-name: widget-feed-two;
    }
  }

  @keyframes widget-feed-one {
    0% {
      transform: translate(0, 0);
    }

    100% {
      transform: translate(-100%, 0);
    }
  }

  @keyframes widget-feed-two {
    0% {
      transform: translate(100%, 0);
    }

    100% {
      transform: translate(0%, 0);
    }
  }
}

ccw-brand {
  line-height: 0.85;
  width: 128px;
  background: black;
  color: $secondary;
  font-size: 14px;

  display: flex;
  justify-content: space-around;
  flex-direction: column;
  align-items: center;

  ccw-span {
    font-family: "katwijk_monolight", "Lucida Console", Monaco, monospace;
    font-weight: bold;
  }

  ccw-w[size="lg"] & {
    width: $cubit - 8px;
  }

  ccw-w[size="md"] & {
    width: $cubit - 16px;
    flex-direction: column;
  }

  ccw-w[size="sm"] &,
  ccw-w[size="xs"] & {
    display: none;
  }

  svg {
    outline: none;
    display: block;
  }

  svg[logo] {
    max-height: 60%;
    max-width: 90%;
    padding-top: 8px;
  }
}

#ccw-chart-wrapper {
  // Use id to increase specificity over cleanslate
  box-sizing: border-box;
  overflow: hidden;
  font-family: "katwijk_monobold", "Lucida Console", Monaco, monospace;
  font-weight: bold;
  text-align: left;
  border-bottom: 16px solid black;
  box-shadow: 0 10px 80px rgba(black, 0.1) inset;
  background: white;
  position: relative;

  display: flex;
  flex-direction: row;
  justify-content: flex-end;

  &.flatten {
    border: none;
  }

  &[size="md"],
  &[size="sm"],
  &[size="xs"] {
    flex-direction: column;
  }

  canvas {
    // Allows the text to go behind the graph
    background: rgba(0, 0, 0, 0);
    position: relative;
  }

  a:link,
  a:visited,
  a:hover {
    color: #009dff;
    font-weight: bold;
    text-decoration: none;
  }

  >ccw-div {
    flex: 4 0 0;
    position: relative;
  }

  ccw-control-panel {
    flex: 3 0 0;
    display: block;
    padding: 16px 48px 32px 0;

    >ccw-div {
      font-family: "katwijk_monoblack", "Lucida Console", Monaco, monospace;
      font-weight: normal;
      font-size: 22px;
      text-align: center;
    }

    ccw-slider {
      margin-bottom: 16px;
      display: block;

      input[type="range"] {
        appearance: none;
        width: 100%;
        height: 16px;
        outline: none;
        margin: 16px 0;
        cursor: pointer;
        background: #bd8760;
        background: linear-gradient(90deg,
            rgba(241, 101, 33, 1) 4%,
            rgba(255, 255, 255, 0) 4.1%,
            rgba(255, 255, 255, 0) 4.9%,
            rgba(255, 0, 0, 1) 5%,
            rgba(255, 0, 0, 1) 36%,
            rgba(255, 255, 255, 0) 36.1%,
            rgba(255, 255, 255, 0) 36.9%,
            rgba(189, 135, 96, 1) 37%,
            rgba(189, 135, 96, 1) 68%,
            rgba(255, 255, 255, 0) 68.1%,
            rgba(255, 255, 255, 0) 68.9%,
            rgba(0, 221, 114, 1) 69%);

        &::-webkit-slider-thumb {
          appearance: none;
          width: 24px;
          height: 48px;
          background: white;
          border: 2px solid black;
          cursor: pointer;
        }

        &::-moz-range-thumb {
          border-radius: 0;
          width: 24px;
          height: 48px;
          background: white;
          border: 2px solid black;
          cursor: pointer;
        }
      }

      ccw-div {
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        text-align: center;
        margin: 0 -10%;
        font-size: 14px;

        ccw-span {
          width: 20%;
        }
      }
    }
  }

  ccw-scenario {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;

    ccw-div:nth-of-type(1) {
      padding: 0 32px 16px 0;
      flex: 1 0 auto;
    }

    ccw-div:nth-of-type(2) {
      flex: 1 0 50%;
    }

    ccw-div {
      display: block;
    }

    .ccw-text ccw-span {
      font-family: Arial, Helvetica, sans-serif;
      font-weight: normal;
      font-size: 14px;
      margin-bottom: 16px;

      ccw-span {
        font-weight: bold;
        font-size: 16px;
        margin: 0;
      }
    }
  }

  ccw-radio {
    cursor: pointer;
    font-size: 20px;
    display: block;
    margin: 8px 0;

    svg {
      max-width: 20px;
      max-height: 20px;
      margin-right: 8px;
    }
  }

  ccw-hr {
    text-transform: uppercase;
    font-family: "katwijk_monoblack", "Lucida Console", Monaco, monospace;
    font-weight: normal;
    font-size: 20px;
    display: flex;
    align-items: center;
    margin: 32px 0 16px 0;

    &::after {
      content: "";
      flex: 1 0 0;
      margin-left: 16px;
      border-bottom: 1px solid #666;
    }
  }

  &[size="md"],
  &[size="sm"],
  &[size="xs"] {
    ccw-control-panel {
      padding: 32px 48px;
    }
  }
}

.ccw-good {
  color: $secondary;
}

.ccw-middle {
  color: #bd8760;
}

.ccw-bad {
  color: $accent;
}

.slide-enter-active {
  transition-duration: 0.2s;
}

.slide-leave-active {
  transition-duration: 0.2s;
}

.slide-enter-to,
.slide-leave {
  max-height: 778px;
  overflow: hidden;
  opacity: 1;
}

ccw-chart-wrapper[size="xs"],
ccw-chart-wrapper[size="sm"],
ccw-chart-wrapper[size="md"] {

  &.slide-enter-to,
  &.slide-leave {
    max-height: 1600px; // slide height
    overflow: hidden;
    opacity: 1;
  }
}

.slide-enter,
.slide-leave-to {
  overflow: hidden;
  max-height: 0;
  opacity: 0;
}

ccw-flatten-header[size="lg"] div:first-of-type {
  font-size: 50px;
}

ccw-flatten-header[size="md"] div:first-of-type {
  font-size: 35px;
}

ccw-flatten-header[size="sm"] div:first-of-type {
  font-size: 20px;
}

ccw-flatten-header[size="sm"] div:nth-of-type(2) svg {
  max-width: 48px;
  max-height: 48px;
}

ccw-flatten-header[size="sm"] {
  padding-top: 0;
}

ccw-flatten-header {
  padding-top: 32px;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: 800;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;

  div:first-of-type {
    font-size: 60px;
    color: #666;

    span {
      color: $secondary;
    }

    svg {
      margin-left: 16px;
    }
  }

  div:nth-of-type(2) {
    svg {
      max-height: 96px;
      max-width: 96px;
      margin-left: 16px;
    }
  }

  div {
    display: flex;
    align-items: center;
  }

  svg {
    max-height: 48px;
    max-width: 48px;
  }
}

ccw-flatten-footer {
  display: block;
  max-width: 800px;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: normal;
  font-size: 14px;
  padding: 16px 0 160px 0;
}
</style>
