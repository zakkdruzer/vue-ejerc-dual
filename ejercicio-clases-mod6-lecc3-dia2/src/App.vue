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

const emailValido = computed(() => {
  const valor = cliente.email.trim()
  return valor.includes('@') && valor.includes('.')
})

const puedeComprar = computed(() => {
  return totalItems.value > 0 &&
    cliente.nombre.trim() !== '' &&
    emailValido.value
})
</script>

<template>
</template>