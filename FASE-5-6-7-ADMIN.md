# Fases 5, 6 y 7: resumen y tareas en el admin

Rama: `feat/paginas-pagos-metricas`. Todo el codigo esta en el theme de desarrollo, nada tocado en el live.

## Nota de ejecucion

El encargo pedia consultar al advisor Fable 5 en las decisiones de arquitectura. La herramienta de advisor no estaba disponible en esta sesion (respondio "unavailable"). La unica decision real (donde vive el contenido de las paginas) se resolvio con el criterio del propio CLAUDE.md: como el encargo exige marcadores `<!-- FAKE: reemplazar -->` grep-eables y piel de marca, el contenido va horneado en secciones de codigo, no en el rich text del admin. Si quieres que Fable valide algo puntual, dimelo y lo reviso.

## Que se construyo (archivos nuevos y modificados)

### Fase 5: paginas estaticas
Cada pagina es una seccion custom con la piel de marca, mas su page template:

| Pagina | Seccion | Template | Handle sugerido |
|---|---|---|---|
| Nosotros | `sections/tcg-nosotros.liquid` | `templates/page.nosotros.json` | `nosotros` |
| Locales | `sections/tcg-locales.liquid` | `templates/page.locales.json` | `locales` |
| Envios y pagos | `sections/tcg-envios-pagos.liquid` | `templates/page.envios-pagos.json` | `envios-pagos` |
| FAQ | `sections/tcg-faq.liquid` | `templates/page.faq.json` | `faq` |

- WhatsApp flotante: `snippets/tcg-whatsapp.liquid`, global, renderizado en `layout/theme.liquid` antes de `</body>`. Boton verde fijo abajo a la derecha en todas las paginas.
- Estilos compartidos de pagina (`.tcg-page`, titulos, etc.) se agregaron a `assets/tcg-tokens.css` para no duplicarlos en cada seccion.

### Fase 7: analitica
- `snippets/tcg-analytics.liquid`, renderizado en el `<head>` de `layout/theme.liquid`. Trae los slots de GA4 y Meta Pixel con IDs placeholder. Estan guardados: mientras el ID siga siendo el placeholder no cargan nada, asi que no rompen la tienda hasta que pegues los IDs reales.

### Fase 6: pagos y envios
Es casi todo config de admin (ver checklist abajo). En codigo solo queda la pagina "Envios y pagos" lista para reflejar las tarifas una vez configuradas.

## Lo que te toca a ti (Vic) en el admin

### Paginas (Fase 5)
1. Online Store > Pages > Add page. Crear 4 paginas: Nosotros, Locales, Envios y pagos, FAQ.
2. En cada una, en "Theme template" elegir el template correspondiente (nosotros, locales, envios-pagos, faq). El contenido ya vive en el theme, el cuadro de contenido del admin puede quedar vacio.
3. Confirmar que el handle de cada pagina quede como el sugerido en la tabla (Shopify lo genera del titulo; ajustalo en "Edit website SEO" si difiere).
4. Menu del footer (Navigation > Footer menu): agregar enlaces a las 4 paginas nuevas y a **Eventos** (la pagina Eventos ya existe, solo enlazala si no aparece).
5. Reemplazar los datos marcados. Busca en el codigo del theme todos los `<!-- FAKE: reemplazar -->`:
   - Direcciones, horarios y referencias reales de Lince y Surco en `sections/tcg-locales.liquid`, mas los `src` de los dos iframes por el embed real de Google Maps de cada sede.
   - Numero real de WhatsApp en `snippets/tcg-whatsapp.liquid` (formato `51` + numero, sin +, espacios ni guiones).
   - Copy e historia real en `sections/tcg-nosotros.liquid`.
   - Tarifas y tiempos en `sections/tcg-envios-pagos.liquid` una vez definidos.

### Pagos y envios (Fase 6)
1. **Bogus Gateway (checkout de prueba, sin dinero real)**: Settings > Payments > en "Supported payment methods" o "Manual payment methods" activar **(for testing) Bogus Gateway**. Solo aparece en dev stores. Para simular:
   - Numero de tarjeta `1` = compra **aprobada**.
   - Numero de tarjeta `2` = compra **rechazada**.
   - Numero de tarjeta `3` = error del gateway.
   - Fecha de expiracion cualquiera en el futuro, CVV cualquiera de 3 digitos, nombre `Bogus Gateway`.
   Con esto puedes correr un checkout completo de principio a fin.
2. **Zonas de envio**: Settings > Shipping and delivery > en tu perfil de envio (General):
   - Zona **Lima**: tarifa fija placeholder (ej. S/ 10). Reemplazar por la real.
   - Zona **Provincias (resto de Peru)**: tarifa fija placeholder (ej. S/ 20). Reemplazar por la real.
   - **Retiro en tienda**: activar "Local pickup" en ambas ubicaciones (Lince y Surco) con costo 0. Primero crea las dos ubicaciones en Settings > Locations si no existen.
3. Actualizar las tarifas y tiempos reales en la pagina "Envios y pagos" (los `<!-- FAKE -->` de esa seccion).
4. **No se puede simular, hazlo tu antes de lanzar**:
   - Crear cuenta real en **Culqi** o **Mercado Pago Peru** (requiere RUC o DNI del negocio).
   - Conectarla en Settings > Payments.
   - Desactivar el Bogus Gateway y activar la pasarela real.

### Metricas (Fase 7)
1. **GA4**: crear la propiedad real en Google Analytics, copiar el Measurement ID (`G-XXXXXXXXXX`) y pegarlo en `snippets/tcg-analytics.liquid` reemplazando el placeholder. Alternativa recomendada por Shopify para eventos de compra server side: Settings > Customer events > Add custom pixel, y pegar ahi el tag de GA4. El snippet ya cubre el PageView de cliente.
2. **Meta Pixel**: crear el Pixel real en Meta Events Manager, copiar el Pixel ID y pegarlo en `snippets/tcg-analytics.liquid` reemplazando el placeholder `000000000000000`.
3. **Shopify Analytics**: ya esta activo por default (Analytics en el menu del admin). No hay que configurar nada; alcanza para v1. No se construyeron dashboards custom.

## Como probarlo localmente

```
shopify theme dev
```

Revisar: las 4 paginas nuevas (via /pages/handle), el boton de WhatsApp en cualquier pagina, y que la tienda cargue sin errores con los IDs de analitica en placeholder (no deben cargar GA4 ni Pixel hasta reemplazarlos).
