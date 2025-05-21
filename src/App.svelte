
<script>
  import './app.css';
  import { onMount } from 'svelte';

  let nombre = "";
  let edad = "";
  let genero = "Hombre";
  let viveSolo = "Sí";
  let amigos = 0;
  let pareja = 0;
  let deporte = 0;
  let familia = 0;
  let estudiar = 0;
  let otra = 0;

  let datos = [];

  const iconos = {
    amigos: "🧑‍🤝‍🧑",
    pareja: "❤️",
    deporte: "🏃‍♂️",
    familia: "👨‍👩‍👧‍👦",
    estudiar: "📚",
    otra: "✨"
  };

  onMount(() => {
    const guardado = localStorage.getItem("gastos");
    if (guardado) {
      datos = JSON.parse(guardado);
    }
  });

  function agregarGasto() {
    const cantidades = { amigos, pareja, deporte, familia, estudiar, otra };
    const total = amigos * 3 + pareja * 8 + deporte * 2 + familia * 5 + estudiar * 10 + otra * 6;

    if (total !== 45) {
      alert(`Debés gastar exactamente 45 pesos. Estás gastando ${total}.`);
      return;
    }

    let gastoVisual = "";
    for (let key in cantidades) {
      gastoVisual += iconos[key].repeat(cantidades[key]);
    }

    const nuevoDato = {
      nombre,
      edad: parseInt(edad),
      genero,
      viveSolo,
      gastoVisual
    };

    datos = [...datos, nuevoDato];
    localStorage.setItem("gastos", JSON.stringify(datos));

    // Limpiar formulario
    nombre = "";
    edad = "";
    genero = "Hombre";
    viveSolo = "Sí";
    amigos = pareja = deporte = familia = estudiar = otra = 0;
  }

  function borrarDatos() {
    if (confirm("¿Estás seguro de que querés borrar todos los datos?")) {
      localStorage.removeItem("gastos");
      datos = [];
    }
  }
</script>

<h1 style="text-align:center; color:#336699">¿En qué gastás tu fin de semana?</h1>

<div class="categorias">
  <h2 style="text-align:center;">Categorías disponibles (y su costo)</h2>
  <span>🧑‍🤝‍🧑 Amigos - 3 pesos</span>
  <span>❤️ Pareja - 8 pesos</span>
  <span>🏃‍♂️ Deporte - 2 pesos</span>
  <span>👨‍👩‍👧‍👦 Familia - 5 pesos</span>
  <span>📚 Estudiar - 10 pesos</span>
  <span>✨ Otra - 6 pesos</span>
</div>

<div class="formulario">
  <h2>Completá tu información</h2>

  <label>Nombre:</label>
  <input bind:value={nombre} required>

  <label>Edad:</label>
  <input type="number" bind:value={edad} required>

  <label>Género:</label>
  <select bind:value={genero}>
    <option value="Hombre">Hombre</option>
    <option value="Mujer">Mujer</option>
    <option value="Otro">Otro</option>
  </select>

  <label>¿Vivís solo/a?</label>
  <select bind:value={viveSolo}>
    <option value="Sí">Sí</option>
    <option value="No">No</option>
  </select>

  <label>🧑‍🤝‍🧑 Amigos</label>
  <input type="number" bind:value={amigos} min="0">

  <label>❤️ Pareja</label>
  <input type="number" bind:value={pareja} min="0">

  <label>🏃‍♂️ Deporte</label>
  <input type="number" bind:value={deporte} min="0">

  <label>👨‍👩‍👧‍👦 Familia</label>
  <input type="number" bind:value={familia} min="0">

  <label>📚 Estudiar</label>
  <input type="number" bind:value={estudiar} min="0">

  <label>✨ Otra</label>
  <input type="number" bind:value={otra} min="0">

  <button on:click={agregarGasto}>Agregar a la tabla</button>
  <button class="borrar" on:click={borrarDatos}>Borrar todos los datos</button>
</div>

<div class="tabla">
  <h2>Gastos de los participantes</h2>
  <table>
    <thead>
      <tr>
        <th>Nombre</th>
        <th>Edad</th>
        <th>Género</th>
        <th>Vive solo</th>
        <th>Distribución visual de gastos</th>
      </tr>
    </thead>
    <tbody>
      {#each datos as d}
        <tr>
          <td class={`tipografia ${d.genero === 'Hombre' ? 'genero-hombre' : d.genero === 'Mujer' ? 'genero-mujer' : ''}`}>{d.nombre}</td>
          <td class={d.edad >= 18 ? 'mayor' : ''}>{d.edad}</td>
          <td class={d.genero === 'Hombre' ? 'genero-hombre' : d.genero === 'Mujer' ? 'genero-mujer' : ''}>{d.genero}</td>
          <td class={d.viveSolo === 'Sí' ? 'vive-solo' : 'vive-no'}>{d.viveSolo}</td>
          <td>{d.gastoVisual}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>
