<template>
  <div>
    <!-- Botón para abrir el modal -->
    <BaseButton
      @click="openModal"
      :icon="mdiEye"
      color="lightDark"
      class="!p-1.5 !rounded-full hover:bg-gray-200 transition-colors"
      title="Ver detalles del documento"
    />

    <!-- Modal de detalles -->
    <Modal :show="isOpen" @close="closeModal" max-width="2xl">
      <div class="p-6 text-left space-y-6 bg-white dark:bg-gray-800">
        <!-- Header con icono mejorado -->
        <div class="flex items-start space-x-4 pb-4 border-b border-gray-200 dark:border-gray-700">
          <div class="bg-gradient-to-br from-blue-500 to-blue-600 dark:from-blue-600 dark:to-blue-700 p-3 rounded-xl shadow-lg flex-shrink-0 transform hover:scale-105 transition-transform duration-200">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
          </div>
          <div class="flex-1 min-w-0">
            <h2 class="text-2xl font-bold text-gray-900 dark:text-gray-100 mb-1 truncate">{{ documento.tipo_de_documento.nombre_documento }}</h2>
            <div class="flex items-center space-x-2">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-gray-400 dark:text-gray-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
              </svg>
              <p class="text-sm font-medium text-gray-600 dark:text-gray-400">{{ documento.departamento.nombre_departamento }}</p>
            </div>
          </div>
        </div>

        <!-- Tarjetas de información mejoradas -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Tarjeta de Revalidación -->
          <div class="bg-gradient-to-br from-gray-50 to-gray-100 dark:from-gray-700 dark:to-gray-800 p-5 rounded-xl border-2 border-gray-200 dark:border-gray-600 shadow-md hover:shadow-lg transition-all duration-200 transform hover:-translate-y-1">
            <div class="flex items-start space-x-3">
              <div class="bg-blue-100 dark:bg-blue-900/40 p-2.5 rounded-lg">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-blue-600 dark:text-blue-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                </svg>
              </div>
              <div class="flex-1">
                <p class="text-xs font-semibold uppercase tracking-wider text-gray-500 dark:text-gray-400 mb-1">Revalidación</p>
                <p class="text-lg font-bold text-gray-900 dark:text-gray-100">{{ formatDate(documento.fecha_revalidacion) }}</p>

                <!-- Estado de Revalidación -->
                <div class="mt-3">
                  <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold shadow-sm" :class="revalidationStatusClass">
                    <svg v-if="documento.dias_restantes_revalidacion <= 0" xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    {{ revalidationStatusText }}
                  </span>
                </div>
              </div>
            </div>
          </div>

          <!-- Tarjeta de Vigencia -->
          <div class="bg-gradient-to-br from-gray-50 to-gray-100 dark:from-gray-700 dark:to-gray-800 p-5 rounded-xl border-2 border-gray-200 dark:border-gray-600 shadow-md hover:shadow-lg transition-all duration-200 transform hover:-translate-y-1">
            <div class="flex items-start space-x-3">
              <div class="bg-green-100 dark:bg-green-900/40 p-2.5 rounded-lg">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-green-600 dark:text-green-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <div class="flex-1">
                <p class="text-xs font-semibold uppercase tracking-wider text-gray-500 dark:text-gray-400 mb-1">Vigencia</p>
                <p class="text-lg font-bold text-gray-900 dark:text-gray-100">{{ formatDate(documento.fecha_vigencia) }}</p>

                <!-- Estado de Vigencia -->
                <div class="mt-3">
                  <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold shadow-sm" :class="expirationStatusClass">
                    <svg v-if="documento.dias_restantes <= 0" xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    {{ expirationStatusText }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Archivos -->
        <div class="border-t border-gray-200 pt-4">
          <div class="flex justify-between items-center mb-3">
            <h4 class="text-xs font-semibold uppercase tracking-wider text-gray-500">Visualizar documentos</h4>
            <button 
              v-if="todosArchivos.length > 0"
              @click="downloadAll"
              class="inline-flex items-center px-3 py-1.5 border border-transparent text-xs font-medium rounded shadow-sm text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="-ml-1 mr-1 h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/>
              </svg>
              Descargar todos
            </button>
          </div>

          <!-- Documentos Principales -->
          <div class="mb-4">
            <h5 class="text-sm font-semibold text-gray-700 dark:text-gray-300 mb-3 flex items-center">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2 text-blue-600 dark:text-blue-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
              </svg>
              Documentos Principales
            </h5>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
              <button
                v-for="(archivo, index) in archivosPrincipales"
                :key="`principal-${index}`"
                @click="showFileInModal(archivo.ruta_archivo, archivo)"
                class="group relative inline-flex items-center px-4 py-3 border-2 border-blue-200 dark:border-blue-700 text-sm font-medium rounded-lg shadow-sm text-blue-700 dark:text-blue-300 bg-blue-50 dark:bg-blue-900/30 hover:bg-blue-100 dark:hover:bg-blue-900/50 hover:border-blue-300 dark:hover:border-blue-600 hover:shadow-md transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 dark:focus:ring-blue-400 transform hover:-translate-y-0.5"
              >
                <div class="flex items-center justify-center w-8 h-8 mr-3 rounded-lg bg-blue-100 dark:bg-blue-800 group-hover:bg-blue-200 dark:group-hover:bg-blue-700 transition-colors duration-200">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-blue-600 dark:text-blue-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                  </svg>
                </div>
                <div class="flex-1 text-left">
                  <p class="font-semibold text-sm truncate">{{ archivo.nombre_original || `Principal ${archivosPrincipales.length > 1 ? index + 1 : ''}` }}</p>
                  <p class="text-xs text-blue-600 dark:text-blue-400 mt-0.5">Click para visualizar</p>
                </div>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 ml-2 text-blue-500 dark:text-blue-400 group-hover:translate-x-1 transition-transform duration-200" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                </svg>
              </button>
            </div>
          </div>

          <!-- Documentos Anexos -->
          <div v-if="archivosAnexos.length > 0">
            <h5 class="text-sm font-semibold text-gray-700 dark:text-gray-300 mb-3 flex items-center">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2 text-gray-600 dark:text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.172 7l-6.586 6.586a2 2 0 102.828 2.828l6.414-6.586a4 4 0 00-5.656-5.656l-6.415 6.585a6 6 0 108.486 8.486L20.5 13" />
              </svg>
              Documentos Anexos
            </h5>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
              <button
                v-for="(archivo, index) in archivosAnexos"
                :key="`anexo-${index}`"
                @click="showFileInModal(archivo.ruta_archivo, archivo)"
                class="group relative inline-flex items-center px-4 py-3 border-2 border-gray-200 dark:border-gray-700 text-sm font-medium rounded-lg shadow-sm text-gray-700 dark:text-gray-300 bg-white dark:bg-gray-800 hover:bg-gray-50 dark:hover:bg-gray-700 hover:border-gray-300 dark:hover:border-gray-600 hover:shadow-md transition-all duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-500 dark:focus:ring-gray-400 transform hover:-translate-y-0.5"
              >
                <div class="flex items-center justify-center w-8 h-8 mr-3 rounded-lg bg-gray-100 dark:bg-gray-700 group-hover:bg-gray-200 dark:group-hover:bg-gray-600 transition-colors duration-200">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-600 dark:text-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                  </svg>
                </div>
                <div class="flex-1 text-left">
                  <p class="font-semibold text-sm truncate">{{ archivo.nombre_original || `Anexo ${archivosAnexos.length > 1 ? index + 1 : ''}` }}</p>
                  <p class="text-xs text-gray-500 dark:text-gray-400 mt-0.5">Click para visualizar</p>
                </div>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 ml-2 text-gray-400 dark:text-gray-500 group-hover:translate-x-1 transition-transform duration-200" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                </svg>
              </button>
            </div>
          </div>
        </div>

        <!-- Visor de PDF mejorado -->
        <transition
          enter-active-class="transition ease-out duration-200"
          enter-from-class="opacity-0 scale-95"
          enter-to-class="opacity-100 scale-100"
          leave-active-class="transition ease-in duration-150"
          leave-from-class="opacity-100 scale-100"
          leave-to-class="opacity-0 scale-95"
        >
          <div v-if="showPdfViewer" class="mt-4 rounded-xl overflow-hidden border border-gray-200 dark:border-gray-700 shadow-2xl">
            <!-- Header del visor con información del documento -->
            <div class="bg-gradient-to-r from-blue-600 to-blue-700 dark:from-blue-700 dark:to-blue-800 px-4 py-3">
              <div class="flex justify-between items-center">
                <div class="flex items-center space-x-3 flex-1 min-w-0">
                  <div class="bg-white/10 p-2 rounded-lg backdrop-blur-sm">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
                    </svg>
                  </div>
                  <div class="flex-1 min-w-0">
                    <p class="text-sm font-semibold text-white">Vista previa del documento</p>
                    <p class="text-xs text-blue-100 truncate">{{ currentFileName }}</p>
                  </div>
                </div>

                <!-- Controles del visor -->
                <div class="flex items-center space-x-2 ml-4">
                  <!-- Botón de descarga -->
                  <button
                    @click="downloadCurrentFile"
                    class="p-2 rounded-lg bg-white/10 hover:bg-white/20 text-white transition-all duration-200 hover:scale-110 focus:outline-none focus:ring-2 focus:ring-white/50"
                    title="Descargar documento"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/>
                    </svg>
                  </button>

                  <!-- Botón de abrir en nueva pestaña -->
                  <button
                    @click="openInNewTab"
                    class="p-2 rounded-lg bg-white/10 hover:bg-white/20 text-white transition-all duration-200 hover:scale-110 focus:outline-none focus:ring-2 focus:ring-white/50"
                    title="Abrir en nueva pestaña"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"/>
                    </svg>
                  </button>

                  <!-- Botón de pantalla completa -->
                  <button
                    @click="toggleFullscreen"
                    class="p-2 rounded-lg bg-white/10 hover:bg-white/20 text-white transition-all duration-200 hover:scale-110 focus:outline-none focus:ring-2 focus:ring-white/50"
                    title="Pantalla completa"
                  >
                    <svg v-if="!isFullscreen" xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8V4m0 0h4M4 4l5 5m11-1V4m0 0h-4m4 0l-5 5M4 16v4m0 0h4m-4 0l5-5m11 5l-5-5m5 5v-4m0 4h-4"/>
                    </svg>
                    <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                    </svg>
                  </button>

                  <!-- Botón de cerrar -->
                  <button
                    @click="closePdfViewer"
                    class="p-2 rounded-lg bg-red-500/20 hover:bg-red-500/30 text-white transition-all duration-200 hover:scale-110 focus:outline-none focus:ring-2 focus:ring-white/50"
                    title="Cerrar visor"
                  >
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                      <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
                    </svg>
                  </button>
                </div>
              </div>
            </div>

            <!-- Área del visor de PDF -->
            <div class="relative bg-gray-100 dark:bg-gray-900" :class="isFullscreen ? 'h-screen' : 'h-[600px]'">
              <!-- Loading spinner -->
              <div v-if="isLoading" class="absolute inset-0 flex items-center justify-center bg-gray-50 dark:bg-gray-900 z-10">
                <div class="text-center">
                  <svg class="animate-spin h-12 w-12 text-blue-600 mx-auto mb-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  <p class="text-sm text-gray-600 dark:text-gray-400">Cargando documento...</p>
                </div>
              </div>

              <!-- Error state -->
              <div v-if="hasError" class="absolute inset-0 flex items-center justify-center bg-gray-50 dark:bg-gray-900 z-10">
                <div class="text-center px-4">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 text-red-500 mx-auto mb-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                  </svg>
                  <p class="text-lg font-semibold text-gray-800 dark:text-gray-200 mb-2">Error al cargar el documento</p>
                  <p class="text-sm text-gray-600 dark:text-gray-400 mb-4">No se pudo cargar el archivo PDF. Por favor, intenta de nuevo.</p>
                  <button
                    @click="reloadPdf"
                    class="px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg transition-colors duration-200"
                  >
                    Reintentar
                  </button>
                </div>
              </div>

              <!-- PDF Viewer -->
              <iframe
                v-show="!isLoading && !hasError"
                :src="pdfUrl"
                @load="handlePdfLoad"
                @error="handlePdfError"
                class="w-full h-full border-0"
                frameborder="0"
              ></iframe>
            </div>

            <!-- Footer informativo -->
            <div class="bg-gray-50 dark:bg-gray-800 px-4 py-2 border-t border-gray-200 dark:border-gray-700">
              <div class="flex items-center justify-between text-xs">
                <div class="flex items-center space-x-4 text-gray-600 dark:text-gray-400">
                  <span class="flex items-center">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    Tipo: {{ currentFile?.tipo === 'principal' ? 'Documento Principal' : 'Anexo' }}
                  </span>
                </div>
                <div class="flex items-center space-x-2 text-gray-500 dark:text-gray-400">
                  <span>Usa Ctrl + scroll para zoom</span>
                </div>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </Modal>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { mdiEye } from '@mdi/js';
import BaseButton from '@/Components/BaseButton.vue';
import Modal from '@/Components/Modal.vue';

const props = defineProps({
  documento: {
    type: Object,
    required: true
  },
  tipoDocumento: {
    type: String,
    default: 'documento'
  }
});

const isOpen = ref(false);
const showPdfViewer = ref(false);
const pdfUrl = ref('');
const currentFile = ref(null);
const isLoading = ref(false);
const hasError = ref(false);
const isFullscreen = ref(false);

const archivosPrincipales = computed(() =>
  props.documento.archivos?.filter(a => a.tipo === 'principal') || []
);

const archivosAnexos = computed(() =>
  props.documento.archivos?.filter(a => a.tipo === 'anexo') || []
);

const todosArchivos = computed(() =>
  [...archivosPrincipales.value, ...archivosAnexos.value]
);

const currentFileName = computed(() => currentFile.value?.nombre_original || 'Documento');

const revalidationStatusClass = computed(() => {
  if (props.documento.dias_restantes_revalidacion <= 0) return 'bg-red-500 text-white';
  if (props.documento.dias_restantes_revalidacion <= 7) return 'bg-red-200 text-red-800';
  return 'bg-green-100 text-green-800';
});

const revalidationStatusText = computed(() =>
  props.documento.dias_restantes_revalidacion <= 0
    ? 'Vencido'
    : `${props.documento.dias_restantes_revalidacion} días restantes`
);

const expirationStatusClass = computed(() => {
  if (props.documento.dias_restantes <= 0) return 'bg-red-500 text-white';
  if (props.documento.dias_restantes <= 7) return 'bg-red-200 text-red-800';
  return 'bg-green-100 text-green-800';
});

const expirationStatusText = computed(() =>
  props.documento.dias_restantes <= 0
    ? 'Vencido'
    : `${props.documento.dias_restantes} días restantes`
);

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('es-MX');
};

const openModal = () => {
  isOpen.value = true;
};

const closeModal = () => {
  isOpen.value = false;
  closePdfViewer();
  if (isFullscreen.value) {
    toggleFullscreen();
  }
};

const showFileInModal = (filePath, file) => {
  isLoading.value = true;
  hasError.value = false;
  pdfUrl.value = `/storage/${filePath}`;
  currentFile.value = file;
  showPdfViewer.value = true;
};

const closePdfViewer = () => {
  pdfUrl.value = '';
  currentFile.value = null;
  showPdfViewer.value = false;
  isLoading.value = false;
  hasError.value = false;
  if (isFullscreen.value) {
    isFullscreen.value = false;
  }
};

const handlePdfLoad = () => {
  isLoading.value = false;
  hasError.value = false;
};

const handlePdfError = () => {
  isLoading.value = false;
  hasError.value = true;
};

const reloadPdf = () => {
  if (currentFile.value) {
    showFileInModal(currentFile.value.ruta_archivo, currentFile.value);
  }
};

const downloadCurrentFile = () => {
  if (pdfUrl.value) {
    const link = document.createElement('a');
    link.href = pdfUrl.value;
    link.download = currentFileName.value;
    link.target = '_blank';
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  }
};

const openInNewTab = () => {
  if (pdfUrl.value) {
    window.open(pdfUrl.value, '_blank');
  }
};

const toggleFullscreen = () => {
  isFullscreen.value = !isFullscreen.value;

  if (isFullscreen.value) {
    // Deshabilitar scroll del body
    document.body.style.overflow = 'hidden';
  } else {
    // Restaurar scroll del body
    document.body.style.overflow = '';
  }
};

const downloadAll = () => {
  window.location.href = `/${props.tipoDocumento}/${props.documento.id}/descargar-todos`;
};

// Manejar tecla ESC para salir de pantalla completa
const handleEscKey = (event) => {
  if (event.key === 'Escape' && isFullscreen.value) {
    toggleFullscreen();
  }
};

onMounted(() => {
  window.addEventListener('keydown', handleEscKey);
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleEscKey);
  // Asegurar que el scroll se restaure al desmontar
  document.body.style.overflow = '';
});
</script>