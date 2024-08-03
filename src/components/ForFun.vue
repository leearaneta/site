<template>
  <div class="flex flex-col gap-8">
    <div class="flex flex-col gap-6">
      <div class="sticky top-0 heading">
        <a href="https://scherz.netlify.app" target="_blank">
          <b> Scherz </b>
        </a>
      </div>
      <div>
        Experimental generative chord progressions
      </div>
      <ul class="text-sm flex flex-col gap-2">
        <li>
          Visualized circle of fifths as an animated spiral using react-three-fiber, rendered staff using SVG / CSS transitions
        </li>
        <li>
          Applied Clojure’s reducers to parallelize intensive computations
        </li>
      </ul>
    </div>
    <hr />
    <div class="flex flex-col gap-6">
      <div class="sticky top-0 heading">
        <a href="https://snippit.fly.dev" target="_blank">
          <b> Snippit </b>
        </a>
      </div>
      <div>
        Musical moodboarding, with friends
      </div>
      <ul class="text-sm flex flex-col gap-2">
        <li>
          Enabled users to create, organize, and share snippets of songs using Spotify’s API
        </li>
        <li>
          Implemented real-time collaboration using Phoenix LiveView / PubSub
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-6">
      <div class="flex sticky top-0 heading">
        <b> Mckaren </b>
      </div>
      <div>
        Alerted users of tennis court openings at McCarren Park
      </div>
      <ul class="text-sm flex flex-col gap-2">
        <li>
          Utilized proxy servers in order to spread requests between different IP addresses and avoid rate limits
        </li>
        <li>
          Notified registered users of court openings via email
        </li>
      </ul>
    </div>
  </div>
</template>