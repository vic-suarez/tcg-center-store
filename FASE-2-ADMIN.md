# Fase 2: tareas en el admin de Shopify

Todo esto se hace en el admin (admin.shopify.com), no en el código. El orden importa: primero colecciones, luego menú.

## 1. Colecciones

Crear estas colecciones. Los handles deben quedar exactamente así porque el homepage ya apunta a ellos.

| Colección | Handle | Tipo | Regla |
|---|---|---|---|
| Pokémon | `pokemon` | Automática | Etiqueta del producto es igual a `game:pokemon` |
| Magic | `magic` | Automática | Etiqueta es igual a `game:mtg` |
| Yu-Gi-Oh! | `yu-gi-oh` | Automática | Etiqueta es igual a `game:yugioh` |
| One Piece | `one-piece` | Automática | Etiqueta es igual a `game:onepiece` |
| Otros TCG | `otros-tcg` | Automática | Etiqueta es igual a `game:lorcana` (agregar más juegos como condiciones "cualquiera") |
| Preventa | `preventa` | Automática | Etiqueta es igual a `preventa` |
| Novedades | `novedades` | Automática | Precio mayor que 0 (atrapa todo), orden: más recientes primero |
| Sellado | `sellado` | Automática | Tipo de producto es igual a `Sellado` |
| Accesorios | `accesorios` | Automática | Tipo de producto es igual a `Accesorio` |
| Peluches y figuras | `peluches-y-figuras` | Automática | Tipo de producto es igual a `Peluche/Figura` |

Colecciones por set (Pokémon / Surging Sparks, etc.): crearlas automáticas por etiqueta `set:surging-sparks` según lo que haya en stock. No urgen para que el home funcione.

A cada colección de juego súbele una imagen: el homepage las usa como tiles de "Compra por juego".

## 2. Menú principal

Navegación > Menú principal, en este orden:

Pokémon | Magic | Yu-Gi-Oh! | One Piece | Otros TCG | Sellado y Preventa | Accesorios | Peluches y Figuras | Eventos

- Cada ítem apunta a su colección; "Sellado y Preventa" puede ser un ítem con dos subítems (colección `sellado` y colección `preventa`).
- Eventos apunta a la página `/pages/eventos` (crearla, ver punto 5).
- Los juegos sin stock todavía: déjalos fuera del menú hasta tener producto, la colección ya queda creada.

## 3. Convenciones al cargar productos

- Tipo de producto: `Single`, `Sellado`, `Accesorio` o `Peluche/Figura`. Preventa NO es un tipo: es una etiqueta.
- Etiquetas por producto según aplique: `game:pokemon`, `set:surging-sparks`, `condition:NM|LP|MP|HP` (solo singles), `lang:es` o `lang:en`, `rarity:...` (opcional), `preventa` (mientras dure la preventa, quitarla al lanzar).
- Producto en preventa: agregar etiqueta `preventa`, llenar la fecha de lanzamiento (punto 4) y, si el dueño quiere vender sin stock, activar "Continuar vendiendo aunque esté agotado" en el inventario.

## 4. Metafields (Configuración > Datos personalizados > Productos)

| Nombre | Namespace y clave | Tipo |
|---|---|---|
| Fecha de lanzamiento | `custom.release_date` | Fecha |
| TCGplayer ID | `custom.tcgplayer_id` | Texto de una línea |
| Multiplicador de precio | `custom.price_multiplier` | Número decimal |
| Precio de mercado USD | `custom.market_price_usd` | Número decimal |
| Última sincronización | `custom.last_synced_at` | Fecha y hora |

La fecha de lanzamiento ya se muestra en la página de producto y en las cards cuando el producto tiene la etiqueta `preventa`. Los otros cuatro quedan vacíos: son para el motor de precios futuro.

## 5. Páginas y contenido

- Crear la página Eventos con handle `eventos` (el banner de Sábados TCG Center ya apunta ahí).
- Subir imagen al banner de Sábados TCG Center y al bloque de Instagram (editor de temas > página de inicio).
- Confirmar el usuario de Instagram: el botón apunta a `instagram.com/tcgcenterperu`, corregir si es otro.
- Poner las direcciones reales y links de Google Maps en el bloque "Nuestras sedes" (editor de temas).
- Subir el logo y favicon en el editor de temas (Configuración del tema > Logo) cuando tengas el PNG transparente; mientras tanto el theme usa el emblema JPG como respaldo.

## 6. Filtros de colección (app Search & Discovery)

Instalar la app gratuita "Shopify Search & Discovery" y activar filtros: disponibilidad, precio, tipo de producto. Ojo: la app no filtra por etiquetas, así que los filtros de condición e idioma van a necesitar metafields propios más adelante; por ahora condición e idioma se ven como chips en cada card. Lo dejamos anotado para revisarlo juntos en Fase 4.
