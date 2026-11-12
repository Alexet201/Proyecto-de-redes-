# Huffman Web Compressor — Análisis de Algoritmos 2026B

Aplicación web interactiva que utiliza la estrategia voraz de **Huffman** para comprimir, analizar, inspeccionar y recuperar archivos de texto en un formato binario propietario (`.huff`).

- **URL Pública (Vercel):** [https://compresor-huffman.vercel.app/](https://compresor-huffman.vercel.app/)
- **Repositorio GitHub:** [https://github.com/Alexet201/Tecnica-Voraz-Huffman](https://github.com/Alexet201/Tecnica-Voraz-Huffman)

---

## 1. Descripción del Algoritmo Voraz (Huffman)
El algoritmo de Huffman asigna códigos de longitud variable a los caracteres basándose en su frecuencia de aparición:
1. **Frecuencias:** Se calcula el número de ocurrencias de cada carácter en el texto.
2. **Construcción del Árbol (Voraz):** Se insertan todos los nodos en una cola de prioridad (min-heap). En cada iteración se extraen los dos nodos con menor frecuencia y se fusionan en un nuevo nodo padre cuya frecuencia es la suma de ambos.
3. **Generación de Códigos Prefijo:** Se asigna `'0'` a las ramas izquierdas y `'1'` a las derechas. Las hojas contienen los símbolos originales.
4. **Empaquetamiento:** Los caracteres se sustituyen por sus secuencias de bits y se empaquetan en bytes de 8 bits.

---

## 2. Especificación del Formato de Archivo Propietario (`.huff`)
El archivo binario `.huff` almacena la estructura necesaria para reconstruir el árbol y decodificar el texto sin requerir el archivo original.

### Estructura de la Cabecera y Datos (Layout)
- **Firma / Magic Number (4 bytes):** `HUFF`
- **Versión (1 byte):** `1`
- **Codificación (1 byte):** `0` = UTF-8, `1` = Latin-1
- **Flags (1 byte):** Reservado (`0`)
- **Tamaño Original (8 bytes):** Big-endian uint64
- **Cantidad de Caracteres (8 bytes):** Big-endian uint64
- **Hash SHA-256 Original (32 bytes):** Digest binario para comprobación de integridad
- **Símbolos Únicos (4 bytes):** Big-endian uint32
- **Bits Válidos (8 bytes):** Big-endian uint64 (para identificar cuántos bits son válidos en el último byte)
- **Longitud del Nombre (2 bytes):** Big-endian uint16
- **Tabla de Frecuencias:** Para cada símbolo único se almacenan 8 bytes (`ord(carácter)` uint32 + `frecuencia` uint32).

---

## 3. Pruebas Mínimas e Integridad

| Prueba | Entrada | Tamaño Orig. | Tamaño .huff | Reducción (%) | Integridad (SHA-256) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Texto Pequeño** | `abracadabra` | 11 B | 102 B (cabecera+datos) | N/A (over-header) | OK (Idéntico byte a byte) |
| **2. Libro Gutenberg** | *Frankenstein* (pg84.txt) | 442 KB | 258 KB | 41.6 % | OK (Idéntico byte a byte) |
| **3. Archivo Nuevo** | Código fuente o TXT externo | > 100 KB | Reducción ~40-45% | Varía | OK (Idéntico byte a byte) |

---

## 4. Tecnologías Utilizadas
- **Frontend:** HTML5, CSS3 (Custom Properties), JavaScript ES2022 (DataView, Web Crypto API `subtle.digest`, SVG dinámico).
- **Backend / Deployment:** Serverless Function en Node.js (`api/download.js`) desplegada en **Vercel** para evitar bloqueos de CORS al conectar con Project Gutenberg.
- **Python:** Scripts CLI opcionales (`huffman_core.py`, `app.py`, `server.py`).

---

## 5. Declaración de Uso de IA
Se utilizó inteligencia artificial como asistente de apoyo para:
- Optimización de algoritmos de manipulación de bits en JavaScript (`DataView` y desplazamiento binario `1 << (7 - bitIndex & 7)`).
- Diseño y estructura CSS responsiva.
- Pruebas unitarias de empaquetado binario.
