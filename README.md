## Práctica 1.

# TAREA 1
### Crea una imagen, p.e. 800x800, con la textura del tablero de ajedrez

Para la realización de esta tarea creimos conveniente encapsular toda la lógica dentro de una clase llamada Chessboard. Esta clase nos permite Crear cualquier tipo de tablero estilo ajedrez con la cantidad de cuadros por fila que queramos.

Para utilizarla simplemente hay que crear la clase especificando en el parámetro n_rows el tipo de tablero que queremos, en este caso de 8 casillas como uno convencional, y finalmente usar el método to_img() para devolver un array de numpy y finalmente mostrarlo.

```python
chessboard = Chessboard(n_rows=8)
image = chessboard.to_img()

plt.imshow(image, cmap='gray')
plt.show()
```

La implementación de esta clase cuenta con un método interno llamado paint_square que dado una posición y un ancho del cuadrado nos lo pinta encima de nuestro array con el color que nosotros queramos (En este caso será o bien negro, o bien blanco).

```python
def paint_quare(self, image, point, width, color):
    image[point[1]:point[1] + width,point[0]:point[0]+width] = color
```

Por último el método to_img para pintar el tablero a través de dos for loops que van recorriendo todo el array de forma vertical dejando los cuadrados pintados en los sitios correspondientes. Indicar que el recorrido va dando saltos del tamaño de uno de los cuadrados para de esta forma por cada cuadrado negro dibujado, se dejara un espacio en blanco de mismo tamaño. La variable fase se utiliza para desplazar el dibujo hacia debajo y que no nos queden rayas negras y blancas sino el tablero de ajedrez.
    
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

SARA

# TAREA 4
## Modifica de alguna forma los valores de un plano de la imagen

Para esta tarea lo que hemos echo es separar los canales rgb de la imagen pero manteniendo el formato rgb es decir una tupla con 3 valores (Aunque cabe destacar que en openCV no se utiliza el rgb sino el bgr)

Para ello lo primero es multiplicar los pixeles de la imagen por 3 matrices [1, 0, 0], [0, 1, 0], [0, 0, 1] de esta forma nos iremos quedando con cada uno de los canales

A la primera imagen le aplicamos un filtro negativo restando por 1 toda la imagen entera. A la segunda la multiplicamos por 2 para aumentar el brillo pero asegurandonos que no haya ningún pixel que pase de 255 usango la función np.clip. Por último al tercero le bajamos el brillo dividiendo todos sus pixeles entre 2.

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

Por ultimo creamos un stack con las tres imagenes formadas y lo mostramos

```python
    final = np.hstack((r, g, b))
    final = final.astype(np.uint8)

    w, h, c = frame.shape

    cv2.imshow("CAMERA", cv2.resize(final, (int(w*3),int(h/3)),cv2.INTER_NEAREST))
```


# TAREA 5
### Pintar círculos en las posiciones del píxel más claro y oscuro de la imagen  ¿Si quisieras hacerlo sobre la zona 8x8 más clara/oscura?

Comencemos por la primera tarea. Para conocer el pixel mas claro y el mas oscuro hemos probado dos métodos: primero recorriendo la imagen matriz con dos bucles for y segundo usando funciones de numpy para poder averiguar el pixel.

El primer método es sencillo recorre toda la matriz guardando en dos variables el punto con máximo brillo y el brillo en ese punto. Si algún pixel supera ese brillo se actualiza el brillo máximo y el punto. Así hasta que termina el recorrido. Lo mismo para la función del pixel mas oscuro pero al revés por lo tanto no considero que haya que explicarlo. Este método daba mucho lag debido al tiempo que tarda en recorrer toda la matriz por iteración

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

El segundo método usa numpy y es bastante más compacto. Para esto primero se usa la función aveerage en el axis2 de esta forma convertimos todos los pixeles rgb a un escalar que es el promedio de los 3 valores.

Después buscamos con argmax el indice del máximo de los valores de esta matriz este método duelve el indice como si fuera un array de 1d por lo tanto tendremos que usar unravel_index para convertirlo en un punto de dos dimensiones sobre nuestra imagen.

```python
    pmas_claro = np.unravel_index(np.argmax(np.average(image, axis=2)), image.shape[:2])
```

Por último pintamos este punto y ya estaría. Para la versión oscura basta con sustituir argmax por argmin por lo tanto veo innecesario exlicarlo.

```python
    cv2.circle(image, (pmas_claro[1], pmas_claro[0]), 20, (255, 0, 0), -1)
    cv2.circle(image, (pmas_oscuro[1], pmas_oscuro[0]), 20, (0, 0, 255), -1)
```

La segunda tarea sería buscar la región de 8x8 con mayor brillo y menor brillo. Para esto hemos igual que antes desarrollado dos formas, una lenta recorriendo con dos bucles toda la matriz y otra rápida usando numpy y una de las propiedades de la operación de convolución.

La primera forma hace exactamente lo mismo que en la de un pixel lo que va calculando el brillo por región envez de puntualmente y apuntando el pixel de arriba a la izquierda de la región procesada. A su vez al recorrer el bucle hay que restar 7 a las dimensiones de la imagen para no pasarnos de indice. De nuevo este método es muy lento y producía cierto lag.

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

Respecto el método con numpy es muy parecido al anterior. Primero hacemos un average para sacar el brillo por pixel. Después aplicamos una convolución con una matriz de todo 1nos de 8x8 de esta forma en cada pixel tendremos el brillo de cada región de 8x8 y por ultimo usamos argmax y unravel index al igual que antes para quedarnos el punto de esa región de 8x8.


```python
    means_matrix = np.average(image, axis=2)

    convolve = cv2.filter2D(means_matrix, -1, np.ones((8, 8)))

    point = np.unravel_index(np.argmax(convolve), convolve.shape[:2])
    point_min = np.unravel_index(np.argmin(convolve), convolve.shape[:2])
```

# TAREA 6
### Haz tu propuesta pop art

SARA