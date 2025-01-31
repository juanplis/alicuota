<template>
  <div id="app" class="max-w-md mx-auto p-6 bg-gradient-to-r from-blue-500 to-green-500 rounded-lg shadow-lg mt-10">
    <h2 class="text-2xl font-bold text-white mb-4 text-center">Calculadora Alícuota INEA</h2>
    <form @submit.prevent="calcularYMostrarResultado" class="bg-white p-4 rounded-lg shadow-md">
      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="uab">Tasa del día (BCV)</label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
               v-model="BCV" type="text" required @input="validarDecimales('BCV')" placeholder="Ej: 1234.56">
      </div>

      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="uab">Unidad de Arqueo Bruto (UAB)</label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
               v-model="uab" type="text" required @input="validarDecimales('uab')" placeholder="Ej: 1234.56">
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
          <option value="puerto_sucre">Puerto Sucre</option>
          <option value="la_vela_de_coro">La Vela de Coro</option>
        </select>
      </div>

      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="maniobras">Cantidad de Maniobras</label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
               v-model="maniobras" type="text"  @input="validarDecimales('maniobras')" required  placeholder="Ej:1">
      </div>

      <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition duration-300">
        Calcular
      </button>
    </form>
    
    <div v-if="resultado" class="mt-6 bg-white p-4 rounded-lg shadow-md">
      <h2 class="text-lg font-bold mb-4 text-gray-800">Monto a cancelar Alícuota</h2>
      <div v-if="uab <= 500">
        <p class="text-gray-700">Total (Euros): {{ resultado.totalEuros }}</p>
        <p class="text-gray-700">Total (Bolivares): {{ resultado.bs }}</p>
        <h2 class="text-lg font-bold mb-4 text-gray-800">Monto a cancelar Pilotaje</h2>
        <p class="text-gray-700">Total Pilotaje (Euros): {{ resultado.totalppilotaje }}</p>
        <p class="text-gray-700">Total Pilotaje (Bolivares): {{ resultado.totalppilotajeVES }}</p>
        <h2 class="text-lg font-bold mb-4 text-gray-800">Costo Lanchaje</h2>
        <p class="text-gray-700">total Lanchaje (Euros): {{ resultado.totallanchaje }}</p>
        <p class="text-gray-700">total Lanchaje (Bolivares): {{ resultado.totallanchajeVES }}</p>
      </div>
      <div v-else>
        <p class="text-gray-700">Total (Euros): {{ resultado.totalEuros }}</p>
        <p class="text-gray-700">Total (Bolivares): {{ resultado.totalFinal }}</p>
        <h2 class="text-lg font-bold mb-4 text-gray-800">Monto a cancelar Pilotaje</h2>
        <p class="text-gray-700">Total Pilotaje (Euros): {{ resultado.totalppilotaje }}</p>
        <p class="text-gray-700">Total Pilotaje (Bolivares): {{ resultado.totalppilotajeVES }}</p>
        <h2 class="text-lg font-bold mb-4 text-gray-800">Costo Lanchaje</h2>
        <p class="text-gray-700">total Lanchaje (Euros): {{ resultado.totallanchaje }}</p>
        <p class="text-gray-700">total Lanchaje (Bolivares): {{ resultado.totallanchajeVES }}</p>
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
      
      pilotajeE:{
      a: 	817.00,
      b:	1058.00,
      c:	1543.00,
      d:	3145.00,
      e:	3266.00,
      f:	3448.00,
      g:	3719.00,
      h:	3810.00,
      i:	4052.00,
      j:	4263.00,
      k:	4506.00,
      l:	5080.00,
      m:	5353.00,
      n:	5533.00,
      o:	5776,
      p:	5866,
      },

      pilotajeV:{
      a: 	204.00,
      b:	265.00,
      c:	383.00,
      d:	786.00,
      e:	816.00,
      f:	862.00,
      g:	930.00,
      h:	953.00,
      i:	1013.00,
      j:	1066.00,
      k:	1126.00,
      l:	1270.00,
      m:	1338.00,
      n:	1383.00,
      o:	1444.00,  
      p:  1466.00,
      },
      lanchajeE:{
        a:	574.00,
        b:	696.00,
        c:	756.00,
        d:	817.00,
        e:	897.00,
        f:	978.00,
        g:	1058.00,
        h:	1139.00,
        i:	1220.00,
        j:	1300.00,
        k:	1381.00,
        l:	1462.00,
        m:	1543.00,
        n:	1623.00,
        o:	1703.00,
        p:	1784.00,

      },
      lanchajeV:{
        a:	172.00,
        b:	209.00,
        c:	227.00,
        d:	245.00,
        e:	269.00,
        f:	293.00,
        g:	318.00,
        h:	342.00,
        i:	366.00,
        j:	390.00,
        k:	414.00,
        l:	439.00,
        m:	463.00,
        n:	487.00,
        o:	511.00,
        p:	535.00,

      },

      tc: 4.60,
      ut: 242.02,
      maniobras: "",
    };
  },
  methods: {
  validarDecimales(campo) {
    const valor = this[campo];
    const regex = /^\d+(\.\d{0,2})?$/; // Permite hasta dos decimales

    if (!regex.test(valor)) {
      this[campo] = valor.slice(0, -1); // Elimina el último carácter no válido
    }
  },


  /* ********** Mostrar resultados  ***********  */


  calcularYMostrarResultado() {
  // Calcula los resultados básicos
  if (this.uab <= 500) {
    this.resultado = this.manejarMenorIgual500(this.uab, this.bandera);
  } else {
    this.resultado = this.manejarMayor501(this.uab, this.bandera);
  }

  // Llama a CalculaPilotaje y añade el resultado
  const pilotajeResult = this.CalculaPilotaje();
  this.resultado.totalppilotaje = pilotajeResult.totalppilotaje; // Agregar el total de pilotaje
  //this.totalppilotajeVES = pilotajeResult.totalppilotajeVES;
  this.resultado.totalppilotajeVES = pilotajeResult.totalppilotajeVES; // Asegúrate de que estás asignando correctamente

  
    const lanchajeResult = this.CalculaLanchaje();
    this.resultado.totallanchaje = lanchajeResult.totallanchaje;
    this.resultado.totallanchajeVES = lanchajeResult.totallanchajeVES;



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
  },


      /* ********************* Calculador de Pilotaje ********************* */

  CalculaPilotaje() {
  let costoPilotaje = 0;

  if (this.bandera === "extranjera") {
    costoPilotaje = this.CalculaPilotajeE(this.uab);
  } else if (this.bandera === "venezolana") {
    costoPilotaje = this.CalculaPilotajeV(this.uab);
  }

    const totalppilotaje = this.maniobras * costoPilotaje.totalppilotaje;
    const totalppilotajeVES = this.BCV * totalppilotaje;

    return {
      totalppilotaje: totalppilotaje.toFixed(2),
      totalppilotajeVES: totalppilotajeVES.toFixed(2),
  };
},

  CalculaPilotajeE(uab) {
    let costoPilotaje = 0;

    // Lógica para calcular el costo de pilotaje para bandera extranjera
    if (uab >= 0 && uab <= 2000) {
        costoPilotaje = this.pilotajeE.a;
    } else if (uab >= 2001 && uab <= 5000) {
        costoPilotaje = this.pilotajeE.b;
    } else if (uab >= 5001 && uab <= 10000) {
        costoPilotaje = this.pilotajeE.c;
    } else if (uab >= 10001 && uab <= 15000) {
        costoPilotaje = this.pilotajeE.d;
    } else if (uab >= 15001 && uab <= 20000) {
        costoPilotaje = this.pilotajeE.e;
    } else if (uab >= 20001 && uab <= 25000) {
        costoPilotaje = this.pilotajeE.f;
    } else if (uab >= 25001 && uab <= 30000) {
        costoPilotaje = this.pilotajeE.g;
    } else if (uab >= 30001 && uab <= 35000) {
        costoPilotaje = this.pilotajeE.h;
    } else if (uab >= 35001 && uab <= 40000) {
        costoPilotaje = this.pilotajeE.i;
    } else if (uab >= 40001 && uab <= 45000) {
        costoPilotaje = this.pilotajeE.j;
    } else if (uab >= 45001 && uab <= 50000) {
        costoPilotaje = this.pilotajeE.k;
    } else if (uab >= 50001 && uab <= 70000) {
        costoPilotaje = this.pilotajeE.l;
    }else if (uab >= 70001  && uab <= 100000) {
        costoPilotaje = this.pilotajeE.m;
    }else if (uab >= 100001  && uab <= 125000) {
        costoPilotaje = this.pilotajeE.n;
    }else if (uab >= 125001  && uab <= 150000) {
        costoPilotaje = this.pilotajeE.o;
    }else if (uab >= 150001) {
        costoPilotaje = this.pilotajeE.p;
    }
      const totalppilotaje = this.maniobras * costoPilotaje ; 
     
      return {
          totalppilotaje: totalppilotaje.toFixed(2),
         
      };

  },

  CalculaPilotajeV(uab) {
    let costoPilotaje = 0;

    // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 2000) {
        costoPilotaje = this.pilotajeV.a;
    } else if (uab >= 2001 && uab <= 5000) {
        costoPilotaje = this.pilotajeV.b;
    } else if (uab >= 5001 && uab <= 10000) {
        costoPilotaje = this.pilotajeV.c;
    } else if (uab >= 10001 && uab <= 15000) {
        costoPilotaje = this.pilotajeV.d;
    } else if (uab >= 15001 && uab <= 20000) {
        costoPilotaje = this.pilotajeV.e;
    } else if (uab >= 20001 && uab <= 25000) {
        costoPilotaje = this.pilotajeV.f;
    } else if (uab >= 25001 && uab <= 30000) {
        costoPilotaje = this.pilotajeV.g;
    } else if (uab >= 30001 && uab <= 35000) {
        costoPilotaje = this.pilotajeV.h;
    } else if (uab >= 35001 && uab <= 40000) {
        costoPilotaje = this.pilotajeV.i;
    } else if (uab >= 40001 && uab <= 45000) {
        costoPilotaje = this.pilotajeV.j;
    } else if (uab >= 45001 && uab <= 50000) {
        costoPilotaje = this.pilotajeV.k;
    } else if (uab >= 50001 && uab <= 70000) {
        costoPilotaje = this.pilotajeV.l;
    }else if (uab >= 70001  && uab <= 100000) {
        costoPilotaje = this.pilotajeV.m;
    }else if (uab >= 100001  && uab <= 125000) {
        costoPilotaje = this.pilotajeV.n;
    }else if (uab >= 125001  && uab <= 150000) {
        costoPilotaje = this.pilotajeV.o;
    }else if (uab >= 150001) {
        costoPilotaje = this.pilotajeV.p;
    }
      const totalppilotaje = this.maniobras * costoPilotaje ; // Ajusta esto
      
      return {
          totalppilotaje: totalppilotaje.toFixed(2),
          
      };

  },



    /*aquí se encuentra el calculador de Lanchaje*/ 
    /* ******************************************* */





  
    CalculaLanchaje() {
    let costolanchaje = 0;

    if (this.bandera === "extranjera") {
        costolanchaje = this.CalculaLanchajeE(this.uab);
    } else if (this.bandera === "venezolana") {
        costolanchaje = this.CalculaLanchajeV(this.uab);
    }

    const totallanchaje = this.maniobras * costolanchaje.totallanchaje;
    const totallanchajeVES = this.BCV * totallanchaje; // Si esta línea no se usa, considera eliminarla

    return {
        totallanchaje: totallanchaje.toFixed(2),
        totallanchajeVES: totallanchajeVES.toFixed(2), // Descomentar si es necesario
    };
},
  CalculaLanchajeE(uab){
    let costolanchaje = 0;
       // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 2000) {
      costolanchaje = this.lanchajeE.a;
    } else if (uab >= 2001 && uab <= 5000) {
      costolanchaje = this.lanchajeE.b;
    } else if (uab >= 5001 && uab <= 10000) {
      costolanchaje = this.lanchajeE.c;
    } else if (uab >= 10001 && uab <= 15000) {
      costolanchaje = this.lanchajeE.d;
    } else if (uab >= 15001 && uab <= 20000) {
      costolanchaje = this.lanchajeE.e;
    } else if (uab >= 20001 && uab <= 25000) {
      costolanchaje = this.lanchajeE.f;
    } else if (uab >= 25001 && uab <= 30000) {
      costolanchaje = this.lanchajeE.g;
    } else if (uab >= 30001 && uab <= 35000) {
      costolanchaje = this.lanchajeE.h;
    } else if (uab >= 35001 && uab <= 40000) {
      costolanchaje = this.lanchajeE.i;
    } else if (uab >= 40001 && uab <= 45000) {
      costolanchaje = this.lanchajeE.j;
    } else if (uab >= 45001 && uab <= 50000) {
      costolanchaje = this.lanchajeE.k;
    } else if (uab >= 50001 && uab <= 70000) {
      costolanchaje = this.lanchajeE.l;
    }else if (uab >= 70001  && uab <= 100000) {
      costolanchaje = this.lanchajeE.m;
    }else if (uab >= 100001  && uab <= 125000) {
      costolanchaje = this.lanchajeE.n;
    }else if (uab >= 125001  && uab <= 150000) {
      costolanchaje = this.lanchajeE.o;
    }else if (uab >= 150001) {
      costolanchaje = this.lanchajeE.p;
    }
      const totallanchaje = this.maniobras * costolanchaje ; // Ajusta esto
      
      return {
        totallanchaje: totallanchaje.toFixed(2),
      };

  },
  CalculaLanchajeV(uab){
    let costolanchaje = 0;
       // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 2000) {
      costolanchaje = this.lanchajeV.a;
    } else if (uab >= 2001 && uab <= 5000) {
      costolanchaje = this.lanchajeV.b;
    } else if (uab >= 5001 && uab <= 10000) {
      costolanchaje = this.lanchajeV.c;
    } else if (uab >= 10001 && uab <= 15000) {
      costolanchaje = this.lanchajeV.d;
    } else if (uab >= 15001 && uab <= 20000) {
      costolanchaje = this.lanchajeV.e;
    } else if (uab >= 20001 && uab <= 25000) {
      costolanchaje = this.lanchajeV.f;
    } else if (uab >= 25001 && uab <= 30000) {
      costolanchaje = this.lanchajeV.g;
    } else if (uab >= 30001 && uab <= 35000) {
      costolanchaje = this.lanchajeV.h;
    } else if (uab >= 35001 && uab <= 40000) {
      costolanchaje = this.lanchajeV.i;
    } else if (uab >= 40001 && uab <= 45000) {
      costolanchaje = this.lanchajeV.j;
    } else if (uab >= 45001 && uab <= 50000) {
      costolanchaje = this.lanchajeV.k;
    } else if (uab >= 50001 && uab <= 70000) {
      costolanchaje = this.lanchajeV.l;
    }else if (uab >= 70001  && uab <= 100000) {
      costolanchaje = this.lanchajeV.m;
    }else if (uab >= 100001  && uab <= 125000) {
      costolanchaje = this.lanchajeV.n;
    }else if (uab >= 125001  && uab <= 150000) {
      costolanchaje = this.lanchajeV.o;
    }else if (uab >= 150001) {
      costolanchaje = this.lanchajeV.p;
    }
      const totallanchaje = this.maniobras * costolanchaje ; // Ajusta esto
      
      return {
        totallanchaje: totallanchaje.toFixed(2),
      };

  },
  },
  mounted() {
    this.obtenerBCV(); // Llamar al método para obtener la tasa al montar el componente
  }
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>
