<script>
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
  let monedasGastadas = 0;
  let perfil = "";

  const iconos = {
    amigos: "🧑‍🤝‍🧑",
    pareja: "❤️",
    deporte: "🏃‍♂️",
    familia: "👨‍👩‍👧‍👦",
    estudiar: "📚",
    otra: "✨"
  };

  const costos = {
    amigos: 3,
    pareja: 8,
    deporte: 2,
    familia: 5,
    estudiar: 10,
    otra: 6
  };

  onMount(() => {
    const guardado = localStorage.getItem("gastos");
    if (guardado) {
      datos = JSON.parse(guardado);
    }
  });

  $: monedasGastadas = amigos * 3 + pareja * 8 + deporte * 2 + familia * 5 + estudiar * 10 + otra * 6;

  function calcularPerfil() {
    const cantidades = {
      familiero: familia * costos.familia,
      amiguero: amigos * costos.amigos,
      "pollera": pareja * costos.pareja,
      estudioso: estudiar * costos.estudiar,
      saludable: deporte * costos.deporte
    };

    const mayor = Object.entries(cantidades).reduce((a, b) => a[1] > b[1] ? a : b);
    const porcentaje = Math.round((mayor[1] / 45) * 100);

    perfil = `Sos un/a ${mayor[0]} (${porcentaje}%).`;
  }

  function agregarGasto() {
    if (monedasGastadas !== 45) {
      alert(`Debés gastar exactamente 45 monedas. Estás gastando ${monedasGastadas}.`);
      return;
    }

    calcularPerfil();

    const cantidades = { amigos, pareja, deporte, familia, estudiar, otra };
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

    nombre = "";
    edad = "";
    genero = "Hombre";
    viveSolo = "Sí";
    amigos = pareja = deporte = familia = estudiar = otra = 0;
    perfil = "";
  }

  function borrarDatos() {
    if (confirm("¿Estás seguro de que querés borrar todos los datos?")) {
      localStorage.removeItem("gastos");
      datos = [];
    }
  }

  function simboloGenero(g) {
    if (g === "Hombre") return "🚹";
    if (g === "Mujer") return "🚺";
    return "⚧️";
  }

  function simboloViveSolo(v) {
    return v === "Sí" ? "✅" : "❌";
  }
</script>

<h1 style="text-align:center; color:#336699">¿En qué gastás tu fin de semana?</h1>

<div class="categorias">
  <h2 style="text-align:center;">Categorías disponibles (y su costo)</h2>
  <span>🧑‍🤝‍🧑 Amigos - 3 monedas</span>
  <span>❤️ Pareja - 8 monedas</span>
  <span>🏃‍♂️ Deporte - 2 monedas</span>
  <span>👨‍👩‍👧‍👦 Familia - 5 monedas</span>
  <span>📚 Estudiar - 10 monedas</span>
  <span>✨ Otra - 6 monedas</span>
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

  <h3>¿Cuántas veces gastarías en cada categoría?</h3>

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

  <p><strong>Total gastado:</strong> {monedasGastadas} / 45 monedas</p>

  {#if perfil}
    <p style="margin-top: 10px;"><strong>{perfil}</strong></p>
  {/if}

  <button on:click={agregarGasto}>Agregar a la tabla</button>
  <button class="borrar" on:click={borrarDatos}>Borrar todos los datos</button>
</div>

<div class="referencias">
  <h2>📘 Referencias</h2>
  <ul>
    <li>✅: Vive solo/a &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ❌: No vive solo/a</li>
    <li>🚹: Hombre &nbsp;&nbsp; 🚺: Mujer &nbsp;&nbsp; ⚧️: Otro</li>
    <li>🟥 Celda roja: Género masculino</li>
    <li>🟪 Celda rosa: Género femenino</li>
    <li><strong>Texto en negrita</strong>: Mayor de edad (18+)</li>
    <li>🟩 Fondo verde: Vive solo/a</li>
    <li>🟥 Fondo rosado: No vive solo/a</li>
  </ul>
</div>

<div class="tabla">
  <h2>Gastos de los participantes</h2>
  <table>
    <thead>
      <tr>
        <th>Nombre</th>
        <th>Edad</th>
        <th>Género</th>
        <th>¿Vive solo/a?</th>
        <th>Distribución visual de gastos</th>
      </tr>
    </thead>
    <tbody>
      {#each datos as d}
        <tr>
          <td class={`tipografia ${d.genero === 'Hombre' ? 'genero-hombre' : d.genero === 'Mujer' ? 'genero-mujer' : ''}`}>{d.nombre}</td>
          <td class={d.edad >= 18 ? 'mayor' : ''}>{d.edad}</td>
          <td class={d.genero === 'Hombre' ? 'genero-hombre' : d.genero === 'Mujer' ? 'genero-mujer' : ''}>{simboloGenero(d.genero)}</td>
          <td class={d.viveSolo === 'Sí' ? 'vive-solo' : 'vive-no'}>{simboloViveSolo(d.viveSolo)}</td>
          <td>{d.gastoVisual}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>
