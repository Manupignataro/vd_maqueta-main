<script>
  import { onMount } from 'svelte';
  let nombre = '';
  let edad = '';
  let genero = 'Hombre';
  let viveSolo = 'Sí';
  let amigos = 0;
  let pareja = 0;
  let deporte = 0;
  let familia = 0;
  let estudiar = 0;
  let otra = 0;

  let gastos = [];

  const iconos = {
    amigos: "🧑‍🤝‍🧑",
    pareja: "❤️",
    deporte: "🏃‍♂️",
    familia: "👨‍👩‍👧‍👦",
    estudiar: "📚",
    otra: "✨"
  };

  onMount(() => {
    const data = localStorage.getItem('gastos');
    if (data) {
      gastos = JSON.parse(data);
    }
  });

  function agregarGasto() {
    const cantidades = { amigos, pareja, deporte, familia, estudiar, otra };

    const total = amigos * 3 + pareja * 8 + deporte * 2 +
                  familia * 5 + estudiar * 10 + otra * 6;

    if (total !== 45) {
      alert(`Debés gastar exactamente 45 pesos. Estás gastando ${total} pesos.`);
      return;
    }

    let gastoVisual = "";
    for (let categoria in cantidades) {
      gastoVisual += iconos[categoria].repeat(cantidades[categoria]);
    }

    const nuevo = {
      nombre,
      edad: parseInt(edad),
      genero,
      viveSolo,
      gastoVisual
    };

    gastos = [...gastos, nuevo];
    localStorage.setItem('gastos', JSON.stringify(gastos));

    nombre = '';
    edad = '';
    genero = 'Hombre';
    viveSolo = 'Sí';
    amigos = pareja = deporte = familia = estudiar = otra = 0;
  }

  function borrarDatos() {
    localStorage.removeItem('gastos');
    gastos = [];
  }

  function getNombreStyle(nombre) {
    return `font-family: 'Courier New', monospace;`;
  }

  function getEdadStyle(edad) {
    return edad >= 18 ? 'font-weight: bold;' : '';
  }

  function getGeneroStyle(genero) {
    return `background-color: ${genero === 'Mujer' ? 'pink' : genero === 'Hombre' ? 'red' : 'lightgray'};`;
  }

  function getViveSoloStyle(vive) {
    return `color: ${vive === 'Sí' ? 'green' : 'red'}; font-weight: bold;`;
  }
</script>

<h1>¿En qué querés gastar tu balance (monedas)?</h1>

<div class="formulario">
  <div class="categorias">
    <span>🧑‍🤝‍🧑 Amigos - 3 pesos</span>
    <span>❤️ Pareja - 8 pesos</span>
    <span>🏃‍♂️ Deporte - 2 pesos</span>
    <span>👨‍👩‍👧‍👦 Familia - 5 pesos</span>
    <span>📚 Estudiar - 10 pesos</span>
    <span>✨ Otra - 6 pesos</span>
  </div>

  <form on:submit|preventDefault={agregarGasto}>
    <input type="text" placeholder="Nombre" bind:value={nombre} required />
    <input type="number" placeholder="Edad" bind:value={edad} required />
    <select bind:value={genero}>
      <option>Hombre</option>
      <option>Mujer</option>
      <option>Otro</option>
    </select>
    <select bind:value={viveSolo}>
      <option>Sí</option>
      <option>No</option>
    </select>

    <input type="number" placeholder="Salidas con amigos" bind:value={amigos} />
    <input type="number" placeholder="Juntadas con pareja" bind:value={pareja} />
    <input type="number" placeholder="Deporte" bind:value={deporte} />
    <input type="number" placeholder="Tiempo en familia" bind:value={familia} />
    <input type="number" placeholder="Estudiar" bind:value={estudiar} />
    <input type="number" placeholder="Otra categoría (opcional)" bind:value={otra} />

    <button type="submit">Agregar</button>
    <button type="button" on:click={borrarDatos}>Borrar base de datos</button>
  </form>
</div>

<div class="tabla">
  <table>
    <thead>
      <tr>
        <th>Nombre</th>
        <th>Edad</th>
        <th>Género</th>
        <th>Vive solo</th>
        <th>Gasto visual</th>
      </tr>
    </thead>
    <tbody>
      {#each gastos as gasto}
        <tr>
          <td style={getNombreStyle(gasto.nombre)}>{gasto.nombre}</td>
          <td style={getEdadStyle(gasto.edad)}>{gasto.edad}</td>
          <td style={getGeneroStyle(gasto.genero)}>{gasto.genero}</td>
          <td style={getViveSoloStyle(gasto.viveSolo)}>{gasto.viveSolo}</td>
          <td>{gasto.gastoVisual}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>
