<template>
  <main class="bg-gray-700 min-h-screen px-4 sm:px-8 py-8 sm:py-16 flex flex-col items-center text-white font-sans">
    <section class="w-full max-w-screen-xl space-y-4">
      <h1 class="text-xl sm:text-2xl md:text-4xl font-bold">Calcular aguinaldo SV</h1>
      <p>Agrega los datos para calcular el aguinaldo</p>

      <form class="flex flex-col space-y-8">
        <label class="space-y-2">
          <h1>Ingresa el Salario Mensual</h1>
          <input 
            type="money"
            class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
            v-model="salario" 
            placeholder="Salario Mensual" 
          />
        </label>
        <label class="space-y-2">
          <h1>(Opcional) Dias totales laborales</h1>
          <p>Dejar en blanco si se usan fechas abajo</p>
          <input 
            type="money"
            class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
            v-model="diasTotales" 
            placeholder="Dias totales" 
          />
        </label>

        <div>
          <p>Años laborados: {{ Math.trunc(anios) }}</p>
          <p>Meses laborados: {{ Math.trunc(meses) }}</p>
          <p>Dias laborados: {{ Math.trunc(dias) }}</p>
        </div>

        <div>
          <p>Meses totales laborados: {{ (Math.trunc(anios) * 12) + Math.trunc(meses) }}</p>
          <p>Dias laborados: {{ Math.trunc(diasAguinaldo) }}</p>
        </div>

        <label v-show="diasTotales === ''" class="space-y-2">
          <h1>Ingresa el Número de Días Laborados DD/MM/YYYY</h1>
          <div class="flex items-center space-x-4">
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaInicio.dias"
              placeholder="Día" 
            />
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaInicio.mes"
              placeholder="Mes"
            />
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaInicio.anio"
              placeholder="Año"
            />
          </div>
        </label>


        <label v-show="diasTotales === ''" class="space-y-2">
          <h1>Ingresa fecha de fin DD/MM/YYYY</h1>
          <div class="flex items-center space-x-4">
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaFin.dias"
              placeholder="Día" 
            />
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaFin.mes"
              placeholder="Mes"
            />
            <input 
              type="number"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black" 
              v-model="fechaFin.anio"
              placeholder="Año"
            />
          </div>
        </label>

        <label class="space-y-2">
          <h1>Resultado</h1>
          <div class="flex items-center space-x-4">
            <input
              type="text"
              class="px-6 py-3 rounded-lg bg-gray-200 text-black"
              v-model="resultado"
              placeholder="Resultado"
              readonly
            />
          </div>
          <pre>Formula usada: {{ formula }}</pre>
        </label>

        <input
          type="button"
          class="px-6 py-3 rounded-lg bg-gray-200 text-black hover:bg-gray-400 transition-all duration-300 ease-in-out cursor-pointer" 
          @click="calcularAguinaldo"
          value="Calcular"
        />
      </form>
    </section>
  </main>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      salario: '',
      fechaInicio: {
        dias: '',
        mes: '',
        anio: ''
      },
      fechaFin: {
        dias: new Date().getDate(),
        mes: new Date().getMonth() + 1,
        anio: new Date().getFullYear()
      },
      anios: 0,
      meses: 0,
      dias: 0,
      tiempoLaborado: 0,
      diasAguinaldo: 0,
      diasAguinaldoCalculado: 0,
      resultado: 0,
      tipo: '',
      formula: '',
      diasTotales: ''
    }
  },
  methods: {
    calcularAguinaldo() {
      this.tiempoLaborado = new Date(this.fechaFin.anio, this.fechaFin.mes, this.fechaFin.dias) - new Date(this.fechaInicio.anio, this.fechaInicio.mes, this.fechaInicio.dias);
      if (this.diasTotales !== ''){
        this.anios = this.diasTotales / 365;
      } else {
        this.anios = this.tiempoLaborado / (1000 * 60 * 60 * 24 * 365);
      }

      if (this.diasTotales !== '') {
        this.meses = this.diasTotales / 30;
      } else {
        this.meses = (this.tiempoLaborado % (1000 * 60 * 60 * 24 * 365)) / (1000 * 60 * 60 * 24 * 30);
      }

      if (this.diasTotales !== ''){
        this.dias = this.diasTotales;
      } else {
        this.dias = (this.tiempoLaborado % (1000 * 60 * 60 * 24 * 365)) % (1000 * 60 * 60 * 24 * 30) / (1000 * 60 * 60 * 24);
      }
      if (this.diasTotales !== '') {
        this.diasAguinaldo = this.diasTotales;
      } else {
        this.diasAguinaldo = this.tiempoLaborado / (1000 * 60 * 60 * 24);
      }

      if (this.anios >= 10) {
        this.diasAguinaldoCalculado = 21;
        this.resultado = '$' + ((this.salario/30) * this.diasAguinaldoCalculado).toFixed(2) + ' de aguinaldo';
        this.tipo = 'Mayor o igual a 10 años';
        this.formula = `
  AGUINALDO NORMAL (MAYOR E IGUAL A 10 AÑOS)
    Paso 1: salario mensual dividido entre 30
    Paso 2: multiplicar el resultado de paso 1 por dias de aguinaldo (21 dias segun tabla)
        `;
      }

      if (this.anios >= 3 && this.anios < 10) {
        this.diasAguinaldoCalculado = 19;
        this.resultado = '$' + ((this.salario/30) * this.diasAguinaldoCalculado).toFixed(2) + ' de aguinaldo';
        this.tipo = 'Mayor o igual a 3 años y menos de 10 años';
        this.formula = `
  AGUINALDO NORMAL (MAYOR E IGUAL A 3 AÑOS Y MENOS DE 10 AÑOS)
    Paso 1: salario mensual dividido entre 30
    Paso 2: multiplicar el resultado de paso 1 por dias de aguinaldo (19 dias segun tabla)
        `;
      }

      if (this.anios >= 1 && this.anios < 3) {
        this.diasAguinaldoCalculado = 15;
        this.resultado = '$' + ((this.salario/30) * this.diasAguinaldoCalculado).toFixed(2) + ' de aguinaldo';
        this.tipo = 'Mayor o igual a 1 año y menos de 3 años';
        this.formula = `
  AGUINALDO NORMAL (MAYOR E IGUAL A 1 AÑO Y MENOS DE 3 AÑOS)
    Paso 1: salario mensual dividido entre 30
    Paso 2: multiplicar el resultado de paso 1 por dias de aguinaldo (15 dias segun tabla)
        `;
      } 

      if (this.anios < 1) {
        this.diasAguinaldoCalculado = 15;
        this.resultado = '$' + ((((this.salario/30) * this.diasAguinaldoCalculado)/365)*this.diasAguinaldo).toFixed(2) + ' de aguinaldo';
        this.tipo = 'Menor a 1 año';
        this.formula = `
  AGUINALDO PROPORCIONAL (MENOR A 1 AÑO)
    Paso 1: salario mensual dividido entre 30
    Paso 2: multiplicar lo anterior por dias laborados
    Paso 3: dividir lo anterior entre 365
    Paso 4: multiplicar lo anterior por dias de aguinaldo (15 dias segun tabla)
        `;
      } 
    },
  },
}
</script>
