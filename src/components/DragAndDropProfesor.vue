<template>
    <div class="container">
      <div class="contenedor-titulo">
        <i
          class="arrow-icon fa fa-arrow-left ml-3 mt-3"
          @click="navigateToStudentList"
        ></i>
        <div class="contenedor-titulo-texto">
          <h1 class="text-center text-3xl font-weight-bold">
            Ronda <span class="font-weight-bold">{{ idRonda }}</span>
          </h1>
          <p class="text-center mb-0">
            Curso {{ charlasPropuestas[0]?.idCurso }}
          </p>
        </div>
      </div>
   
      <div class="row mt-4">
        <!-- Charlas Propuestas -->
        <div class="col-md-6">
          <div class="card p-4 shadow-sm border">
            <div class="d-flex justify-content-between">
              <h2 class="h5 mb-4">Charlas Propuestas</h2>
            </div>
            <div
              class="scrollable-content"
              @dragover.prevent
              @drop="onDrop($event, 'propuestas')"
            >
              <div
                v-for="charla in charlasPropuestas"
                :key="charla.idCharla"
                class="draggable-item"
                draggable="true"
                @dragstart="onDragStart($event, charla)"
              >
                <div class="charla-card">
                  <div class="charla-card-left">
                    <img style="height: 7vh" src="../assets/drag_png 1.png" />
                  </div>
                  <div class="charla-card-content">
                    <div class="charla-card-info">
                      <div class="speaker-time">
                        <span
                          >{{ charla.usuario.split(" ")[0] }} - 🕒{{
                            charla.tiempo
                          }}
                          min</span
                        >
                      </div>
                      <div class="votes">Votos: {{ charla.votos }}</div>
   
                    </div>
                    <div class="charlitsas">
                      <h3>{{ charla.titulo }}</h3>
                    </div>
                    <p class="limited-text">
                      {{ charla.descripcion }}
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
   
        <!-- Charlas Aceptadas -->
        <div class="col-md-6">
          <div class="card p-4 shadow-sm border">
            <h2 class="h5 mb-4">Charlas Aceptadas</h2>
            <div
              class="scrollable-content"
              @dragover.prevent
              @drop="onDrop($event, 'aceptadas')"
            >
              <div
                v-if="!charlasAceptadas.length"
                class="draggable-item text-center p-5 bg-light shadow-sm rounded mb-4 border-dashed border-2 border-info"
                @click="acceptCharla"
              >
                <i class="fas fa-plus text-muted" style="font-size: 2rem"></i>
                <p class="text-muted">Arrastra aquí una charla para aceptarla</p>
              </div>
              <div
                v-for="charla in charlasAceptadas"
                :key="charla.idCharla"
                class="relative draggable-item"
                draggable="true"
                @dragstart="onDragStart($event, charla)"
              >
                <div
                  v-if="charla.idEstadoCharla === 2"
                  class="position-absolute top-0 right-0 mt-2 mr-2 text-warning"
                ></div>
                <div class="charla-card">
                  <div class="charla-card-left">
                    <img style="height: 7vh" src="../assets/drag_png 1.png" />
                  </div>
                  <div class="charla-card-content">
                    <div class="charla-card-info">
                      <div class="votes">Votos: {{ charla.votos }}</div>
                      <div class="speaker-time">
                        <span
                          >{{ charla.usuario.split(" ")[0] }} - 🕒{{
                            charla.tiempo
                          }}
                          min</span
                        >
                      </div>
                    </div>
                    <div class="charlitsas">
                      <h3>{{ charla.titulo }}</h3>
                    </div>
                    <p class="limited-text">
                      {{ charla.descripcion }}
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
   
      <!-- Botones de acción -->
      <div class="d-flex justify-content-between mt-4">
        <button class="btn custom-button-cancel" @click="cancelarCambios">
          Cancelar cambios
        </button>
        <button class="btn custom-button" @click="guardarCambios">
          Guardar cambios
        </button>
      </div>
   
      <!-- Popup de confirmación -->
      <div
        v-if="mostrarPopup"
        class="fixed inset-0 bg-dark bg-opacity-75 d-flex align-items-center justify-content-center z-50"
      >
        <div class="bg-white p-5 rounded-lg shadow-lg max-w-sm w-100">
          <h3 class="h5 mb-4">{{ mensajePopup }}</h3>
          <div class="d-flex justify-content-between">
            <button class="btn btn-success" @click="confirmarAccion">
              Confirmar
            </button>
            <button class="btn btn-danger" @click="cerrarPopup">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
  </template>
   
  <script>
  import CharlasService from "@/services/CharlasService";
  export default {
    data() {
      return {
        idRonda: 0,
        idCurso: 0,
        charlasPropuestas: [],
        charlasAceptadas: [],
        charlasTotal: [],
        draggedCharla: null,
        mostrarPopup: false,
        mensajePopup: "",
        votosPropuestos: 0,
        totalVotos: 0,
        charlasService: new CharlasService(),
      };
    },
    methods: {
      onImageError(event) {
        event.target.src =
          "https://cdn-icons-png.freepik.com/512/3415/3415488.png";
      },
      navigateToStudentList() {
        this.$router.push(`/charlas`);
      },
      onDragStart(event, charla) {
        this.draggedCharla = charla;
      },
      onDrop(event, targetList) {
        if (this.draggedCharla) {
          if (targetList === "propuestas") {
            const charlaExistente = this.charlasPropuestas.some(
              (c) => c.idCharla === this.draggedCharla.idCharla
            );
            if (!charlaExistente) {
              this.charlasAceptadas = this.charlasAceptadas.filter(
                (c) => c.idCharla !== this.draggedCharla.idCharla
              );
              this.charlasPropuestas.push(this.draggedCharla);
              this.draggedCharla.idEstadoCharla = 1;
            }
          } else {
            const charlaExistente = this.charlasAceptadas.some(
              (c) => c.idCharla === this.draggedCharla.idCharla
            );
            if (!charlaExistente) {
              this.charlasPropuestas = this.charlasPropuestas.filter(
                (c) => c.idCharla !== this.draggedCharla.idCharla
              );
              this.charlasAceptadas.push(this.draggedCharla);
              this.draggedCharla.idEstadoCharla = 2;
            }
          }
          this.draggedCharla = null;
        }
      },
      guardarCambios() {
        console.log("ADFSDFEFDGAFDGHEFGFDAHFDHGHFD");
        // Implementar lógica para guardar cambios en las charlas
        const updatedCharlas = this.charlasAceptadas.map((charla) => ({
          idCharla: charla.idCharla,
          titulo: charla.titulo,
          descripcion: charla.descripcion,
          tiempo: charla.tiempo,
          fechaPropuesta: charla.fechaPropuesta,
          idUsuario: charla.idUsuario,
          idEstadoCharla: charla.idEstadoCharla,
          idRonda: charla.idRonda,
          imagenCharla: charla.imagenCharla,
        }));
   
        //console.log(updatedCharlas);
        updatedCharlas.forEach((charla) => {
          console.log(charla);
          this.charlasService
            .updateEstadoCharla(charla.idCharla, charla.idEstadoCharla)
            .then((response) => {
              console.log(response);
            });
        });
   
        const updatedCharlasCanceladas = this.charlasPropuestas.map((charla) => ({
          idCharla: charla.idCharla,
          titulo: charla.titulo,
          descripcion: charla.descripcion,
          tiempo: charla.tiempo,
          fechaPropuesta: charla.fechaPropuesta,
          idUsuario: charla.idUsuario,
          idEstadoCharla: charla.idEstadoCharla,
          idRonda: charla.idRonda,
          imagenCharla: charla.imagenCharla,
        }));
   
        //console.log(updatedCharlas);
        updatedCharlasCanceladas.forEach((charla) => {
          console.log(charla);
          this.charlasService
            .updateEstadoCharla(charla.idCharla, charla.idEstadoCharla)
            .then((response) => {
              console.log(response);
              this.navigateToStudentList();
            });
        });
      },
      cancelarCambios() {
        this.navigateToStudentList();
      },
      estadocharlas() {
        this.charlasTotal.forEach((charla, i) => {
          console.log(i);
          if (charla.estadoCharla == "ACEPTADA") {
            this.charlasAceptadas.push(charla);
          } else {
            this.charlasPropuestas.push(charla);
            console.log(charla);
          }
        });
      },
    },
    mounted() {
      this.idRonda = this.$route.params.id;
      this.idCurso = this.$route.params.idCurso;
      // Aquí debes cargar las charlas y alumnos de la misma forma que en el componente Angular
      this.charlasService.getCharlasRonda(this.idRonda).then((r) => {
        this.charlasTotal = r;
        this.estadocharlas();
      });
    },
  };
  </script>
   
  <style scoped>
  .contenedor-titulo {
    display: flex;
    align-items: center;
  }
  .contenedor-titulo-texto h1 {
    margin-top: 2%;
    width: 100%;
  }
  .contenedor-titulo-texto {
    display: flex;
    flex-direction: column; /* Acomoda los elementos en columna */
    align-items: center; /* Centra los elementos horizontalmente */
    margin-left: 40%;
  }
   
   
  .contenedor-titulo-texto p {
    margin-top: 2%;
    width: 100%;
  }
  .arrow-icon {
    background-color: #4651c5;
    padding: 10px;
    border-radius: 10%;
    color: white;
  }
  .custom-button {
    border-radius: 20px;
    color: white;
    font-size: 15px;
    font-family: "Montserrat", serif;
    font-weight: 600;
    text-decoration: none;
    padding: 5px 20px;
    height: 50px;
    background-color: #4651c5;
    transition: background-color 0.3s ease, transform 0.2s ease;
    border: none;
  }
  .custom-button-cancel {
    border-radius: 20px;
    color: white;
    font-size: 15px;
    font-family: "Montserrat", serif;
    font-weight: 600;
    text-decoration: none;
    padding: 5px 20px;
    height: 50px;
    background-color: #c54646;
    transition: background-color 0.3s ease, transform 0.2s ease;
    border: none;
  }
  .limited-text {
    display: -webkit-box;
    -webkit-line-clamp: 1; /* Limita a 2 líneas */
    -webkit-box-orient: vertical;
    overflow: hidden;
    text-overflow: ellipsis; /* Agrega "..." al final si el texto es muy largo */
  }
   
  .scrollable-content {
    box-shadow: inset 0 4px 8px #dddddd;
    max-height: 400px;
    /* Ajusta según sea necesario */
    overflow-y: auto;
    display: flex;
    border-radius: 3%;
    flex-direction: column;
    gap: 8px;
  }
   
  .draggable-item {
    display: flex;
    align-items: center;
    padding: 8px 12px;
  }
  .draggable-item:hover {
    cursor: grab;
  }
  .draggable-item:active {
    cursor: grabbing;
  }
   
  @keyframes float {
    0% {
      transform: translateY(0) translateX(0);
      opacity: 1;
    }
   
    50% {
      transform: translateY(-100px) translateX(50px);
      opacity: 0.7;
    }
   
    100% {
      transform: translateY(-200px) translateX(-50px);
      opacity: 0;
    }
  }
   
  body {
    font-family: Arial, sans-serif;
  }
   
  .charlitsas {
    display: flex;
  }
  .charla-card {
    display: flex;
    align-items: center;
    background-color: #ffff;
    border: 1px solid #ccc;
    border-radius: 8px;
    max-width: 600px;
    width: 100%;
    margin: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
   
  .charla-card-left {
    background-color: #d1e0fc;
    width: 10%;
    height: 20vh;
    border-radius: 8px 0 0 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-right: 1px solid #516ba5;
  }
   
  .charla-card-content {
    flex: 1;
    padding: 16px;
  }
   
  .charla-card-content h3 {
    font-size: 18px;
    margin-top: 2%;
    font-weight: bold;
  }
   
  .charla-card-content p {
    margin: 0;
    color: #555;
    font-size: 14px;
  }
   
  .charla-card-info {
    display: flex;
    gap: 10px;
    width: 100%;
    justify-content: flex-end;
    margin-top: -18px;
  }
   
  .votes {
    background-color: #c9dcff;
    padding: 4px 8px;
    border-radius: 4px;
    color: #333;
    font-size: 12px;
    height: 3.5vh;
  }
   
  .speaker-time span {
    font-size: 12px;
    color: #333;
    background-color: #87d0b1;
    padding: 4px 8px;
    border-radius: 4px;
    height: 3.5vh;
  }
  </style>