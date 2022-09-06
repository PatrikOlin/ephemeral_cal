<script type="text/javascript">
    export let availableDates = [];
</script>

<div>
    <ul>
        {#each availableDates as date}
            <li>{date}</li>
        {/each}
    </ul>
</div>
