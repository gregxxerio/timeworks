<template>
  <div>
    <!-- Modal -->
    <div class="modal" :class="{ 'show': isOpen }" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header" style="border: 0 solid red">
            <h5 class="modal-title text-center w-100">{{ title }}</h5>
          </div>
          <div class="modal-body">
            <!-- Contenido del modal (proporcionado por el componente padre) -->
            <div class="text-center"><img src="/assets/Icons/sent.png" alt=""></div>
            <div class="text-center mt-5">
              <button style="background-color: #643fed; padding: 5px 15px; width: 200px; border-radius: 10px; color: white; border: 1px solid white" @click="closeModal">
                Aceptar &nbsp;&nbsp;<i class="fa fa-check-circle" aria-hidden="true"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Modal backdrop -->
    <div class="modal-backdrop" :class="{ 'show': isOpen }"></div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';

@Component
export default class CustomModal extends Vue {
  @Prop({ default: false }) private isOpen!: boolean; // Propiedad booleana para indicar si el modal está abierto
  @Prop({ default: 'Reporte enviado exitosamente' }) private title!: string; // Propiedad para el título del modal

  // Método para abrir el modal
  private openModal() {
    this.isOpen = true;
  }

  // Método para cerrar el modal
  private closeModal() {
    this.isOpen = false;
  }

  // Método para guardar cambios (aquí puedes realizar acciones adicionales cuando se guarden los cambios)
  private saveChanges() {
    // Cerrar el modal al guardar cambios (opcional)
    this.isOpen = false;
  }
}
</script>

<style>
/* Estilos del componente modal (puedes personalizarlo según tus necesidades) */
.modal {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal.show {
  display: block;
}

.modal-dialog {
  margin: 10% auto;
  max-width: 600px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
}

.modal-content {
  padding: 20px;
}

.modal-backdrop {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-backdrop.show {
  display: block;
}
</style>
