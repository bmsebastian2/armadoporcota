# Cota

Planos navegables en 3D para vender inmuebles en pozo y loteos. Mercado: Uruguay.
Responsable: Sebastián, Montevideo.

## Dos repos, dos dominios

| Repo | Dominio | Contenido |
|------|---------|-----------|
| **armadoporcotauy** (este) | https://armadoporcotauy.vercel.app | Landing de Cota + demo de loteo |
| **armadoporcota** | https://armadoporcota.vercel.app | Demo de ROSSO by Block, en la raíz |

Al ser dominios distintos, **los enlaces entre landing y demo van con URL absoluta**. No
usar `/rosso`: la demo está en la raíz de su propio dominio, no en una subcarpeta.

- Landing → demo ROSSO: `https://armadoporcota.vercel.app/`
- Demo ROSSO → landing, sello "Armado por Cota": `https://armadoporcotauy.vercel.app/`
- `BASE` en la demo, para los links por unidad: `https://armadoporcota.vercel.app/`

### Archivos de este repo

```
index.html            Landing de Cota
mapa-uruguay-3d.html  Demo de loteo (grilla de lotes con calles) — enlace relativo
logo-desarrollo.jpg   Logo de Desarrollo de Soluciones Tecnológicas
README.md
```

## Qué es esto

Un desarrollador vende un edificio de 78 unidades y solo puede mostrarlas como avisos
sueltos en un portal. Nadie muestra el **inventario**: qué queda, dónde está, cuánto sale.
Cota convierte la lista de precios en un edificio navegable donde cada unidad es
clickeable y tiene link propio.

No compite con MercadoLibre ni InfoCasas: el portal trae el tráfico, Cota cierra.

### El valor real, por orden de importancia comercial

1. **Link por unidad.** El vendedor manda `?u=503` por WhatsApp en vez de la planilla. Es
   lo que mete la herramienta en su operación diaria y evita que la den de baja.
2. **Dato de demanda por unidad.** Qué tipología, orientación y franja de pisos concentra
   los clics. Sirve para reajustar precios. Ningún portal lo da.
3. **Estado en vivo**, sincronizado desde la planilla que el equipo de ventas ya usa.
4. **El lead es del desarrollador**, sin competencia lateral en la misma página.

### Objeción típica
"No quiero mostrar cuánto me queda sin vender." Se resuelve con un modo que muestra estado
por unidad sin el contador global.

### Idea pendiente: QR en el cartel de obra
El que pasa por la fachada escanea y ve el edificio terminado desde la vereda. Medible con
un parámetro tipo `?src=obra`. No mencionarlo en el primer contacto: es la segunda carta,
para cuando el cliente ya respondió. El cartel es del desarrollador, así que se propone.

## Modelo de negocio

| Plan | Armado | Mensual | Alcance |
|------|--------|---------|---------|
| Loteo | 1.400 | 140 | Hasta 120 lotes |
| Torre | 2.400 | 190 | Hasta 100 unidades |
| Portafolio | 4.500 | 390 | Varios proyectos, CRM, vistas por altura |

USD, sin IVA. Pago anual: dos meses bonificados.
Anzuelo: se arma una planta del proyecto gratis, en 3 días.

## Stack

HTML estático con three.js r128 por CDN (cdnjs). Sin build, sin framework, sin
dependencias. Se despliega arrastrando la carpeta a Vercel.

### Patrón común de las piezas

- Datos al principio del `<script>`, en constantes. Todo lo demás los lee.
- Cámara orbital escrita a mano: r128 por CDN no trae OrbitControls. Esférica
  `theta/phi/radio` alrededor de un `objetivo`, con `pointerdown/move/up` y `wheel`.
- Unidades y lotes = `BoxGeometry` + `EdgesGeometry` para el borde. Raycasting sobre las
  cajas activas para hover y clic.
- Filtros no esconden: bajan opacidad y desactivan el raycast, para no perder el volumen.
- Panel lateral en escritorio, hoja inferior en celular (≤860px).
- `canvas { touch-action: none }` o el dedo hace scroll en vez de girar.
- `history.replaceState` envuelto en try/catch: en previsualizaciones `about:srcdoc` tira
  SecurityError.

### Paletas

Landing — oscura, verde de marca:
```
--tinta #0E1513   --tinta-2 #141D1B   --papel #EDE9E2
--verde #3E9B84   --ambar #D3A04A     --gris #7C8886
```

Demo ROSSO — clara, tipo maqueta de arquitecto:
```
--fondo #DEE2E4   --tinta #1A2124   --suave #5D686C
--verde #2F7A68   unidad DISPONIBLE (con aviso publicado)
--gris  #9AA2A5   unidad SIN ESTADO PÚBLICO
--ambar #C08429   borde del cartel de aviso del cabezal
```

Los verdes son distintos a propósito: el de la landing es más claro porque va sobre fondo
oscuro; el de la demo va sobre fondo claro.

En ROSSO hay solo **dos estados** — `disponible` y `sin_dato` — porque no hay información
pública de reservas. El gris no significa "vendido": significa "nadie sabe". Esa
ambigüedad es deliberada y es el gancho de venta. En un proyecto con datos reales del
cliente, los estados pasan a ser disponible / reservado / vendido.

## La demo de ROSSO (repo armadoporcota)

ROSSO by Block, Ana Monterroso de Lavalleja 2166, Cordón. Entrega marzo 2028, 10 pisos,
78 unidades. Reconstruido con datos de avisos públicos de setiembre 2026.

- 9 pisos de 8 unidades + 6 penthouses = 78.
- Precios reales de 1 dormitorio: 47 m²→136.673, 48→136.673, 51→128.340, 60→138.904,
  64→139.874, 78→155.976 USD. Los de 2 dormitorios son estimados y el panel lo dice.
- `PUBLICADAS` = las 12 unidades con aviso vigente. El resto queda en gris.
- `?u=503` abre con la cámara enfrente de esa unidad y el panel abierto.
- Botón de WhatsApp con el código de unidad ya escrito. En la versión de un cliente ese
  número es el del vendedor, no el de Sebastián.
- `plantaSVG()` dibuja la planta esquemática por código desde la tipología. **No es el
  plano del arquitecto** y así está rotulado.

## Reglas del proyecto

- **Nunca presentar una demo como cliente.** ROSSO no es cliente: es una reconstrucción
  con datos públicos y así lo dice tanto la landing como la demo. Esa línea no se saca.
- No copiar renders, planos ni fotos de terceros. Los esquemas se dibujan por código.
- Datos estimados siempre marcados como tales en la interfaz.
- La forma del edificio debe ser **dato, no código**. Si un proyecto nuevo obliga a
  escribir geometría a mano, el negocio no escala.
- Rutas relativas dentro de un repo; absolutas entre los dos dominios.
- Si cambia la demo enlazada, actualizar también el texto de su tarjeta en la landing y los
  parámetros de su miniatura 3D en `montar()`. Ya se desincronizaron una vez.

## Contacto

- **WhatsApp: `59891856500`** (+598 91 856 500).
- **Correo: `bmsebastian2@gmail.com`.** El anterior, `hola@cota.uy`, era inventado: ese
  dominio nunca se registró. Si algún día se compra `cota.uy` en NIC Uruguay, conviene
  mover la landing ahí — `cota.uy` transmite empresa, una URL de Vercel transmite prueba.

## Rumbo técnico

**Próximo paso, ya definido:** sacar los datos de las unidades del HTML a un `datos.json`
que la página lea con `fetch`, y mostrar en pantalla la fecha de última actualización
(en ámbar si tiene más de 7 días). Ojo: `fetch` + `file://` se bloquea por CORS, así que
deja de andar el doble clic y hay que levantar un servidor local.

Ese cambio desbloquea todo lo demás, porque a partir de ahí da igual quién escriba el JSON.

**Después, en este orden:**

1. **n8n lee la planilla del cliente** (Google Sheets) y regenera `datos.json`. Es el
   escenario que mejor se vende: el vendedor no aprende nada nuevo, sigue en su Excel.
2. **Maqueta interior** del apartamento: muros extruidos desde el polígono de la planta,
   vista cenital en ángulo tipo casa de muñecas. **No** recorrido en primera persona:
   caminar ambientes vacíos sin muebles se siente barato. Impresiona pero no cierra
   ventas; la planta 2D cubre el 80%.
3. **Supabase + dashboard con login**, solo cuando un cliente lo pida o haya varios
   proyectos y equipos de venta. Dashboard y n8n son alternativas, no complementos: con
   dashboard el cliente escribe directo en la base y n8n sobra.

**Esquema de Supabase para cuando llegue:**

```
organizations   id, nombre, slug, plan
members         org_id, user_id, rol
projects        id, org_id, slug, nombre, tipo, layout jsonb, marca jsonb,
                publicado, muestra_totales
typologies      id, project_id, nombre, dorms, m2, orientacion
units           id, project_id, typology_id, codigo, piso, geom jsonb,
                precio, estado, notas
media           id, scope, ref_id, tipo, url
leads           id, unit_id, nombre, contacto, mensaje, origen
events          id, project_id, unit_id, tipo, sesion, creado_en
```

- `projects.layout` = planta paramétrica de la torre. `units.geom` = polígono del lote o
  posición en grilla. Con esas dos columnas el mismo motor dibuja cualquier proyecto.
- Torre = paramétrica (una planta × N pisos). Loteo = poligonal (GeoJSON o DXF del
  agrimensor, que ya existe porque el fraccionamiento está aprobado).
- `media.scope` engancha plantas y renders a la **tipología**, no a cada unidad: seis
  tipologías cubren un edificio de setenta apartamentos.
- RLS por `org_id` en escritura. Lectura pública solo vía vista `public_units` con columnas
  seguras: el margen y las notas internas no viajan al browser. `leads` y `events` sin
  lectura anónima.
- Realtime en `units` para el estado en vivo.
- Pantalla más importante del futuro admin: grilla de unidades editable tipo Excel. Ahí
  vive el vendedor todos los días.

**Upsell posterior:** fotos de vista real por altura con dron, 4 alturas × 4 orientaciones.
No ofrecerlo en el primer contacto. Requiere registro DINACIA y hay restricciones sobre
zona urbana.

## Pipeline de prospección

Criterio: 40 a 120 unidades, entrega 2027-2028, desarrolladora chica o mediana, avisos con
precios y tipologías publicados, sede en Montevideo para poder ir en persona.

Contactar al **vendedor que figura en el aviso**, no al gerente ni al dueño.

1. ROSSO by Block — Cordón, 78 u, 10 pisos, marzo 2028. **Demo lista, sin enviar aún.**
2. Altamira Tres Cruces — 49 u, 6 niveles, 4 tipologías, mediados 2028.
3. Park Suites II — Pocitos, 39 u, 12 pisos, 2027.
4. Nostrum Plaza III — Tres Cruces, 2 torres, 2027. Marca grande, dejar para después.
5. Proyecto de Cordón de 158 apartamentos, julio 2028.

Uno por vez: mandar, esperar respuesta, ajustar el mensaje.
