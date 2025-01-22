<script>
    export let animals = [];
    export let onEdit
    export let onDelete
  </script>
  
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
      {#each animals as animal (animal.id)}
        <tr>
          <td>{animal.id}</td>
          <td>{animal.name}</td>
          <td>{animal.age}</td>
          <td>{animal.specie}</td>
          <td>
            <button on:click={() => onEdit(animal)}>Edit</button>
            <button on:click={() => onDelete(animal.id)}>Delete</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  
  <style>
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
  