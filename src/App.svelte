<script>
  let currentSongIndex = $state(0);
  let isPlaying = $state(false);
  let songRefs = $state([]);
  let searchMode = $state('jump'); // jump or filter
  let searchTerm = $state(''); // for filter mode
  
  // Sample playlist data
  const playlist = [
    { id: 1, title: "Bohemian Rhapsody", artist: "Queen", duration: "3:45" },
    { id: 2, title: "Sweet Child O' Mine", artist: "Guns N' Roses", duration: "4:20" },
    { id: 3, title: "Hotel California", artist: "Eagles", duration: "3:30" },
    { id: 4, title: "Stairway to Heaven", artist: "Led Zeppelin", duration: "5:15" },
    { id: 5, title: "Sweet Home Alabama", artist: "Lynyrd Skynyrd", duration: "3:55" },
    { id: 6, title: "Sweet Dreams", artist: "Eurythmics", duration: "4:10" },
    { id: 7, title: "Sweet Emotion", artist: "Aerosmith", duration: "3:25" },
    { id: 8, title: "November Rain", artist: "Guns N' Roses", duration: "4:45" },
  ];

  let filteredPlaylist = $derived(playlist.filter((song) => {
    return song.title.toLowerCase().includes(searchTerm.toLowerCase()) ||
    song.artist.toLowerCase().includes(searchTerm.toLowerCase());
  }))

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

  const highlightSearchTerm = (text) => {
    if (!searchTerm) return text;
    const parts = text.split(new RegExp(`(${searchTerm})`, 'gi'));
    return parts.map((part, index) => {
      return part.toLowerCase() === searchTerm.toLowerCase()
        ? `<span class="bg-yellow-200">${part}</span>`
        : part;
    }).join('');
  }

  // Jump to first matching song
  $effect(() => {
    if (searchMode === 'jump' && searchTerm) {
      const firstMatch = playlist.findIndex(song => {
        return song.title.toLocaleLowerCase().includes(searchTerm.toLowerCase()) ||
        song.artist.toLowerCase().includes(searchTerm.toLowerCase());
      });

      if (firstMatch !== -1) {
        songRefs[firstMatch]?.scrollIntoView({
          behavior: 'smooth',
          block: 'center'
        });
      }
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

  <div class="mb-4">
    <div class="flex items-center gap-2 mb-2">
      <div class="relative flex-1">
        <input type="text" bind:value={searchTerm} placeholder="Search songs or artists..."
        class="w-full pl-10 pr-4 py-2 border rounded">
      </div>
      <select bind:value={searchMode} class="border rounded px-3 py-2">
        <option value="jump">Jump</option>
        <option value="filter">Filter</option>
      </select>
    </div>
  </div>

  <div class="h-64 overflow-y-auto border rounded">
    {#each (searchMode === 'filter' ? filteredPlaylist : playlist) as song, index}
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      <div bind:this={songRefs[index]} class={`p-4 border-b cursor-pointer ${currentSongIndex === index ? 'bg-blue-100' : 'hover:bg-gray-50'}`}
        onclick={() => playSong(index)}>
        <div class="font-medium">{@html highlightSearchTerm(song.title)}</div>
        <div class="text-sm text-gray-500">{@html highlightSearchTerm(song.artist)} . {song.duration}</div>
      </div>
    {/each}
  </div>
</div>