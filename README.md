# programacion
Parcial Programación Audio

Main:

from seno import Seno
from archivar import Archivo


def main():
    print "La profundidad de bits es: 16 bits"
    print "La frecuencia de muestreo es: 44100 Hz"
    frecuencia = input("Ingrese la frecuencia: ")
    tiempo = input("Ingrese el tiempo: ")
    profundidadbits = 16#input("Ingrese la profundidad de bits: ")
    valor_senal = input ("Que valor desea ingresar?: \n1. Nivel Pico \n2. Valor RMS \n")
    if valor_senal == 1:
        nivel_pico = input("Ingrese el nivel pico de la senal: ")
        frecuenciamuestreo = 44100#input("Ingrese la frecuencia de muestreo: ")
        nombre = raw_input("Ingrese el nombre del archivo (sin extension): ")
        nombre=nombre+".wav"

        tono = Seno(frecuencia,profundidadbits,tiempo,frecuenciamuestreo,nivel_pico)
        arr = tono.generar()
        tono.graficar(arr)

        Archivador = Archivo(frecuenciamuestreo,profundidadbits,nombre)
        Archivador.Guardararchivo(arr)

    elif valor_senal == 2:
        rms = input("Ingrese el valor RMS de la senal: ")
        frecuenciamuestreo = 44100#input("Ingrese la frecuencia de muestreo: ")
        nombre = raw_input("Ingrese el nombre del archivo (sin extension): ")
        nombre=nombre+".wav"

        ValorLinealRMS = (2**profundidadbits/2)*10**(rms/20)
        ValorLinealPico = (ValorLinealRMS/2**0.5)

        tono = Seno(frecuencia,profundidadbits,tiempo,frecuenciamuestreo,ValorLinealPico)
        arr = tono.generar()
        tono.graficar(arr)

        Archivador = Archivo(frecuenciamuestreo,profundidadbits,nombre)
        Archivador.Guardararchivo(arr)


if __name__ == "__main__":
    main()
    
Seno:

import math
import matplotlib.pylab as plt

class Seno:
    def __init__(self, frecuencia, bits, tiempo, muestreo, Valorpico):
        self.frecuencia = frecuencia
        self.bitdepth = bits
        self.tiempo = tiempo
        self.pico = Valorpico
        self.frecuenciamuestreo = muestreo
        self.frames = tiempo * muestreo
        self.periodomuestreo = 1 / float(muestreo)

    def generar(self):
        array = []
        for i in range(0, self.frames):
            # x(t) = asin(2pifrecuencia*periodomuestreo)
            dato = self.pico * math.sin(2 * math.pi * self.frecuencia * self.periodomuestreo * i)
            array.append(dato)
        return array

    def graficar(self, arreglo):
        plt.plot(arreglo)
        plt.show()
        
Square:

import math
import matplotlib.pylab as plt


class Square:
    def __init__(self, frecuencia, bits, tiempo, muestreo, Valorpico):
        self.frecuencia = frecuencia
        self.bitdepth = bits
        self.tiempo = tiempo
        self.pico = Valorpico
        self.frecuenciamuestreo = muestreo
        self.frames = tiempo * muestreo
        self.periodomuestreo = 1 / float(muestreo)

    def generar(self):
        array = []
        for i in range(0, self.frames):
            # x(t) = asin(2pifrecuencia*periodomuestreo)
            dato = self.pico * math.sin(2 * math.pi * self.frecuencia * self.periodomuestreo * i)
            if dato > 0:
                dato = self.pico
            elif dato < 0:
                dato = -self.pico
            array.append(dato)
        return array

    def graficar(self, arreglo):
        plt.plot(arreglo)
        plt.show()
        
Saw:

import math
import matplotlib.pylab as plt


class Saw:
    def __init__(self, frecuencia, bits, tiempo, muestreo, Valorpico):
        self.frecuencia = frecuencia
        self.bitdepth = bits
        self.tiempo = tiempo
        self.pico = Valorpico
        self.frecuenciamuestreo = muestreo
        self.frames = tiempo * muestreo
        self.periodomuestreo = 1 / float(muestreo)

    def generar(self):
        array = []
        for i in range(0, self.frames):
            # x(t) = asin(2pifrecuencia*periodomuestreo)
            dato = self.pico * math.sin(2 * math.pi * self.frecuencia * self.periodomuestreo * i)
            if dato > 0:
                dato = (self.pico * i) / math.pi
            array.append(dato)
        return array

    def graficar(self, arreglo):
        plt.plot(arreglo)
        plt.show()
        
        
Triangle:

import math
import matplotlib.pylab as plt
from sympy import *

class Triangle:
    def __init__(self, frecuencia, bits, tiempo, muestreo, Valorpico):
        self.frecuencia = frecuencia
        self.bitdepth = bits
        self.tiempo = tiempo
        self.pico = Valorpico
        self.frecuenciamuestreo = muestreo
        self.frames = tiempo * muestreo
        self.periodomuestreo = 1 / float(muestreo)
        self.periodo = 1 / float(frecuencia)

    def generar(self):
        array = []
        for i in range(0, self.frames):
            # x(t) = asin(2pifrecuencia*periodomuestreo)
            dato = self.pico * math.sin(2 * math.pi * self.frecuencia * self.periodomuestreo * i)
            m = self.pico / (self.periodo/4)
            y = m*(self.periodomuestreo)
            diferencia = y - dato

            if dato > 0:
                dato = dato + diferencia

            elif dato < 0:
                dato = dato - diferencia

            else:
                dato = dato

            """if dato > 0:
                dato = diff(sin(2 * math.pi * self.frecuencia * self.periodomuestreo * i),i)
                print dato"""

            array.append(dato)
        return array

    def graficar(self, arreglo):
        plt.plot(arreglo)
        plt.show()
        
Archivar:

import wave, struct


class Archivo:

    def __init__(self, frecuenciamuestreo, bits, nombre):
        self.frecuencia = frecuenciamuestreo
        self.bits = bits
        self.nombre = nombre

    def Guardararchivo(self, datos):
        archivodesalida = wave.open(self.nombre, "w")
        Set_Bits = self.bits/8
        archivodesalida.setparams((1, Set_Bits, self.frecuencia, 0, 'NONE', 'not compressed'))

        values = []
        for i in range(0, len(datos)):

            packed_value = struct.pack('<h', datos[i])

            values.append(packed_value)

        value_str = ''.join(values)
        archivodesalida.writeframes(value_str)
        archivodesalida.close()
        
Valor Pico:

import math

a = 0
while a == 0:
    max = input("Ingrese el valor pico maximo: ")
    Valor_pico_dBFS =	20 * math.log10(math.fabs(float(max))/((2**16)/2))
    print (Valor_pico_dBFS)

Rango Dinámico:

import math

a = 0
while a == 0:
    bitdepth = input("Ingrese la profundidad de bits: ")
    exp = 2**bitdepth
    rango_dinamico = 20 * math.log10(exp/(26.02*6))
    print rango_dinamico

Sympy


from sympy import *
x = Symbol('x')
y = Symbol('y')
a = diff(sin(2*pi*440*44100*x),x)
print a
