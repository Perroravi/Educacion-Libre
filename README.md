Libros de estudios.

.............PYTHON...................ARRANCANDO VENTILADORES


import math
import os
import time

# Velocidades de rotación
A = 0.0
B = 0.0

while True:
    # Buffers de profundidad (z) y de caracteres (b)
    z = [0.0] * 1760
    b = [' '] * 1760

    # Recorremos los ángulos del toro
    for j in range(0, 628, 7):      # 0..2π con paso ~0.07
        for i in range(0, 628, 2):  # 0..2π con paso ~0.02
            j_ = j / 100.0
            i_ = i / 100.0

            c = math.sin(i_)
            l = math.cos(i_)
            d = math.cos(j_)
            f = math.sin(j_)

            e = math.sin(A)
            g = math.cos(A)
            m = math.cos(B)
            n = math.sin(B)

            h = d + 2.0
            t = c * h * g - f * e
            D = 1.0 / (c * h * e + f * g + 5.0)

            # Proyección a 2D
            x = int(40 + 30 * D * (l * h * m - t * n))
            y = int(12 + 15 * D * (l * h * n + t * m))
            o = x + 80 * y

            # Cálculo de iluminación
            N = int(
                8 * ((f * e - c * d * g) * m - c * d * e - f * g - l * d * n)
            )

            if 0 <= x < 80 and 0 <= y < 22 and 0 <= o < 1760 and D > z[o]:
                z[o] = D
                # Escogemos el carácter según la "intensidad de luz"
                chars = ".,-~:;=!*#$@"
                idx = max(0, min(len(chars) - 1, N))
                b[o] = chars[idx]

    # Limpiar pantalla
    os.system("cls" if os.name == "nt" else "clear")

    # Imprimir el frame (80 columnas x 22 filas)
    for k in range(0, 1760, 80):
        print("".join(b[k:k + 80]))

    # Actualizar ángulos (rotación)
    A += 0.04
    B += 0.02

    time.sleep(0.03)
                                       
