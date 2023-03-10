<script>
import { onMount } from 'svelte';

let volumeState
let playbackRange = [0.25, 0.5, 1, 1.25, 1.5, 2]
let currentSpeed = 2
let fullTime = `0:00`
let currentTime = `0:00`

var player
var videoPlayer
var playButton
var currentTimer
var volumeButton
var volumeRange
var playback
var timelineContainer
var timelineThumb
var dragzone

onMount(() => {
    player = document.querySelector(".player")
    videoPlayer = document.querySelector(".video-player")
    playButton = document.querySelector(".play-button")
    timelineContainer = document.querySelector(".timeline")
    timelineThumb = document.querySelector(".timeline-thumb")
    currentTimer = document.querySelector(".current-time")
    volumeButton = document.querySelector(".volumeButton")
    volumeRange = document.querySelector(".volumeAppear")
    playback = document.querySelector(".playback")
    dragzone = document.querySelector(".dragzone")

    timelineContainer.addEventListener("mousemove", handleTimelineUpdate)
    videoPlayer.addEventListener("timeupdate", () => {
        currentTime = formatDuration(videoPlayer.currentTime)
    })

    fullTime = formatDuration(videoPlayer.duration)
});

function togglePlay() {
    videoPlayer.paused ? videoPlayer.play() : videoPlayer.pause()
    videoPlayer.paused ? playButton.textContent = `play_arrow` : playButton.textContent = `pause`
    videoPlayer.paused ? player.classList.add("paused") : player.classList.remove("paused")
}

function toggleVolume() {
    if (volumeState != 0) {
        videoPlayer.muted = !videoPlayer.muted
    }
}

function changeVolume() {
    if (volumeState > 0) {
        videoPlayer.muted = false
        videoPlayer.volume = volumeState
    } else {
        videoPlayer.muted = true
    }
}

const leadingZeroFormatter = new Intl.NumberFormat(undefined, {
  minimumIntegerDigits: 2,
})

function formatDuration(time) {
  const seconds = Math.floor(time % 60)
  const minutes = Math.floor(time / 60) % 60
  const hours = Math.floor(time / 3600)
  if (hours === 0) {
    return `${minutes}:${leadingZeroFormatter.format(seconds)}`
  } else {
    return `${hours}:${leadingZeroFormatter.format(
      minutes
    )}:${leadingZeroFormatter.format(seconds)}`
  }
}

function loadDuration() {
    fullTime = formatDuration(videoPlayer.duration)
}

function playerBackChange() {
    if (currentSpeed >= 5) {
        currentSpeed = 0
    } else {
        currentSpeed++
    }
    videoPlayer.playbackRate = playbackRange[currentSpeed]
    playback.textContent = playbackRange[currentSpeed]
}

function requestMiniplayerMode() {
    if (document.pictureInPictureElement == null) {
        videoPlayer.requestPictureInPicture()
    } else {
        document.exitPictureInPicture() 
    }
}

function requestFullScreenMode() {
    if (document.fullscreenElement == null) {
        videoPlayer.requestFullscreen()
    } else {
        document.exitFullscreen()
    }
}

function handleTimelineUpdate(e) {
    const box = timelineContainer.getBoundingClientRect()
    const percentage = Math.min(Math.max(0, e.x - box.x), box.width) / box.width
}
</script>

<div class="container">
    <div class="player paused">
        <div class="controls">
            <div class="mx-2 flex gap-4 justify-center items-center">
                <button class="btn material-icons play-button" on:click={togglePlay}>play_arrow</button>
                <div class="flex gap-2 justify-center items-center">
                    <div class="volume flex justify-center items-center gap-1 sm:gap-2 relative" >
                        <button class="volumeButton material-icons" on:click={toggleVolume}>volume_up</button>
                        <input type="range" bind:value={volumeState} on:change={changeVolume} class="volumeAppear" min="0" max="10">
                    </div>
                </div>
                <div class="duration-time">
                    <div class="current-time">{currentTime}/{fullTime}</div>
                </div>
            </div>
            <div class="mx-2 flex gap-4 justify-center items-center">
                <div class="sm:hidden relative">
                    ...
                    <div class="hidden absolute">
                        <button class="playback w-10 font-bold text-xl" on:click={playerBackChange}>1x</button>
                        <button class="material-icons" on:click={requestMiniplayerMode}>
                            picture_in_picture_alt
                        </button>
                    </div>
                </div>
                <button class="playback w-10 font-bold text-xl hidden sm:block" on:click={playerBackChange}>1x</button>
                <button class="material-icons hidden sm:block" on:click={requestMiniplayerMode}>
                    picture_in_picture_alt
                </button>
                <button class="material-icons" on:click={requestFullScreenMode}>
                    open_in_full
                </button>
            </div>
        </div>
        <video class="video-player" src="https://www.w3schools.com/tags/movie.mp4" on:loadeddata={loadDuration}  on:click={togglePlay}></video>
    </div>
    <div class="timeline">
        <div class="timeline-thumb"></div>
    </div>
    <div class="extras">
        <button class="Share material-icons m-2 text-white">share</button>
        <button class="Download material-icons m-2 text-white">file_download</button>
    </div>
</div>