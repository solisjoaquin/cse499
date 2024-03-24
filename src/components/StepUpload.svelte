<script>
    import {appStatus, setStatusLoading, setStatusError, setStatusChatMode} from '../store.ts';
    import Dropzone from "svelte-file-dropzone";
  
    let files = {
      accepted: [],
      rejected: []
    };
  
    async function handleFilesSelect(e) {
      const { acceptedFiles, fileRejections } = e.detail;
      files.accepted = [...files.accepted, ...acceptedFiles];
      files.rejected = [...files.rejected, ...fileRejections];

      if (acceptedFiles.length > 0) {
        setStatusLoading();

        const formData = new FormData();
        formData.append('file', acceptedFiles[0]);

        const res = await fetch('/api/upload', {
          method: 'POST',
          body: formData
        });

        if (!res.ok) {
            setStatusError();
            return
        }

        const {id, url, pages} = await res.json();
        setStatusChatMode({id, url, pages});
          
    }
}
        
</script>

{#if files.accepted.length === 0}
<Dropzone 
    on:drop={handleFilesSelect} 
    accept="application/pdf"
    multiple={false}
    >
    <div class="flex justify-center items-center rounded-md">
        <div class="bg-gray-200 p-4 rounded-md">
            <p class="text-center">Arrastra tus archivos aquí</p>
        </div>
    </div>
</Dropzone>
{/if}
<ol>
    {#each files.accepted as item}
    <li>{item.name}</li>
    {/each}
</ol>