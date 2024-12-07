<script>
  let currentSongIndex = $state(0);
  let isPlaying = $state(false);
  let songRefs = $state([]);
  
  // Sample playlist data
  const playlist = [
    { id: 1, title: "Song 1", duration: "3:45" },
    { id: 2, title: "Song 2", duration: "4:20" },
    { id: 3, title: "Song 3", duration: "3:30" },
    { id: 4, title: "Song 4", duration: "5:15" },
    { id: 5, title: "Song 5", duration: "3:55" },
    { id: 6, title: "Song 6", duration: "4:10" },
    { id: 7, title: "Song 7", duration: "3:25" },
    { id: 8, title: "Song 8", duration: "4:45" },
  ];

  // Simulate song completion and auto-scroll to next
  const handleSongEnd = () => {
    if (currentSongIndex < playlist.length - 1) {
      currentSongIndex += 1;
    }
  }

  const playSong = (index) => {
    currentSongIndex = index;
    isPlaying = true;
  }

  $effect(() => {
    if (songRefs[currentSongIndex]) {
      songRefs[currentSongIndex].scrollIntoView({
        behavior: 'smooth',
        block: 'center'
      });
    }
  });
</script>

<div class="w-full max-w-md mx-auto">
  <div class="bg-gray-100 p-4 mb-4 rounded">
    <div class="text-lg font-bold mb-2">
      Now Playing: {playlist[currentSongIndex].title}
    </div>
    <div class="flex gap-2">
      <button onclick={() => isPlaying = !isPlaying}
        class="bg-blue-500 text-white px-4 py-2 rounded">
        {isPlaying ? 'Pause' : 'Play'}
      </button>
      {#if (isPlaying)}
        <button onclick={handleSongEnd} class="bg-gray-500 text-white px-4 py-2 rounded">Next</button>
      {/if}
    </div>
  </div>

  <div class="h-64 overflow-y-auto border rounded">
    {#each playlist as song, index}
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      <div bind:this={songRefs[index]} class={`p-4 border-b cursor-pointer ${currentSongIndex === index ? 'bg-blue-100' : 'hover:bg-gray-50'}`}
        onclick={() => playSong(index)}>
        <div class="font-medium">{song.title}</div>
        <div class="text-sm text-gray-500">{song.duration}</div>
      </div>
    {/each}
  </div>
</div>