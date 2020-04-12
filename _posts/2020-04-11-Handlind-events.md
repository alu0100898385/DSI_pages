---
layout: post
title:  "Handling events"
---
# Handling events

A continuación se presentan ejemplos prácticos sacados del 
[Capitulo Handling events](https://eloquentjavascript.net/15_event.html#c_srTkrKlkl+ "https://eloquentjavascript.net").

## test1.html
Codigo de un controlador de eventos sencillo:
````
<p>Click this document to activate the handler.</p>
<script>
  window.addEventListener("click", () => {
    console.log("You knocked?");
  });
</script>
````
- Con **window.addEventListener** dejamos al navegador esperando a que ocurra un evento, en este caso cuando hacemos *click* y cuando ocurre muestra el mensaje en consola.

# Events and DOM nodes

En el anterior ejemplo resgistraba eventos en toda la ventana. Los oyentes de eventos solo se llaman cuando el evento ocurre en el contexto del objeto en el que están registrados. 

## test2.html
En el siguiente ejemplo se le añade un controlador de eventos solo en un botón:
````
<button>Click me</button>
<p>No handler here.</p>
<script>
  let button = document.querySelector("button");
  button.addEventListener("click", () => {
    console.log("Button clicked.");
  });
</script>
````
- A diferencia del anterior codigo no llamamos a la función **addEventListener** desde **window** sino desde **button**.
- Este codigo hace que detectes tantos eventos como "clicks" en dicho botón.

## test3.html
En caso de que solo quieres registrar el evento de click una sola vez:
````
<button>Act-once button</button>
<script>
  let button = document.querySelector("button");
  function once() {
    console.log("Done.");
    button.removeEventListener("click", once);
  }
  button.addEventListener("click", once);
</script>
````
- Cuando **button.addEventListener** detecta un evento ejecuta la función **once**, después elimina el controlador del evento con **button.removeEventListener**.

# Event objects
Las funciones del controlador de eventos pasan un argumento: el objeto de evento . Este objeto contiene información adicional sobre el evento. Por ejemplo, si queremos saber qué botón del mouse se presionó, podemos ver la propiedad del evento del objeto **buttom**.
## test4.html
````
<button>Click me any way you want</button>
<script>
  let button = document.querySelector("button");
  button.addEventListener("mousedown", event => {
    if (event.button == 0) {
      console.log("Left button");
    } else if (event.button == 1) {
      console.log("Middle button");
    } else if (event.button == 2) {
      console.log("Right button");
    }
  });
</script>
````
# Propagation
## test5.html
En el siguiente codigo se pueden ver dos eventos, **click en el parrafo** y **click en el botón** que está dentro del párrafo. Al dar click en el boton también se detecta el evento en el parrafo. El orden en el que se ejecutan es del más especifico, en este caso el del botón y después se propaga hacia fuera, el evento del parrafo.
````
<p>A paragraph with a <button>button</button>.</p>
<script>
  let para = document.querySelector("p");
  let button = document.querySelector("button");
  para.addEventListener("mousedown", () => {
    console.log("Handler for paragraph.");
  });
  button.addEventListener("mousedown", event => {
    console.log("Handler for button.");
    if (event.button == 2) event.stopPropagation();
  });
</script>
````

También es posible usar una propiedad **target** para lanzar una red amplia para un tipo específico de evento. Por ejemplo, si contiene una larga lista de botones, puede ser más conveniente registrar un controlador de un solo clic externo y hacer que use una propiedad **target** para determinar si se hizo clic en un botón, en lugar de registrar controladores individuales en todos los botones.

## test6.html
Un Ejemplo de lo explicado anteriormente del uso de la propiedad target:
````
<button>A</button>
<button>B</button>
<button>C</button>
<script>
  document.body.addEventListener("click", event => {
    if (event.target.nodeName == "BUTTON") {
      console.log("Clicked", event.target.textContent);
    }
  });
</script>
````
- **document.body.addEventListener**: escucha eventos en toda la ventana.
- **event.target.nodeName == "BUTTON"**: Si el evento que se detecta es en un botón lanza el mensaje en consola.

# Default actions

Muchos eventos tienen acciones por defecto, como por ejemplo al darle click a un link te redirige a la página de dicho link.
## test7.html
En este ejemplo trata sobre cambiar la acción por defecto de un link, en vez de redirigirte a una página te de un mensaje por consola:
````
<a href="https://developer.mozilla.org/">MDN</a>
<script>
  let link = document.querySelector("a");
  link.addEventListener("click", event => {
    console.log("Nope.");
    event.preventDefault();
  });
</script>
````
- Similar a otros ejmplos explicados anteriormente, pero aquí hay que añadir **event.preventDefault();** para cambiar su acción por defecto.

# Key events
## Test8.html
Se le pueden asignar eventos a teclas como en el siguiente código:
````
<p>This page turns violet when you hold the V key.</p>
<script>
  window.addEventListener("keydown", event => {
    if (event.key == "v") {
      document.body.style.background = "violet";
    }
  });
  window.addEventListener("keyup", event => {
    if (event.key == "v") {
      document.body.style.background = "";
    }
  });
</script>
````
- Cuando se pulsa la tecla V (**keydown**) cambia el fondo de pantalla a violeta.
- Cuando se deja de pulsar (**keyup**) vuelve a su color de defecto.
## test9.html

````
<p>Press Control-Space to continue.</p>
<script>
  window.addEventListener("keydown", event => {
    if (event.key == " " && event.ctrlKey) {
      console.log("Continuing!");
    }
  });
</script>
````

- En este ejemplo se muestra como detectar la pulsación de dos teclas.

# Pointer events

## Mouse clicks (test10.html)
````
<style>
    body {
      height: 200px;
      background: beige;
    }
    .dot {
      height: 8px; width: 8px;
      border-radius: 4px; /* rounds corners */
      background: blue;
      position: absolute;
    }
  </style>
  <script>
    window.addEventListener("click", event => {
      let dot = document.createElement("div");
      dot.className = "dot";
      dot.style.left = (event.pageX - 4) + "px";
      dot.style.top = (event.pageY - 4) + "px";
      document.body.appendChild(dot);
    });
  </script>
````
- No solo detecta el evento de un click si no también *donde ocurre en pantalla* con **pageX** y **pageY**.

## Mouse motion (test11.html)
````
<p>Drag the bar to change its width:</p>
<div style="background: orange; width: 60px; height: 20px">
</div>
<script>
  let lastX; // Tracks the last observed mouse X position
  let bar = document.querySelector("div");
  bar.addEventListener("mousedown", event => {
    if (event.button == 0) {
      lastX = event.clientX;
      window.addEventListener("mousemove", moved);
      event.preventDefault(); // Prevent selection
    }
  });

  function moved(event) {
    if (event.buttons == 0) {
      window.removeEventListener("mousemove", moved);
    } else {
      let dist = event.clientX - lastX;
      let newWidth = Math.max(10, bar.offsetWidth + dist);
      bar.style.width = newWidth + "px";
      lastX = event.clientX;
    }
  }
</script>
````
- Aquí detecta el movimiento de Mouse cuando hace click en el elemento **bar**. Esto lo hace con **window.addEventListener("mousemove", moved)**.

## Touch events
````
<style>
  dot { position: absolute; display: block;
        border: 2px solid red; border-radius: 50px;
        height: 100px; width: 100px; }
</style>
<p>Touch this page</p>
<script>
  function update(event) {
    for (let dot; dot = document.querySelector("dot");) {
      dot.remove();
    }
    for (let i = 0; i < event.touches.length; i++) {
      let {pageX, pageY} = event.touches[i];
      let dot = document.createElement("dot");
      dot.style.left = (pageX - 50) + "px";
      dot.style.top = (pageY - 50) + "px";
      document.body.appendChild(dot);
    }
  }
  window.addEventListener("touchstart", update);
  window.addEventListener("touchmove", update);
  window.addEventListener("touchend", update);
</script>
````
- Aquí en vez de controlar clicks de ratón controla eventos de una pantalla táctil: cuando el usuario toca la pantalla (**touchstart**), cuando mueve el dedo por ella (**touchmove**) y cuando deja de tocarla (**touchend**).

# Balloon.html
Uno de los ejercicios que nos piden hacer, hacer inflar o desinflar un goblo en pantalla cada vez que pulsamos las teclas arriba y abajo.
````
<p id="balloon" style="font-size:20px">🎈</p>

<script>
let balloon = document.querySelector("p");
let size = 20;

window.addEventListener("keydown", event => {
    if (size < 200){
        if (event.key == "ArrowUp"  ) {
            // console.log("UP!");
            // console.log(balloon.style.fontSize);
            size+=10;
            balloon.style.fontSize=`${size}px`;
            event.preventDefault();
        }
        if (event.key == "ArrowDown"  ) {
        // console.log("DOWN!");
            if (size > 10 ){
                size-=10;
                balloon.style.fontSize=`${size}px`;
            }
            event.preventDefault();
        }
    }
    else
        document.getElementById("balloon").innerHTML = "<p>💥</p>";
});
</script>
````
- En la variable **let = ballon** guardo el parrafo que contiene el globo 🎈.
- En la variable **let = size** guarda el tamaño del globo (tamaño del texto).
- El script está pediente de pulsasiones de tecla con **window.addEventListener("keydown", event => ...**
- Si el tamaño es menor de 200 se podrá modificar el tamaño, si es mayor se cambiara el contenido del parrafo por 💥 y ya no podrá cambiar de tamaño.

# Mouse-Trail.html
Segundo ejercicio, aqui nos piden que implementemo un rastro del ratón:
````
<!DOCTYPE html>
<style>
    .trail { /* className for trail elements */
        position: absolute;
        height: 12px; width: 12px;
        border-radius: 6px;
        background: rgb(218, 89, 207);
    }
    body {
        height: 600px;
        background-color: rgb(47, 216, 160);
    }
</style>

<body>
<script>
    document.body.addEventListener("mousemove", moved);
    // create, style, append trail elements
    var trailElements = [];
    var numOfTrailElements = 10;
    for (var i = 0; i < numOfTrailElements; i++) {
        var element = document.createElement('div');
        element.className = 'trail';
        document.body.appendChild(element);
        trailElements.push(element);
    }

    // when mouse moves, display trail elements in wake of mouse pointer
    var counter = 0; // current trail element index
    function moved(event) {              
        trailElements[counter].style.left = event.clientX + 'px';
        trailElements[counter].style.top = event.clientY + 'px';
        if (counter < numOfTrailElements -1) 
            counter += 1; 
        else 
        counter = 0;
    }
    </script>
</body>
````
- Primero creamos los elementos (**elements**) que formarán parte del rastro del ratón estos se guardarán en un vector **trailElements**. El bucle **for** se encargará de crear cada uno de los elementos con su estilo y los insertara en el vector.
- La página está pendiente del movimiento del ratón **document.body.addEventListener("mousemove", moved);**, cuando detecta movimiento, este ejecuta la función **moved** que se encarga de posicionar cada elemento en la posición del ratón, creando así el rastro.

# Censored-Keyboard.html
Ejercicio en el cual debemos programar un campo de texto en el que no se pueda escribir las letras Q, W y X.
````
<p>Cesored Keyboard</p>
<p>Tipe something: <input type="text"></p>

<script>
    let input = document.querySelector("input");
    input.addEventListener("keydown", event => {
        if (event.key == "q" || event.key == "Q") {
            console.log("You can't tipe "+event.key);
            event.preventDefault();
        }
        if (event.key == "w" || event.key == "W") {
            console.log("You can't tipe "+event.key);
            event.preventDefault();
        }
        if (event.key == "x" || event.key == "X") {
            console.log("You can't tipe "+event.key);
            event.preventDefault();
        }
    });
</script>

````
- Primero creamos el campo de texto **imput** donde se escribirá.
- En el **script** añadimos un **addEventListener** pendiente de pulsaciones de tecla **keydown**.
- Si la tecla pulsada es una de las letras censuradas no se podrá escribir en el campo de texto **event.preventDefault()**, en su lugar se enviara un mensaje por consola.

# Tabs.html
Último ejercicio en el que se debe implementar una interfaz por pestañas que muestren su contenido al dar click sobre ellas.
````
<!-- Tab links -->
  <div class="tab">
    <button class="tablinks" onclick="asTabs(event, 'London')">London</button>
    <button class="tablinks" onclick="asTabs(event, 'Paris')">Paris</button>
    <button class="tablinks" onclick="asTabs(event, 'Tokyo')">Tokyo</button>
  </div>
  
  <!-- Tab content -->
  <div id="London" class="tabcontent">
    <h3>London</h3>
    <p>London is the capital city of England.</p>
  </div>
  
  <div id="Paris" class="tabcontent">
    <h3>Paris</h3>
    <p>Paris is the capital of France.</p>
  </div>
  
  <div id="Tokyo" class="tabcontent">
    <h3>Tokyo</h3>
    <p>Tokyo is the capital of Japan.</p>
  </div>

<style>
/* Style the tab */
.tab {
  overflow: hidden;
  border: 1px solid #ccc;
  background-color: #f1f1f1;
}

/* Style the buttons that are used to open the tab content */
.tab button {
  background-color: inherit;
  float: left;
  border: none;
  outline: none;
  cursor: pointer;
  padding: 14px 16px;
  transition: 0.3s;
}

/* Change background color of buttons on hover */
.tab button:hover {
  background-color: #ddd;
}

/* Create an active/current tablink class */
.tab button.active {
  background-color: #ccc;
}

/* Style the tab content */
.tabcontent {
  /* Display: none (No aparece en pantalla) */
  display: none;
  padding: 6px 12px;
  border: 1px solid #ccc;
  border-top: none;
}
</style>

<script>
function asTabs(evt, cityName) {

  // Get all elements with class="tabcontent" and hide them
  let tabcontent = document.getElementsByClassName("tabcontent");
  for (let i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }

  // Get all elements with class="tablinks" and remove the class "active"
  let tablinks = document.getElementsByClassName("tablinks");
  for (let i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }

  // Show the current tab, and add an "active" class to the button that opened the tab
  document.getElementById(cityName).style.display = "block";
  evt.currentTarget.className += " active";
}
</script>
````
- *Primero*: Creamos los **div** con codigo html. Vease que aquí se usa **onclick** que viene a ser similar a **button.addEventListener("click", () =>**
- *Segundo*: Creamos el estilo css, cabe destacar que la clase **.tabcontent** contiene **display: none;**, esto hace que no aparezca en pantalla.
- *Tercero*: Añadimos código Javascript, la función **asTabs** primero se asegura de quitar información que puedar está presente (En caso que ya hemos dado clink anteriormente a otra pestaña) y por último activa el **.tabcontent** de la pestaña activa.

# Jekyll and Github Pages
A continuación se procede a explicar como realizar el informe de practicas con Jekyll y Github Pages.
# Jekyll
Primero se ha istalado jekyll en la máquina local, cabe destacar que hay que tener instalado **ruby** y **gem** por lo tanto nos aseguramos de que los tenemos intalados.
````
...$ ruby -v

...$ gem -v
```` 
Una vez resuelto lo anterior procedemos a instalar jekyll:
````
...$ gem install jekyll bundler
````
Una vez terminada la instalación podemos comprobar la que se ha realizado correctamente:
````
...$ jekyll -v
````
Ya tendriamos todo instalado y listo para preparar nuestro repostorio y subirlo a la web. Para poder crear nuestra página web ejecutamos el siguiente comándo:

````
...$ jekyll new (name)
````
En este caso se ejecutó **jekyll new dsi-pages**. Dentro del proyecto nos crea el directorio *handdling-events* con contenido por defecto.
````
└── dsi-pages
    ├── 404.html
    ├── Gemfile
    ├── _config.yml
    ├── _posts
    │   └── 2020-04-10-welcome-to-jekyll.markdown
    ├── about.md
    └── index.md
```` 
**_config.yml**: Este archivo de configuración está destinado a configuraciones que afectan a todo su blog, valores que se espera que configure una vez y que rara vez edite después de eso. Aqui podemos modificar el titulo, descripción de la página, correo electrónico, usuario de github, etc..

Para poder ver nuestra pagina web nos situamos en el directorio generado y ejecutamos el siguiente comando la primera vez que ejecutemos el servidor:
````
...$ bundle exec jekyll serve
````
Una vez hecho esto por primera vez solo para volver a arrancar el servidor solo hace falta ejecutar:
````
...$ jekyll serve
````
Al ejecutar los comandos mencionados anteriormente nos muestra que la dirección del servidor es http://127.0.0.1:4000/, en dicha dirección podemos ver la página que nos a creado por defecto. 

![captura](capturas/jekyll1.png?raw=true "jekyll1")
- Página por defecto creada por Jekyll.

Para poder añadir informes de prácticas como este solo debemos añadir este fichero en el directorio **_posts** y añadirle las siguientes líneas:
````
---
layout: post
title:  "Handling events"
---
````
- **layout: post** es el estilo de la página.

# GitHub Pages

Con Github Pages podemos alojar directamente nuestro repositorio de GitHub. Simplemente subimos nuestro repositorio (*push*) y los cambios se verán online.
- 1º Iniciamos sesión en [Github](https://github.com/).
- 2º Creamos un nuevo repositorio y lo nombramos **"*(nombre de usuario)*.github.io"**.
- 3º Subimos nuestro repositorio donde trabajamos con jekyll anteriormente.
- 4º En GitHub dentro del repositorio vamos a *settings* y activamos GitHub Pages.

Ya deberiamos tener nuestra pagina creada con Jekyll subida a **"*(nombre de usuario)*.github.io"**. En este caso puedes encontrar este informe en [esta página web](https://alu0100898385.github.io).