<script setup>
import { reactive, computed, ref } from 'vue'

const tienda = reactive({
  catalogo: [
    { id: 1, nombre: 'Polera TalentoDev', precio: 12990 },
    { id: 2, nombre: 'Taza "console.log"', precio: 6990 },
    { id: 3, nombre: 'Stickers pack', precio: 3990 },
    { id: 4, nombre: 'Hoodie Vue', precio: 34990 }
  ],
  carrito: []
})

const cliente = reactive({
  nombre: '',
  email: ''
})

function agregarAlCarrito(producto) {
  const item = tienda.carrito.find(p => p.id === producto.id)
  if (item) {
    item.cantidad++
  } else {
    tienda.carrito.push({
      id: producto.id,
      nombre: producto.nombre,
      precio: producto.precio,
      cantidad: 1
    })
  }
}

function quitarDelCarrito(id) {
  const index = tienda.carrito.findIndex(p => p.id === id)
  if (index !== -1) {
    tienda.carrito.splice(index, 1)
  }
}

function cambiarCantidad(id, delta) {
  const item = tienda.carrito.find(p => p.id === id)
  if (!item) return
  const nuevaCantidad = item.cantidad + delta
  item.cantidad = nuevaCantidad < 1 ? 1 : nuevaCantidad
}

const totalItems = computed(() =>
  tienda.carrito.reduce((acc, item) => acc + item.cantidad, 0)
)

const subtotal = computed(() =>
  tienda.carrito.reduce((acc, item) => acc + item.precio * item.cantidad, 0)
)

const descuento = computed(() => {
  if (subtotal.value >= 50000) {
    return subtotal.value * 0.1
  }
  return 0
})

const total = computed(() => subtotal.value - descuento.value)

const emailValido = computed(() =>
  /^[^@]+@[^@]+\.[^@]+$/.test(cliente.email)
)

const puedeComprar = computed(() => {
  return totalItems.value > 0 &&
    cliente.nombre.trim() !== '' &&
    emailValido.value
})

// Mensaje de éxito
const compraFinalizada = ref(false)

function finalizarCompra() {
  if (!puedeComprar.value) return

  compraFinalizada.value = true

  // Vaciar carrito de forma reactiva
  tienda.carrito.splice(0, tienda.carrito.length)

  // Resetear datos del cliente
  cliente.nombre = ''
  cliente.email = ''

  setTimeout(() => {
    compraFinalizada.value = false
  }, 3000)
}
</script>

<template>
  <main class="app">
    <!-- Catálogo -->
    <section class="panel panel-catalogo">
      <h2>Catálogo</h2>

      <ul class="lista">
        <li
          v-for="producto in tienda.catalogo"
          :key="producto.id"
          class="item item-producto"
        >
          <div class="item-info">
            <span class="item-nombre">{{ producto.nombre }}</span>
            <span class="item-precio">
              \${{ producto.precio.toLocaleString('es-CL') }}
            </span>
          </div>
          <button class="btn btn-agregar" @click="agregarAlCarrito(producto)">
            + Agregar
          </button>
        </li>
      </ul>
    </section>

    <!-- Carrito -->
    <section class="panel panel-carrito">
      <h2>Carrito ({{ totalItems }})</h2>

      <p v-if="tienda.carrito.length === 0" class="carrito-vacio">
        El carrito está vacío 🛒
      </p>

      <ul v-else class="lista">
        <li
          v-for="item in tienda.carrito"
          :key="item.id"
          class="item item-carrito"
        >
          <div class="item-info">
            <span class="item-nombre">{{ item.nombre }}</span>
            <span class="item-precio">
              Unidad: \${{ item.precio.toLocaleString('es-CL') }}
            </span>
            <span class="item-cantidad">
              Cantidad: {{ item.cantidad }}
            </span>
          </div>

          <div class="item-controles">
            <button class="btn btn-cantidad" @click="cambiarCantidad(item.id, -1)">−</button>
            <button class="btn btn-cantidad" @click="cambiarCantidad(item.id, +1)">+</button>
            <button class="btn btn-quitar" @click="quitarDelCarrito(item.id)">Quitar</button>
          </div>
        </li>
      </ul>

      <!-- Totales -->
      <div class="totales">
        <p>Subtotal: <span class="monto">\${{ subtotal.toLocaleString('es-CL') }}</span></p>
        <p>Descuento: <span class="monto">−\${{ descuento.toLocaleString('es-CL') }}</span></p>
        <p class="totales-total">
          Total: <span class="monto">\${{ total.toLocaleString('es-CL') }}</span>
        </p>
      </div>
    </section>

    <!-- Datos del cliente -->
    <section class="panel panel-cliente">
      <h2>Tus datos</h2>

      <div class="campo">
        <label>
          Nombre *
          <input
            type="text"
            v-model="cliente.nombre"
            placeholder="Tu nombre"
          />
        </label>
      </div>

      <div class="campo">
        <label>
          Email *
          <input
            type="email"
            v-model="cliente.email"
            placeholder="tucorreo@mail.com"
          />
        </label>
      </div>

      <button
        class="btn btn-finalizar"
        :disabled="!puedeComprar"
        @click="finalizarCompra"
      >
        Finalizar compra
      </button>

      <p v-if="!puedeComprar" class="ayuda">
        Completa carrito y datos
      </p>

      <p v-if="compraFinalizada" class="mensaje-exito">
        ¡Gracias por tu compra, {{ cliente.nombre }}! Total pagado:
        \${{ total.toLocaleString('es-CL') }}
      </p>
    </section>
  </main>
</template>

<style scoped>
.app {
  max-width: 1100px;
  margin: 2rem auto;
  padding: 1.5rem;
  display: grid;
  grid-template-columns: 2fr 2fr 1.8fr;
  gap: 1.5rem;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}

/* Paneles generales */
.panel {
  background-color: #f7f7f9;
  border-radius: 12px;
  padding: 1rem;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.08);
}

.panel h2 {
  margin-bottom: 0.75rem;
  font-size: 1.1rem;
}

/* Listas */
.lista {
  list-style: none;
  margin: 0;
  padding: 0;
}

.item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.6rem 0.7rem;
  border-radius: 8px;
  background-color: #ffffff;
  margin-bottom: 0.5rem;
}

.item-info {
  display: flex;
  flex-direction: column;
  gap: 0.2rem;
}

.item-nombre {
  font-weight: 600;
}

.item-precio {
  font-size: 0.9rem;
  color: #555;
}

.item-cantidad {
  font-size: 0.85rem;
  color: #777;
}

/* Botones */
.btn {
  border: none;
  border-radius: 999px;
  padding: 0.4rem 0.9rem;
  font-size: 0.9rem;
  cursor: pointer;
  transition: transform 0.1s ease, box-shadow 0.1s ease, background-color 0.1s ease;
}

.btn:active {
  transform: translateY(1px);
  box-shadow: none;
}

.btn-agregar {
  background-color: #26a65b;
  color: #fff;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.12);
}

.btn-agregar:hover {
  background-color: #1f8a4c;
}

.item-controles {
  display: flex;
  gap: 0.3rem;
  align-items: center;
}

.btn-cantidad {
  background-color: #ecf0f1;
  color: #333;
  padding-inline: 0.6rem;
}

.btn-quitar {
  background-color: #e74c3c;
  color: #fff;
}

.btn-finalizar {
  width: 100%;
  margin-top: 0.75rem;
  background-color: #3498db;
  color: #fff;
  font-weight: 600;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.12);
}

.btn-finalizar:disabled {
  background-color: #95a5a6;
  cursor: not-allowed;
  box-shadow: none;
}

/* Carrito */
.panel-carrito .totales {
  margin-top: 0.8rem;
  border-top: 1px solid #ddd;
  padding-top: 0.6rem;
}

.panel-carrito .monto {
  font-weight: 600;
}

.panel-carrito .totales-total {
  margin-top: 0.4rem;
  font-size: 1rem;
}

.carrito-vacio {
  font-size: 0.9rem;
  color: #666;
  font-style: italic;
}

/* Cliente */
.panel-cliente .campo {
  margin-bottom: 0.9rem;
}

.panel-cliente label {
  display: block;
  font-size: 0.9rem;
}

.panel-cliente input {
  display: block;
  width: 100%;
  box-sizing: border-box;
  margin-top: 0.35rem;
  padding: 0.5rem 0.6rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 0.9rem;
  background-color: #fff;
}

.panel-cliente {
  padding: 1.2rem;
}

.mensaje-exito {
  margin-top: 0.7rem;
  padding: 0.6rem 0.7rem;
  background-color: #e8f7e4;
  border-radius: 8px;
  font-size: 0.9rem;
  color: #2e7d32;
}

/* Responsive simple */
@media (max-width: 900px) {
  .app {
    grid-template-columns: 1fr;
  }

  .panel {
    margin-bottom: 1rem;
  }
}
</style>