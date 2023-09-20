## Práctica 1.

# TAREA 1
### Crea una imagen, p.e. 800x800, con la textura del tablero de ajedrez

Para llevar a cabo esta tarea, consideramos conveniente encapsular toda la lógica dentro de una clase llamada Chessboard. Esta clase nos permite crear tableros de estilo ajedrez de cualquier tamaño, especificando la cantidad de cuadros por fila que deseamos.

Para utilizarla, simplemente debemos crear una instancia de la clase, indicando en el parámetro n_rows el tamaño del tablero que queremos; por ejemplo, 8 casillas para uno convencional. Luego, podemos utilizar el método to_img() para obtener un arreglo de numpy y mostrar el tablero.

```python
chessboard = Chessboard(n_rows=8)
image = chessboard.to_img()

plt.imshow(image, cmap='gray')
plt.show()
```

La implementación de esta clase incluye un método interno llamado paint_square que permite dibujar un cuadrado en una posición específica del tablero con un ancho determinado y el color que elijamos. En este caso, los colores disponibles son negro o blanco.

```python
def paint_quare(self, image, point, width, color):
    image[point[1]:point[1] + width,point[0]:point[0]+width] = color
```

Finalmente, el método to_img se encarga de dibujar el tablero mediante dos bucles for que recorren todo el arreglo verticalmente, colocando los cuadrados en las posiciones correspondientes. Es importante destacar que en cada iteración, los bucles avanzan en incrementos del tamaño de uno de los cuadrados. De esta manera, por cada cuadrado negro dibujado, se deja un espacio en blanco del mismo tamaño. La variable fase se utiliza para ajustar el desplazamiento del dibujo hacia abajo, evitando que se formen filas alternas de cuadrados negros y blancos y logrando así el patrón de un tablero de ajedrez.
    
```python
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

```python
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

Se ha escogido resolver la tarea de Mondrian haciendo uso de las funciones de dibujo de OpenCV. 

La librería *cv2* de OpenCV cuenta con una función llamada *rectangle()* la cual se usa para dibujar rectángulos en una imagen pasando como argumentos la imagen, el punto inicial del rectángulo superior izquierdo, el punto final inferior derecho, el color en formato RGB con el que se va a colorear y su grosor. Si el grosor tiene como valor '-1', se rellenará del color escogido por dentro.

```python
    cv2.rectangle(image, (x0, y1), (x1, y0), (r, g, b), -1)
```

Siendo que los dibujos al estilo Mondrian se basan principalmente en el dibujo de rectángulos, para resolver el ejercicio solo habría que hacer uso de la función por cada rectángulo que queramos dibujar, partiendo de una base de la imagen en color negro como fondo.

# TAREA 4
## Modifica de alguna forma los valores de un plano de la imagen

Para esta tarea, hemos realizado la separación de los canales RGB de una imagen, manteniendo el formato RGB, aunque es importante mencionar que en OpenCV se utiliza el formato BGR.

Primero, multiplicamos los píxeles de la imagen por tres matrices: [1, 0, 0], [0, 1, 0], [0, 0, 1]. De esta manera, obtenemos cada uno de los canales por separado.

Para la primera imagen, aplicamos un filtro negativo restando 1 a todos los píxeles de la imagen. En el segundo canal, multiplicamos por 2 para aumentar el brillo, asegurándonos de que ningún píxel supere el valor de 255 utilizando la función np.clip. Por último, en el tercer canal, reducimos el brillo dividiendo todos sus píxeles entre 2.

```python
    # SEPARO LOS CANALES MULTIPLICANDO POR EL ARRAY [1, 0, 0] PARA QUE MANTENGA EL FORMATO BGR
    # LO CONVERTIMOS EN NEGATIVO RESTANDO 1
    r = 1 - (frame[:, :] *  [1, 0, 0])

    #AUMENTO EL BRILLO DE LA IMAGEN MULTIPLICANDO POR UN ESCALAR
    g = (frame[:, :] * [0, 1, 0]) * 2

    #PASA QUE MUCHAS VECES ESA MULTIPLICACION SE SALE POR ENCIMA DE 255 POR LO TANTO LA IMAGEN SE DISTORSIONA
    #A SI QUE NOS ASEGURAMOS QUE EL MINIMO SEA 0 Y EL MAXIMO SEA 255 DE LOS DIFERENTES ELEMENTOS
    g = np.clip(g, 0, 255)

    #VAMOS A OSCURECER LA IMAGEN DIVIDIENDOLA ENTRE UN NUMERO
    # EN ESTE CASO NO HAY PELIGRO DE QUE EL VALOR BAJE DE 0 PUES LA FUNCION F(X) = X/n tiene una asintota horizontal en 0
    b = (frame[:, :] * [0, 0, 1])/2
```

Por ultimo creamos un stack con las tres imagenes formadas y las mostramos.

```python
    final = np.hstack((r, g, b))
    final = final.astype(np.uint8)

    w, h, c = frame.shape

    cv2.imshow("CAMERA", cv2.resize(final, (int(w*3),int(h/3)),cv2.INTER_NEAREST))
```

# TAREA 5
### Pintar círculos en las posiciones del píxel más claro y oscuro de la imagen  ¿Si quisieras hacerlo sobre la zona 8x8 más clara/oscura?

Para abordar la tarea de encontrar el píxel más claro y el más oscuro en una imagen, exploramos dos enfoques diferentes:

Método de Bucles For: En este enfoque, recorrimos la matriz de la imagen utilizando dos bucles for. Durante este recorrido, mantuvimos un registro de la ubicación del píxel con el brillo máximo y su valor de brillo correspondiente. Si encontramos un píxel con un brillo superior, actualizamos los valores del píxel más brillante. Aplicamos un enfoque similar para encontrar el píxel más oscuro. Sin embargo, este método puede resultar menos eficiente debido al tiempo que lleva recorrer toda la matriz en cada iteración.

```python
def find_brightest_pixel(image):
    # Initialize variables to keep track of the brightest pixel and its brightness
    brightest_pixel = None
    max_brightness = -1  # Initialize to a low value

    # Iterate through all the pixels in the image
    for y in range(image.shape[0]):
        for x in range(image.shape[1]):
            # Calculate the brightness of the current pixel
            pixel_brightness = np.mean(image[y, x])

            # Check if this pixel is brighter than the previous brightest
            if pixel_brightness > max_brightness:
                max_brightness = pixel_brightness
                brightest_pixel = (x, y)  # Store the coordinates of the brightest pixel

    return brightest_pixel, max_brightness
```

El segundo método se basa en el uso de NumPy y es considerablemente más conciso. Para ello, primero aplicamos la función average en el eje 2, lo que nos permite convertir todos los píxeles RGB en un valor escalar que representa el promedio de los tres componentes de color.

Luego, utilizamos la función argmax para encontrar el índice del valor máximo en esta matriz. Es importante destacar que esta función devuelve el índice como si fuera un array de una dimensión. Por lo tanto, empleamos la función unravel_index para convertir este índice en un punto de dos dimensiones que corresponde a la ubicación del píxel en nuestra imagen.

```python
    pmas_claro = np.unravel_index(np.argmax(np.average(image, axis=2)), image.shape[:2])
```

Por último pintamos este punto y ya estaría. Para la versión oscura basta con sustituir argmax por argmin por lo tanto veo innecesario exlicarlo.

```python
    cv2.circle(image, (pmas_claro[1], pmas_claro[0]), 20, (255, 0, 0), -1)
```

Comencemos con la tarea de calcular la región 8x8 mas brillante o mas oscuro.

En el primer enfoque, realizamos una tarea similar a la de encontrar el píxel más brillante y oscuro, pero en lugar de trabajar con píxeles individuales, calculamos el brillo por regiones de 8x8 píxeles en toda la imagen. Comenzamos desde la esquina superior izquierda de la imagen y avanzamos a través de bucles para procesar cada región. Durante este proceso, mantenemos un seguimiento de la región con el brillo más alto y el brillo más bajo. Sin embargo, debido a las dimensiones de la imagen, debemos restar 7 a cada dimensión para evitar desbordar los índices. Este método, aunque cumple su objetivo, tiende a ser lento y puede causar cierto retraso.


```python
def find_darkest_8x8_region(image):
    # Initialize variables to keep track of the darkest region and its brightness
    darkest_region = None
    min_region_brightness = 256  # Initialize to a high value (assuming 8-bit grayscale)
    
    # Iterate through all possible 8x8 regions in the image
    for y in range(image.shape[0] - 7):
        for x in range(image.shape[1] - 7):
            # Extract the 8x8 region from the image
            region = image[y:y+8, x:x+8]
            
            # Calculate the average brightness of the region
            region_brightness = np.mean(region)
            
            # Check if this region is darker than the previous darkest
            if region_brightness < min_region_brightness:
                min_region_brightness = region_brightness
                darkest_region = (x, y)  # Store the top-left coordinates of the darkest region
    
    return darkest_region, min_region_brightness
```

En el segundo método, empleamos NumPy para lograr un enfoque más eficiente. Primero, calculamos el brillo promedio por píxel en la imagen utilizando la función average. Luego, aplicamos una operación de convolución utilizando una matriz de 8x8 compuesta por unos. Esta convolución nos proporciona, en cada píxel, el brillo promedio de la región circundante de 8x8 píxeles.

A continuación, utilizamos argmax y unravel_index, como mencionamos anteriormente, para determinar la ubicación de la región de 8x8 con el brillo máximo y mínimo en la imagen.


```python
    means_matrix = np.average(image, axis=2)

    convolve = cv2.filter2D(means_matrix, -1, np.ones((8, 8)))

    point = np.unravel_index(np.argmax(convolve), convolve.shape[:2])
    point_min = np.unravel_index(np.argmin(convolve), convolve.shape[:2])
```

# TAREA 6
### Haz tu propuesta pop art

