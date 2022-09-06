<script type="text/javascript">
    import calendarize from "calendarize";
    import Arrow from "./Arrow.svelte";
    import AvailabilityList from "./AvailabilityList.svelte";
    import { onMount } from "svelte";

    export let today = null; // date
    let year = 2022;
    let month = 0; // jan
    let offset = 0; // sun
    let availableDates = [];

    let labels = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
    let months = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "July",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
    ];
    let colors = [
        "#F94144",
        "#F3722C",
        "#F8961E",
        "#F9844A",
        "#F9C74F",
        "#90BE6D",
        "#43AA8B",
        "#4D908E",
        "#577590",
        "#277DA1",
    ];
    let activeColor;

    $: today_month = month = today?.getMonth();
    $: today_year = year = today?.getFullYear();
    $: today_day = today?.getDate();

    let prev = calendarize(new Date(year, month - 1), offset);
    let current = calendarize(new Date(year, month), offset);
    let next = calendarize(new Date(year, month + 1), offset);

    onMount(() => {
        activeColor = colors[Math.floor(Math.random() * colors.length)];
    });

    $: cssVar = `--active-color:${activeColor}`;

    function toNext() {
        [prev, current] = [current, next];

        if (++month > 11) {
            month = 0;
            year++;
        }

        next = calendarize(new Date(year, month + 1), offset);
    }

    function toPrev() {
        [current, next] = [prev, current];

        if (--month < 0) {
            month = 11;
            year--;
        }

        prev = calendarize(new Date(year, month - 1), offset);
    }

    function toggleAvailable(day) {
        const date = new Date(`${day} ${months[month]} ${year}`).toISOString();
        const idx = availableDates.findIndex((avail) => avail === date);
        if (idx > -1) {
            availableDates.splice(idx, 1);
        } else {
            availableDates.push(date);
        }

        availableDates = [...availableDates];
        current = current;
    }

    function isAvailable(day) {
        if (day < 1) return false;
        const date = new Date(`${day} ${months[month]} ${year}`).toISOString();
        return availableDates.includes(date);
    }

    const isToday = (day) => {
        return (
            today &&
            today_year === year &&
            today_month === month &&
            today_day === day
        );
    };
</script>

<header>
    <Arrow left on:click={toPrev} />
    <h3>{months[month]} {year}</h3>
    <Arrow on:click={toNext} />
</header>
<div class="month" style={cssVar}>
    {#each labels as txt, idx (txt)}
        <span class="label">{labels[idx + (offset % 7)]}</span>
    {/each}
    {#each { length: 6 } as week, idxw (idxw)}
        {#if current[idxw]}
            {#each { length: 7 } as day, idxd (idxd)}
                {#if current[idxw][idxd] != 0}
                    <span
                        class="date"
                        class:today={isToday(current[idxw][idxd])}
                        class:available={isAvailable(current[idxw][idxd])}
                        on:click={toggleAvailable(current[idxw][idxd])}
                    >
                        {current[idxw][idxd]}
                    </span>
                {:else if idxw < 1}
                    <span
                        class="date other"
                        on:click={setAvailable(current[idxw][idxd])}
                        >{prev[prev.length - 1][idxd]}</span
                    >
                {:else}
                    <span
                        class="date other"
                        on:click={setAvailable(current[idxw][idxd])}
                        >{next[0][idxd]}</span
                    >
                {/if}
            {/each}
        {/if}
    {/each}
</div>
<AvailabilityList {availableDates} />

<style>
    header {
        display: flex;
        flex-direction: row;
        margin: auto;
        justify-content: center;
    }
    .month {
        display: grid;
        grid-template-columns: repeat(7, 1fr);
        text-align: right;
        grid-gap: 4px;
    }

    .label {
        font-weight: 300;
        text-align: center;
        text-transform: uppercase;
        margin-bottom: 0.5rem;
        opacity: 0.6;
    }

    .date {
        height: 50px;
        font-size: 16px;
        letter-spacing: -1px;
        border: 1px solid #e6e4e4;
        padding-right: 4px;
        font-weight: 700;
        padding: 0.5rem;
    }

    .date.today {
        color: #5286fa;
        background: #c4d9fd;
        border-color: currentColor;
    }

    .date.other {
        opacity: 0.2;
    }

    .date.available {
        background: var(--active-color);
    }
</style>
