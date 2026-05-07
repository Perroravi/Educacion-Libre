Libros de estudios.   (✖╭╮✖) (≧◡≦) (¬_¬) https://github.com/manjunath5496/999-Computer-Books

……(\_/)
……( ‘_’)
…./”"”"”"”"”"”"\======░      XOOOOOX
 /”"”"”"”"”"”"”"”"”"”\
   \_@_@_@_@_@_/


....PYTHON........ARRANCANDO VENTILADORES!!!


import math
import os
import time

# Ángulos de rotación
A = 0.0  # inclinación (flip)
B = 0.0  # giro lateral

# Colores ANSI (si tu terminal no los soporta, puedes dejar las cadenas vacías)
GOLD = "\033[33m"         # amarillo
BRIGHT_GOLD = "\033[93m"  # amarillo brillante
RESET = "\033[0m"

# Tabla de caracteres para iluminación
chars = ".,-~:;=!*#$@"

while True:
    # Buffers de profundidad (z) y de caracteres (b)
    z = [0.0] * 1760
    b = [' '] * 1760

    # Superficie de una "esfera aplanada" (moneda)
    for j in range(0, 628, 4):      # theta 0..2π
        theta = j / 100.0
        for i in range(0, 314, 2):  # phi 0..π
            phi = i / 100.0

            # Radio de la moneda
            R = 1.0

            # Coordenadas base (esfera)
            x0 = R * math.sin(phi) * math.cos(theta)
            y0 = R * math.cos(phi)
            z0 = R * math.sin(phi) * math.sin(theta)

            # Aplastar en Y para que parezca una moneda
            thickness = 0.35
            y0 *= thickness

            # Rotaciones
            sinA = math.sin(A)
            cosA = math.cos(A)
            sinB = math.sin(B)
            cosB = math.cos(B)

            # Rotación fuerte alrededor del eje X (flip)
            y1 = y0 * cosA - z0 * sinA
            z1 = y0 * sinA + z0 * cosA
            x1 = x0

            # Rotación alrededor del eje Z (giro lateral más suave)
            x2 = x1 * cosB - y1 * sinB
            y2 = x1 * sinB + y1 * cosB
            z2 = z1

            # Alejar la moneda de la cámara
            z2 += 3.0
            inv_z = 1.0 / z2

            # Proyección a 2D (pantalla 80x22)
            x = int(40 + 30 * inv_z * x2)
            y = int(12 - 15 * inv_z * y2)

            if 0 <= x < 80 and 0 <= y < 22:
                o = x + 80 * y
                if inv_z > z[o]:
                    z[o] = inv_z

                    # Normal aproximada (de la esfera sin aplanar)
                    nx = math.sin(phi) * math.cos(theta)
                    ny = math.cos(phi)
                    nz = math.sin(phi) * math.sin(theta)

                    # Luz direccional
                    lx, ly, lz = 0.0, 1.0, -1.0
                    lenL = math.sqrt(lx * lx + ly * ly + lz * lz)
                    lx /= lenL
                    ly /= lenL
                    lz /= lenL

                    # Producto escalar N·L => brillo [-1, 1]
                    lum = nx * lx + ny * ly + nz * lz

                    # Distancia al centro en el plano de la moneda
                    r = math.sqrt(x0 * x0 + z0 * z0)

                    ch = ' '

                    # 1) B GRANDE en el centro (patrón sobre x0,z0)
                    if r < 0.6 and lum > -0.2:
                        u = x0 / 0.6   # -1..1 aprox (horizontal)
                        v = z0 / 0.6   # -1..1 aprox (vertical)

                        # Barra vertical izquierda de la B
                        in_stem = (u < -0.2) and (abs(v) < 0.9)

                        # Lóbulo superior de la B
                        upper_loop = (u >= -0.2) and ((u - 0.1) ** 2 + (v - 0.5) ** 2 < 0.45)

                        # Lóbulo inferior de la B
                        lower_loop = (u >= -0.2) and ((u - 0.1) ** 2 + (v + 0.5) ** 2 < 0.45)

                        if in_stem or upper_loop or lower_loop:
                            base = 'B'
                            ch = BRIGHT_GOLD + base + RESET

                    # 2) Si no está en el logo, probamos resto de la moneda
                    if ch == ' ':
                        # Anillo externo muy brillante (borde de la “cara”)
                        if 0.80 < r < 1.02 and lum > -0.3:
                            base = '@'
                            ch = BRIGHT_GOLD + base + RESET

                        # "Canto" de la moneda (parte lateral)
                        elif abs(y0) > thickness * 0.6:
                            base = '=' if lum > 0 else '-'
                            ch = GOLD + base + RESET

                        # Resto de la cara, con sombreado normal dorado
                        else:
                            idx = int((lum + 1.0) * (len(chars) - 1) / 2.0)
                            idx = max(0, min(len(chars) - 1, idx))
                            base = chars[idx]

                            if base == ' ':
                                ch = ' '
                            else:
                                ch = GOLD + base + RESET

                    b[o] = ch

    # Limpiar pantalla
    os.system("cls" if os.name == "nt" else "clear")

    # Imprimir el frame (80 columnas x 22 filas)
    for k in range(0, 1760, 80):
        print("".join(b[k:k + 80]))

    # ---- TEXTO "BITCOIN" DEBAJO ----
    label = "BITCOIN"
    margen = (80 - len(label)) // 2
    print(" " * margen + label)

    # Actualizar ángulos (más flip en X, giro suave en Z)
    A += 0.10   # flip rápido (moneda girando en el aire)
    B += 0.02   # giro más suave

    time.sleep(0.03)
