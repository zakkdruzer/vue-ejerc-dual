<script setup>
import { reactive, computed } from 'vue'

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
</script>

<template>
  <main class="app">
    <!-- Catálogo -->
    <section class="catalogo">
      <h2>Catálogo</h2>

      <ul>
        <li
          v-for="producto in tienda.catalogo"
          :key="producto.id"
          class="producto"
        >
          <span>{{ producto.nombre }}</span>
          <span>\${{ producto.precio.toLocaleString('es-CL') }}</span>
          <button @click="agregarAlCarrito(producto)">
            + Agregar
          </button>
        </li>
      </ul>
    </section>
    <!-- Carrito -->
    <section class="carrito">
      <h2>Carrito ({{ totalItems }})</h2>

      <p v-if="tienda.carrito.length === 0">
        El carrito está vacío 🛒
      </p>

      <ul v-else>
        <li
          v-for="item in tienda.carrito"
          :key="item.id"
          class="carrito-item"
        >
          <span>{{ item.nombre }}</span>
          <span>Unidad: \${{ item.precio.toLocaleString('es-CL') }}</span>
          <span>Cantidad: {{ item.cantidad }}</span>

          <button @click="cambiarCantidad(item.id, -1)">−</button>
          <button @click="cambiarCantidad(item.id, +1)">+</button>
          <button @click="quitarDelCarrito(item.id)">Quitar</button>
        </li>
      </ul>

      <!-- Totales -->
      <div class="totales">
        <p>Subtotal: \${{ subtotal.toLocaleString('es-CL') }}</p>
        <p>Descuento: −\${{ descuento.toLocaleString('es-CL') }}</p>
        <p>Total: \${{ total.toLocaleString('es-CL') }}</p>
      </div>
    </section>
    <!-- Datos del cliente -->
    <section class="cliente">
      <h2>Tus datos</h2>

      <div>
        <label>
          Nombre *
          <input
            type="text"
            v-model="cliente.nombre"
            placeholder="Tu nombre"
          />
        </label>
      </div>

      <div>
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
        :disabled="!puedeComprar"
      >
        Finalizar compra
      </button>

      <p v-if="!puedeComprar">
        Completa carrito y datos
      </p>
    </section>
  </main>
</template>