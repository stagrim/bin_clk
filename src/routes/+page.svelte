<script lang="ts">
    import { onMount } from "svelte";
    import { page } from '$app/stores';
    export const prerender = true;

    let time = new Date();

    let config = {
        split: true,
        numbers: true,
        show_inactive_numbers: false,
        winter: false,
        true_binary_seconds: false,
    }

    const to_bin = (num: number, pad: number = 6) => 
        num.toString(2).padStart(pad, '0')

    const weekdays = ["Sun","Mon","Tue","Wed","Thu","Fri","Sat"]

    $: weekday = weekdays[time.getDay()]
    $: hours = to_bin(time.getHours(), 5).padStart(6, '-')
	$: minutes = to_bin(time.getMinutes())
    // get 64:th of a second seconds version: Math.round((time.getSeconds() * time.getMilliseconds()) / 937.5)
	$: seconds = to_bin(config.true_binary_seconds ? Math.floor((time.getSeconds() + (time.getMilliseconds() / 1000)) / 0.9375) : time.getSeconds())

	onMount(() => {
        config = {
            split: !$page.url.searchParams.has("split"),
            numbers: !$page.url.searchParams.has("numbers"),
            show_inactive_numbers: $page.url.searchParams.has("show_inactive_numbers"),
            winter: $page.url.searchParams.has("winter"),
            true_binary_seconds: $page.url.searchParams.has("true_binary_seconds"),
        }

		let handle: NodeJS.Timer
        if (config.true_binary_seconds) {
            handle = setInterval(() => time = new Date(), 937.5)
        } else {
            handle = setInterval(() => time = new Date(), 1000)
        }

		return () => clearInterval(handle)
	})

    let odd_heart: boolean
    $: odd_heart = hours.at(-1) === '1'

    const random_snowflake = () =>
        `&#1005${Math.floor(Math.random() * 3 + 2)}`
</script>

{#if config.winter}
    {#each Array(75) as i}
        <div class="snow">{@html random_snowflake()}</div>
    {/each}
{/if}

<div class="container">
    {#each [hours, minutes, seconds] as row, i}
        <div class="row">
            {#each row.split('') as unit, j}
                {@const secondary = config.split && (i % 2 == 0 ? j % 2 == 0 : j % 2 == 1)}
                {#if unit === '-'}
                    <div class="circle show_inactive_numbers" class:active={odd_heart} class:secondary>
                        <div class="legend">{weekday}</div>
                    </div>
                {:else}
                    {@const active = Boolean(Number.parseInt(unit))}
                    <div class="circle" class:secondary class:active class:show_inactive_numbers={config.show_inactive_numbers}>
                        {#if config.numbers}
                            <div class="legend">{2 ** (5-j)}</div>
                        {/if}
                    </div>
                {/if}
            {/each}
        </div>
    {/each}
</div>

<p class="footer">
    Made with <svg viewBox="0 0 1792 1792" preserveAspectRatio="xMidYMid meet" xmlns="http://www.w3.org/2000/svg" style="height: 0.8rem;"><path class="heart" class:odd_heart d="M896 1664q-26 0-44-18l-624-602q-10-8-27.5-26T145 952.5 77 855 23.5 734 0 596q0-220 127-344t351-124q62 0 126.5 21.5t120 58T820 276t76 68q36-36 76-68t95.5-68.5 120-58T1314 128q224 0 351 124t127 344q0 221-229 450l-623 600q-18 18-44 18z"></path></svg> by RootM
</p>
    
<style lang="scss">
    @font-face {
        font-family: 'Roboto Mono';
        src: url('/RobotoMono-VariableFont_wght.ttf');
    }

    $background: #0e0e0e;
    $inactive: #2e2e2e;
    $main: #f280a1;
    $secondary: #9966cc;
    
    $diameter: 16vw;
    $diameter-height-breakpoint: 26vh;

    $diameter-vertical: 15vh;
    $diameter-height-breakpoint-vertical: 26vw;

    :global(body) {
        background-color: $background;
        font-size: 16px;
    }

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
        background-color: $inactive;
        border-radius: 50%;
        transition: 0.1s;

        display: flex;
        justify-content: center;
        vertical-align: middle;
        align-items: center;
        color: $inactive;
    }

    .circle.show_inactive_numbers {
        color: $main;
    }

    .circle.secondary.show_inactive_numbers {
        color: $secondary;
    }

    .circle.active {
        background-color: $main;
        color: $inactive;
    }

    .circle.secondary.active {
        background-color: $secondary;
        color: $inactive;
    }

    .circle .legend {
        font-family: Roboto Mono;
        font-weight: 700;
        font-size: 3.5rem;
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
        fill: $secondary;
    }

    @media (orientation: portrait) {
        .container {
            justify-content: center;
            flex-direction: row;
        }

        .row {
            display: flex;
            flex-direction: column-reverse;
            
        }
        
        .circle {
            height: min($diameter-vertical, $diameter-height-breakpoint-vertical);
            width: $diameter-vertical;
            max-width: $diameter-height-breakpoint-vertical;
        }
    }

    // Snow effects

    :global(body) {
        /* height: 100vh; */
        /* background: radial-gradient(ellipse at bottom, #1b2735 0%, #090a0f 100%); */
        overflow: hidden;
    }

    @function random_range($min, $max) {
        $rand: random();
        $random_range: $min + floor($rand * (($max - $min) + 1));
        @return $random_range;
    }

    .snow {
        // filter: drop-shadow(0 0 10px white);
        $total: 200;
        position: absolute;
        width: 10px;
        height: 10px;
        // background: white;
        // border-radius: 50%;
        color: white;

        @for $i from 1 through $total {
            $random-x: random(1000000) * 0.0001vw;
            $random-offset: random_range(-100000, 100000) * 0.0001vw;
            $random-x-end: $random-x + $random-offset;
            $random-x-end-yoyo: $random-x + ($random-offset / 2);
            $random-yoyo-time: random_range(30000, 80000) / 100000;
            $random-yoyo-y: $random-yoyo-time * 100vh;
            $random-scale: random(3);
            $fall-duration: random_range(10, 30) * 1s;
            $fall-delay: random(30) * -1s;
            $rotate: random(720) * 1deg - 360deg;

            &:nth-child(#{$i}) {
                opacity: random(7000) * 0.0001 + 0.3;
                transform: translate($random-x, -60px) scale($random-scale) rotate($rotate);
                animation: fall-#{$i} $fall-duration $fall-delay linear infinite;
            }

            @keyframes fall-#{$i} {
                #{percentage($random-yoyo-time)} {
                    transform: translate($random-x-end, $random-yoyo-y) scale($random-scale);
                }

                to {
                    transform: translate($random-x-end-yoyo, 100vh) scale($random-scale) rotate($rotate);
                }
            }
        }
    }
</style>
