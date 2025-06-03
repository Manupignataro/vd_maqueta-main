<script>
  import MonoViewer from './MonoViewer.svelte';
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
  let ultimo = null;
  let porcentajeMensaje = "";

  const categorias = [
    { clave: 'amigos', icono: '🧑‍🤝‍🧑', nombre: 'Amigos', costo: 3 },
    { clave: 'pareja', icono: '❤️', nombre: 'Pareja', costo: 8 },
    { clave: 'deporte', icono: '🏃‍♂️', nombre: 'Deporte', costo: 2 },
    { clave: 'familia', icono: '👨‍👩‍👧‍👦', nombre: 'Familia', costo: 5 },
    { clave: 'estudiar', icono: '📚', nombre: 'Estudiar', costo: 10 },
    { clave: 'otra', icono: '✨', nombre: 'Otra', costo: 6 }
  ];

  onMount(() => {
    const guardado = localStorage.getItem("gastos");
    if (guardado) datos = JSON.parse(guardado);
  });

  function calcularPorcentaje(gastos) {
    const totalGastado = Object.entries(gastos).reduce((acc, [cat, cant]) => acc + cant * categorias.find(c => c.clave === cat).costo, 0);
    const maxCategoria = Object.entries(gastos).reduce((a, b) => b[1] > a[1] ? b : a)[0];
    const porcentaje = Math.round((gastos[maxCategoria] * categorias.find(c => c.clave === maxCategoria).costo) * 100 / totalGastado);

    let tipo = "";
    if (maxCategoria === 'familia') tipo = "familiero";
    else if (maxCategoria === 'amigos') tipo = "amiguero";
    else if (maxCategoria === 'pareja') tipo = "enamorado";
    else if (maxCategoria === 'deporte') tipo = "saludable";
    else if (maxCategoria === 'estudiar') tipo = "estudioso";
    else tipo = "curioso";

    return `Sos un ${tipo} en un ${porcentaje}%.`;
  }

  function agregarGasto() {
    const gastos = { amigos, pareja, deporte, familia, estudiar, otra };
    const total = Object.entries(gastos).reduce((acc, [cat, cant]) => acc + cant * categorias.find(c => c.clave === cat).costo, 0);
    if (total !== 45) {
      alert(`Debés gastar exactamente 45 monedas. Estás gastando ${total}.`);
      return;
    }

    const categoriaMax = Object.entries(gastos).reduce((a, b) => b[1] > a[1] ? b : a)[0];

    const nuevo = {
      nombre,
      edad: parseInt(edad),
      genero,
      viveSolo,
      categoria: categoriaMax
    };

    datos = [...datos, nuevo];
    ultimo = nuevo;
    porcentajeMensaje = calcularPorcentaje(gastos);
    localStorage.setItem("gastos", JSON.stringify(datos));

    nombre = "";
    edad = "";
    genero = "Hombre";
    viveSolo = "Sí";
    amigos = pareja = deporte = familia = estudiar = otra = 0;
  }

  function borrarDatos() {
    if (confirm("¿Borrar todos los datos?")) {
      datos = [];
      localStorage.removeItem("gastos");
      ultimo = null;
      porcentajeMensaje = "";
    }
  }
</script>

<h1>¿En qué gastás tu fin de semana?</h1>

<div class="categorias">
  <h2>Categorías disponibles (y su costo)</h2>
  {#each categorias as c}
    <span>{c.icono} {c.nombre} - {c.costo} monedas</span>
  {/each}
</div>

<div class="formulario">
  <h2>Completá tu información</h2>
  <label>Nombre:</label>
  <input bind:value={nombre}>

  <label>Edad:</label>
  <input type="number" bind:value={edad}>

  <label>Género:</label>
  <select bind:value={genero}>
    <option>Hombre</option>
    <option>Mujer</option>
    <option>Otro</option>
  </select>

  <label>¿Vivís solo/a?</label>
  <select bind:value={viveSolo}>
    <option>Sí</option>
    <option>No</option>
  </select>

  <h3>¿Cuántas veces gastarías en cada categoría?</h3>
  {#each categorias as c}
    <label>{c.icono} {c.nombre}</label>
    <input type="number" bind:value={eval(c.clave)} min="0">
  {/each}

  <button on:click={agregarGasto}>Agregar a la tabla</button>
  <button on:click={borrarDatos} class="borrar">Borrar datos</button>
</div>

{#if ultimo}
  <h2>Tu mono personalizado</h2>
  <MonoViewer {...ultimo} />
  <p style="text-align:center; margin-top:10px">{porcentajeMensaje}</p>
{/if}

<div class="referencias">
  <h3>Referencias:</h3>
  <ul>
    <li><strong>✔</strong>: Vive solo | <strong>❌</strong>: No vive solo</li>
    <li><strong>🟪 fondo violeta</strong>: Mujer | <strong>🟧 fondo naranja</strong>: Hombre</li>
    <li><strong>Letra en negrita</strong>: Mayor de edad | <strong>Letra normal</strong>: Menor de edad</li>
    <li><strong>Mono {categoria}</strong>: Atuendo según categoría más elegida</li>
  </ul>
</div>

<h2>Monos de otros usuarios</h2>
<div class="galeria">
  {#each datos.filter(d => d !== ultimo) as d}
    <MonoViewer {...d} />
  {/each}
</div>
