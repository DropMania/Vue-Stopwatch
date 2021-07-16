<template>
    <div class="card border-primary mb-3 app-border">
        <div class="card-body mt-5">
            <div class="timer-box">
                <div v-for="(n, i) in timerArray" :key="i" style="display:flex">
                    <div class="card text-white bg-primary mb-3 time-number">
                        <div class="card-body">{{ n }}</div>
                    </div>
                    <div style="margin-left: 1vw" v-if="i == 1">:</div>
                </div>
            </div>
            <div class="scroll-parent">
                <div id="slider"></div>
            </div>

            <div class="button-group">
                <button
                    type="button"
                    class="btn btn-primary btn-lg"
                    @click="start()"
                >
                    Start
                </button>
                <button
                    type="button"
                    class="btn btn-primary btn-lg"
                    @click="stop()"
                >
                    Stop
                </button>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import CircleSlider from 'circle-slider'
export default {
    setup() {
        let timerArray = ref([0, 0, 0, 0])
        let debug = ref(0)
        let timer = 0
        let interval
        let running = false

        onMounted(() => {
            const cs = new CircleSlider('slider', {
                snap: 45,
                clockwise: false,
                startPos: 'top'
            })
            let prevAngle = 0
            cs.on('sliderMove', (angle) => {
                console.log(arguments)
                if (angle > prevAngle) {
                    if (timer >= 0) {
                        secondsToMS(--timer)
                    }
                } else if (angle < prevAngle) {
                    secondsToMS(++timer)
                }
                prevAngle = angle
            })
        })

        function start() {
            if (running == false) {
                running = true
                interval = setInterval(() => {
                    if (timer >= 0) {
                        secondsToMS(--timer)
                    }
                }, 1000)
            }
        }
        function stop() {
            clearInterval(interval)
            running = false
        }
        function secondsToMS(s) {
            if (s >= 0) {
                let minutes = Math.floor(s / 60)
                let seconds = s - minutes * 60
                let mString = String(minutes).padStart(2, '0')
                let sString = String(seconds).padStart(2, '0')
                document.title = `${mString}:${sString}`
                timerArray.value = [...mString, ...sString]
            }
        }

        return {
            timerArray,
            start,
            stop,
            timer,
            debug
        }
    }
}
</script>

<style>
@import url('https://bootswatch.com/5/vapor/bootstrap.min.css');
* {
    margin: 0px;
    padding: 0px;
}

body,
html {
    width: 100%;
    height: 100%;
}
#app {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}
.app-border {
    /* max-width: 20rem; */
    aspect-ratio: 9/16;
    min-height: 90%;
    max-height: 95vh;
}
.timer-box {
    display: flex;
    gap: 1vw;
    font-size: 9vh;
    text-align: center;
}
.timer-number {
    max-width: 20rem;
}
.scroll-parent {
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 4vh 0vh;
}
.scroll-section {
    height: 20vh;
    aspect-ratio: 14/16;
    cursor: n-resize;
    scroll-behavior: unset;
    touch-action: none;
}
/* blow: everything corresponding to the slider */
#slider {
    /*
    position: relative is needed for the handle to be
    positioned correctly, and border-radius: 100% just
    makes the div round.
  */
    position: relative;
    border-radius: 100%;

    /* Other than the above two, go wild! */
    height: 25vh;
    width: 25vh;
    /*  background: linear-gradient(90deg, var(--bs-primary), var(--bs-secondary)); */
    border: solid var(--bs-primary);
}

/*
  Probably best to paste this exactly as is.
  These CSS rules make sure that the handle rotates
  properly, so don't change anything here.
*/
.cs-handle-container {
    position: absolute;
    left: 0;
    right: 0;
    top: 50%;
    height: 2px;
    margin-top: -1px;
}

/* Also paste as is */
.cs-handle {
    position: absolute;
    transform: translateY(-50%);
}

/* the appearance of the handle, feel free to change! */
#slider .cs-handle {
    height: 50px;
    width: 50px;
    /*
    Change 'right' to change the offset from the edge.
    E.g right: 0 puts the handle just next to the edge
    of #slider, on the inside
  */
    right: -15px;
    cursor: default;
    border-radius: 100%;
    background: linear-gradient(180deg, #ffffff, #efefef);
    box-shadow: rgba(0, 0, 0, 0.3) 0 1px 10px 0;
}

#slider .cs-handle:active {
    background: linear-gradient(180deg, #ebebeb, #dfdfdf);
}
</style>
