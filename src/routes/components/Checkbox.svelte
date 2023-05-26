<script>
    export let label = "";
    export let checked = false;
    let id = Math.floor(Math.random() * 100).toString();
</script>

<div class="form-check">
    <input class="form-check-input" type="checkbox" bind:checked {id} />
    <label class="form-check-label" for={id}>
        {label}
    </label>
</div>
