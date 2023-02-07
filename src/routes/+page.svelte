<svelte:head>
  <title>bin2header</title>
  <meta name="description" content="Convert binary files to C header files" />
</svelte:head>

<div class="h-screen flex justify-center items-center flex-col">
  <h1 class="text-2xl font-semibold">bin2header</h1>
  <p class="mb-5">
    Insipired by <a class="underline font-medium" href="https://github.com/AntumDeluge/bin2header">
      AntumDeluge's bin2header
    </a>
  </p>
  <label
    class="relative aspect-video w-[24rem] flex flex-col justify-center items-center bg-neutral-100 border-2 border-neutral-300 border-dashed rounded-lg"
  >
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="w-8 h-8 text-neutral-600 mb-2">
      <!--! Font Awesome Pro 6.2.1 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. -->
      <path
        fill="currentColor"
        d="M200 344V280H136C122.7 280 112 269.3 112 256C112 242.7 122.7 232 136 232H200V168C200 154.7 210.7 144 224 144C237.3 144 248 154.7 248 168V232H312C325.3 232 336 242.7 336 256C336 269.3 325.3 280 312 280H248V344C248 357.3 237.3 368 224 368C210.7 368 200 357.3 200 344zM0 96C0 60.65 28.65 32 64 32H384C419.3 32 448 60.65 448 96V416C448 451.3 419.3 480 384 480H64C28.65 480 0 451.3 0 416V96zM48 96V416C48 424.8 55.16 432 64 432H384C392.8 432 400 424.8 400 416V96C400 87.16 392.8 80 384 80H64C55.16 80 48 87.16 48 96z"
      />
    </svg>
    <span>Drop a file here</span>
    <input
      id="file"
      class="absolute inset-0 opacity-0 cursor-pointer z-20"
      type="file"
      on:change={(event) => {
        // read file binary data
        const file = event.target.files[0];
        const reader = new FileReader();
        reader.onload = (event) => {
          // convert binary data to hex string
          const hex = Array.from(new Uint8Array(event.target.result))
            .map((b) => b.toString(16).padStart(2, '0'))
            .join('');
          // convert to c array
          const cArray = hex
            .match(/.{1,2}/g)
            .map((b) => `0x${b}`)
            .join(', ');
          // make a header file
          // format file name to be a valid c variable name
          const name = file.name.replace(/[^a-zA-Z0-9_]/g, '_');
          const header = `#pragma once

const unsigned char ${name}[] = {
    ${cArray.match(/.{1,48}/g).join('\n    ')}
};`;
          // output to file
          const blob = new Blob([header], { type: 'text/plain' });
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a');
          a.download = file.name + '.h';
          a.href = url;
          a.click();
        };
        reader.readAsArrayBuffer(file);
      }}
    />
  </label>
</div>
