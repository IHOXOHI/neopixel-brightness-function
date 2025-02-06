The famous neopixel lib under MIT licence, by Damien George and Jim Mussared
with on/off and brightness functions added

to use it:

from machine import Pin

from neopixel5 import NeoPixel

np = NeoPixel(Pin('X7'), 1) #one neopixel

np.fill((20,30,40))

np.write()

np.brightnessUp()

...
