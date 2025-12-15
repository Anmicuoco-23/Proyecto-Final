Sistema Logístico: Gestión de Inventario y Pedidos (AVL + Colas FIFO)

Este proyecto implementa un sistema básico de gestión logística para inventario de productos perecederos (o con fecha de caducidad/lote), priorizando el despacho del producto con la fecha de vencimiento más cercana (primero en vencer, primero en salir - FIFO).

La estructura de datos principal es un **Árbol Binario de Búsqueda (ABB) con balanceo AVL**, donde cada nodo representa un lote de producto identificado por su **fecha de vencimiento** (clave). Dentro de cada nodo AVL, se gestiona una **Cola FIFO** para los pedidos pendientes asociados a ese lote específico.

Arquitectura de Datos

El sistema se basa en la combinación de dos estructuras de datos:

1.  Árbol AVL (`Node`):

    Propósito:Almacenar y buscar lotes de productos por su "fecha de vencimiento" (`fecha` - clave). Al ser un AVL, garantiza que la búsqueda, inserción y eliminación sean rápidas (tiempo $O(\log N)$).
      * Clave:** `fecha` (entero AAAAMMDD).
      * Contenido del Nodo:**
      * `fecha`, `producto`, `stock`.
      * `pedidos`: Una **Cola FIFO** de pedidos asociados a este lote.

2. Cola FIFO (`Queue` y `Order`):**

      * Propósito: Gestionar los pedidos de despacho para un lote específico. Los pedidos se procesan en el orden en que fueron recibidos (First In, First Out).
      * Contenido de `Order`: `id`, `destino`, `cantidad`.

 Estructuras y Funciones Clave

 Estructuras

| Estructura | Propósito | Clave de Búsqueda |

| `Order` | Representa un pedido de despacho. | `id` (único por pedido) |
| `Queue` | Cabecera para la cola de pedidos. | N/A |
| `Node` | Representa un lote (nodo AVL). | `fecha` (AAAAMMDD) |

 Funciones Principales

 1\ AVL Tree (Gestión de Lotes)

| Función | Descripción | Complejidad |

| `insertAVL` | Inserta un nuevo lote (nodo) por `fecha`. Mantiene el balanceo del árbol mediante rotaciones (LL, RR, LR, RL). | $O(\log N)$ |
| `deleteNode` | Elimina un lote por `fecha`. "Primero libera la cola de pedidos" asociada y luego reestructura el árbol AVL. | $O(\log N)$ |
| `searchNode` | Busca un lote por `fecha` exacta. | $O(\log N)$ |
| `findNearestExpire` | Devuelve el nodo con la clave (`fecha`) más pequeña, es decir, el lote más cercano a vencer. (Utiliza `minValueNode`). | $O(\log N)$ |
| `rightRotate`, `leftRotate` | Utilidades para realizar las rotaciones de balanceo. | $O(1)$ |

2\. Queue (Gestión de Pedidos)

| Función | Descripción | Complejidad |

| `enqueue` | Registra un nuevo pedido en la cola del lote seleccionado. | $O(1)$ |
| `dequeue` | Despacha el pedido más antiguo de la cola (no usado en el menú, pero implementado). | $O(1)$ |
| `removeOrderFromTree` | "Recorre todo el árbol" para encontrar y eliminar un pedido específico por `orderId`. **Restaura el stock** del lote. | $O(N)$ (Recorrido de árbol, ya que `orderId` no es la clave del árbol) |
| `freeQueue` | Libera la memoria de todos los pedidos en la cola. | $O(M)$ (Donde $M$ es el número de pedidos) |

 Uso del Programa

El sistema se opera a través de un menú interactivo:

 1) Recepción de Mercancía (Insertar en AVL)

  * Registra un nuevo lote de producto con su fecha de vencimiento (`fecha`), nombre del producto y cantidad inicial (`stock`).
  * Restricción:** No permite insertar fechas duplicadas (lotes con la misma fecha de vencimiento).

2) Registrar Pedido de Despacho (Encolar en FIFO)

  * Política FIFO/FEFO: Busca automáticamente el lote con la **fecha de vencimiento más cercana (`findNearestExpire`).
  * Si hay suficiente `stock` en ese lote, crea un pedido y lo encola en la `Queue` del nodo.
  * El `stock` del lote se reduce inmediatamente (representa la cantidad reservada/pendiente de despacho).

 3) Cancelar Pedido

  * Requiere el `order_id`.
  * Busca el pedido en "todas las colas del árbol" (`removeOrderFromTree`).
  * Si lo encuentra, lo elimina de la cola y "restaura la cantidad (`stock`)" al lote asociado.

4) Baja de Producto (Eliminar nodo)

  * Elimina un lote completo por su `fecha` de vencimiento.
  * Importante: La función `deleteNode` asegura la liberación de la memoria de "todos los pedidos" de la cola antes de eliminar el nodo AVL.
 5) Reporte de Estado (In-Order)

  * Realiza un recorrido In-Order del árbol. Dado que el árbol está ordenado por `fecha`, esto lista los lotes "desde el más próximo a vencer" hasta el más lejano.

6) Mostrar pedidos de una fecha específica

  * Permite ver la cola completa de pedidos de un lote específico buscando por `fecha`.


El código está escrito en C.

