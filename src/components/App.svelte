<script>
    import { APP_STATUS, appStatus } from '../store.ts';
    import StepUpload from './StepUpload.svelte';
    import StepChat from './StepChat.svelte';
</script>


{#if $appStatus === APP_STATUS.INIT}
    <StepUpload />
{:else if $appStatus === APP_STATUS.LOADING}
    <div class="flex justify-center items-center h-screen">
        <div class="bg-red-800 animate-spin rounded-full h-32 w-32 border-b-2 border-gray-900"></div>
    </div>
{:else if $appStatus === APP_STATUS.ERROR}
    <div class="bg-red-400 text-white p-2 rounded-md">
        Hubo un error
    </div>
{:else if $appStatus === APP_STATUS.CHAT_MODE}
    <StepChat />
{:else}
    <div class="bg-red-400 text-white p-2 rounded-md">
        Error
    </div>
{/if}
