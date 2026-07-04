# Encargo consolidado: Fases 5, 6 y 7

## Estrategia de ejecucion (advisor dentro de Claude Code)

- **Ejecutor: Opus 4.8** (este agente). Ejecuta las tres fases seguidas, de forma autonoma, sin pausar a confirmar cada paso menor. Decide lo ordinario tu mismo y documenta la decision al final.
- **Advisor: Fable 5** (configurado con `/advisor` en Claude Code). Cuando encuentres una decision real de arquitectura, algo ambiguo, o algo que contradiga CLAUDE.md, **consulta al advisor Fable directamente en la sesion** antes de seguir. No salgas a preguntarle al usuario salvo que sea un dato que solo el tenga (cuenta de pago, IDs reales).
- El usuario (Vic) revisa el resultado al final. No lo interrumpas turno a turno.

## Regla de contenido

Donde falte texto o dato real (direcciones, RUC, redes sociales, copy), usa contenido de relleno realista y marcalo con el comentario `<!-- FAKE: reemplazar -->` justo al lado, para que sea grep-eable despues. Nunca inventes datos que parezcan reales sin marcarlos (nada de direcciones o telefonos que parezcan verdaderos sin el marcador).

## Fase 5: Paginas estaticas

Crear como page templates o secciones de Dawn, con el sistema de marca ya existente:

- **Nosotros**: historia breve de TCG Center (relleno marcado), que venden, por que version espanol importa.
- **Locales**: sedes Lince y Surco. Direccion, horario y mapa embebido con datos placeholder marcados.
- **Envios y pagos**: delivery Lima, envio a provincia (Olva/Shalom), retiro en tienda, y metodos de pago aceptados (dejar como placeholder hasta Fase 6).
- **FAQ**: 6 a 8 preguntas tipicas de tienda TCG (condicion de cartas, tiempos de preventa, cambios y devoluciones, garantia de autenticidad).
- **Boton de WhatsApp**: flotante o en el footer, numero placeholder marcado, enlazado a wa.me con mensaje prellenado tipo "Hola, tengo una consulta sobre...".

Eventos ya existe como pagina, solo enlazala donde falte.

## Fase 6: Pagos y envios (lo que es codigo o config de dev store)

- Activar el **Bogus Gateway** de Shopify (gateway de prueba nativo de las tiendas de desarrollo) para poder correr un checkout completo de principio a fin sin dinero real. Documentar en el resumen como se activa y como se simula una compra aprobada o rechazada con las tarjetas de prueba.
- Configurar **zonas de envio** en el admin via settings estandar: Lima (tarifa fija placeholder), Provincias (tarifa fija placeholder), Retiro en tienda (costo 0, ambas sedes).
- Dejar la pagina "Envios y pagos" de la Fase 5 enlazada a estos datos una vez configurados.
- **Lo que NO se puede fake, dejalo en el resumen final como checklist para Vic**: crear cuenta real en Culqi o Mercado Pago Peru (requiere RUC/DNI del negocio), conectarla en Settings > Payments, y switchear de Bogus Gateway a la pasarela real antes de lanzar.

## Fase 7: Metricas y analitica

- Dejar los slots de configuracion listos: **GA4** (Settings > Customer events o via tema, con Measurement ID placeholder marcado) y **Meta Pixel** (mismo patron, Pixel ID placeholder marcado).
- Confirmar que Shopify Analytics nativo esta activo (lo esta por default, solo documentarlo).
- En el resumen final, dejar como checklist para Vic: crear la propiedad GA4 real, crear el Pixel de Meta real, y pegar los IDs reales donde quedaron los placeholders marcados.
- No construir dashboards custom todavia. Shopify Analytics nativo alcanza para v1.

## Al terminar

Un solo resumen consolidado de las tres fases: archivos y templates nuevos, estructura de carpetas, decisiones clave (incluyendo cualquier consulta que le hiciste al advisor Fable y como se resolvio), y una lista corta y clara de **que le toca a Vic en el admin** (cuenta de pago real, IDs de GA4 y Meta, datos reales de sedes y WhatsApp para reemplazar los `<!-- FAKE -->`).

Sigue las reglas permanentes del CLAUDE.md: sin em dashes, copy en espanol, trabajo en rama nueva (`feat/paginas-pagos-metricas`), nunca sobre el live.
