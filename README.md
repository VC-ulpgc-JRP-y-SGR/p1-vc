## Práctica 1.

# TAREA 1
### Crea una imagen, p.e. 800x800, con la textura del tablero de ajedrez

    Para la realización de esta tarea creimos conveniente encapsular toda la lógica dentro de una clase llamada Chessboard. Esta clase nos permite Crear cualquier tipo de tablero estilo ajedrez con la cantidad de cuadros por fila que queramos.

    Para utilizarla simplemente hay que crear la clase especificando en el parámetro n_rows el tipo de tablero que queremos, en este caso de 8 casillas como uno convencional, y finalmente usar el método to_img() para devolver un array de numpy y finalmente mostrarlo.

```python3
chessboard = Chessboard(n_rows=8)
image = chessboard.to_img()

plt.imshow(image, cmap='gray')
plt.show()
```

    La implementación de esta clase cuenta con un método interno llamado paint_square que dado una posición y un ancho del cuadrado nos lo pinta encima de nuestro array con el color que nosotros queramos (En este caso será o bien negro, o bien blanco).

```
def paint_quare(self, image, point, width, color):
    image[point[1]:point[1] + width,point[0]:point[0]+width] = color
```

    Por último el método to_img para pintar el tablero a través de dos for loops que van recorriendo todo el array de forma vertical dejando los cuadrados pintados en los sitios correspondientes. Indicar que el recorrido va dando saltos del tamaño de uno de los cuadrados para de esta forma por cada cuadrado negro dibujado, se dejara un espacio en blanco de mismo tamaño. La variable fase se utiliza para desplazar el dibujo hacia debajo y que no nos queden rayas negras y blancas sino el tablero de ajedrez.
    
```
    def to_img(self):
        img = np.zeros((self.size, self.size,1), dtype = np.uint8)
        paint_phase = 0
        
        for x in range(0, self.size, (self.size//self.n_rows)):
            for y in range(paint_phase, self.size, (self.size//self.n_rows) * 2):
                self.paint_quare(img, (y, x), self._square_size, 255)
            paint_phase = 100 if paint_phase == 0 else 0
        return img
```


# TAREA 2
### Crear una imagen estilo Mondrian

    Para hacer un dibujo al estilo Mondrian, como estos se basan sobre todo en dibujar rectángulos, se ha intentado generalizar la creación de estos por medio de una clase que los represente, la cual hemos llamado Rectangle.
    
    Esta clase tiene como atributos el color, la altura y anchura del rectángulo, así como un valor 'offset' que nos sirve para determinar el tamaño en píxeles del padding del rectángulo.
    
    Para poder dibujar un objeto de clase Rectangle en una imagen, se hace uso de su método *apply()*, al cual se le pasa como argumentos la imagen donde se quiere dibujar y el punto de la imagen donde comienza el rectángulo. Con esto, con simplemente trabajar con las medidas del rectángulo y el punto inicial, podemos establecer los píxeles de la imagen donde se va a tener que pintar del color del rectángulo.

```python3
    class Rectangle:
        def __init__(self, width, height, rgb, offset = 0):
            self.width = width
            self.height = height
            self.rgb = rgb
            self.offset = offset

        def apply(self, image, point):
            image[point[1]+self.offset:point[1] + self.height-self.offset , point[0]+self.offset:point[0]+self.width-self.offset] = self.rgb
```
    Para hacer uso de esto y crear un dibujo estilo Mondrian, se ha creado una imagen con todos sus píxeles a color negro y se han creado una serie de rectángulos con los valores deseados de anchura, altura, color y padding y se recorren uno a uno para que se apliquen en la imagen final a través del método *apply()*.

# TAREA 3
## Crea una imagen estilo Mondrian con OPENCV

SARA

# TAREA 4
## Modifica de alguna forma los valores de un plano de la imagen

Para esta tarea lo que hemos echo es separar los canales rgb de la imagen pero manteniendo el formato rgb es decir una tupla con 3 valores (Aunque cabe destacar que en openCV no se utiliza el rgb sino el bgr)

Para ello lo primero es multiplicar los pixeles de la imagen por 3 matrices [1, 0, 0], [0, 1, 0], [0, 0, 1] de esta forma nos iremos quedando con cada uno de los canales

```

```

# TAREA 5
### Pintar círculos en las posiciones del píxel más claro y oscuro de la imagen  ¿Si quisieras hacerlo sobre la zona 8x8 más clara/oscura?

PEPE


# TAREA 6
### Haz tu propuesta pop art

SARA