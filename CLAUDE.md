TCG Center Perú — Build Brief (Shopify Storefront v1)

Documento de trabajo para Claude Code (Fable 5). Opus define el qué y el porqué; Fable 5 ejecuta el código. Este archivo puede vivir como CLAUDE.md en la raíz del repo del theme.


0. Contexto y objetivo

TCG Center Perú es una tienda de trading card games en Lima (rebrand de "Pokemon Center Perú"). Vende singles, sellado, preventa, accesorios y peluches y figuras. Multi-juego desde el día uno (Pokémon primero, pero la estructura no debe quedar amarrada a Pokémon).

Objetivo de v1: una tienda Shopify que se vea como TCG Center, con carga manual de productos por parte del dueño y sus empleados. El motor de precios automático (TCGplayer x multiplicador) queda para una fase posterior, pero el esquema de datos de v1 debe dejarlo listo para conectar sin retrabajo.

Fuera de alcance en v1: el price-sync engine, scraping de precios, cualquier cron job. Solo la tienda visual y funcional.


1. Enfoque técnico

Theme: partir de Dawn (theme gratis y oficial de Shopify, Online Store 2.0) y customizarlo. No construir Liquid desde cero. Razón: Dawn ya trae secciones, accesibilidad y estructura sólida; el trabajo es re-skinnearlo y agregar las piezas TCG. Los empleados siguen cargando productos en el admin nativo de Shopify, que es justo el requisito de simplicidad.

Workflow de desarrollo:


Shopify CLI (shopify theme dev para preview local con hot reload, shopify theme push para subir a un theme de desarrollo, nunca directo al live).
Git para versionar. Rama main estable, ramas por fase (feat/brand-skin, feat/home, etc.).
Nunca publicar sobre el theme activo sin revisión. Se trabaja sobre un theme duplicado / unpublished y se publica al final.


Restricción de CSS: cuidar la especificidad de selectores. Dawn mezcla selectores por clase de sección y por elemento; es fácil generar reglas de padding/margin que se cancelan entre secciones. Definir los tokens en :root y usar variables CSS en vez de valores sueltos.


2. Sistema de marca

Tokens extraídos del logo y del feed de Instagram. Los hex son aproximados a ojo desde las imágenes; confirmar con el diseñador si existe el manual de marca antes de cerrar la paleta.

Color (definir en :root)

--tcg-red:      #E12B24;  /* rojo escarlata, color primario de marca */
--tcg-oxblood:  #5A0E12;  /* rojo muy oscuro, texto sobre claro y trazo del logo */
--tcg-coral:    #F2937E;  /* coral cálido, degradados y fondos suaves */
--tcg-ember:    #EC6A1E;  /* naranja energía, CTAs y acentos */
--tcg-gold:     #F4C13A;  /* dorado, precios destacados y badges */
--tcg-cream:    #FFF6F0;  /* fondo cálido casi blanco */
--tcg-ink:      #1C0B0B;  /* casi negro cálido, texto principal */

Uso: fondo --tcg-cream, texto --tcg-ink, botones primarios --tcg-red (hover --tcg-oxblood), CTAs de urgencia y preventa --tcg-ember, precios y badges --tcg-gold. El oxblood da profundidad y contraste; evitar que todo sea rojo plano.

Tipografía

Pareja deliberada, ambas gratis (Google Fonts o el font picker de Shopify):


Display (títulos, hero, nombres de sección): una condensada bold y con carácter tipo Anton o Archivo Black, que hace eco del "CENTER" del logo. Usar con restricción, solo en headers grandes.
Cuerpo: Inter o Archivo regular/medium, alta legibilidad para catálogo y precios.


Escala tipográfica clara y con intención (por ejemplo 12 / 14 / 16 / 20 / 28 / 40 / 64), no tamaños al azar.

Signature element

El elemento que hace memorable la tienda: el abanico de dos cartas con el destello de energía (fuego y rayo) del logo, reinterpretado como acento gráfico en el hero y en los separadores de sección (el mismo lenguaje "starburst" naranja que ya usan en los posts de "Sábado TCG Center"). Gastar la audacia visual aquí, y mantener el resto limpio y ordenado.

Logo


Emblema cuadrado (para favicon, avatar, footer).
Lockup horizontal "TCG Center Perú" (para header).
Pedir versiones en SVG o PNG a alta resolución con fondo transparente. Generar favicon desde el emblema.



3. Arquitectura de información

Navegación principal (multi-juego)

Pokémon | Magic | Yu-Gi-Oh! | One Piece | Otros TCG | Sellado y Preventa | Accesorios | Peluches y Figuras | Eventos

En v1 se llenan solo las categorías con stock real; el resto queda estructurado pero puede ir oculto hasta tener producto.

Product types (campo product_type de Shopify)

Single, Sellado, Preventa, Accesorio, Peluche/Figura. Manejar preventa como estado del producto, no como tipo separado si complica; decidir en implementación y documentar la elección.

Taxonomía por tags (esto maneja el filtrado)


game:pokemon, game:mtg, game:yugioh, game:onepiece, game:lorcana
set:surging-sparks, set:... (mapear a los sets reales que stockean)
condition:NM, condition:LP, condition:MP, condition:HP (solo singles)
lang:es, lang:en (recalcan "versión español"; es un diferenciador, hacerlo visible)
rarity:... (opcional en v1)


Colecciones

Espejo de cómo ya organizan en WooCommerce: juego, luego set. Ejemplo: Pokémon / Scarlet & Violet / Surging Sparks. Más colecciones transversales: Preventa, Novedades, Sellado, Accesorios, Peluches y Figuras. Usar smart collections por tag donde se pueda para que se auto-poblen.


4. Plantillas y páginas a construir


Home: hero con signature de marca, bloque de Preventa destacado (con fecha de lanzamiento), tiles "compra por juego", carrusel de novedades (singles y sellado), banner de Sábados TCG Center (comunidad), bloque de locales (Sede Lince y Sede Surco), feed de Instagram, newsletter.
Colección: filtros por juego, set, condición, idioma y precio; orden; grid de product cards. La card muestra precio en S/, badge de condición (singles), estado de stock y badge de preventa cuando aplica.
Producto — Single: imagen, condición, set, rareza, idioma (ES/EN), precio S/, cantidad, agregar al carrito, y botón secundario de consulta por WhatsApp.
Producto — Sellado / Preventa: normal, más badge de preventa y fecha de release; permitir vender agotado en preventa (continue selling) según decida el dueño.
Carrito y checkout: nativo de Shopify, con opción de contacto por WhatsApp como respaldo (en Perú WhatsApp pesa; mantenerlo como canal, no como único método).
Páginas estáticas: Nosotros, Locales (Lince y Surco con mapa), Eventos / Sábados TCG, Envíos y pagos, Preguntas frecuentes.



5. Esquema de producto y metafields (diseñar para el futuro motor de precios)

Aunque v1 es carga manual, definir desde ya los metafields en los singles para que el price-sync engine se conecte después sin migración:

custom.tcgplayer_id      (string)   ID del producto en la fuente de precios
custom.price_multiplier  (number)   multiplicador del dueño, ej. 3 o 4
custom.market_price_usd  (number)   último precio de referencia en USD
custom.last_synced_at    (date)     cuándo se actualizó por última vez

En v1 estos campos quedan vacíos o se llenan a mano si el dueño quiere. En la fase futura, el cron los lee/escribe. Esto es lo único de "diseño para el futuro" que hacemos ahora; no construir nada del engine todavía.


6. Pagos y envíos Perú (configuración, no código)


Pagos: evaluar Culqi o Mercado Pago Perú (aceptan tarjetas, Yape, PagoEfectivo). Confirmar cuál integra mejor con Shopify al momento de configurar.
Envíos: delivery Lima, provincias vía courier (Olva / Shalom), y retiro en tienda en ambas sedes (Lince y Surco).
Esto lo configura el dueño en el admin; el brief solo lo deja anotado como checklist de lanzamiento.



7. Fases de ejecución (MVP primero)


Fase 0 — Setup: Shopify CLI, clonar Dawn, repo Git, theme de desarrollo conectado a la tienda, archivo de tokens en :root.
Fase 1 — Brand skin: aplicar colores, tipografías, logo, favicon, botones y estilos globales. Solo con esto ya "se ve" TCG Center. Entregable de mayor impacto visible.
Fase 2 — IA y navegación: menús multi-juego, estructura de colecciones, tags.
Fase 3 — Home: todas las secciones del punto 4.1.
Fase 4 — Plantillas de colección y producto: filtros y las variantes single vs sellado/preventa, con condición, set, idioma y badges.
Fase 5 — Páginas estáticas y contacto: Nosotros, Locales, Eventos, Envíos y pagos, FAQ, WhatsApp.
Fase 6 — Pagos y envíos: checklist de configuración Perú.
Fase 7 — Métricas y analítica (fase distinta): GA4, Shopify Analytics, Meta Pixel (son fuertes en IG/FB), eventos de conversión (view product, add to cart, checkout, purchase), y KPIs base: sesiones, tasa de conversión, AOV, productos más vistos, preventas reservadas. Definir un tablero simple de lectura semanal.
Fase 8 — Futuro, no ahora: conectar el price-sync engine usando los metafields de la sección 5.



8. Reglas permanentes para el implementador (Fable 5)


Cada entrega termina con: archivos actualizados, estructura de carpetas del theme, y explicación de los cambios y decisiones clave.
Métricas siempre como fase o elemento distinto, nunca mezcladas dentro de otra cosa.
Sin em dashes en ningún texto, copy, comentario o commit. Usar comas, dos puntos, paréntesis o puntos.
Trabajar sobre theme de desarrollo, nunca sobre el live sin revisión.
Copy de la tienda en español, tono conversimo y directo, en sentence case, sin relleno.



9. Primer paso concreto

Arrancar por Fase 0 y Fase 1. El primer PR debe dejar la tienda con la piel de marca puesta (colores, tipografías, logo, favicon, botones) sobre Dawn, en un theme de desarrollo. Ese es el primer "se ve real" que le muestras a tu amigo.

Antes de Fase 1, confirmar con Vic: los hex exactos de la marca (los de este brief son a ojo) y los archivos de logo en SVG o PNG transparente.