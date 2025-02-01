<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import Form from "./components/Form.svelte";
  import Table from "./components/Table.svelte";

  const animals = writable([]);
  const apiUrl = "https://backside-svelte-1.onrender.com/api";

  let form = writable({
    name: "",
    age: "",
    specie: ""
  });

  let selectedAnimal = writable(null);

  const fetchAnimals = async () => {
    try {
      const response = await fetch(apiUrl);
      const data = await response.json();
      animals.set(data.data);
    } catch (error) {
      console.error("Error fetching animals:", error);
    }
  };

  const addAnimal = async () => {
    try {
      const response = await fetch(apiUrl, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify($form)
      });

      if (response.ok) {
        fetchAnimals();
        form.set({ name: "", age: "", specie: "" });
      }
    } catch (error) {
      console.error("Error adding animal:", error);
    }
  };

  const editAnimal = async () => {
    if (!$selectedAnimal) return;
    
    try {
      const response = await fetch(`${apiUrl}/${$selectedAnimal.id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify($form)
      });

      if (response.ok) {
        fetchAnimals();
        selectedAnimal.set(null);
        form.set({ name: "", age: "", specie: "" });
      }
    } catch (error) {
      console.error("Error editing animal:", error);
    }
  };

  const deleteAnimal = async (id) => {
    try {
      const response = await fetch(`${apiUrl}/${id}`, { method: "DELETE" });
      if (response.ok) fetchAnimals();
    } catch (error) {
      console.error("Error deleting animal:", error);
    }
  };

  const populateForm = (animal) => {
    selectedAnimal.set(animal);
    form.set({ ...animal });
  };

  onMount(fetchAnimals);
</script>

<div class="container">
  <h2>Please wait 50 seconds before making the next request.</h2>
  <h1>Animal Management</h1>
  <Form 
    {addAnimal}
    {editAnimal}
    bind:form={$form}
    bind:selectedAnimal={$selectedAnimal}
  />

  <h2>Animals List</h2>

  <Table 
    animals={$animals}
    onDelete={deleteAnimal}
    onEdit={populateForm}
  />
</div>
