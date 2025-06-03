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
    amigos: "🧑‍🧑",
    pareja: "❤️",
    deporte: "🏃‍♂️",
    familia: "👨‍👩‍👦",
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

  $: monedasGastadas =
    amigos * costos.amigos +
    pareja * costos.pareja +
    deporte * costos.deporte +
    familia * costos.familia +
    estudiar * costos.estudiar +
    otra * costos.otra;

  function calcularPerfil() {
    const cantidades = {
      Familiero: familia * costos.familia,
      Amiguero: amigos * costos.amigos,
      Romantico: pareja * costos.pareja,
      Estudioso: estudiar * costos.estudiar,
      Deportista: deporte * costos.deporte
    };
    const mayor = Object.entries(cantidades).reduce((a, b) => (a[1] > b[1] ? a : b));
    const porcentaje = Math.round((mayor[1] / 45) * 100);
    perfil = `Sos un/a ${mayor[0]} (${porcentaje}%).`;
    return mayor[0];
  }

  function obtenerRutaImagen(d) {
    const color = d.edad >= 18 ? 'N' : 'V';
    const arito = d.viveSolo === 'Sí' ? 'A' : '';
    const gorro = d.edad >= 18 ? 'G' : '';
    const categoria = d.tipoMono;
    return `/src/visualizacion/imagenes/mono${color}${arito}${gorro}${categoria}.png`;
  }

  function agregarGasto() {
    if (monedasGastadas !== 45) {
      alert(`Debés gastar exactamente 45 monedas. Estás gastando ${monedasGastadas}.`);
      return;
    }

    const tipoMono = calcularPerfil();
    let gastoVisual = "";
    const cantidades = { amigos, pareja, deporte, familia, estudiar, otra };
    for (let key in cantidades) {
      gastoVisual += iconos[key].repeat(cantidades[key]);
    }

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

<h1 class="titulo">¿En qué gastás tu fin de semana?</h1>

<!-- (formulario, tabla y referencias siguen igual) -->

<div class="mono-principal">
  <h2>Tu mono NFT personalizado</h2>
  {#if datos.length > 0}
    <div class="mono-imagen">
      <img src={obtenerRutaImagen(datos[datos.length - 1])} alt="Mono NFT" class="mono-img" />
    </div>
    <p><strong>{datos[datos.length - 1].nombre}</strong> - Mono {datos[datos.length - 1].tipoMono}</p>
  {/if}
</div>

<div class="monos-galeria">
  {#each datos.slice(0, -1) as d}
    <div class="mono-galeria">
      <img src={obtenerRutaImagen(d)} alt="Mono NFT" class="mono-img" />
      <p><strong>{d.nombre}</strong><br />Mono {d.tipoMono}</p>
    </div>
  {/each}
</div>
