


# NeoPixel driver for MicroPython
# MIT license; Copyright (c) 2016 Damien P. George, 2021 Jim Mussared

from machine import bitstream
class NeoPixel:
    # G R B W
    ORDER = (1, 0, 2, 3)

    def __init__(self, pin, n, bpp=3, timing=1, color=[0,0,0],grad=0.5):
        self.pin = pin
        self.n = n
        self.bpp = bpp
        self.buf = bytearray(n * bpp)
        self.pin.init(pin.OUT)
        # Timing arg can either be 1 for 800kHz or 0 for 400kHz,
        # or a user-specified timing ns tuple (high_0, low_0, high_1, low_1).
        self.timing = (
            ((400, 850, 800, 450) if timing else (800, 1700, 1600, 900))
            if isinstance(timing, int)
            else timing
        )
        self.color = color ##color in rgb format for keep it in memory and retreive it if necessary
        self.grad = grad ##for change brightness. grad is a float number between 0.0 and 1.0 

    def __len__(self):
        return self.n

    def __setitem__(self, i, v):
        offset = i * self.bpp
        for i in range(self.bpp):
            self.buf[offset + self.ORDER[i]] = round(v[i] * self.grad)
        lista = [v[0],v[1],v[2]]
        self.color = lista

    def __getitem__(self, i):
        offset = i * self.bpp
        return tuple(self.buf[offset + self.ORDER[i]] for i in range(self.bpp))

    def fill(self, v):
        b = self.buf
        l = len(self.buf)
        bpp = self.bpp
        for i in range(bpp):
            c = round(v[i] * self.grad)
            j = self.ORDER[i]
            while j < l:
                b[j] = c
                j += bpp
        lista = [v[0],v[1],v[2]]
        self.color = lista
    
    def fill2(self, v): ##another fill which do not save the color when a shutdown is doing
        b = self.buf
        l = len(self.buf)
        bpp = self.bpp
        for i in range(bpp):
            c = round(v[i] * self.grad)
            j = self.ORDER[i]
            while j < l:
                b[j] = c
                j += bpp

    def write(self):
        # BITSTREAM_TYPE_HIGH_LOW = 0
        bitstream(self.pin, 0, self.timing, self.buf)

    def brightness(self,grad):##grad is a float between 0.0 and 1.0
        color = self.color
        self.grad = grad
        if grad == 0:
            self.fill2((0,0,0))         
        else:
            self.fill(color)
        
    def brightnessOn(self):
        color = self.color
        self.fill(color)
        self.write()
       
    def brightnessOff(self):
        color = (0,0,0)
        self.fill2(color)
        self.write()
   
    def brightnessUp(self):
        color = self.color
        #print(color)
        
        r,g,b = color[0],color[1],color[2]

        if (r != 0) and (g != 0) and (b != 0):
            if (r < 245) and (g < 245) and (b < 245):
                for i in color:
                    i += 10
        if (r == 0) and (g != 0) and (b != 0):
            if (g < 245) and (b < 245):
                color[1] += 10
                color[2] += 10
        if (r != 0) and (g == 0) and (b != 0):
            if (r < 245) and (b < 245):
                color[0] += 10
                color[2] += 10
        if (r != 0) and (g != 0) and (b == 0):
            if (r < 245) and (g < 245):
                color[0] += 10
                color[1] += 10
        if (r == 0) and (g == 0) and (b != 0):
            if (b < 245):
                color[2] += 10
        if (r != 0) and (g == 0) and (b == 0):
            if (r < 245):
                color[0] += 10
        if (r == 0) and (g != 0) and (b == 0):
            if (g < 245):
                color[1] += 10
        
        self.color = color
        self.fill(color)
        self.write()
        
    def brightnessDown(self):
        color = self.color
        #print(color)
        
        r,g,b = color[0],color[1],color[2]

        if (r != 0) and (g != 0) and (b != 0):
            if (r < 245) and (g < 245) and (b < 245):
                for i in color:
                    i -= 10
        if (r == 0) and (g != 0) and (b != 0):
            if (g < 245) and (b < 245):
                color[1] -= 10
                color[2] -= 10
        if (r != 0) and (g == 0) and (b != 0):
            if (r < 245) and (b < 245):
                color[0] -= 10
                color[2] -= 10
        if (r != 0) and (g != 0) and (b == 0):
            if (r < 245) and (g < 245):
                color[0] -= 10
                color[1] -= 10
        if (r == 0) and (g == 0) and (b != 0):
            if (b < 245):
                color[2] -= 10
        if (r != 0) and (g == 0) and (b == 0):
            if (r < 245):
                color[0] -= 10
        if (r == 0) and (g != 0) and (b == 0):
            if (g < 245):
                color[1] -= 10
        
        self.color = color
        self.fill(color)
        self.write()
