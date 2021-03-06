Esta guía te enseñará cómo construir una aplicación simple usando Ember desde cero.

Daremos estos pasos:

1. Instalación de Ember.
2. Creación de una nueva aplicación.
3. Definir una ruta.
4. Escribir un componente.
5. Construir la aplicación para ser desplegada en producción.

## Instalar Ember

Ember se puede instalar mediante un solo comando usando npm, el gestor de paquetes de Node.js. Escribe esto en tu terminal:

```sh
npm install -g ember-cli
```

Don't have npm? [Learn how to install Node.js and npm here](https://docs.npmjs.com/getting-started/installing-node). For a full list of dependencies necessary for an Ember CLI project, consult our [Installing Ember](../../getting-started/) guide.

## Crear una nueva aplicación

Una vez que has instalado Ember CLI mediante npm, ya puedes ejecutar el nuevo comando `ember` en la terminal. En concreto y para empezar, puedes utilizar el comando `ember new` para crear una nueva aplicación.

```sh
ember new ember-quickstart
```

Este comando creará un nuevo directorio llamado `ember-quickstart` y configurará una nueva aplicación de Ember en su interior. Por defecto, esta nueva aplicación permitirá:

* Utilizar un servidor de desarrollo.
* Compilar las plantillas.
* Minificar los ficheros de JavaScript y CSS.
* Usar las nuevas características de ES2015 gracias a Babel.

Proporcionando todo lo necesario para construir aplicaciones web en un paquete integrado, Ember facilita enormemente el inicio de nuevos proyectos.

Comprobemos que todo esté funcionando correctamente. Accedamos al directorio de la aplicación mediante `cd ember-quickstart` y arranquemos el servidor de desarrollo escribiendo:

```sh
cd ember-quickstart
ember server
```

Después de unos segundos, deberías leer en el terminal algo parecido a esto:

```text
Livereload server on http://localhost:49152
Serving on http://localhost:4200/
```

(Para detener el servidor en cualquier momento, haz Ctrl-C en el terminal.)

Abre [`http://localhost:4200`](http://localhost:4200) en el navegador web de tu preferencia. Deberías ver unicamente una pagina de bienvenida de Ember. ¡Felicidades! Acabas de crear y arrancar tu primer aplicación Ember.

Creemos una nueva template (plantilla) usando el comando `ember generate`.

```sh
ember generate template application
```

La template `application` simpre esta en la pantalla mientra el usuario tenga tu aplicación cargada. En tu editor, abre `app/templates/application.hbs` y agrega lo siguiente:

```app/templates/application.hbs 

# PeopleTracker

{{outlet}}

    <br />Observe que Ember detecta el nuevo archivo y automáticamente recarga la pagina para ti en segundo plano. You should see that the welcome page
    has been replaced by "PeopleTracker". You also added an `{{outlet}}` to this page, which means that any nested route will be rendered in that place.
    
    ## Define a Route
    
    Let's build an application that shows a list of scientists. To do that,
    the first step is to create a route. For now, you can think of routes as
    being the different pages that make up your application.
    
    Ember comes with _generators_ that automate the boilerplate code for
    common tasks. To generate a route, type this in your terminal:
    
    ```sh
    ember generate route scientists
    

Verás una salida como esta:

```text
installing route   
create app/routes/scientists.js  
create app/templates/scientists.hbs 
updating router   
add route scientists 
installing route-test
create tests/unit/routes/scientists-test.js
```

Se trata de Ember diciendo que ha creado:

1. Una plantilla que aparecerá cuando el usuario visite `/scientists`.
2. Un objeto de tipo `Route` que obtendrá el modelo utilizado por esa plantilla.
3. Una entrada en el enrutador de la aplicación (ubicado en `app/router.js`).
4. Una test unitario para esta ruta.

Abre la plantilla recién creada en `app/templates/scientists.hbs` y agrega el siguiente código HTML:

'''app/templates/scientists.hbs 

## Lista de científicos

    <br />En tu navegador web, abre [`http://localhost:4200/scientists`](http://localhost:4200/scientists). You should
    see the `<h2>` you put in the `scientists.hbs` template, right below the
    `<h1>` from our `application.hbs` template.
    
    Now that we've got the `scientists` template rendering, let's give it some
    data to render. Podemos logrando al especificar un _model_ para esa route, y podemos especificar un modelo editando `app/routes/scientists.js`.
    
    We'll take the code created for us by the generator and add a `model()`
    method to the `Route`:
    
    ```app/routes/scientists.js{+4,+5,+6}
    import Ember from 'ember';
    
    export default Ember.Route.extend({
      model() {
        return ['Marie Curie', 'Mae Jemison', 'Albert Hofmann'];
      }
    });
    

(Este ejemplo de código utiliza las últimas características de JavaScript, algunas de las cuales pueden no serte familiares aún. Aprende más sobre ellas con este [resumen de las nuevas características de JavaScript](https://ponyfoo.com/articles/es6).)

En el método `model()` de la ruta retornamos la información que quieres tener disponible en la plantilla. Si necesitas obtener datos asincrónicamente, el método `model()` soporta cualquier librería que utilice [JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

Ahora le indicaremos a Ember cómo convertir esa matriz de cadenas en HTML. Abre la plantilla `scientists` y añade el código Handlebars necesario para recorrer el array y mostrarlo:

```app/templates/scientists.hbs{+3,+4,+5,+6,+7} 

## Lista de científicos

{{#each model as |scientist|}} 

* {{scientist}} {{/each}} 

    <br />Aqui usamos el `each` helper para iterar sobre cada elemento en el array que le proveímos al `model()` hook y lo imprimimos dentro de un elemento `<li>`.
    
    ## Create a UI Component
    
    As your application grows and you notice you are sharing UI elements
    between multiple pages (or using them multiple times on the same page),
    Ember makes it easy to refactor your templates into reusable components.
    
    Let's create a `people-list` component that we can use
    in multiple places to show a list of people.
    
    As usual, there's a generator that makes this easy for us. Make a new
    component by typing:
    
    ```sh
    ember generate component people-list
    

Copia y pega la plantilla de `scientists` en la plantilla del componente `people-list` y modifícala para que se vea de la siguiente forma:

```app/templates/components/people-list.hbs 

## {{title}}

{{#each people as |person|}} 

* {{person}} {{/each}} 

    <br />Puedes notar que hemos cambiado el título estático ("List of Scientists") para que sea una propiedad dinámica (`{{title}}`). También hemos cambiado el nombre de `scientist` a uno mas genérico `person`, disminuyendo el acoplamiento de nuestro componente a donde este se use.
    
    Guarde este template y vuelva a abrir la template `scientists`. Reemplace nuestro viejo código por nuestra nueva versión que utiliza componentes. Los components (componentes) lucen como etiquetas HTML pero en vez de usar signos de menor y mayor que (`<tag>`) estos usan doble llaves (`{{component}}`). 
    1. Que titulo usar, vía el atributo `title`.
    2. Que array de personas usar, vía el atributo `people`. We'll
       provide this route's `model` as the list of people.
    
    ```app/templates/scientists.hbs
    {-1,-2,-3,-4,-5,-6,-7,+8} 
    <h2>List of Scientists</h2>
     <ul>  
     {{#each model as |scientist|}} 
        <li>{{scientist}}</li>
     {{/each}}
     </ul>
     {{people-list title="List of Scientists" people=model}}    
    

Regresa al navegador y verás que la interfaz de usuario se ve idéntica. La única diferencia es que ahora hemos componentizado nuestra lista en una versión que es más reusable y más fácil de mantener.

Puedes ver esto en acción si creas una nueva ruta que muestra una lista diferente de personas. Como ejercicio para el lector, puedes tratar de crear una route (ruta) `programmers` que muestre una lista de programadores famosos. Reusando el component (componente) `people-list`, puedes hacerlo prácticamente sin escribir más código.

## Click Events

So far, your application is listing data, but there is no way for the user to interact with the information. In web applications, you often want to listen for user events like clicks or hovers. Ember makes this easy to do. First add an `action` helper to the `li` in your `people-list` component.

```app/templates/components/people-list.hbs{-5,+6} 

## {{title}}

{{#each people as |person|}} 

* {{person}}<li {{action "showperson" person}}>{{person}}</li> {{/each}} 

    <br />The `action` helper allows you to add event listeners to elements and call named functions.
    By default, the `action` helper adds a `click` event listener,
    but it can be used to listen for any element event.
    Now, when the `li` element is clicked a `showPerson` function will be called
    from the `actions` object in the `people-list` component.
    Think of this like calling `this.actions.showPerson(person)` from our template.
    
    To handle this function call you need to modify the `people-list` component file
    to add the function to be called.
    In the component, add an `actions` object with a `showPerson` function that
    alerts the first argument.
    
    ```app/components/people-list.js{+4,+5,+6,+7,+8}
    import Ember from 'ember';
    
    export default Ember.Component.extend({
      actions: {
        showPerson(person) {
          alert(person);
        }
      }
    });
    

Now in the browser when a scientist's name is clicked, this function is called and the person's name is alerted.

## Building For Production

Now that we've written our application and verified that it works in development, it's time to get it ready to deploy to our users. To do so, run the following command:

```sh
ember build --env production
```

The `build` command packages up all of the assets that make up your application&mdash;JavaScript, templates, CSS, web fonts, images, and more.

In this case, we told Ember to build for the production environment via the `--env` flag. This creates an optimized bundle that's ready to upload to your web host. Once the build finishes, you'll find all of the concatenated and minified assets in your application's `dist/` directory.

The Ember community values collaboration and building common tools that everyone relies on. If you're interested in deploying your app to production in a fast and reliable way, check out the [Ember CLI Deploy](http://ember-cli-deploy.com/) addon.

If you deploy your application to an Apache web server, first create a new virtual host for the application. To make sure all routes are handled by index.html, add the following directive to the application's virtual host configuration

    FallbackResource index.html