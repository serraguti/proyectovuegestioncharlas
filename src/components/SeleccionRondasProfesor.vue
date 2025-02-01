<template>
<div>
    <i class="arrow-icon fa fa-arrow-left ml-10 mt-10"></i>
        <h1
            class="text-3xl font-extrabold text-center ">
            Ronda <span class="font-extrabold">{{ idRonda }}</span>
        </h1>
        <p class="text-center mb-0">Curso {{charlasPropuestas[0].idCurso}}</p>
    <br>
    <br>
    <div class="grid grid-cols-2 gap-2 ">
        <!-- Charlas Propuestas -->
        <mat-card class="p-4 shadow-md border border-gray-200">
            <div class="flex items-center justify-between mb-4">
                <h2 class="text-xl font-semibold">Charlas Propuestas</h2>
                <div class="flex items-center space-x-2 bg-blue-200 text-blue-800 px-4 py-2 rounded-full">
                    <span class="font-bold">Votos Totales</span>
                    <span class="font-semibold text-xl">{{ votosPropuestos }} / {{ totalVotos }}</span>
                </div>
            </div>
            <mat-card-content class="scrollable-content" 
            @dragover="onDragOver($event)"
                @drop="onDrop($event, 'propuestas')">
                <div v-for="charla in charlasPropuestas" 
                :key="charla.idCharla"
                    class="draggable-item flex items-center p-4 bg-gray-100 shadow-md rounded-lg mb-4" draggable="true"
                    @click="onDragStart($event, charla)">
                    <img src="charla.imagenCharla"  
                    onerror="this.src='https://cdn-icons-png.freepik.com/512/3415/3415488.png'" alt="Imagen Charla" class="w-16 h-16 object-cover rounded-full mr-4" />
                    <div class="flex justify-between w-full">
                        <div>
                            <h3 class="text-lg font-bold">{{ charla.titulo }}</h3>
                            <div class="flex items-center space-x-2">
                                <div class="flex items-center space-x-2">
                                  <span v-if="charla.idEstadoCharla == 1"
                                   class="px-2 py-1 text-xs font-medium rounded-full bg-gray-200 text-gray-800">
                                        {{ charla.idEstadoCharla === 1 ? 'Propuesta' : charla.idEstadoCharla === 2 ?
                                        'Aceptada' : '' }}
                                  </span>
                                  <span v-if="charla.idEstadoCharla == 2"
                                   class="px-2 py-1 text-xs font-medium rounded-full bg-green-200 text-green-800">
                                        {{ charla.idEstadoCharla === 1 ? 'Propuesta' : charla.idEstadoCharla === 2 ?
                                        'Aceptada' : '' }}
                                  </span>                                  
                                </div>
                                <div class="flex items-center space-x-2">
                                    <span class="px-2 py-1 text-xs font-medium rounded-full bg-blue-200 text-blue-800">
                                        Votos: {{charla.votos}}
                                    </span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </mat-card-content>
        </mat-card>

        <!-- Charlas Aceptadas -->
        <mat-card class="p-4 shadow-md border border-gray-200">
            <h2 class="text-xl font-semibold mb-4">Charlas Aceptadas</h2>
            <mat-card-content class="scrollable-content" 
            @dragover="onDragOver($event)"
                @drop="onDrop($event, 'aceptadas')">
                <div
                    class="draggable-item flex items-center p-6 bg-gray-50 shadow-md rounded-lg mb-6 text-center cursor-pointer border-dashed border-2 border-blue-300">
                    <i class="fas fa-plus text-4xl text-gray-500"></i>
                    <p class="text-gray-600 ml-4">Arrastra aquí una charla para poder aceptarla</p>
                </div>
                <div 
                    v-for="charla in charlasAceptadas" 
                    :key="charla.idCharla"
                    class="relative draggable-item flex items-center p-4 bg-gray-100 shadow-md rounded-lg mb-4"
                    draggable="true" @dragstart="onDragStart($event, charla)">

                    <div v-if="charla.idEstadoCharla === 2" 
                    class="absolute top-0 right-0 mt-2 mr-2 text-yellow-500">
                        <i class="fas fa-crown text-3xl"></i>
                    </div>
    
                    <img src="charla.imagenCharla" alt="Imagen Charla" onerror="this.src='https://cdn-icons-png.freepik.com/512/3415/3415488.png'" class="w-16 h-16 object-cover rounded-full mr-4" />
                    <div class="flex justify-between w-full">
                        <div>
                            <h3 class="text-lg font-bold">{{ charla.titulo }}</h3>
                            <div class="flex items-center space-x-2">
                                <div class="flex items-center space-x-2">
                                  <span v-if="charla.idEstadoCharla == 1"
                                   class="px-2 py-1 text-xs font-medium rounded-full bg-gray-200 text-gray-800">
                                        {{ charla.idEstadoCharla === 1 ? 'Propuesta' : charla.idEstadoCharla === 2 ?
                                        'Aceptada' : '' }}
                                  </span>
                                  <span v-if="charla.idEstadoCharla == 2"
                                   class="px-2 py-1 text-xs font-medium rounded-full bg-green-200 text-green-800">
                                        {{ charla.idEstadoCharla === 1 ? 'Propuesta' : charla.idEstadoCharla === 2 ?
                                        'Aceptada' : '' }}
                                  </span>   
                                </div>
                                <div class="flex items-center space-x-2">
                                    <span class="px-2 py-1 text-xs font-medium rounded-full bg-blue-200 text-blue-800">
                                        Votos: {{charla.votos}}
                                    </span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </mat-card-content>
        </mat-card>
    </div>
    <!-- Botones para guardar y cancelar -->
    <div class="grid grid-cols-2 gap-4 mt-4">
        <!-- Cancelar cambios -->
        <button mat-raised-button color="warn" @click="abrirConfirmacionCancelar()"
            class=" button-cancel p-4 shadow-md border-gray-200">
            Cancelar cambios
        </button>

        <!-- Guardar cambios -->
        <button mat-raised-button color="primary" @click="abrirConfirmacionGuardar()"
            class="p-4 shadow-md border-gray-200">
            Guardar cambios
        </button>
    </div>

    <!-- Popup de confirmación -->
    <div v-if="mostrarPopup" class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center z-50">
        <div class="bg-white p-6 rounded-lg shadow-lg max-w-sm w-full">
            <h3 class="text-xl font-semibold mb-4">{{ mensajePopup }}</h3>
            <div class="flex justify-between">
                <button @click="confirmarAccion()"
                    class="bg-blue-500 text-white px-4 py-2 rounded-md">Confirmar</button>
                <button @click="cerrarPopup()" class="bg-red-500 text-white px-4 py-2 rounded-md">Cancelar</button>
            </div>
        </div>
    </div>
</div>
</template>
<script>
import PerfilService from "@/services/PerfilService";
import CharlasService from "@/services/CharlasService";
//import Swal from "sweetalert2";

export default {
  name: "SeleccionRondasProfesor",
  data() {
    return {
      usuario: null,
      idRonda: 1,
      idCurso: 3213,
      charlasPropuestas: [],
      charlasAceptadas: [],
      draggedCharla: null,
      // Variables para el popup
      mostrarPopup: false,
      mensajePopup: '',
      votosPropuestos: 0, // Total de votos de las charlas propuestas
      totalVotos: 0, // Total de votos posibles
      perfilService: new PerfilService(),
      charlasService: new CharlasService(),
      cargando: false,
    };
  },
  methods: {
  async cargarAlumnos() {
    const alumnosPorCurso = await this.perfilService.getAlumnosActivoProfesor();
    //this.idCurso = this.charlasPropuestas[0].idCurso
    // Buscamos el curso que coincide con el idCurso de la ruta
    console.log("Antes del error");
    const curso = alumnosPorCurso.find(curso => curso.curso.idCurso === this.idCurso);
    console.log(curso);
    //this.idCurso = this.charlasPropuestas[0].idCurso

    // Si encontramos el curso, asignamos el número de alumnos
    if (curso) {
        this.totalVotos = curso.numeroAlumnos;  // Correctamente asignamos el número de alumnos
    } else {
      this.totalVotos = 0;
    }
  },
  async cargarCharlas() {
    try {
      this.cargando = true;
      // const charlas = await this.perfilService.getCharlasRonda(this.idRonda);
      const charlas = await this.charlasService.getCharlasRonda(this.idRonda);
      this.charlasPropuestas = charlas.filter((charla) => charla.idEstadoCharla === 1);
      this.charlasAceptadas = charlas.filter((charla) => charla.idEstadoCharla === 2);

      // Procesar votos para charlas propuestas
      for (const charla of this.charlasPropuestas) {
        const charlaConVotos = await this.perfilService.getVotosCharla(charla.idCharla);
        charla.votos = charlaConVotos.votos ?? 0;
        this.votosPropuestos += charla.votos; 
      }
      
      this.charlasPropuestas.sort((a, b) => b.votos - a.votos);

      // Procesar votos para charlas aceptadas
      for (const charla of this.charlasAceptadas) {
        const charlaConVotos = await this.perfilService.getVotosCharla(charla.idCharla);
        charla.votos = charlaConVotos.votos ?? 0;
        this.votosPropuestos += charla.votos; 
      }
      this.charlasAceptadas.sort((a, b) => b.votos - a.votos);
         } catch (error) {
        console.error("Error al cargar los cursos:", error);
        alert("No se pudieron cargar los cursos.");
      } finally {
        this.cargando = false; // Desactiva el spinner
      }
  },  
  abrirConfirmacionGuardar(){
    this.mostrarPopup = true;
    this.mensajePopup = '¿Está seguro de que desea guardar los cambios?';
  },
    abrirConfirmacionCancelar() {
    this.mostrarPopup = true;
    this.mensajePopup = '¿Está seguro de que desea cancelar los cambios?';
  },  
  confirmarAccion()
   {
    if (this.mensajePopup === '¿Está seguro de que desea guardar los cambios?') {
      this.guardarCambios();
      //this._router.navigate(["/courses"])
    } else if (this.mensajePopup === '¿Está seguro de que desea cancelar los cambios?') {
      this.cancelarCambios();
    }
    this.cerrarPopup();
  },

  cerrarPopup() {
    this.mostrarPopup = false;
  },
    // Guardar cambios
  guardarCambios(){
    let updatedCharlas = 
    this.charlasAceptadas.map(charla => {
      return {
        idCharla: charla.idCharla,
        titulo: charla.titulo,
        descripcion: charla.descripcion,
        tiempo: charla.tiempo,
        fechaPropuesta: charla.fechaPropuesta,
        idUsuario: charla.idUsuario,
        idEstadoCharla: charla.idEstadoCharla,
        idRonda: charla.idRonda,
        imagenCharla: charla.imagenCharla
      };
    });

    updatedCharlas.forEach(charla => {
      this.perfilService.updateCharla(charla).then(response => {
        console.log("Updating charlas" + response);
      })
    });
  },

  // Cancelar cambios
  cancelarCambios(){
    console.log("Cancelando cambios");
    //this._router.navigate(["/courses"])
  },

  onDragStart(event, charla) {
    this.draggedCharla = charla;
  },

  onDragOver(event) {
    event.preventDefault();
  },

  onDrop(event, targetList) {
    if (this.draggedCharla) {
      if (targetList === 'propuestas') {
        const charlaExistente = this.charlasPropuestas.some(c => c.idCharla === this.draggedCharla?.idCharla);
        if (!charlaExistente) {
          this.charlasAceptadas = this.charlasAceptadas.filter(c => c.idCharla !== this.draggedCharla?.idCharla);
          this.charlasPropuestas.push(this.draggedCharla);
          this.draggedCharla.idEstadoCharla = 1;
          this.charlasPropuestas.sort((a, b) => b.votos - a.votos);
        }
      } else {
        const charlaExistente = this.charlasAceptadas.some(c => c.idCharla === this.draggedCharla?.idCharla);
        if (!charlaExistente) {
          this.charlasPropuestas = this.charlasPropuestas.filter(c => c.idCharla !== this.draggedCharla?.idCharla);
          this.charlasAceptadas.push(this.draggedCharla);
          this.draggedCharla.idEstadoCharla = 2;
        }
      }
      this.draggedCharla = null;
    }
  }
  },
  created() {
    this.cargarAlumnos();
    this.cargarCharlas();
  },
  }
</script>

<style scoped>
.pie {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 15px;
  margin-top: auto;
}

.no-hover:active,
.no-hover {
  pointer-events: none;
  border-radius: 16px;
}

.image-container {
  position: relative;
  display: inline-block;
}
.profile-image {
  border: 4px solid #314b78;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.edit-icon {
  height: 10%;
  position: relative;
  top: 0px;
  right: 15px;
  font-size: 20px;
  color: white;
  background-color: #314b78;
  padding: 8px;
  border-radius: 50%;
  cursor: pointer;
  z-index: 10;
}

.edit-icon:hover {
  background-color: #41619a;
}

.card {
  background-color: #f8f9fa;
}

.card h4 {
  color: #333;
}

.list-group-item {
  font-size: 1.1em;
  padding: 15px;
  border: 1px solid #ddd;
  background-color: #ffffff;
  margin-bottom: 10px;
}

.list-group-item strong {
  color: #314b78;
}

.btn-ver{
  color: white;
  background-color: #314b78 !important;
}

.detalles-btn {
  margin-left: auto;
  margin-bottom: auto;
  margin-top: 0px;
  background-color: #314b78 !important;
  cursor: pointer;
  color: white;
}

.detalles-btn:hover {
  border-color: #41619a !important;
  background-color: #41619a !important;
  color: white;
}

.detalles-btn:active {
  border-color: #41619a !important;
  background-color: #41619a !important;
  color: white;
}

.botoncrearcurso {
  margin-left: 0px !important;
  background-color: #40685c !important; 
  color: white;
  cursor: pointer;
}

.botoncrearcurso:active {
  border-color: #578e73 !important;
  background-color: #578e73 !important;
  color: white;
}

.botoncrearcurso:hover {
  border-color: #578e73 !important;
  background-color: #578e73 !important;
  color: white;
}

@media (min-width: 768px) {
  .card {
    padding: 30px;
  }

  .profile-image {
    width: 150px;
    height: 150px;
  }
}

@media (min-width: 1200px) {
  .container {
    max-width: 1200px;
  }
  .card {
    padding: 40px;
  }
  .profile-image {
    width: 180px;
    height: 180px;
  }
}

@media (min-width: 991px) {
  .profile-buttons {
    width: 50%;
  }
}

@media (min-width: 991px) and (max-width: 1200px) {
  .profile-info {
    padding-bottom: 50px !important;
  }
}

@media (min-width: 500px) {
  .pequeno {
    display: flex;
    justify-content: center;
    gap: 15px;
  }
}

@media (max-width: 500px) {
  .pequeno {
    display: grid;
  }
}

body {
  background-color: #f5f5f5;
}

.profile-card {
  background-color: #e0e0e0;
  border-radius: 10px;
  padding: 20px;
}

.profile-header {
  background-color: #7782c6 !important;
  height: 150px;
  border-radius: 10px 10px 0 0;
}

.profile-content {
  display: flex;
  margin-top: -80px;
  gap: 20px;
}

.profile-image {
  width: 240px;
  height: 240px;
  border-radius: 30px;
  object-fit: cover;
  margin-left: 20px;
  margin-bottom: 30px;
}

.profile-info {
  display: block;
  flex-grow: 1;
  margin-top: auto;
  padding-bottom: 80px;
}

.profile-info h3 {
  margin: 0;
}

.profile-buttons {
  margin-top: 20px;
  margin-top: auto;
  padding-bottom: 0px;
}

.profile-buttons button {
  margin-right: 20px;
}

.row {
  display: flex;
  margin-left: 0;
  margin-right: 0;
}

.course-card {
  background-color: #d6d6d6;
  border-radius: 10px;
  position: relative;
  height: 100%;
  width: 100%;
}

.course-card .card-header {
  background-color: #a0a0a0;
  padding: 20px;
  border-radius: 10px 10px 0 0;
  font-size: 18px;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.course-card .status {
  font-size: 12px;
  padding: 5px 10px;
  margin-left: auto;
}

.course-card .card-body {
  padding: 15px;
  height: 150px;
}

.course-card .add-button {
  position: absolute;
  bottom: 10px;
  right: 10px;
  width: 25px;
  height: 25px;
  background-color: #fff;
  border: none;
  border-radius: 50%;
  font-weight: bold;
  line-height: 1;
  text-align: center;
  cursor: pointer;
}
@media (max-width: 991px) {
  .d-flex {
    justify-content: center !important;
  }

  .col-12 {
    width: 80% !important;
  }

  .profile-content {
    flex-wrap: wrap;
  }

  .profile-info {
    flex: 1;
    text-align: left;
    margin-bottom: 0;
  }

  .profile-info h3 {
    margin-bottom: 5px;
  }

  .profile-buttons {
    width: 75%;
    margin-top: 20px;
    display: flex;
    align-items: center;
    margin-left: auto;
    margin-right: auto;
    gap: 15px;
  }

  .profile-buttons button {
    flex: 1 1 auto;
    margin: 0 auto;
    white-space: nowrap;
  }

  @media (max-width: 767px) {
    .profile-info {
      text-align: center;
      padding-bottom: 0px;
      flex: auto;
    }
    .profile-image {
      width: 200px;
      height: 200px;
      border-radius: 30px;
      object-fit: cover;
      margin-left: 20px;
      margin-bottom: 30px;
    }
  }

  @media (max-width: 400px) {
    .profile-image {
      width: 200px;
      height: 200px;
      border-radius: 30px;
      object-fit: cover;
      margin-left: 20px;
      margin-bottom: 30px;
    }
  }

  @media (max-width: 360px) {
    .profile-image {
      width: 150px;
      height: 150px;
      border-radius: 30px;
      object-fit: cover;
      margin-left: 20px;
      margin-bottom: 30px;
    }
    .profile-buttons {
      display: contents;
    }
    .botoncrearcurso {
      margin-left: 0px !important;
    }
  }

  @media (max-width: 450px) {
    .profile-buttons {
      width: auto;
    }
  }
}
</style>