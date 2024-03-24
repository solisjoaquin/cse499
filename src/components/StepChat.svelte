<script>
    import { appStatusInfo, setStatusError } from "../store";
    const {url, pages, id} = $appStatusInfo;

    let answer = '';
    let loading = false;

    const numOfImagesToShow = Math.min(pages, 5);
    const images = Array.from({length: numOfImagesToShow}, (_, i) => {
        const page = i + 1;

        return url
            .replace('/upload/', `/upload/w-400,h_540,c_fill,pg_${page}/`)
            .replace('.pdf', '.jpg');
    });

    const handleSubmit = async (event)=> {
        event.preventDefault();
        loading = true;
        answer = '';
        const question = event.target.message.value;

        const searchParams = new URLSearchParams()
        searchParams.append("id", id)
        searchParams.append("question", question)

        try {
            const eventSource = new EventSource(`/api/ask?${searchParams.toString()}`)

            eventSource.onmessage = (event) => {
                loading = false
                const incomingData = JSON.parse(event.data)

                if (incomingData === "__END__") {
                    eventSource.close()
                    return
                }

                answer += incomingData
            }
        } catch (e) {
            setStatusError()
        } finally {
            loading = false
        }
    }

</script>

<div class="grid grid-cols-5 gap-2">
    {#each images as image}
        <img class="rounded w-full h-auto aspect-[400/540]" src={image} alt="page" />
    {/each}
</div>

<form class="mt-8" on:submit={handleSubmit}>
    <label for="message">Mensaje</label>
    <input type="text" required id="message" placeholder="Preguntaa" />
</form>

{#if loading}
    <div class="flex justify-center items-center h-screen">
        <div class="bg-blue-500 animate-spin rounded-full h-32 w-32 border-b-2 border-gray-900"></div>
    </div>
{/if}

{#if answer}
    <div class="bg-green-400 text-white p-2 rounded-md">
        <p class="font-medium">answer</p>
        <p>{answer}</p>
    </div>
{/if}
