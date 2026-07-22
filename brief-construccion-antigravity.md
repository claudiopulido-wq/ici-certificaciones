# Brief de construcción — ICI / Certify Your Future
**Para: Google Antigravity (agente constructor)**
**De: Dirección Senior de Imagen (aprobador y revisor de calidad)**
**Estado: Diseño aprobado por el cliente — listo para construcción**

---

## 1. Rol y cadena de mando
- Tú (Antigravity) eres el agente constructor. Ejecutas, no rediseñas.
- Cualquier decisión de diseño, contenido o UX que no esté cubierta aquí debe preguntarse antes de improvisar — no asumas.
- La Dirección de Imagen revisa cada entrega antes de pasar a producción. No publiques directo a producción sin ese visto bueno.

## 2. Fuente de verdad
Adjunto: `ici-maqueta.html` (maqueta HTML interactiva aprobada). Es la referencia exacta de:
- Estructura y orden de secciones
- Paleta de color y tipografía (variables CSS `:root` al inicio del archivo)
- Comportamiento responsive e interacciones (menú, acordeones, contador, countdown, chatbot)

No te bases en el sitio actual en producción (certifyyourfuture.org) para diseño — solo la maqueta adjunta es válida como referencia visual/estructural.

## 3. Stack objetivo
El sitio se construye como **HTML5/CSS/JS estático real** (recomendado: Astro como generador, compilando a HTML puro — o HTML escrito a mano si se prefiere cero build tools). Motivo: el cliente cuenta con hosting compartido tipo cPanel (solo PHP, sin Node.js) y el contenido siempre lo actualiza un desarrollador/agencia, no un equipo de marketing — por lo que no se necesita WordPress ni un CMS headless.

Reglas de esta decisión:
- Nada de WordPress, plugins ni bases de datos MySQL para el contenido del sitio.
- Los datos de certificados/instructores viven como archivos estructurados (Markdown/JSON) dentro del propio proyecto, y se compilan a páginas HTML estáticas en el build. Cada certificado/instructor nuevo = un archivo de datos nuevo, no HTML copiado a mano.
- El deploy final son archivos estáticos subidos por FTP/File Manager al hosting cPanel existente — sin requerir Node.js en el servidor de producción.

### Chatbot RAG — arquitectura separada
El hosting cPanel compartido no puede correr un servicio persistente con base vectorial, así que el chatbot **no vive en el mismo hosting que el sitio**:
- Se despliega como un microservicio aparte (función serverless tipo Cloudflare Workers/Vercel, o un VPS económico dedicado solo a esto).
- El sitio estático solo hace una petición HTTPS (`fetch`) a esa API cuando el usuario escribe en el chat — ver función `chatSend()` en la maqueta.
- Si el chatbot falla o está en mantenimiento, el sitio principal no se ve afectado.

## 4. Alcance de páginas
1. **Home** (`/`)
2. **Detalle de certificado** (`/certificaciones/{slug}`) — plantilla reutilizable, no una página fija. Debe soportar múltiples certificaciones (Personal Trainer, Nutrición Deportiva, Entrenamiento Funcional, y futuras) desde el mismo template.
3. **Instructores** (`/instructores`) — listado + ficha individual reutilizable por instructor (`/instructores/{slug}`), aunque la maqueta solo muestra el listado.

## 5. Design tokens (obligatorios, no reinterpretar)
```
Azul:      #1B3F8C
Azul dark: #132C63
Rojo:      #E31E2E
Amarillo:  #F7B500
Negro:     #111111
Gris claro:#F5F6F8
Display:   Poppins (600/700/800)
Body:      Inter (400/500/600/700)
```
El hero usa el diagonal azul/amarillo con franja roja inferior — respetar el corte exacto (ver CSS `.hero::before` / `.hero::after` en la maqueta), no un rectángulo de color plano.

## 6. Contenido
- Reemplazar TODO el contenido placeholder (temarios, precios, nombres de instructores, testimonios, cifras de contadores) por el contenido real que el cliente ya tiene aprobado.
- Todos los bloques marcados como `video-frame` / `video-label` / `tour-video` deben recibir los archivos `.mp4` 3D en alta definición del cliente — respetar el aspect-ratio indicado en cada bloque (16:10 en hero, 21:9 en tour del centro, 9:11 en testimonios).
- No inventar cifras, precios ni testimonios nuevos — solicitar al cliente si falta algún dato real.

## 7. Responsive
- Mobile-first y desktop igual de prioritarios (no uno "adaptado" del otro).
- Breakpoints de referencia ya en la maqueta: 900px (tablet/menú hamburguesa) y 600px (mobile: 1 columna, CTA fija inferior en detalle de certificado).
- Probar en anchos reales: 375px, 768px, 1024px, 1440px — no solo redimensionar el navegador de escritorio.

## 8. Animaciones y motion
- Mantener únicamente las animaciones ya definidas en la maqueta (fade/parallax sutil, count-up de estadísticas, countdown en vivo, hover en tarjetas). No agregar animaciones adicionales "porque sí".
- Respetar `prefers-reduced-motion` (ya contemplado en la maqueta).

## 9. Chatbot con memoria RAG — especificación funcional
El front-end del widget ya está construido en la maqueta (botón flotante + panel). El backend vive **fuera** del hosting cPanel del sitio (ver sección 3):
- **Despliegue:** microservicio independiente (serverless o VPS pequeño), expuesto como endpoint HTTPS que el sitio estático consume vía `fetch`.
- **Base de conocimiento:** ingerir temarios, precios, planes de pago, FAQs y políticas (garantía laboral, validez internacional) de cada certificación como fuente de retrieval.
- **Comportamiento esperado:** responder solo con base en el contenido indexado de ICI (no inventar precios ni promesas fuera de lo publicado). Si no tiene la respuesta, debe derivar a un asesor humano (no alucinar).
- **Actualización de contenido:** la base de conocimiento debe poder actualizarse cuando cambien precios/temarios, sin requerir un nuevo deploy del sitio estático.
- **Punto de integración en el código:** función `chatSend()` en el JS de la maqueta — ahí se reemplaza la respuesta simulada por la llamada `fetch` real al endpoint del microservicio.
- **Idioma:** español, tono cercano y profesional, igual que el resto del copy del sitio.

## 10. SEO y performance
- Server-side rendering / HTML real (no depender solo de JS para el contenido indexable de certificados e instructores).
- Imágenes y videos optimizados/comprimidos, carga diferida (lazy load) fuera del viewport inicial.
- Meta tags, Open Graph y datos estructurados (schema.org Course/Organization) por página de certificado.

## 11. Accesibilidad
- Contraste AA mínimo en todos los textos sobre fondo de color (revisar especialmente texto blanco sobre el hero azul/amarillo).
- Foco de teclado visible (ya definido en la maqueta vía `:focus-visible`).
- Textos alternativos reales en imágenes/video (no "imagen1.jpg").

## 12. Checklist de aceptación (antes de pasar a revisión de Dirección de Imagen)
- [ ] Las 3 plantillas (home, certificado, instructor) funcionan con contenido real, no placeholder
- [ ] Responsive validado en los 4 anchos de referencia
- [ ] Chatbot conectado a datos reales, sin alucinaciones verificadas manualmente con 10 preguntas de prueba
- [ ] Videos 3D cargando correctamente y con fallback de imagen si el video falla
- [ ] Lighthouse: Performance y SEO ≥ 90 en Home
- [ ] Ningún color/tipografía fuera de los tokens definidos en la sección 5

---
**Siguiente paso:** entregar este brief + `ici-maqueta.html` a Antigravity como instrucción única de construcción. Cuando tenga un primer avance, lo reviso antes de que continúe con el resto del sitio.
