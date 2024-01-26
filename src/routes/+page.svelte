
<script lang="ts">

  const pages: any = {
    'www.adidas.ca': {
      image_src: 'https://assets.adidas.com/images/w_600'
    }
  }

  let items: {
    images: string[],
    url: string,
    selectedImage: number,
    viewedImage: number,
    hostname: string,
  }[] = [];

  async function fetchPage(url: string) {
    const hostname: string = new URL(url).hostname;

    if (visitedUrls.find(visitedUrl => visitedUrl === url)) return;

    console.log('not visited');

    const response = await fetch(url);
    const text = await response.text();

    // console.log(text);

    const parser = new DOMParser();
    const html = parser.parseFromString(text, 'text/html');

    // console.log(html);

    const images = Array.from(html.querySelectorAll('img')).filter(i => i.src.includes(pages[hostname].image_src));

    console.log(images);

    const item = {
      images: images.map((i: any) => i.attributes.src.value),
      url,
      hostname: 'www.adidas.ca',
      selectedImage: 0,
      viewedImage: 0,
    }

    // console.log(item);

    items = [...items, item];

    visitedUrls.push(url);
  }

  let url: string = '';
  let visitedUrls: string[] = [];

  const previousImage = (index: number) => {
    let _index = items[index].viewedImage - 1;

    if (_index === -1) _index = items[index].images.length - 1;

    items[index].viewedImage = _index;
  }
  
  const nextImage = (index: number) => {
    let _index = items[index].viewedImage + 1;

    if (_index === items[index].images.length) _index = 0;

    items[index].viewedImage = _index;
  }

  // const viewImage = (imageIndex: number, index: number) => {
  //   const _items = [...items];
  //   _items[imageIndex].viewedImage = index;
  // }

</script>

<div class="flex gap-2">
  <input placeholder="https://www.example.com" type="text" bind:value={url} class="rounded border-2 border-gray-200 p-2 w-full" />
  <button type="button" on:click={() => fetchPage(url)} class="w-full bg-black text-white p-2 rounded">Go</button>
</div>
<br/>
<div class="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-4 gap-4">
  {#each items as item, index}
    <div class="flex flex-col gap-2">
      <div class="flex items-center gap-2">
        <button
          class="border-2 border-gray-200 p-2 rounded-full"
          type="button"
          on:click={() => previousImage(index)}
        >
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5L8.25 12l7.5-7.5" />
          </svg>
        </button>
        <div class="w-[512px]">
          <img
            src={item.images.at(item.viewedImage)}
            alt={item.images.at(item.viewedImage)}
            width="512"
            class="rounded"
          />
        </div>
        <button
          class="border-2 border-gray-200 p-2 rounded-full"
          type="button"
          on:click={() => nextImage(index)}
        >
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 4.5l7.5 7.5-7.5 7.5" />
          </svg>
        </button>
      </div>
      <a href={item.url} target="_blank" class="flex gap-2 justify-center border-gray-200 border-2 rounded-full p-2">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M13.19 8.688a4.5 4.5 0 011.242 7.244l-4.5 4.5a4.5 4.5 0 01-6.364-6.364l1.757-1.757m13.35-.622l1.757-1.757a4.5 4.5 0 00-6.364-6.364l-4.5 4.5a4.5 4.5 0 001.242 7.244" />
        </svg>
        <span>{item.hostname}</span>
      </a>
      <!-- <div>
        {#each item.images as images, imageIndex}
          <button
            type="button"
            on:click={() => viewImage(index, imageIndex)}
          ></button>
        {/each}
      </div> -->
    </div>
  {/each}
</div>

<!-- todo: fix images not being same height in some cases, fix images being fetched in duplicate instead of all images being fetched -->