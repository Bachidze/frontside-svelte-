<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  const animals = writable([]);
  const apiUrl = "http://localhost:3000/api";

  let form = {
    name: "",
    age: "",
    specie: ""
  };

  let selectedAnimal = null;

  const fetchAnimals = async () => {
    const response = await fetch(apiUrl);
    const data = await response.json();
    animals.set(data.data);
  };

  const addAnimal = async () => {
    const response = await fetch(apiUrl, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(form)
    });

    if (response.ok) {
      await fetchAnimals();
      form = { name: "", age: "", specie: "" };
    }
  };

  const editAnimal = async () => {
    const response = await fetch(`${apiUrl}/${selectedAnimal.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(form)
    });

    if (response.ok) {
      await fetchAnimals();
      selectedAnimal = null;
      form = { name: "", age: "", specie: "" };
    }
  };

  const deleteAnimal = async (id) => {
    const response = await fetch(`${apiUrl}/${id}`, { method: "DELETE" });
    if (response.ok) await fetchAnimals();
  };

  const populateForm = (animal) => {
    selectedAnimal = animal;
    form = { ...animal };
  };

  onMount(() => {
    fetchAnimals();
  });
</script>

<style>
  .container {
    padding: 20px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
  }

  th, td {
    border: 1px solid #ccc;
    padding: 10px;
    text-align: left;
  }

  th {
    background-color: #f4f4f4;
  }
</style>

<div class="container">
  <h1>Animal Management</h1>

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
        on:click={() => {
          selectedAnimal = null;
          form = { name: "", age: "", specie: "" };
        }}
      >
        Cancel
      </button>
    {/if}
  </form>

  <h2>Animals List</h2>

  <table>
    <thead>
      <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Age</th>
        <th>Specie</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each $animals as animal (animal.id)}
        <tr>
          <td>{animal.id}</td>
          <td>{animal.name}</td>
          <td>{animal.age}</td>
          <td>{animal.specie}</td>
          <td>
            <button on:click={() => populateForm(animal)}>Edit</button>
            <button on:click={() => deleteAnimal(animal.id)}>Delete</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>
