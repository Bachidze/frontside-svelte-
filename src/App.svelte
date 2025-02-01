<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import Form from "./components/Form.svelte";
  import Table from "./components/Table.svelte";

  const animals = writable([]);
  const apiUrl = "https://backside-svelte-1.onrender.com/api";

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


<div class="container">
  <h1>Animal Management</h1>
  <Form 
  addAnimal={addAnimal}
  editAnimal={editAnimal}
  bind:form
  selectedAnimal={selectedAnimal}
  />

  <h2>Animals List</h2>

  <Table 
  animals={$animals}
  onDelete={deleteAnimal}
  onEdit={editAnimal}
  />
</div>
