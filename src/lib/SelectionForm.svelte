<svelte:options tag="my-selection-form" />

<script lang="ts">
  import FolderListing from "./FolderListing.svelte";

  class File {
    name: string;
  }
  class Folder {
    name: string;
    children: File[] | Folder[];
  }
  enum files_folders_enum {
    file = "file",
    folder = "folder",
  }
  let folders_or_files: files_folders_enum | "" = "",
    input_name = "",
    save_folder = "",
    show_folder = "hide";
  let directory: File[] | Folder[] = [];
  let allFoldersNames: string[] = [];
  let allFilesNames: string[] = [];

  function handleSelectChange(folders_or_files: files_folders_enum | "") {
    console.log(folders_or_files, input_name);
    if (folders_or_files !== "") {
      //show dropdown populated with folders
      show_folder = "show";
    } else {
      show_folder = "hide";
    }
  }
  function handleSaveClick() {
    if (!input_name || !folders_or_files) {
      alert("Input missing");
      return;
    }

    let final: File | Folder;
    if (folders_or_files === "file") {
      if (allFilesNames.includes(input_name)) {
        alert("Cannot have duplicate file name");
        return;
      }
      let file = new File();
      file.name = input_name;
      final = file;
      allFilesNames[allFilesNames.length] = file.name;
    } else {
      if (allFoldersNames.includes(input_name)) {
        alert("Cannot have duplicate folder name");
        return;
      }
      let folder = new Folder();
      folder.name = input_name;
      folder.children = [];
      final = folder;
    }

    if (save_folder) {
      const folderIndex = directory.findIndex(
        (dir: Folder) => dir instanceof Folder && dir.name === save_folder
      );
      if (folderIndex === -1) {
        alert("Parent folder not found");
      } else {
        (directory[folderIndex] as Folder).children[
          (directory[folderIndex] as Folder).children.length
        ] = final;
      }
    } else {
      directory[directory.length] = final;
      if (final instanceof Folder) {
        allFoldersNames[allFoldersNames.length] = final.name;
      }
    }
  }

  $: {
    handleSelectChange(folders_or_files);
  }
  $: {
    console.log(directory, allFilesNames, allFoldersNames);
  }
</script>

<form>
  <input bind:value={input_name} required placeholder="Name" />

  <select bind:value={folders_or_files} required>
    <option value="">Select File or Folder</option>
    <option value="file">File</option>
    <option value="folder">Folder</option>
  </select>

  <select bind:value={save_folder} class={show_folder} required>
    <option value="">Folder name (Empty = root)</option>
    {#each allFoldersNames as folder_name}
      <option value={folder_name}>{folder_name}</option>
    {/each}
  </select>

  <button on:click|preventDefault={handleSaveClick} type="submit">
    Save
  </button>

  <input type="reset" value="Cancel" />
</form>

<FolderListing {directory} />

<style>
  .hide {
    display: none;
  }
</style>
