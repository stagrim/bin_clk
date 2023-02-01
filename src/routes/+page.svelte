<script lang="ts">
    import { onMount } from "svelte";
    export const prerender = true;

    let time = new Date();

    const to_bin = (num: number, pad: number = 6) => 
        (num >>> 0).toString(2).padStart(pad, '0')
    
    $: hours = to_bin(time.getHours(), 5).padStart(6, '-')
	$: minutes = to_bin(time.getMinutes())
	$: seconds = to_bin(time.getSeconds())

	onMount(() => {
		const interval = setInterval(() => {
			time = new Date();
		}, 1000);

		return () => {
			clearInterval(interval);
		};
	});
</script>

<div class="container">
    {#each [hours, minutes, seconds] as row}
        <div class="row">
            {#each row.split('') as unit}
                {#if unit === '-'}
                    <div class="circle" style="visibility: hidden"></div>
                {:else}
                    {@const active = Boolean(Number.parseInt(unit))}
                    <div class="circle" class:active></div>
                {/if}
            {/each}
        </div>
    {/each}
</div>

<style>
    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .row {
        display: flex;
    }

    .circle {
        width: 10vw;
        height: 10vw;
        margin: 4px;
        background-color: #2e2e2e;
        border-radius: 50%;
        transition: 0.1s;
        box-sizing: border-box;
        resize: horizontal;
        aspect-ratio: 1 / 1;
        /* padding-bottom: calc(50% / 3); */
    }

    .circle.active {
        background-color: #F280A1;
    }
</style>
