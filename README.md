# JS43MoviendoelProyectoCriptomonedasaAsyncAwait
JS 43. PROYECTO Moviendo el Proyecto de Criptomonedas a Async Await

```javascript
// 39. PROYECTO Cotizador de Criptomonedas
// Dirección web https://min-api.cryptocompare.com/documentation
// esta API no es obligatorio crear un usuario
// fsym=BTC&tsyms=USD,JPY,EUR 
// fsym: cod de cryptomoneda 
// tsyms: cod páis ej: chile cl
// limit=10 extrae un listado de 10 criptomonedas, sirve para combobox
// Lista: abreviaturas de países de 3 letras
// https://laendercode.net/es/3-letter-list.html

// Selectores
// Selector para el <select> <option> de criptomonedas
const criptomonedasSelect = document.querySelector('#criptomonedas');
// Selector para el <select> <option> de moneda
const monedaSelect = document.querySelector('#moneda');

// Selector que al dar submit al formulario poder cotizar y obtener un precio
const formulario = document.querySelector('#formulario');

// Selector para imprimir HTML resultado de la API
const resultado = document.querySelector('#resultado');


// Objeto global vacío
const objBusqueda = {
    // Estos dos se hirán llenando conforme el usuario llene algo
    moneda: '',
    criptomoneda: ''
}

// Crear un Promise. criptomonedas(obtiene una serie de cripto)
// Promise que tiene solo un resolve, es decir, se ejecutará solo
// en caso de que resuelva
const obtenerCriptomonedas =  criptomonedas => new Promise(resolve => {
    resolve(criptomonedas);
});

// Una vez que el documento este listo
document.addEventListener('DOMContentLoaded', () => {
    consultarCriptomonedas();
    formulario.addEventListener('submit', submitFormulario);
    // input select de criptomoneda. 'Mientras esté cambiando'
    criptomonedasSelect.addEventListener('change', leerValor);
    monedaSelect.addEventListener('change', leerValor);
})

async function consultarCriptomonedas() {
    const url = 'https://min-api.cryptocompare.com/data/top/mktcapfull?limit=10&tsym=USD';

    // fetch(url)
    //     .then( respuesta => respuesta.json() )        
    //     .then( resultado => 
            // Crear un Promise que solo resuelva cuando la cripto se hayan descargado
            // console.log(resultado.Data)
            // console.log(resultado)
            // obtenerCriptomonedas(resultado.Data))
        // Resolvemos la criptomoneda
        //.then( criptomonedas => //console.log(criptomonedas)
        // selectCriptomonedas(criptomonedas))

        try {
            const respuesta = await fetch(url);
            const resultado = await respuesta.json();
            const criptomonedas = await obtenerCriptomonedas(resultado.Data);
            selectCriptomonedas(criptomonedas);

        } catch (error) {
            console.log(error);
        }
}

// función que toma la criptomonedas desde el fetch
function selectCriptomonedas(criptomonedas){
    // el parametro criptomonedas es un Array
    criptomonedas.forEach(cripto => {
        // iterar los datos 
        console.log(cripto);
        // destructuring
        const { FullName, Name } = cripto.CoinInfo;

        // Crear <option> para el <select> usando Scripting
        const option = document.createElement('option');
        option.value = Name; //Cód de 3 dígitos
        option.textContent = FullName;

        criptomonedasSelect.appendChild(option);
    });    
}

// Llenar ObjBusqueda 3. Leer la moneda y criptomoneda seleccionada
function leerValor(e){    
    // Cargar select cripto a el objBusqueda
    objBusqueda[e.target.name] = e.target.value;
    console.log(objBusqueda);
}

function submitFormulario(e){
    e.preventDefault();
    // Destructuring al objBusqueda
    const { moneda, criptomoneda } = objBusqueda; 

    // Validar
    if (moneda === '' || criptomoneda === '') {
        mostrarAlerta('Ambos campos son obligatorios');
        return;
    }

    // Consultar la API con los resultados 5. Consultando la API
    consultarAPI();
}

// 4. Validación del Formulario
function mostrarAlerta(msg){
    console.log(msg);
    // para que aparezca mensaje solo una vez
    const existeError = document.querySelector('.error');
    if(!existeError) {
        const divMensaje = document.createElement('div');
        divMensaje.classList.add('error');
        // Mensaje de error
        divMensaje.textContent = msg;
        // Insertar en el DOM
        formulario.appendChild(divMensaje);
    
        setTimeout(() => {
            divMensaje.remove();
        }, 3000);
    }
   
}

// No tomará valores, sino que ocupará los valores globales objBusqueda
async function consultarAPI() {
    // ya pasó la validación y tiene datos
    //  Extrear los valores
    const { moneda, criptomoneda } = objBusqueda;
    // ir a la API en la página https://min-api.cryptocompare.com/

    // Single Symbol Price. copiar la llamada
    // Esta url solo mostrará el valor de la cripto en peso
    // url = `https://min-api.cryptocompare.com/data/price?fsym=${criptomoneda}&tsyms=${moneda}`;

    // elegir endPoint Multiple Symbols Full Data, que entrega más info
    url = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${criptomoneda}&tsyms=${moneda}`;

    // antes de dar fetch mostrar el spinner
    mostrarSpinner();


    // fetch(url)
    //     .then(respuesta => respuesta.json())
    //     .then(cotizacion => {
    //         //  console.log(cotizacion);//Para ver los objetos
            // BTC: {USD: ETH: {MXN: Notar que los objetos de la API son dinámicos
            // Entonces se debe agregar variables dinámicamente tambien
            // console.log(cotizacion.DISPLAY);


            // Agregando valores dinámicamente
            // console.log(cotizacion.DISPLAY[criptomoneda][moneda]);
        //     mostrarCotizacionHTML(cotizacion.DISPLAY[criptomoneda][moneda]);
        // })

    try {
        const respuesta = await fetch(url);
        const cotizacion = await respuesta.json();
        mostrarCotizacionHTML(cotizacion.DISPLAY[criptomoneda][moneda]);
    } catch (error) {
        console.log(error);
    }
}

// 6. Imprimiendo el resto de la Información
function mostrarCotizacionHTML(cotizacion) {
    // console.log(cotizacion);
    limpiarHTML();// para que no se llene de información hacia abajo
    // destructuring a la API de la página web
    const { PRICE, HIGHDAY, LOWDAY, CHANGEPCT24HOUR, LASTUPDATE } = cotizacion;

    const precio = document.createElement('p');
    precio.classList.add('precio');
    precio.innerHTML = `El Precio es: <span>${PRICE}</span>`;

    const precioAlto = document.createElement('p');   
    precioAlto.innerHTML = `Precio más alto del día <span>${HIGHDAY}</span>`;

    const precioBajo = document.createElement('p');   
    precioBajo.innerHTML = `Precio más bajo del día <span>${LOWDAY}</span>`;

    const ultimasHoras = document.createElement('p');   
    ultimasHoras.innerHTML = `Variación últimas 24 horas <span>${CHANGEPCT24HOUR}%</span>`;

    const ultimaActualizacion = document.createElement('p');   
    ultimaActualizacion.innerHTML = `Última actualización <span>${LASTUPDATE}</span>`;


    resultado.appendChild(precio);
    resultado.appendChild(precioAlto);
    resultado.appendChild(precioBajo);
    resultado.appendChild(ultimasHoras);
    resultado.appendChild(ultimaActualizacion);
}

function limpiarHTML() {
    while (resultado.firstChild) {
        resultado.removeChild(resultado.firstChild);
    }
}

function mostrarSpinner() {
    limpiarHTML();
    const spinner = document.createElement('div');
    spinner.classList.add('spinner');

    spinner.innerHTML = `
        <div class="bounce1"></div>
        <div class="bounce2"></div>
        <div class="bounce3"></div>
    `;
    resultado.appendChild(spinner);
}
```
