import React, { useState } from "react";
import { Button } from "./components/Button";
import { Card } from "./components/Card";
import { Input } from "./components/Input";
import { motion } from "framer-motion";
import { ShoppingCart, Trash2 } from "lucide-react";
import { loadStripe } from "@stripe/stripe-js";

const stripePromise = loadStripe("pk_test_REEMPLAZA_CON_TU_CLAVE_PUBLICA");

const productosDisponibles = [
  { nombre: "Caléndula", precio: 5.99, img: "/img/producto1.jpg" },
  { nombre: "Avena & Miel", precio: 6.49, img: "/img/producto2.jpg" },
  { nombre: "Manzanilla", precio: 5.49, img: "/img/producto3.jpg" },
];

export default function App() {
  const [carrito, setCarrito] = useState([]);

  const agregarAlCarrito = (producto) => {
    setCarrito((prev) => [...prev, producto]);
  };

  const eliminarDelCarrito = (index) => {
    setCarrito((prev) => prev.filter((_, i) => i !== index));
  };

  const total = carrito.reduce((acc, p) => acc + p.precio, 0).toFixed(2);

  const handleCheckout = async () => {
    const stripe = await stripePromise;
    const response = await fetch("https://mi-backend-stripe.vercel.app/api/checkout", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ items: carrito }),
    });
    const session = await response.json();
    await stripe.redirectToCheckout({ sessionId: session.id });
  };

  return (
    <div className="min-h-screen bg-pink-50 text-gray-800">
      {/* Header */}
      <header className="bg-white shadow-md p-6 flex justify-between items-center">
        <h1 className="text-3xl font-bold text-pink-600">BabyCare Jabones</h1>
        <nav className="space-x-4">
          <a href="#productos" className="text-gray-600 hover:text-pink-600">Productos</a>
          <a href="#sobre-nosotros" className="text-gray-600 hover:text-pink-600">Sobre Nosotros</a>
          <a href="#contacto" className="text-gray-600 hover:text-pink-600">Contacto</a>
          <a href="#carrito" className="text-pink-600 flex items-center">
            <ShoppingCart className="mr-1" />
            ({carrito.length})
          </a>
        </nav>
      </header>

      {/* Hero Section */}
      <section className="bg-pink-100 p-12 text-center">
        <motion.h2 
          className="text-4xl font-bold mb-4"
          initial={{ opacity: 0, y: -20 }} 
          animate={{ opacity: 1, y: 0 }} 
          transition={{ duration: 0.8 }}
        >
          Cuidado Natural para la Piel Delicada de tu Bebé
        </motion.h2>
        <p className="text-lg mb-6">
          Nuestros jabones están formulados con ingredientes orgánicos, ideales para pieles sensibles.
        </p>
        <Button className="bg-pink-600 text-white px-6 py-2 rounded-full hover:bg-pink-700">
          Ver Productos
        </Button>
      </section>

      {/* Productos */}
      <section id="productos" className="p-10 grid grid-cols-1 md:grid-cols-3 gap-6">
        {productosDisponibles.map((producto, index) => (
          <Card key={index} className="bg-white shadow-md rounded-2xl p-4">
            <img src={producto.img} alt={producto.nombre} className="w-full h-48 object-cover rounded-lg mb-4" />
            <h3 className="text-xl font-semibold text-pink-600 mb-2">Jabón de {producto.nombre}</h3>
            <p className="text-gray-600 text-sm mb-2">
              Suave y natural, perfecto para la higiene diaria de tu bebé.
            </p>
            <p className="text-pink-600 font-bold mb-4">${producto.precio.toFixed(2)}</p>
            <Button 
              className="bg-pink-500 text-white hover:bg-pink-600" 
              onClick={() => agregarAlCarrito(producto)}
            >
              Agregar al Carrito
            </Button>
          </Card>
        ))}
      </section>

      {/* Carrito */}
      <section id="carrito" className="bg-white p-10">
        <h2 className="text-3xl font-bold text-center text-pink-600 mb-6">Tu Carrito</h2>
        {carrito.length === 0 ? (
          <p className="text-center text-gray-600">Aún no has agregado productos al carrito.</p>
        ) : (
          <div className="max-w-xl mx-auto space-y-4">
            {carrito.map((item, index) => (
              <div key={index} className="flex justify-between items-center bg-pink-50 p-4 rounded-xl">
                <span>{item.nombre}</span>
                <span>${item.precio.toFixed(2)}</span>
                <Button variant="ghost" onClick={() => eliminarDelCarrito(index)}>
                  <Trash2 className="text-red-500" />
                </Button>
              </div>
            ))}
            <div className="text-right font-semibold text-pink-600">
              Total: ${total}
            </div>
            <Button onClick={handleCheckout} className="bg-pink-600 text-white hover:bg-pink-700 w-full rounded-full">
              Proceder al Pago
            </Button>
          </div>
        )}
      </section>

      {/* Sobre Nosotros */}
      <section id="sobre-nosotros" className="bg-pink-100 p-10 text-center">
        <h2 className="text-3xl font-bold text-pink-600 mb-4">Sobre Nosotros</h2>
        <p className="text-gray-700 max-w-2xl mx-auto">
          En BabyCare Jabones nos dedicamos a ofrecer productos de la más alta calidad, pensando en el bienestar de los más pequeños. Nuestro compromiso con ingredientes naturales y procesos artesanales garantiza una experiencia segura y confiable para toda la familia.
        </p>
      </section>

      {/* Contacto */}
      <section id="contacto" className="bg-pink-50 p-10">
        <h2 className="text-3xl font-bold text-center text-pink-600 mb-6">Contáctanos</h2>
        <form className="max-w-xl mx-auto grid gap-4">
          <Input placeholder="Nombre" className="rounded-xl" />
          <Input placeholder="Correo electrónico" type="email" className="rounded-xl" />
          <Input placeholder="Mensaje" className="rounded-xl" />
          <Button className="bg-pink-600 text-white hover:bg-pink-700 rounded-full">
            Enviar Mensaje
          </Button>
        </form>
      </section>

      {/* Footer */}
      <footer className="bg-white text-center text-gray-500 text-sm py-6">
        © 2025 BabyCare Jabones. Todos los derechos reservados.
      </footer>
    </div>
  );
}
