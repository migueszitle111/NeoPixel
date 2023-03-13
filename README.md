# <h1 align="center"><img width="100" height="32" src="https://hackster.imgix.net/uploads/attachments/612650/giphy_SpgfnjAs5X.gif?auto=compress%2Cformat&gifq=35&w=740&h=555&fit=max"/> NeoPixel <img width="100" height="32" src="https://hackster.imgix.net/uploads/attachments/612650/giphy_SpgfnjAs5X.gif?auto=compress%2Cformat&gifq=35&w=740&h=555&fit=max"/></h1>

<p align="center">
  <img width="250" height="250" src="https://http2.mlstatic.com/D_NQ_NP_795368-MLM53302232527_012023-W.jpg">
</p>


Los neopíxeles son dispositivos de iluminación LED programables que se pueden controlar individualmente. También se conocen como LED inteligentes, LED programables o LED direccionables. 
Los neopíxeles se pueden controlar utilizando microcontroladores y software, lo que permite crear efectos de iluminación complejos y animaciones. Cada neopíxel tiene su propio controlador integrado que permite controlar su brillo y color individualmente. Además, los neopíxeles se pueden encadenar juntos para crear tiras o matrices de neopíxeles controlados de manera individual.

Los neopíxeles son una marca registrada de Adafruit Industries, pero existen muchos otros tipos de dispositivos de iluminación LED programables

# Neopxiel WS2812B 
Este es el Neopixel más común. Tiene 4 pines (VCC, GND, DIN y DOUT) y se puede controlar con cualquier microcontrolador que tenga un puerto de salida de datos y una fuente de alimentación de 5V.

- VCC: Es el pin de alimentación y se conecta a la fuente de alimentación del proyecto. Normalmente se utiliza una fuente de alimentación de 5V.
- GND: Es el pin de tierra y se conecta a la tierra del proyecto.
- DI (Data In): Es el pin de entrada de datos y se conecta al pin de salida de datos del microcontrolador o del controlador de Neopixel.
- DO (Data Out): Es el pin de salida de datos y se utiliza para conectar en serie varios LEDs WS2812B.

<table>
  <tr>
    <td>
      <img src="https://http2.mlstatic.com/D_NQ_NP_908147-MLM31224377577_062019-O.webp" alt="Descripción de la imagen 1" width="400" height="250">
    </td>
    <td>
      <img src="https://http2.mlstatic.com/D_NQ_NP_863306-MLM31224378019_062019-O.webp" alt="Descripción de la imagen 2" width="400" height="250">
    </td>
    <td>
      <img src="https://m.media-amazon.com/images/I/81EvVf-1K1L.jpg" alt="Descripción de la imagen 3" width="400" height="250">
    </td>
  </tr>
</table>

# Neopixel Ring
Un Neopixel Ring es un tipo de dispositivo LED direccionable digital que pertenece a la familia de dispositivos Neopixel de Adafruit. Como su nombre lo indica, el Neopixel Ring tiene forma de anillo
Las conexiones del Neopixel Ring son bastante sencillas. El anillo tiene cuatro pines de conexión que deben conectarse a la fuente de alimentación y al microcontrolador o controlador de Neopixel. Estos son los pines de conexión:

- VCC: Este es el pin de alimentación y debe estar conectado a la fuente de alimentación del proyecto. Normalmente se utiliza una fuente de alimentación de 5V.
- GND: Este es el pin de tierra y debe estar conectado a la tierra del proyecto.
- DI (Data In): Este es el pin de entrada de datos y debe estar conectado al pin de salida de datos del microcontrolador o del controlador de Neopixel.
- DO (Data Out): Este es el pin de salida de datos y se utiliza para conectar varios Neopixel Rings en serie.

<table>
  <tr>
    <td>
      <img src="https://boutique.semageek.com/741-large_default/neopixel-ring-with-12-led-rgb-led-and-driver-integrated.jpg" width="400" height="250">
    </td>
    <td>
      <img src="https://blog.moddable.com/blog/wp-content/uploads/2018/08/IMG_0860_1.gif"  width="330" height="250">
    </td>
    <td>
      <img src="https://europe1.discourse-cdn.com/arduino/original/4X/a/7/9/a796d2ba73f28db6bdee769b26120fb743151694.png" width="400" height="250">
    </td>
  </tr>
</table>




# Ejemplo de codigo en el simualdor de  Wokwi.com utlizando la Raspberry pi pico 2020 
```python
# NeoPixels on the Pi Pico
# Sample code from http://www.pibits.net/code/raspberry-pi-pico-and-neopixel-example-in-micropython.php

import array, time
from machine import Pin
import rp2
 
# Configure the number of WS2812 LEDs, pins and brightness.
NUM_LEDS = 16
PIN_NUM = 7
brightness = 1
 
@rp2.asm_pio(sideset_init=rp2.PIO.OUT_LOW, out_shiftdir=rp2.PIO.SHIFT_LEFT, autopull=True, pull_thresh=24)
def ws2812():
    T1 = 2
    T2 = 5
    T3 = 3
    wrap_target()
    label("bitloop")
    out(x, 1)               .side(0)    [T3 - 1]
    jmp(not_x, "do_zero")   .side(1)    [T1 - 1]
    jmp("bitloop")          .side(1)    [T2 - 1]
    label("do_zero")
    nop()                   .side(0)    [T2 - 1]
    wrap()
 
 
# Create the StateMachine with the ws2812 program, outputting on Pin(16).
sm = rp2.StateMachine(0, ws2812, freq=8_000_000, sideset_base=Pin(PIN_NUM))
 
# Start the StateMachine, it will wait for data on its FIFO.
sm.active(1)
 
# Display a pattern on the LEDs via an array of LED RGB values.
ar = array.array("I", [0 for _ in range(NUM_LEDS)])
 
def pixels_show():
    dimmer_ar = array.array("I", [0 for _ in range(NUM_LEDS)])
    for i,c in enumerate(ar):
        r = int(((c >> 8) & 0xFF) * brightness)
        g = int(((c >> 16) & 0xFF) * brightness)
        b = int((c & 0xFF) * brightness)
        dimmer_ar[i] = (g<<16) + (r<<8) + b
    sm.put(dimmer_ar, 8)
    time.sleep_ms(10)
 
def pixels_set(i, color):
    ar[i] = (color[1]<<16) + (color[0]<<8) + color[2]
 
def pixels_fill(color):
    for i in range(len(ar)):
        pixels_set(i, color)
 
 
BLACK = (0, 0, 0)
RED = (255, 0, 0)
YELLOW = (255, 150, 0)
GREEN = (0, 255, 0)
CYAN = (0, 255, 255)
BLUE = (0, 0, 255)
PURPLE = (180, 0, 255)
WHITE = (255, 255, 255)
COLORS = (BLACK, RED, YELLOW, GREEN, CYAN, BLUE, PURPLE, WHITE)
 
 
for color in COLORS:
    pixels_fill(color)
    pixels_show()
    time.sleep(0.5)
```

# Salidad del programa
<p align="center">
  <img width="400" height="400" src="https://i.ibb.co/4S5YbmV/simulador.gif">
</p>

