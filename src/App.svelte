<!-- src/App.svelte -->
<script>
  import { onMount } from 'svelte';

  // Datos del formulario
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

  // Array donde guardamos todas las entradas
  let datos = [];
  let monedasGastadas = 0;
  let perfil = "";

  // Iconos para la visualización de gastos
  const iconos = {
    amigos: "🧑‍🧑",
    pareja: "❤️",
    deporte: "🏃‍♂️",
    familia: "👨‍👩‍👦",
    estudiar: "📚",
    otra: "✨"
  };

  // Costos por categoría
  const costos = {
    amigos: 3,
    pareja: 8,
    deporte: 2,
    familia: 5,
    estudiar: 10,
    otra: 6
  };

  // Al montar el componente, cargamos del LocalStorage (si existe)
  onMount(() => {
    const guardado = localStorage.getItem("gastos");
    if (guardado) {
      datos = JSON.parse(guardado);
    }
  });

  // Recalcular el total gastado cada vez que cambien las cantidades
  $: monedasGastadas =
    amigos * costos.amigos +
    pareja * costos.pareja +
    deporte * costos.deporte +
    familia * costos.familia +
    estudiar * costos.estudiar +
    otra * costos.otra;

  // Calcula qué perfil (categoría) es el que más gastó y arma el string "perfil"
  function calcularPerfil() {
    const cantidades = {
      Familiero: familia * costos.familia,
      Amiguero: amigos * costos.amigos,
      Romantico: pareja * costos.pareja,
      Estudioso: estudiar * costos.estudiar,
      Deportista: deporte * costos.deporte
    };
    // Encuentra la categoría con mayor gasto
    const mayor = Object.entries(cantidades).reduce((a, b) =>
      a[1] > b[1] ? a : b
    );
    const porcentaje = Math.round((mayor[1] / 45) * 100);
    perfil = `Sos un/a ${mayor[0]} (${porcentaje}%).`;
    return mayor[0];
  }

  // Construye la ruta de la imagen del mono según edad, viveSolo, perfil
  function obtenerRutaImagen(d) {
  const color     = d.edad >= 18 ? "N" : "V";       // N = naranja, V = violeta
  const arito     = d.viveSolo === "Sí" ? "A" : ""; // A = con arito, "" = sin arito
  const gorro     = d.edad >= 18 ? "G" : "";        // G = con gorro, "" = sin gorro
  const categoria = d.tipoMono;                     // “Amiguero”, “Familiero”, etc.

  // AHORA PIDE TODO DESDE public/imagenes
  // El slash inicial hace que el navegador busque en la carpeta “public/” de tu proyecto.
  return `/imagenes/mono${color}${arito}${gorro}${categoria}.png`;
}


  // Cuando el usuario hace clic en "Agregar a la tabla"
  function agregarGasto() {
    if (monedasGastadas !== 45) {
      alert(`Debés gastar exactamente 45 monedas. Estás gastando ${monedasGastadas}.`);
      return;
    }

    const tipoMono = calcularPerfil();

    // Arma la cadena visual de emojis
    let gastoVisual = "";
    const cantidades = { amigos, pareja, deporte, familia, estudiar, otra };
    for (let key in cantidades) {
      gastoVisual += iconos[key].repeat(cantidades[key]);
    }

    // Creamos un objeto con todos los datos de esta persona
    const nuevoDato = {
      nombre,
      edad: parseInt(edad),
      genero,
      viveSolo,
      gastoVisual,
      perfil,
      tipoMono,
      amigos,
      pareja,
      deporte,
      familia,
      estudiar,
      otra
    };

    // Lo agregamos al array y guardamos en LocalStorage
    datos = [...datos, nuevoDato];
    localStorage.setItem("gastos", JSON.stringify(datos));

    // Reseteamos el formulario
    nombre = "";
    edad = "";
    genero = "Hombre";
    viveSolo = "Sí";
    amigos = pareja = deporte = familia = estudiar = otra = 0;
    perfil = "";
  }

  // Borra todos los datos guardados (y LocalStorage)
  function borrarDatos() {
    if (confirm("¿Estás seguro de que querés borrar todos los datos?")) {
      localStorage.removeItem("gastos");
      datos = [];
    }
  }

  // Iconos para género y si vive solo
  function simboloGenero(g) {
    if (g === "Hombre") return "🚹";
    if (g === "Mujer") return "🚺";
    return "⚧️";
  }
  function simboloViveSolo(v) {
    return v === "Sí" ? "✅" : "❌";
  }
</script>

<!-- Importamos el CSS completo: -->
<style src="./visualizacion/estilos.css"></style>

<!-- 1. Título -->
<h1 class="titulo">¿En qué gastás tu fin de semana?</h1>

<!-- 2. Sección de categorías y sus costos -->
<div class="categorias">
  <h2>Categorías disponibles (y su costo)</h2>
  <span>🧑‍🤝‍🧑 Amigos – 3 monedas</span>
  <span>❤️ Pareja – 8 monedas</span>
  <span>🏃‍♂️ Deporte – 2 monedas</span>
  <span>👨‍👩‍👧‍👦 Familia – 5 monedas</span>
  <span>📚 Estudiar – 10 monedas</span>
  <span>✨ Otra – 6 monedas</span>
</div>

<!-- 3. Formulario de carga -->
<div class="formulario">
  <h2>Completá tu información</h2>

  <label>Nombre:</label>
  <input type="text" bind:value={nombre} placeholder="Tu nombre" required />

  <label>Edad:</label>
  <input type="number" bind:value={edad} min="0" placeholder="Tu edad" required />

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
  <input type="number" bind:value={amigos} min="0" />

  <label>❤️ Pareja</label>
  <input type="number" bind:value={pareja} min="0" />

  <label>🏃‍♂️ Deporte</label>
  <input type="number" bind:value={deporte} min="0" />

  <label>👨‍👩‍👧‍👦 Familia</label>
  <input type="number" bind:value={familia} min="0" />

  <label>📚 Estudiar</label>
  <input type="number" bind:value={estudiar} min="0" />

  <label>✨ Otra</label>
  <input type="number" bind:value={otra} min="0" />

  <!-- Mostramos total gastado y perfil si ya está calculado -->
  <p><strong>Total gastado:</strong> {monedasGastadas} / 45 monedas</p>
  {#if perfil}
    <p><strong>{perfil}</strong></p>
  {/if}

  <button on:click={agregarGasto}>Agregar a la tabla</button>
  <button class="borrar" on:click={borrarDatos}>Borrar todos los datos</button>
</div>

<!-- 4. Sección de referencias visuales -->
<div class="referencias">
  <h2>📘 Referencias</h2>
  <ul>
    <li>✅ : Vive solo/a  ❌ : No vive solo/a</li>
    <li>🚹 : Hombre  🚺 : Mujer  ⚧️ : Otro</li>
    <li><strong>Texto en negrita</strong> : Mayor de edad</li>
    <li>🟩 Fondo verde: Vive solo/a  🟥 Fondo rosado: No vive solo/a</li>
  </ul>
</div>

<!-- 5. Tabla con todos los participantes -->
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
          <td class={`tipografia ${d.genero === "Hombre" ? "genero-hombre" : d.genero === "Mujer" ? "genero-mujer" : ""}`}>
            {d.nombre}
          </td>
          <td class={d.edad >= 18 ? "mayor" : ""}>{d.edad}</td>
          <td class={d.genero === "Hombre" ? "genero-hombre" : d.genero === "Mujer" ? "genero-mujer" : ""}>
            {simboloGenero(d.genero)}
          </td>
          <td class={d.viveSolo === "Sí" ? "vive-solo" : "vive-no"}>
            {simboloViveSolo(d.viveSolo)}
          </td>
          <td>{d.gastoVisual}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>

<!-- 6. Sección: Mono NFT principal (el último ingresado) -->
<div class="mono-principal">
  <h2>Tu mono NFT personalizado</h2>
  {#if datos.length > 0}
    <div class="mono-imagen">
      <img
        src={obtenerRutaImagen(datos[datos.length - 1])}
        alt="Mono NFT"
        class="mono-img"
      />
    </div>
    <p>
      <strong>{datos[datos.length - 1].nombre}</strong> – Mono {datos[datos.length - 1].tipoMono}
    </p>
  {/if}
</div>

<!-- 7. Sección: Galería de monos (todos menos el último) -->
<div class="monos-galeria">
  {#each datos.slice(0, -1) as d}
    <div class="mono-galeria">
      <img src={obtenerRutaImagen(d)} alt="Mono NFT" class="mono-img" />
      <p><strong>{d.nombre}</strong><br />Mono {d.tipoMono}</p>
    </div>
  {/each}
</div>
