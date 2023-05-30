<svelte:options tag="my-folder-listing" />
<script lang="ts">
  import File from "./File.svelte";
  export let directory = [];
</script>

<ul style="text-align: left;">
  {#each directory as file_or_folder}
    <li>
      {#if file_or_folder.children}
        <span style="">Folder: {file_or_folder.name}</span>
        <svelte:self directory={file_or_folder.children} />
      {:else}
        <File name={file_or_folder.name} />
      {/if}
    </li>
  {/each}
</ul>

<style>
	ul {
    text-align: right;
  }
</style>
