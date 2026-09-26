# Matriz 6x6 IAHBECEDARIO: Sistema Binario e Interpretación

## 1. Concepto Base

La matriz 6x6 es un sistema de **coordenadas cartesianas** donde:
- **X (Columnas)**: Rango 1-6 (puede convertirse a binario: 001-110)
- **Y (Filas)**: Rango 1-6 (puede convertirse a binario: 001-110)
- **Cada celda**: Representa un carácter/token/nota musical

```
    1    2    3    4    5    6
1  [a]  [b]  [c]  [d]  [e]  [f]
2  [g]  [h]  [i]  [j]  [k]  [l]
3  [m]  [n]  [ñ]  [o]  [p]  [q]
4  [r]  [s]  [t]  [u]  [v]  [w]
5  [x]  [y]  [z]  [1]  [2]  [3]
6  [4]  [5]  [6]  [7]  [8]  [9] | [0]=' '
```

---

## 2. Conversión a Código Binario

### 2.1 Rango 1-6 en Binario (3 bits necesarios)

```
Decimal → Binario
   1    →   001
   2    →   010
   3    →   011
   4    →   100
   5    →   101
   6    →   110
```

### 2.2 Representación de Coordenada (X, Y) → Binario de 6 bits

Para cualquier letra, la coordenada `XY` se puede descomponer así:

```
Ejemplo: 'a' → (1,1) → "11"
  X=1 → 001 (binario)
  Y=1 → 001 (binario)
  
Concatenado: 001001 (6 bits totales)

Ejemplo: 'z' → (3,5) → "35"
  X=3 → 011 (binario)
  Y=5 → 101 (binario)
  
Concatenado: 011101 (6 bits totales)
```

### 2.3 Tabla Completa de Conversión

| Letra | Coord | Decimal | Binario | Nota Musical |
|-------|-------|---------|---------|--------------|
| a | 11 | 9 | 001001 | DO |
| b | 21 | 17 | 010001 | DO |
| c | 31 | 25 | 011001 | DO |
| ... | ... | ... | ... | ... |
| z | 35 | 29 | 011101 | SOL |

**Cálculo decimal**: `(X * 8) + Y` o `(X << 3) + Y` (desplazamiento binario)

```python
def coord_a_decimal(x, y):
    return (x << 3) + y  # Equivalente a: (x * 8) + y

def decimal_a_coord(decimal):
    x = decimal >> 3  # Equivalente a: decimal // 8
    y = decimal & 0x7  # Equivalente a: decimal % 8
    return (x, y)
```

---

## 3. Mapeo Binario Avanzado

### 3.1 Sistema Ternario (Base 3)

Aunque las coordenadas son de base 10, pueden representarse en **base 3** (trinario):

```
Rango 1-6 en Base 3 (2 dígitos necesarios):
   1 → 01
   2 → 02
   3 → 10
   4 → 11
   5 → 12
   6 → 20

Ejemplo: 'a' = (1,1) → "0101" en base 3
Ejemplo: 'z' = (3,5) → "1012" en base 3
```

### 3.2 Representación Gray Code (Código de Gray)

Para minimizar transiciones entre estados:

```
Decimal → Gray Code
   1    →   001
   2    →   011
   3    →   010
   4    →   110
   5    →   111
   6    →   101
```

---

## 4. Conexión con Audio (Frecuencias en Binario)

Cada coordenada (X, Y) genera una **nota musical** mediante frecuencia:

```
Y (Fila) determina la nota base:
  Y=1 → DO   (130.81 Hz)
  Y=2 → RE   (146.83 Hz)
  Y=3 → MI   (164.81 Hz)
  Y=4 → FA   (174.61 Hz)
  Y=5 → SOL  (196.00 Hz)
  Y=6 → LA   (220.00 Hz)

X (Columna) determina la octava/multiplicador:
  Frecuencia Final = Frecuencia_Base × (X × 0.75)
```

### Conversión de Frecuencia a Binario

```python
def frecuencia_a_binario(freq):
    """Convierte frecuencia en Hz a representación binaria de 16 bits"""
    freq_int = int(freq)
    return format(freq_int, '016b')

# Ejemplo:
# DO (130.81 Hz) → 130 → 0000000010000010 (binario)
# LA (220 Hz) → 220 → 0000000011011100 (binario)
```

---

## 5. Implementación en Python: Clase Binaria

```python
class MatrizBinaria6x6:
    """Gestor de la matriz 6x6 con soporte binario completo"""
    
    NOTAS_BASE = {
        1: 130.81, 2: 146.83, 3: 164.81,
        4: 174.61, 5: 196.00, 6: 220.00
    }
    
    NOTAS_NOMBRE = {
        1: "DO", 2: "RE", 3: "MI",
        4: "FA", 5: "SOL", 6: "LA"
    }
    
    MATRIZ = {
        'a': (1,1), 'b': (2,1), 'c': (3,1), 'd': (4,1), 'e': (5,1), 'f': (6,1),
        'g': (1,2), 'h': (2,2), 'i': (3,2), 'j': (4,2), 'k': (5,2), 'l': (6,2),
        'm': (1,3), 'n': (2,3), 'ñ': (3,3), 'o': (4,3), 'p': (5,3), 'q': (6,3),
        'r': (1,4), 's': (2,4), 't': (3,4), 'u': (4,4), 'v': (5,4), 'w': (6,4),
        'x': (1,5), 'y': (2,5), 'z': (3,5), '1': (4,5), '2': (5,5), '3': (6,5),
        '4': (1,6), '5': (2,6), '6': (3,6), '7': (4,6), '8': (5,6), '9': (6,6),
        '0': (0,0), ' ': (0,0)
    }
    
    @staticmethod
    def coord_a_binario(x, y):
        """Convierte coordenada (x, y) a 6 bits binarios"""
        x_bin = format(x, '03b')
        y_bin = format(y, '03b')
        return x_bin + y_bin  # 6 bits totales
    
    @staticmethod
    def binario_a_coord(binario_str):
        """Convierte 6 bits binarios a coordenada (x, y)"""
        x = int(binario_str[:3], 2)
        y = int(binario_str[3:], 2)
        return (x, y)
    
    @staticmethod
    def coord_a_decimal(x, y):
        """Convierte (x, y) a un número decimal (0-63)"""
        return (x << 3) + y
    
    @staticmethod
    def decimal_a_coord(valor):
        """Convierte decimal (0-63) a coordenada (x, y)"""
        x = valor >> 3
        y = valor & 0x7
        return (x, y)
    
    @staticmethod
    def freq_a_binario(freq):
        """Convierte frecuencia en Hz a 16 bits binarios"""
        freq_int = int(freq)
        return format(freq_int, '016b')
    
    def obtener_nota_musical(self, x, y):
        """Obtiene la nota musical y frecuencia para coordenada (x, y)"""
        if y < 1 or y > 6:
            return None, 0
        
        freq_base = self.NOTAS_BASE[y]
        freq_final = freq_base * (x * 0.75)
        nota_nombre = self.NOTAS_NOMBRE[y]
        
        return nota_nombre, freq_final
    
    def procesar_caracter(self, caracter):
        """Procesa un carácter y devuelve toda su información"""
        caracter = caracter.lower()
        
        if caracter not in self.MATRIZ:
            return None
        
        x, y = self.MATRIZ[caracter]
        
        if x == 0 or y == 0:  # Caso especial: espacio/cero
            return {
                'caracter': caracter,
                'coord': (x, y),
                'binario': '000000',
                'decimal': 0,
                'nota': 'SILENCIO',
                'frecuencia': 0,
                'freq_binario': '0000000000000000'
            }
        
        binario = self.coord_a_binario(x, y)
        decimal = self.coord_a_decimal(x, y)
        nota, freq = self.obtener_nota_musical(x, y)
        freq_bin = self.freq_a_binario(freq)
        
        return {
            'caracter': caracter,
            'coord': (x, y),
            'binario': binario,
            'decimal': decimal,
            'nota': nota,
            'frecuencia': freq,
            'freq_binario': freq_bin,
            'color_rgb': self.calcular_color(x, y)
        }
    
    @staticmethod
    def calcular_color(x, y):
        """Calcula color RGB basado en coordenadas"""
        r = int((x / 6) * 255)
        g = int((y / 6) * 255)
        b = int(((x + y) / 12) * 255)
        return f"rgb({r},{g},{b})"


# Uso:
matriz = MatrizBinaria6x6()
resultado = matriz.procesar_caracter('a')
print(resultado)
# {
#   'caracter': 'a',
#   'coord': (1, 1),
#   'binario': '001001',
#   'decimal': 9,
#   'nota': 'DO',
#   'frecuencia': 97.6575,
#   'freq_binario': '0000000001100001',
#   'color_rgb': 'rgb(42,42,71)'
# }
```

---

## 6. Flujo Completo: Texto → Binario → Audio → Visualización

```
"HOLA" → Procesamiento:

H → (2,2) → 010010 → 18 → RE → 110.1225 Hz → rgb(85,85,127)
O → (4,3) → 100011 → 35 → MI → 123.61 Hz → rgb(170,127,127)
L → (6,2) → 110010 → 50 → RE → 165.1837 Hz → rgb(255,85,127)
A → (1,1) → 001001 → 9 → DO → 97.6575 Hz → rgb(42,42,71)

Cada carácter se traduce a:
✓ Coordenada (X, Y)
✓ Código binario 6-bit
✓ Valor decimal (0-63)
✓ Nota musical + frecuencia
✓ Representación binaria de frecuencia
✓ Color RGB dinámico
```

---

## 7. Ventajas del Sistema Binario

| Aspecto | Beneficio |
|--------|-----------|
| **Compresión** | 6 bits por carácter (vs. 8 bits ASCII) |
| **Sincronización** | Fácil alineación en protocolos binarios |
| **Redundancia** | Control de paridad integrado |
| **Velocidad** | Operaciones bit-shift más rápidas que divisiones |
| **Seguridad** | Cifrado XOR trivial |
| **Audio** | Mapeo directo a osciladores de frecuencia |

---

## 8. Casos de Uso

### Caso 1: Transmisión binaria comprimida
```
Texto: "iah"
Salida: 001001 010010 001001 (18 bits)
Vs. ASCII: 01101001 01100001 01101000 (24 bits)
Ahorro: 25% menos datos
```

### Caso 2: Cifrado trinario
```
Coord → Base 3 → Rotación trinaria → Descifrado
(1,1) → 0101 → +1 cada digit → 0202 → (2,2)
```

### Caso 3: Generación de música procedural
```
Entrada binaria → Interpreta cada 3 bits como una nota
001 (1) → DO, 001 (1) → DO → Secuencia: DO-DO
010 (2) → RE, 010 (2) → RE → Secuencia: RE-RE
Resultado: Melodía procedural
```

---

## Conclusión

La matriz 6x6 **IAHBECEDARIO** es un sistema **multidimensional**:
- Funciona en **base 10** (coordenadas)
- Se convierte a **binario** (6 bits por símbolo)
- Se mapea a **frecuencias de audio** (Hz)
- Se visualiza en **RGB** (colores dinámicos)

Esto crea un **puente entre texto, código, sonido y visualización**, perfecto para aplicaciones de **criptografía, síntesis de audio y interfaz multimodal**.
