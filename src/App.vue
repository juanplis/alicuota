<template>
  <div id="app" class="max-w-md mx-auto p-6 bg-gradient-to-r from-blue-500 to-green-500 rounded-lg shadow-lg mt-10">
    <h2 class="text-2xl font-bold text-white mb-4 text-center">Calculadora  Alícuota INEA</h2>
    <form @submit.prevent="calcularYMostrarResultado" class="bg-white p-4 rounded-lg shadow-md">
      <div class="mb-4">
    <label class="block text-gray-800 text-sm font-bold mb-2" for="uab">Taza del día (BCV)</label>
    <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
           v-model="BCV" type="number" step="0.01" required>
</div>

      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="uab">Unidad de Arqueo Bruto (UAB)</label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
               v-model="uab" type="number" required>
      </div>
      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="bandera">Bandera</label>
        <select v-model="bandera" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300" required>
          <option value="">Seleccione bandera</option>
          <option value="venezolana">Venezolana</option>
          <option value="extranjera">Extranjera</option>
        </select>
      </div>
      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="puerto">Capitanía</label>
        <select v-model="puerto" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300" id="puerto" name="puerto" required>
          <option value="">Seleccione puerto</option>
          <option value="apure">Apure</option>
          <option value="guiria">Guiria</option>
          <option value="la_ceiba">La Ceiba</option>
          <option value="sede_central">Sede Central</option>
          <option value="la_guaira">La Guaira</option>
          <option value="ciudad_guayana">Ciudad Guayana</option>
          <option value="puerto_cabello">Puerto Cabello</option>
          <option value="las_piedras">Las Piedras</option>
          <option value="pampatar">Pampatar</option>
          <option value="puerto_la_cruz">Puerto la Cruz</option>
          <option value="ciudad_bolivar">Ciudad Bolívar</option>
          <option value="amazonas">Amazonas</option>
          <option value="carupano">Carúpano</option>
          <option value="maracaibo">Maracaibo</option>
          <option value="miranda">Miranda</option>
          <option value="caripito">Caripito</option>
          <option value="puerto_sucre">Puerto Sucre</option>
          <option value="la_vela_de_coro">La Vela de Coro</option>
        </select>
      </div>
      <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition duration-300">
        Calcular
      </button>
    </form>

    <div v-if="resultado" class="mt-6 bg-white p-4 rounded-lg shadow-md">
      <h2 class="text-lg font-bold mb-4 text-gray-800">Monto a cancelar Alícuota</h2>
      <div v-if="uab <= 500">
        <p class="text-gray-700">Total (VES): {{ resultado.bs }}</p>
        <p class="text-gray-700">Total (Euros): {{ resultado.totalEuros }}</p>
      </div>
      <div v-else>
        <p class="text-gray-700">Total (Euros): {{ resultado.totalEuros }}</p>
        <p class="text-gray-700">Total (Bolivares): {{ resultado.totalFinal }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      uab: null,
      bandera: '',
      puerto: '',
      resultado: null,
      BCV: '',
      alicuotas: {
        a: 1,
        b: 0.0045,
        c: 0.0040,
        d: 0.0035,
        e: 0.0030,
      },
      tc: 4.60,
      ut: 242.02
    };
  },
  methods: {
    calcularYMostrarResultado() {
      if (this.uab <= 500) {
        this.resultado = this.manejarMenorIgual500(this.uab, this.bandera);
      } else {
        this.resultado = this.manejarMayor501(this.uab, this.bandera);
      }
    },
    manejarMenorIgual500(uab, bandera) {
      let bs = 0;
      let totalEuros = 0;

      if (bandera === 'venezolana') {
        bs = 1315.00;
        totalEuros = 26.30;
      } else if (bandera === 'extranjera') {
        bs = 2630.50;
        totalEuros = 52.61;
      }

      return { bs: bs.toFixed(2), totalEuros: totalEuros.toFixed(2) };
    },
    manejarMayor501(uab, bandera) {
      let alicuota = 0;

      if (uab >= 501 && uab <= 5000) {
        alicuota = this.alicuotas.b;
      } else if (uab >= 5001 && uab <= 20000) {
        alicuota = this.alicuotas.c;
      } else if (uab >= 20001 && uab <= 40000) {
        alicuota = this.alicuotas.d;
      } else {
        alicuota = this.alicuotas.e;
      }

      const porcentaje = (bandera === 'venezolana') ? 0.5 : 1;
      const total = uab * this.ut * alicuota * porcentaje;
      const totalEuros = total / this.tc;
      const totalFinal = totalEuros * this.BCV;
      return {
        bs: total.toFixed(2),
        totalEuros: totalEuros.toFixed(2),
        totalFinal: totalFinal.toFixed(2)
      };
    }
  }
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>
