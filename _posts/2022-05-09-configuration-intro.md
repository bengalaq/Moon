---
layout: post
title: "Configurando entorno + Capture The Ether (Hardhat, Metamask y varios)"
date: 2022-05-09
excerpt: "Todo lo que necesitás para trabajar comodx"
tag: [hardhat, metamask, configuracion, cte]
comments: true
---
<figure class="half">
	<img src="../assets/img/2021-11-14-configuration-intro/hardhat.png">
	<img src="../assets/img/2021-11-14-configuration-intro/vscode.png">
</figure>

<figure class="half">
  <img src="../assets/img/2021-11-14-configuration-intro/capturetheether.png">
  <img src="../assets/img/2021-11-14-configuration-intro/metamask.png">
</figure>

En este primer posteo, lo que haremos será:

1. ***Crear un proyecto con Hardhat***
2. ***Conectar nuestra cuenta de Metamask con Hardhat.***
3. ***Resolver un desafío de CTE con nuestro entorno ya listo.***

# 1. Hardhat

## *¿Qué es?*
Hardhat es un entorno de trabajo blockchain que nos facilitará el desarrollo de Pruebas de Concepto (En inglés PoC) mediante ayudas tales como una blockchain local, ejecución de tests, scripts y más. En otras palabras, una caja de herramientas para hacer nuestro trabajo más amigable.

## *Instalación*

Primero debemos inicializar nuestro proyecto con ```npm init```. Una vez creado, dentro del mismo ejecutamos.
~~~
npm i -D hardhat @nomiclabs/hardhat-ethers @nomiclabs/hardhat-waffle @types/mocha @types/chai @types/node chai ethereum-waffle ethers ts-node typescript
~~~
~~~
npm i dotenv
~~~

```i``` Refiere a Install.```-D``` Indica que es una dependencia de desarrollo (no es necesario que quien vaya a utilizar nuestra app las use).

Con Hardhat instalado, pasaremos a crear nuestro primer proyecto. Ejecutamos ```npx hardhat``` y se nos aparecerá un menú como este.

<img src="/imagenes/01-1.png">

Como verán, la opción que eligiremos será ***Create an advanced sample project that uses Typescript*** ¿Por qué? Siento que es mejor acostumbrarse a utilizar typescript desde el inicio. Es una ventaja que nos ayuda a preveer inconvenientes y solucionarlos fácilmente. Sin embargo, para no quitarte la sonrisa, podemos simplificar nuestro proyecto eliminando los siguientes archivos (tranquile, mantendré todo lo más sencillo posible)
```.eslintignore``````.eslintrc.js``````.npmignore``````.prettierignore``````.prettierrc``````.solhint.json``````.solhintignore```
```README.md```

Pese a ello, recomiendo a aquelles que sean o apunten a convertirse en desarrolladores, echar un vistazo a los archivos de arriba (solhint.json en particular). Podrían encontrarle mucha utilidad 😉 

¡Sigamos!

Solo nos restan 2 cosas para terminar nuestra configuración de Hardhat. Lo primero, será copiar este <a href="https://gist.github.com/bengalaq/76e8c9e2fea761fcb836b7781b9ec1a5">archivo de configuración</a> y reemplazarlo por el que teníamos (de nada amigue). La segunda y no menos complicada, será copiar este otro <a href="https://gist.github.com/bengalaq/17e24fca92e72492ca6df7df58073495">gist</a> y reemplazarlo por el ya existente *.env.example* (cambiando el nombre a .env solo). Sin embargo, nuestro .env tendrá información que vamos a querer mantener privada: API Key de Alchemy + Mnemonic. Así que expliquemos un poco ambas.

## *API KEY Alchemy*

Para no tener que hostear un nodo que se comunique con la blockchain que querramos, podemos utilizar nodos hosteados por otra persona o grupo. Dentro de estos seres de luz se encuentra <a href="https://www.alchemy.com/">Alchemy</a>, que nos provee un <a href="https://criptomundo.com/tipos-de-nodos-nodos-ligeros-nodos-completos-y-nodos-maestros/">nodo completo</a>, lo que será importante para tareas futuras.

Simplemente nos creamos una cuenta y en el panel principal, damos click en *CREATE APP*.

<img src="/imagenes/01-2.png">

Creamos la aplicación en nuestro panel.

<img src="/imagenes/01-3.png">

Una vez creada, damos click en *VIEW KEY* (bien a la derecha del renglón donde se muestra la nueva app creada). La copiamos y pegamos en nuestro *.env* en la sección correspondiente.

## *Mnemonic*

Cuando hablamos de mnemonic, nos estamos refiriendo a lo que probablemente hayas leído o escuchado como "frase semilla" o "seed phrase" si pintó el inglés. No es la gran ciencia, son solo una serie de palabras generadas de forma aleatoria para proteger una cuenta o dirección (por eso es importante no compartirlas con nadie). Con esta información nos alcanza. Mantengamos las cosas simples.

Para generar un mnemonic, podemos utilizar <a href="https://iancoleman.io/bip39/">ésta página</a>. Elegimos generar un mnemonic de 12 palabras, damos click en *GENERATE* y voilà, ¡Tenemos nuestro mnemonic! (Con rojo en la siguiente imagen)

<img src="/imagenes/01-4.png">

Lo colocamos en nuestro .env en la sección correspondiente y <a href="https://es.wikipedia.org/wiki/Jos%C3%A9_Mar%C3%ADa_Listorti">*listorti*</a>.

Finalmente, nunca está de más revisar que nuestro *.gitignore* incluya nuestro *.env*. Solo por si las moscas 😊

# 2. Conectar Metamask

Este paso es sumamente sencillo. Podés poner a calentar la pava para esos buenos <a href="https://www.filo.news/viral/Diccionario-argento-distintas-formas-de-llamar-al-mate-20191115-0015.html">*matemáticos*</a> mientras conectás al zorrito naranja de la siguiente manera.

## *Instalación*

Buscá y descargá la extensión de Metamask para tu navegador preferido. Una vez que la tengas, abrila y seleccioná la opción de *Importar cartera*. Dentro, pega el mnemonic que creaste en el paso anterior.

<img src="/imagenes/01-5.png">

Creá una contraseña y aceptá los términos y condiciones (no sin antes leerlos rufián). Ahora que tenemos Metamask, necesitamos no solo ver la red de Ethereum (también llamada mainnet), si no también las redes de prueba (testnet). Para ello hacemos click en la red (rectángulo verde) --> *Show/Hide* y activamos la opción *Show test networks*.

<img src="/imagenes/01-6.png">

Ahora, al dar click en el logo circular ubicado a la derecha de la red, podemos ir a la configuración de Metamask. Dentro de ella, hacemos el caminito de: *Redes* --> *Localhost* --> *Identificador de cadena* --> Cambiamos el 1337 por 31337

<img src="/imagenes/01-7.png">

Si me seguiste al pie de la letra, ya estás en condiciones de <a href="https://youtu.be/RsgkfC_QtgI">digievolucionar</a>. Abrí una consola a la altura de nuestro proyecto, y ejecutá el comando.

~~~
npx hardhat node
~~~

Si todo está en orden, al cambiar de *Red Ethereum* a *Localhost* en Metamask, deberías poder ver cómo tu cuenta coincide con la *Account 0* listada.

<img src="/imagenes/01-10.png">

<img src="/imagenes/01-11.png">

¿Qué indica esto? Que la conexión Hardhat-Metamask ya está hecha. ¡Felicitaciones! Andá por esos merecidos <a href="https://www.filo.news/viral/Diccionario-argento-distintas-formas-de-llamar-al-mate-20191115-0015.html">*materiales*</a>.

# 3. Hora de probar todo con CTE

## *¿Qué es Capture The Ether (CTE)?*

*Capture The Ether* es un juego que presenta una serie de desafíos relacionados a seguridad blockchain. Para jugar primero debemos ir a su <a href="https://capturetheether.com/">*página web*</a>. Dentro, se nos pedirá que conectemos nuestra cuenta Metamask y que cambiemos la red a *Ropsten*.

Tal vez no lo sepas, pero existen varias redes de prueba. Al momento de escribir este posteo, este juego utiliza la red de Ropsten, aunque también existen otras como *Rinkeby*, *Kovan* o *Goerli*, por ejemplo. Dicho esto, debo confesarte algo: necesitarás Ether dentro de esta red Ropsten para jugar. 

*Amado amigue, necesitas Ether. ¿Dónde conseguirlo? No puedo decirte. ¿Cómo continuarás? No puedes saberlo. Pero puedo decirte algo, cada vez que escuches al viento, susurrará Bengala y la rep...*
{: .notice}

¡Mentira! Todavía no insultes a ningún familiar mío. Si bien a veces es complicado conseguir Ether en redes de prueba, siempre se puede conseguir algo siguiendo instrucciones en algún *faucet* como <a href="https://faucet.paradigm.xyz/">Paradigm</a> o <a href="https://faucet.egorfine.com/">Egorfine</a>. Sin embargo, recomiendo MUCHO interactuar con gente y manguear algunos ETH. ¿Por qué? Ni idea, pero hablar con los demás siempre es bueno 😏

Ok. Ya tenés tus Ether mangueados, ¿Y ahora? Solo resta divertirse con el dinero ajeno. Hora de resolver un desafío en CTE.

**Atenti:** A continuación resolveremos el desafío número 3 del juego. Estaría buenísimo que pudieras resolver los otros 2 por tu propia cuenta. La página está en inglés pero son bastante cortitos (no dije fáciles ni difíciles). Confío en vos tanto como confía la gente en los términos y condiciones de Whatsapp.
{: .notice}

## *Nivel 3: Choose a nickname*

Al entrar al nivel, nos encontraremos con el siguiente código

{% highlight typescript %}
pragma solidity ^0.4.21;

// Relevant part of the CaptureTheEther contract.
contract CaptureTheEther {
    mapping (address => bytes32) public nicknameOf;

    function setNickname(bytes32 nickname) public {
        nicknameOf[msg.sender] = nickname;
    }
}

// Challenge contract. You don't need to do anything with this; it just verifies
// that you set a nickname for yourself.
contract NicknameChallenge {
    CaptureTheEther cte = CaptureTheEther(msg.sender);
    address player;

    // Your address gets passed in as a constructor parameter.
    function NicknameChallenge(address _player) public {
        player = _player;
    }

    // Check that the first character is not null.
    function isComplete() public view returns (bool) {
        return cte.nicknameOf(player)[0] != 0;
    }
}
{% endhighlight %}

Ahora:

1. Copiamos el código mencionado en un archivo Nickname.sol dentro del directorio *contracts*
2. Creamos un archivo nickname.ts dentro de la carpeta *test*. Este será el test que correremos para solucionar el desafío.

***ALERTA SPOILER:*** Si querés investigar por tu cuenta cómo utilizar el entorno para resolver los desafíos, este es un excelente momento para que lo hagas. A continuación voy a explicar cómo interactuar con todo lo que hemos configurado para resolver el desafío 3, por lo que indefectiblemente, tendré que mostrar la solución del problema.
{: .notice}

{% highlight typescript %}
// nickname.ts

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract } from "ethers";

let nicknameContract: Contract;

beforeEach(async ()=>{
  const nicknameFactory = await ethers.getContractFactory("CaptureTheEther");
  nicknameContract = await nicknameFactory.attach("0x71c46Ed333C35e4E6c62D32dc7C8F00D125b4fee"); // Dirección del contrato CaptureTheEther
})

describe("Nickname", async ()=>{
  it("Resuelve el challenge Warmup - NicknameChallenge", async ()=>{
    const tx = await nicknameContract.setNickname(ethers.utils.formatBytes32String("TU-NOMBRE-DE-ESTRELLA-DE-ROCK")); // Por defecto ethers invoca la función desde la primer account en ethers.getSigners();
    const txHash = tx.hash;
    console.log(`El hash de la transacción es ${txHash}`); // Para revisar la transacción en caso que demore un poco.

    expect(txHash).to.not.be.undefined;
  })
})
{% endhighlight %}

## *Desglocémoslo de a poco*


{% highlight typescript %}
// nickname.ts

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract } from "ethers";
{% endhighlight %}

* Para los test siempre (o casi siempre) utilizaremos <a href="https://docs.ethers.io/ethers.js/v3.0/html/">ethers js</a> para conectar con la red Ethereum, <a href="https://mochajs.org/">mocha</a> como framework para tests y <a href="https://www.chaijs.com/">chai</a> como librería de aserción.

{% highlight typescript %}
let nicknameContract: Contract;
{% endhighlight %}

* Arriba de todo, ubicaremos las variables globales que vayamos a utilizar, indicando su tipo. En este caso tendremos un contrato tipo *Contract* (no me digas 😋).

{% highlight typescript %}
beforeEach(async ()=>{
  const nicknameFactory = await ethers.getContractFactory("CaptureTheEther");
  nicknameContract = await nicknameFactory.attach("0x71c46Ed333C35e4E6c62D32dc7C8F00D125b4fee"); // Dirección del contrato CaptureTheEther
})
{% endhighlight %}

* La sección ***beforeEach*** es un bloque de código que se ejecutará antes de cada test. Nos servirá para generar un estado por defecto o precondición. En esta ocasión, no es realmente necesario debido a que solo tenemos una prueba, pero no es un mal momento para saber cómo armar una buena estructura. Acá, la precondición que vamos a generar es la de tener a nuestra disposición el contrato CaptureTheEther para poder invocar su función de *setNickname*, y para ello utilizaremos el plugin de hardhat con ethers js. Con él, podemos usar el método *getContractFactory*, que tomará el contrato que tenga el nombre indicado en su argumento (en este caso "CaptureTheEther") y nos creará un objeto ContractFactory. Este, nos permite utilizar el método *attach*, al cual podemos pasarle la dirección del contrato que queremos traernos. Genial, ¿No? (Si no lo decís vos, lo digo yo: Gracias hardhat-ethers).

{% highlight typescript %}
describe("Nickname", async ()=>{
  it("Resuelve el challenge Warmup - NicknameChallenge", async ()=>{
    const tx = await nicknameContract.setNickname(ethers.utils.formatBytes32String("TU-NOMBRE-DE-ESTRELLA-DE-ROCK")); // Por defecto ethers invoca la función desde la primer account en ethers.getSigners();
    const txHash = tx.hash;
    console.log(`El hash de la transacción es ${txHash}`); // Para revisar la transacción en caso que demore un poco.

    expect(txHash).to.not.be.undefined;
  })
})
{% endhighlight %}

* Parte final.
  * ***describe:*** Sección que contiene uno o más tests. Recibe como primer parámetro un nombre representativo de lo que se va a testear, y como segundo la función que contendrá los tests a correr. Es una buena idea que este segundo parámetro sea una función asíncrona (le clavamos el async adelante).
  * ***it:*** El test en sí. Recibe como primer parámetro el nombre del test y como segundo el bloque de código a probar.

* Invocando función *setNickname*.
  * ***¿Desde qué cuenta se llama a la función?*** Bueno, ¿Te acordás cuando al correr ```npx hardhat node``` Hardhat nos mostró como 20 cuentas? Ethers va a usar la primera de todas, es decir, nuestra *Account 0*. En otro momento, tal vez necesitemos invocar funciones de contratos desde otras cuentas, pero no te preocupes, eso es problema de tu yo del futuro.
  * ***¿Await?*** Las transacciones no ocurren al instante. Por eso es necesario esperar y mantener un orden. Paciencia muchache...
  * ***¿formatBytes32String?*** Si te fijás, en el código propuesto por el juego, la función *setNickname* recibe por parámetro un nickname de tipo *bytes32*. Así como muchos lenguajes, solidity también tiene sus tipos (<a href="https://www.youtube.com/watch?v=k1ePkrGLrlA">esto no es javascript papi</a>). Con el tiempo aprenderás todos, no son muchos pero hay algunas trampitas.
  * ***expect:*** Necesitamos tener la certeza de que nuestro test logró su cometido. Con expect podemos ver si nuestro resultado es el esperado.


¡Listo, estamos a punto de terminar! Ya tenemos todo lo necesario para completar el nivel, solo restan 3 pasitos más: ***1) Activar el nivel 2) Correr nuestro test localmente*** y ***3) Correr el test en la red de Ropsten.***

## *Activar el nivel*

Solo basta con entrar al nivel en la página del juego, dar click al botón de *Begin Challenge* y esperar que termine la creación del nivel (metamask dará un aviso de transacción confirmada)

## *Correr nuestro test localmente*

¿Ves la dirección donde está el contrato de tu desafío? La marqué en verde.

<img src="/imagenes/01-12.png">

Si le dás click, algo maravilloso pasará: entrarás al <a href="https://ethereum.org/es/developers/docs/data-and-analytics/block-explorers/">explorador de bloques</a> de la red Ropsten. Los exploradores de bloques tienen muchas utilidades. Nosotros simplemente queremos conocer el bloque en el que se creó nuestro desafío, lo cual podemos observar al entrar al apartado ***Internal Txns***

<img src="/imagenes/01-13.png">

Lo copiamos y lo pegamos como valor de *blockNumber* en nuestro hardhat.config.ts. ¿Qué ganamos con esto? Principalmente acelerar la velocidad hasta 20 veces más. Hardhat utiliza este bloque como "bloque fijo" para poder crear una especie de memoria caché en disco con la información referida a la blockchain. Para ello le es imprescindible contar con un nodo completo como el elegimos anteriormente de Alchemy (todo va conectándose poco a poco, ¿viste?).

Ya con el blockNumber establecido, corremos nuevamente ```npx hardhat node```, mientras que en otra consola, ejecutamos el test con

~~~
npx hardhat test .\test\nickname.ts --network localhost
~~~

```--network``` Sirve para especificar la red en la que queremos correr nuestro test.

Deberíamos observar nuestro test funcionando correctamente.

<img src="/imagenes/01-14.png">

Dato de color, si revisamos la consola en la que levantamos la blockchain local con hardhat (sí, esa del ```npx hardhat node```), podemos observar cómo llegó la transacción y se "minó" sin inconvenientes.

<img src="/imagenes/01-15.png">

## *Correr el test en la red de Ropsten*

No es nada distinto a lo que hicimos anteriormente, solo hay que cambiar de objetivo. Como ya tenemos configurada nuestra red Ropsten en hardhat.config.ts, lo hacemos de la siguiente forma

~~~
npx hardhat test .\test\nickname.ts --network ropsten
~~~

Finalizado el test, puedes dar click en el botón de *Check solution* y disfrutar de tus 200 puntos y un nombre que quedará en la historia.

<img src="/imagenes/01-16.png">


¡Y eso fue todo! Las configuraciones siempre son tediosas (o al menos para mí), así que si llegaste hasta acá, quiero que sepas que acabás de aprender M U C H Í S I M O. Algunos temas no fueron explicados al detalle simplemente por ser un post enfocado en "configurar para comenzar", así que si te gusta entender a fondo cómo funcionan las cosas, espero que los siguientes posteos sean de tu agrado.

Que tengas buen día, no te alteres :)


*BengalaQ*




