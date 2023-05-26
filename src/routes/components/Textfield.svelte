<script>
    export let label = "";
    export let value = "";
    export let placeholder = "";
    export let helpText = "";
    export let disabled = false
    let id = Math.floor(Math.random() * 100).toString();
</script>

<div class="form-group">
    <label for={id}>{label}</label>
    <input type="text" class="form-control" {id} {placeholder} {disabled} bind:value />
    {#if helpText}
        <small class="form-text text-muted">{helpText}</small>
    {/if}
</div>
