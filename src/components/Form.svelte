
<script>
    export let form;
    export let selectedAnimal;
    export let addAnimal;
    export let editAnimal;
  
    const resetForm = () => {
      form.name = "";
      form.age = "";
      form.specie = "";
      selectedAnimal = null;
    };
  </script>

<form on:submit|preventDefault={selectedAnimal ? editAnimal : addAnimal}>
    <div>
      <label for="name">Name:</label>
      <input id="name" type="text" bind:value={form.name} required />
    </div>
    <div>
      <label for="age">Age:</label>
      <input id="age" type="number" bind:value={form.age} required />
    </div>
    <div>
      <label for="specie">Specie:</label>
      <input id="specie" type="text" bind:value={form.specie} required />
    </div>
    <button type="submit">{selectedAnimal ? "Update" : "Add"} Animal</button>
    {#if selectedAnimal}
      <button
        type="button"
        on:click={resetForm}
      >
        Cancel
      </button>
    {/if}
  </form>
  