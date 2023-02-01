<script lang="ts">
    import { onMount } from "svelte";
    export const prerender = true;

    let time = new Date();

    const to_bin = (num: number, pad: number = 6) => 
        num.toString(2).padStart(pad, '0')
    
    $: hours = to_bin(time.getHours(), 5).padStart(6, '-')
	$: minutes = to_bin(time.getMinutes())
	$: seconds = to_bin(time.getSeconds())

	onMount(() => {
		const handle = setInterval(() => time = new Date(), 1000)

		return () => clearInterval(handle)
	})

    // Custamizations

    let split = false

    let odd_heart: Boolean
    $: odd_heart = hours.at(-1) === '1'
</script>

<div class="container">
    {#each [hours, minutes, seconds] as row, i}
        <div class="row">
            {#each row.split('') as unit, j}
                {@const secondary = split && (i % 2 == 0 ? j % 2 == 0 : j % 2 == 1)}
                {#if unit === '-'}
                    <div class="circle" class:secondary style="visibility: hidden"></div>
                {:else}
                    {@const active = Boolean(Number.parseInt(unit))}
                    <div class="circle" class:secondary class:active></div>
                {/if}
            {/each}
        </div>
    {/each}
</div>

<p class="footer">
    Made with <svg viewBox="0 0 1792 1792" preserveAspectRatio="xMidYMid meet" xmlns="http://www.w3.org/2000/svg" style="height: 0.8rem;"><path class="heart" class:odd_heart d="M896 1664q-26 0-44-18l-624-602q-10-8-27.5-26T145 952.5 77 855 23.5 734 0 596q0-220 127-344t351-124q62 0 126.5 21.5t120 58T820 276t76 68q36-36 76-68t95.5-68.5 120-58T1314 128q224 0 351 124t127 344q0 221-229 450l-623 600q-18 18-44 18z"></path></svg> by RootM
</p>
    
<style lang="scss">
    $main: #f280a1;
    $secondary: #9966cc;
    $diameter: 16vw;
    $diameter-height-breakpoint: 26vh;

    * {
        font-family: "Roboto", "Helvetica", "Arial", sans-serif;
    }

    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-top: 2%;
    }

    .row {
        display: flex;
    }
    .circle {
        width: min($diameter, $diameter-height-breakpoint);
        height: $diameter;
        max-height: $diameter-height-breakpoint;
        margin: 5px;
        background-color: #2e2e2e;
        border-radius: 50%;
        transition: 0.1s;
    }

    .circle.active {
        background-color: $main;
    }

    .circle.secondary.active {
        background-color: $secondary;
    }

    .footer {
        bottom: 0;
        width: 100%;
        position: fixed;
        text-align: center;
        color: #787878;
    }

    .heart {
        fill: $main;
        transition: 1s;
    }

    .heart.odd_heart {
        fill: $secondary
    }
</style>
