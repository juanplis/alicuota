<template>
  <div id="app" class="min-h-screen bg-gradient-to-r from-blue-500 to-green-500 flex flex-col items-center justify-center p-6">
    <h2 class="text-2xl font-bold text-white mb-4 text-center">Calculadora Alícuota INEA</h2>
    <form @submit.prevent="calcularYMostrarResultado" class="bg-white p-4 rounded-lg shadow-md w-full max-w-md">
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
          <option value="2">La Guaira</option>
        </select>
      </div>

      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="maniobras">Cantidad de Maniobras</label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300"
               v-model="maniobras" type="text" @input="validarDecimales('maniobras')" required placeholder="Ej:1">
      </div>

      <div class="mb-4">
        <label class="block text-gray-800 text-sm font-bold mb-2" for="bandera">Tipo de buque</label>
        <select v-model="tipo_buque" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline focus:ring focus:ring-blue-300" required>
          <option value="">Seleccione tipo de buque</option>
          <option value="pesca">Pesquero</option>
          <option value="carga">Carga</option>
          <option value="pasajeros">Pasajeros</option>
          <option value="recreo">Recreativo</option>
          <option value="deportivos">Deportivo</option>


        </select>
      </div>

      <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline transition duration-300">
        Calcular
      </button>
    </form>
    
    <div v-if="resultado" class="mt-6 bg-white p-4 rounded-lg shadow-md w-full max-w-3xl">
      <h2 class="text-lg font-bold mb-4 text-gray-800">Resultados</h2>
      <div class="flex flex-wrap justify-between">
        <div class="w-1/2 p-2">
          <h3 class="text-lg font-bold mb-2">Monto a cancelar Alícuota</h3>
          <p class="text-gray-700">Total (Euros): {{ resultado.totalEuros }}</p>
          <p class="text-gray-700">Total (Bolivares): {{ resultado.bs }}</p>
        </div>
        <div class="w-1/2 p-2">
          <h3 class="text-lg font-bold mb-2">Monto a cancelar Pilotaje</h3>
          <p class="text-gray-700">Total Pilotaje (Euros): {{ resultado.totalppilotaje }}</p>
          <p class="text-gray-700">Total Pilotaje (Bolivares): {{ resultado.totalppilotajeVES }}</p>
        </div>
        <div class="w-1/2 p-2">
          <h3 class="text-lg font-bold mb-2">Costo Lanchaje</h3>
          <p class="text-gray-700">Total Lanchaje (Euros): {{ resultado.totallanchaje }}</p>
          <p class="text-gray-700">Total Lanchaje (Bolivares): {{ resultado.totallanchajeVES }}</p>
        </div>
        <div class="w-1/2 p-2">
          <h3 class="text-lg font-bold mb-2">Costo Remolcador</h3>
          <p class="text-gray-700">Total Remolcador (Euros): {{ resultado.totalremolcador }}</p>
          <p class="text-gray-700">Total Remolcador (Bolivares): {{ resultado.totalremolcadorVES }}</p>
        </div>
        <div class="w-1/2 p-2">
          <h3 class="text-lg font-bold mb-2">Costo Zarpes</h3>
          <p class="text-gray-700">Total Zarpes (Euros): {{ resultado.totalzarpes }}</p>
          <p class="text-gray-700">Total Zarpes (Bolivares): {{ resultado.totalzarpesVES }}</p>
        </div>
            <div class="w-1/2 p-2">
        <h3 class="text-lg font-bold mb-2">Costo Inspección</h3>
        <p class="text-gray-700">Total Inspecciones (Euros): {{ resultado.totalinspecciones }}</p>
        <p class="text-gray-700">Total Inspecciones (Bolivares): {{ resultado.totalinspeccionesVES }}</p>
    </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tipo_buque:'',
      uab: null,
      bandera: '',
      puerto: '',
      resultado: null,
      BCV: '',
      tc: 4.60,
      ut: 242.02,
      maniobras: '',
      alicuotas: {
        a: 1,
        b: 0.0045,
        c: 0.0040,
        d: 0.0035,
        e: 0.0030,

      },
      
      pilotajeE:{
      a: 	571.90,
      b:	740.60,
      c:	1080.10,
      d:	2201.50,
      e:	2000.00,
      f:	2800.00,
      g:	3000.00,
      h:	3429.00,
      i:	3645.80,
      j:	3836.70,
      k:	4055.40,
      l:	4572.00,
      m:	5353.00,
      n:	5533.00,
      o:	5776,
      p:	5866,
      },

      pilotajeV:{
      a: 	142.80,
      b:	185.50,
      c:	270.20,
      d:	471.20,
      e:	550.20,
      f:	675.80,
      g:	737.00,
      h:	857.70,
      i:	911.70,
      j:	959.40,
      k:	1013.40,
      l:	1143.00,
      m:	1338.00,
      n:	1383.00,
      o:	1444.00,  
      p:  1466.00,
      },
      lanchajeE:{
        a:	401.80,
        b:	487.20,
        c:	529.20,
        d:	578.90,
        e:	627.90,
        f:	880.20,
        g:	952.20,
        h:	1025.10,
        i:	1098.00,
        j:	1170.00,
        k:	1242.90,
        l:	1315.80,
        m:	1543.00,
        n:	1623.00,
        o:	1703.00,
        p:	1740.00,

      },
      lanchajeV:{
        a:	120.40,
        b:	146.30,
        c:	158.90,
        d:	171.50,
        e:	188.30,
        f:	263.70,
        g:	286.20,
        h:	307.80,
        i:	329.40,
        j:	351.00,
        k:	372.60,
        l:	395.10,
        m:	463.00,
        n:	587.00,
        o:	511.00,
        p:	535.00,

      },  

      remolcadorE:{
        a:	1654.80,
        b:	1905.40,
        c:	2179.80,
        d:	2434.60,
        e:	2500.00,
        f:	3600.00,
        g:	3900.00,
        h:	4626.90,
        i:	4898.70,
        j:	5170.50,
        k:	5443.20,
        l:	5715.00,
        m:	6653.00,
        n:	7010.00,
        o:	7368.00,
        p:	7724.00,

      },
      remolcadorv:{
      a:	413.70,
      b:	476.00,
      c:	545.30,
      d:	608.30,
      e:	740.60,
      f:  1020.60,
      g:	1089.00,
      h:	1156.50,
      i:  1224.90,
      j:	1292.40,
      k:	1360.80,
      l:	1429.20,
      m:	1663.00,
      n:	1753.00,
      o:	1842.00,
      p:	1931.00,

      },

      zarpesVPCP:{
      a:	2.50,
      b:	5.00,
      c:	10.00,
      d:	20.00,
      e:	30.00,
      f:  40.00,
      g:  50.00,
      h:  60.00,
      },

      zarpesVRD:{
      a:	10.00,
      b:	20.00,
      c:	25.00,
      d:	35.00,
      e:	45.00,
      f:  55.00,
      g:  65.00,
      h:  75.00,
      },


      zarpesEPCP:{
      a:	30.00,
      b:	50.00,
      c:	60.00,
      d:	70.00,
      e:	80.00,
      f:  90.00,
      g:  100.00,
      h:  120.00,
      },
      zarpesERD:{
      a:	20.00,
      b:	40.00,
      c:	50.00,
      d:	70.00,
      e:	90.00,
      f:  120.00,
      g:  150.00,
      h:  200.00,
      },  
      inspecciones:{
      a:	10.00,
      b:	25.00,
      c:	50.00,
      d:	100.00,
      },
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

  
  const pilotajeResult = this.CalculaPilotaje();
  this.resultado.totalppilotaje = pilotajeResult.totalppilotaje; 
  this.resultado.totalppilotajeVES = pilotajeResult.totalppilotajeVES; 

  
    const lanchajeResult = this.CalculaLanchaje();
    this.resultado.totallanchaje = lanchajeResult.totallanchaje;
    this.resultado.totallanchajeVES = lanchajeResult.totallanchajeVES;
    
    const remolcadorResult = this.CalculaRemolcador();
    this.resultado.totalremolcador = remolcadorResult.totalremolcador;
    this.resultado.totalremolcadorVES = remolcadorResult.totalremolcadorVES;


    const zarpesResult =this.CalculaZarpes();
    this.resultado.totalzarpes = zarpesResult.totalzarpes;
    this.resultado.totalzarpesVES = zarpesResult.totalzarpesVES;
    

    const inspecionesResult =this.CalculaInsp();
    this.resultado.totalinspecciones = inspecionesResult.totalinspecciones;
    this.resultado.totalinspeccionesVES = inspecionesResult.totalinspeccionesVES;
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
    
  
    CalculaLanchaje(){
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




      /*aquí se encuentra el calculador de Remolcadores*/ 
    /* ******************************************* */
    



    CalculaRemolcador() {
    let costoremolcador = 0;

    if (this.bandera === "extranjera") {
      costoremolcador = this.CalculaRemolcadorE(this.uab, this.puerto);
    } else if (this.bandera === "venezolana") {
      costoremolcador = this.CalculaRemolcadorV(this.uab, this.puerto);
    }
    //console.log('Costo Remolcador:', costoremolcador);

    const totalremolcador = this.puerto * costoremolcador.totalremolcador;
    const totalremolcadorVES = this.BCV * totalremolcador; // Si esta línea no se usa, considera eliminarla
    return {
        totalremolcador: totalremolcador.toFixed(2),
        totalremolcadorVES: totalremolcadorVES.toFixed(2), // Descomentar si es necesario
    };
},
CalculaRemolcadorE(uab){
    let costoremolcador = 0;
       // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 2000) {
      costoremolcador = this.remolcadorE.a;
    } else if (uab >= 2001 && uab <= 5000) {
      costoremolcador = this.remolcadorE.b;
    } else if (uab >= 5001 && uab <= 10000) {
      costoremolcador = this.remolcadorE.c;
    } else if (uab >= 10001 && uab <= 15000) {
      costoremolcador = this.remolcadorE.d;
    } else if (uab >= 15001 && uab <= 20000) {
      costoremolcador = this.remolcadorE.e;
    } else if (uab >= 20001 && uab <= 25000) {
      costoremolcador = this.remolcadorE.f;
    } else if (uab >= 25001 && uab <= 30000) {
      costoremolcador = this.remolcadorE.g;
    } else if (uab >= 30001 && uab <= 35000) {
      costoremolcador = this.remolcadorE.h;
    } else if (uab >= 35001 && uab <= 40000) {
      costoremolcador = this.remolcadorE.i;
    } else if (uab >= 40001 && uab <= 45000) {
      costoremolcador = this.remolcadorE.j;
    } else if (uab >= 45001 && uab <= 50000) {
      costoremolcador = this.remolcadorE.k;
    } else if (uab >= 50001 && uab <= 70000) {
      costoremolcador = this.remolcadorE.l;
    }else if (uab >= 70001  && uab <= 100000) {
      costoremolcador = this.remolcadorE.m;
    }else if (uab >= 100001  && uab <= 125000) {
      costoremolcador = this.remolcadorE.n;
    }else if (uab >= 125001  && uab <= 150000) {
      costoremolcador = this.remolcadorE.o;
    }else if (uab >= 150001) {
      costoremolcador = this.remolcadorE.p;
    }
      const totalremolcador =  costoremolcador ; // Ajusta esto
      //console.log(totalremolcador)
      return {
        totalremolcador: totalremolcador.toFixed(2),
      };

  },
  CalculaRemolcadorV(uab){
    let costoremolcador = 0;
       // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 2000) {
      costoremolcador = this.remolcadorv.a;
    } else if (uab >= 2001 && uab <= 5000) {
      costoremolcador = this.remolcadorv.b;
    } else if (uab >= 5001 && uab <= 10000) {
      costoremolcador = this.remolcadorv.c;
    } else if (uab >= 10001 && uab <= 15000) {
      costoremolcador = this.remolcadorv.d;
    } else if (uab >= 15001 && uab <= 20000) {
      costoremolcador = this.remolcadorv.e;
    } else if (uab >= 20001 && uab <= 25000) {
      costoremolcador = this.remolcadorv.f;
    } else if (uab >= 25001 && uab <= 30000) {
      costoremolcador = this.remolcadorv.g;
    } else if (uab >= 30001 && uab <= 35000) {
      costoremolcador = this.remolcadorv.h;
    } else if (uab >= 35001 && uab <= 40000) {
      costoremolcador = this.remolcadorv.i;
    } else if (uab >= 40001 && uab <= 45000) {
      costoremolcador = this.remolcadorv.j;
    } else if (uab >= 45001 && uab <= 50000) {
      costoremolcador = this.remolcadorv.k;
    } else if (uab >= 50001 && uab <= 70000) {
      costoremolcador = this.remolcadorv.l;
    }else if (uab >= 70001  && uab <= 100000) {
      costoremolcador = this.remolcadorv.m;
    }else if (uab >= 100001  && uab <= 125000) {
      costoremolcador = this.remolcadorv.n;
    }else if (uab >= 125001  && uab <= 150000) {
      costoremolcador = this.remolcadorv.o;
    }else if (uab >= 150001) {
      costoremolcador = this.remolcadorv.p;
    }
      const totalremolcador =  costoremolcador ; // Ajusta esto
      
      return {
        totalremolcador: totalremolcador.toFixed(2),
      };

  },



            /*************************aqui se calcula el zarpes **************************/
       /*****---------------------------------------------------------------------------******/
             /***************************************************************************/




            CalculaZarpes() {
    let costozarpes = 0;


/*******************CALCÚLO DE ZARPES VENEZOLANO**************************/ 
      switch (true) {
      case (this.bandera === "venezolana" && 
            this.tipo_buque === "pesca" ):
          costozarpes = this.CalculaZarpesVPCP(this.uab);
          break;

      case (this.bandera === "venezolana" && this.tipo_buque === "carga"):
          costozarpes = this.CalculaZarpesVPCP(this.uab);
          break;

      case (this.bandera === "venezolana" && this.tipo_buque === "pasajeros"):
          costozarpes = this.CalculaZarpesVPCP(this.uab);
          break;
          
      case (this.bandera === "venezolana" && this.tipo_buque === "recreo"):
          costozarpes = this.CalculaZarpesVRD(this.uab);
          break;

      case (this.bandera === "venezolana" && this.tipo_buque === "deportivos"):
          costozarpes = this.CalculaZarpesVRD(this.uab);
          break;

  /*******************CALCÚLO DE ZARPES EXTRANJERO**************************/ 

      case (this.bandera === "extranjera" && this.tipo_buque === "pesca"):
          costozarpes = this.CalculaZarpesEPCP(this.uab);
          break;


      case (this.bandera === "extranjera" && this.tipo_buque === "carga"):
          costozarpes = this.CalculaZarpesEPCP(this.uab);
          break;

      case (this.bandera === "extranjera" && this.tipo_buque === "pasajeros"):
          costozarpes = this.CalculaZarpesEPCP(this.uab);
          break;

      case (this.bandera === "extranjera" && this.tipo_buque === "recreo"):
          costozarpes = this.CalculaZarpesERD(this.uab);
          break;
      case (this.bandera === "extranjera" && this.tipo_buque === "deportivos"):
          costozarpes = this.CalculaZarpesERD(this.uab);
          break;
  
      default:

      
          // Puedes manejar otros casos aquí si es necesario
          break;
          
         }

      const totalzarpes = costozarpes.totalzarpes;
      const totalzarpesVES = this.BCV * totalzarpes; // Si esta línea no se usa, considera eliminarla
      return {
          totalzarpes: totalzarpes,
          totalzarpesVES: totalzarpesVES, // Descomentar si es necesario
      };
  },


CalculaZarpesVPCP(uab){

    let costozarpes = 0;
       // Lógica para calcular el costo de pilotaje para bandera Venezolana
    if (uab >= 0 && uab <= 5) {
      costozarpes = this.zarpesVPCP.a;
    } else if (uab >= 5 && uab <= 25) {
      costozarpes = this.zarpesVPCP.b;
    } else if (uab >= 25 && uab <= 50) {
      costozarpes = this.zarpesVPCP.c;
    } else if (uab >= 50 && uab <= 150) {
      costozarpes = this.zarpesVPCP.d;
    } else if (uab >= 150 && uab <= 500) {
      costozarpes = this.zarpesVPCP.e;
    } else if (uab >= 500 && uab <= 1500) {
      costozarpes = this.zarpesVPCP.f;
    } else if (uab >= 1500 && uab <= 5000) {
      costozarpes = this.zarpesVPCP.g;
    } else if (uab >= 5000 ) {
      costozarpes = this.zarpesVPCP.h;
    } 
    const totalzarpes =  costozarpes ; // Ajusta esto
      
      return {
        totalzarpes: totalzarpes,
      };

  },

  CalculaZarpesVRD(uab) {
    let costozarpes = 0;
    if (uab >= 0 && uab <= 5) {
        costozarpes = this.zarpesVRD.a;
    } else if (uab >= 5 && uab <= 25) {
        costozarpes = this.zarpesVRD.b;
    } else if (uab >= 25 && uab <= 50) {
        costozarpes = this.zarpesVRD.c;
    } else if (uab >= 50 && uab <= 150) {
        costozarpes = this.zarpesVRD.d;
    } else if (uab >= 150 && uab <= 500) {
        costozarpes = this.zarpesVRD.e;
    } else if (uab >= 500 && uab <= 1500) {
        costozarpes = this.zarpesVRD.f;
    } else if (uab >= 1500 && uab <= 5000) {
        costozarpes = this.zarpesVRD.g;
    } else if (uab >= 5000) {
        costozarpes = this.zarpesVRD.h;
    }
    const totalzarpes = costozarpes; // Ajusta esto
    return {
        totalzarpes: totalzarpes,
    };

},

CalculaZarpesEPCP(uab) {
    let costozarpes = 0;
    if (uab >= 0 && uab <= 5) {
        costozarpes = this.zarpesEPCP.a;
    } else if (uab >= 5 && uab <= 25) {
        costozarpes = this.zarpesEPCP.b;
    } else if (uab >= 25 && uab <= 50) {
        costozarpes = this.zarpesEPCP.c;
    } else if (uab >= 50 && uab <= 150) {
        costozarpes = this.zarpesEPCP.d;
    } else if (uab >= 150 && uab <= 500) {
        costozarpes = this.zarpesEPCP.e;
    } else if (uab >= 500 && uab <= 1500) {
        costozarpes = this.zarpesEPCP.f;
    } else if (uab >= 1500 && uab <= 5000) {
        costozarpes = this.zarpesEPCP.g;
    } else if (uab >= 5000) {
        costozarpes = this.zarpesEPCP.h;
    }
    const totalzarpes = costozarpes; // Ajusta esto
    return {
        totalzarpes: totalzarpes,
    };

},

CalculaZarpesERD(uab) {
    let costozarpes = 0;
    if (uab >= 0 && uab <= 5) {
        costozarpes = this.zarpesERD.a;
    } else if (uab >= 5 && uab <= 25) {
        costozarpes = this.zarpesERD.b;
    } else if (uab >= 25 && uab <= 50) {
        costozarpes = this.zarpesERD.c;
    } else if (uab >= 50 && uab <= 150) {
        costozarpes = this.zarpesERD.d;
    } else if (uab >= 150 && uab <= 500) {
        costozarpes = this.zarpesERD.e;
    } else if (uab >= 500 && uab <= 1500) {
        costozarpes = this.zarpesERD.f;
    } else if (uab >= 1500 && uab <= 5000) {
        costozarpes = this.zarpesERD.g;
    } else if (uab >= 5000) {
        costozarpes = this.zarpesERD.h;
    }
    const totalzarpes = costozarpes; // Ajusta esto
    return {
        totalzarpes: totalzarpes,
    };

},

            /************************************************************************************/
            /*************************aqui se calcula las Inspecciones **************************/
       /*****---------------------------------------------------------------------------******/

       CalculaInsp() {

      let inspecciones = 0;


      if (this.bandera === "extranjera") {
        inspecciones = this.CalculaInspE(this.uab);
        
      }
      
      const totalinspecciones = inspecciones.totalinspecciones;
      const totalinspeccionesVES = this.BCV * totalinspecciones; // Descomentar si es necesario

      return {
        totalinspecciones: totalinspecciones,
        totalinspeccionesVES: totalinspeccionesVES, // Descomentar si es necesario
      };
    },

    CalculaInspE(uab) {

      let inspecciones = 0;

      if (uab >= 5 && uab <= 50) {
        inspecciones = this.inspecciones.a;
      } else if (uab >= 51 && uab <= 100) {
        inspecciones = this.inspecciones.b;
      } else if (uab >= 101 && uab <= 500) {
        inspecciones = this.inspecciones.c;
      } else if (uab > 500) {
        inspecciones = this.inspecciones.d;
      }

      const totalinspecciones = inspecciones; // Ajusta esto
      return {
        totalinspecciones: totalinspecciones,
      };
    },

    mounted() {
      // this.obtenerBCV(); // Llamar al método para obtener la tasa al montar el componente
    },
  },
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>