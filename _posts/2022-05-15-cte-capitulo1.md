---
layout: post
title: "Capture The Ether - Parte 1"
date: 2022-05-15
excerpt: "Capítulo 1 de la serie de desafíos en CTE"
tag: [cte, challenge, resolución]
comments: true
---

<img src="/imagenes/02-Titulo.png">
<br>
<div style="text-align:center;">
Antes de comenzar, quería aprovechar este breve párrafo para aclarar un poco la filosofía de este blog. Pese a estar indefectiblemente enseñando la solución a los problemas, <b>NO ES LO IDEAL EN ABSOLUTO</b> recurrir a esta explicación sin antes intentarlo por cuenta propia. No importa cuánto demores, muchos de estos desafíos enseñan, más allá del conocimiento, a ser pacientes con uno mismo y adquirir esa habilidad de <b><i><a href="https://www.youtube.com/watch?v=C0JbAJmf9eI&t=9s">levantarse y seguir intentando</a></i></b>. Por ello, la metodología de <i>Lectura-Ideas-Test-Explicación-Conclusión</i> se enseñará solo dando click a los botones de "Revelar", con lo que vas a poder obtener pistas de una forma más atómica. Dicho esto, si no entendiste el problema del todo o querés ver cómo lo resolvió otra persona, ¡Bienvenide! Estás en el lugar que soñabas 😎.

<p>En este primer capítulo intentaré explicar los desafíos:</p>

<span><b>1. </b>Guess the number</span>
<br>
<span><b>2. </b>Guess the secret number</span>
<br>
<span><b>3. </b>Guess the random number</span>
<h1 style="color:#089990; margin-bottom: 0px;">1. Guess the number</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Estoy pensando en un número. Todo lo que tienes que hacer es adivinarlo."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract GuessTheNumberChallenge {
    uint8 answer = 42;

    function GuessTheNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura1" onclick="revelarSeccion(document.getElementById('lectura1'), 'botonRevelarLectura1')">Revelar</button>
<div id="lectura1" style="display:none"><ul><li>El número 42 aparece a simple vista.</li><li>La función <i><b>guess</b></i> compara un número que le pasemos con el que posea la variable <i><b>answer</b></i>.</li></ul></div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones1" onclick="revelarSeccion(document.getElementById('ideasSoluciones1'), 'botonRevelarIdeasSoluciones1')">Revelar</button>
<div id="ideasSoluciones1" style="display:none"><ul><li>Llamar a la función <i><b>guess</b></i> con el valor 42, el cual es el almacenado en la variable <i><b>answer</b></i>.</li></ul></div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion1" onclick="revelarSeccion(document.getElementById('explicacion1'), 'botonRevelarExplicacion1')">Revelar</button>
<div id="explicacion1" style="display: none">El número que debemos adivinar está practicamente frente a nuestras narices. No deberían subestimarnos tanto, ¿No creés?</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest1" onclick="revelarSeccion(document.getElementById('testAEjecutar1'), 'botonRevelarTest1')">Revelar</button>
<div id="testAEjecutar1" style="display:none">
{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract, Signer } from "ethers";

let lotteryContract: Contract;

beforeEach(async () => {
  const lotteryFactory = await ethers.getContractFactory(
    "GuessTheNumberChallenge"
  );
  lotteryContract = lotteryFactory.attach(
    "DIRECCION_DEL_CHALLENGE"
  );
});

describe("Guess The Number", async () => {
  it("Resuelve el Lottery challenge - Guess The Number", async () => {
    const tx = await lotteryContract.guess(42, {
      value: ethers.utils.parseEther("1"),
      gasLimit: 1e5,
    });
    const txHash = await tx.hash;
    console.log(`El Hash de la transaccion es ${txHash}`);

    expect(txHash).not.to.be.undefined;
  });
});

{% endhighlight %}
</div>

### Conclusión:
Leer código es SÚPER importante. Así, muchas veces encontraremos cosas que los desarrolladores olvidaron borrar o simplemente pensaron que no habría problema alguno en dejarlo ahí, a nuestro alcance. Conoce a tu enemigo y esas cosas, vos me entendés...
<br>
<br>

<hr>
<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">2. Guess the secret number</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Esta vez solo guardé el hash del número. Buena suerte reverseando el hash criptográfico!"</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract GuessTheSecretNumberChallenge {
    bytes32 answerHash = 0xdb81b4d58595fbbbb592d3661a34cdca14d7ab379441400cbfa1b78bc447c365;

    function GuessTheSecretNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }
    
    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (keccak256(n) == answerHash) {
            msg.sender.transfer(2 ether);
        }
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura2" onclick="revelarSeccion(document.getElementById('lectura2'), 'botonRevelarLectura2')">Revelar</button>
<div id="lectura2" style="display:none">
  <ul>
    <li style="white-space:nowrap;">
      El hash criptográfico (answerHash) es <b>0xdb81b4d58595fbbbb592d3661a34cdca14d7ab379441400cbfa1b78bc447c365</b>.
    </li>
    <li>
      La función <i><b>guess</b></i> en esta ocasión compara el <i><b>keccak256</b></i> (vaya uno a saber qué es eso) de un número que le pasemos, con el <i><b>answerHash</b></i>.
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones2" onclick="revelarSeccion(document.getElementById('ideasSoluciones2'), 'botonRevelarIdeasSoluciones2')">Revelar</button>
<div id="ideasSoluciones2" style="display:none">
  <ul>
    <li>
      Averiguar qué es <i><b>keccak256</b></i>.
    </li>
    <li>
      Encontrar un "n" que pasado por parámetro a ese <i><b>keccak256</b></i> raro, logre generar algo igual al <i><b>answerHash</b></i>.
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion2" onclick="revelarSeccion(document.getElementById('explicacion2'), 'botonRevelarExplicacion2')">Revelar</button>
<div id="explicacion2" style="display: none">
  <h4><i><u>Keccak256</u></i></h4>
  <i><b>Keccak256</b></i> es una función hash. Las funciones hash, son funciones que toman una entrada, y generan un resultado de tal forma que la probabilidad de poder crear ese mismo resultado con otra entrada distinta, sea muy (muy, muy, muy, extremadamente muy) baja. En caso que ésta función hash pueda predefinir su conjunto de entrada, la misma es llamada "función hash perfecta", o lo que matemáticamente se le conoce como <i><b>función inyectiva</b></i> (al valor 1 solo le pertenece el valor D, tal y como muestra la imagen).
  <br>

  <img src="/imagenes/02-1.png">

  Con esto nos va a alcanzar. Quedan muchas interrogantes sobre esta función (obviamente, no esperabas que semejante belleza terminara de entenderse en 5 renglones, ¿O sí?), pero las veremos más a futuro. De momento, estamos sobrados.

  <h4><i><u>Encontrar "n"</u></i></h4>
  Sabemos que es un número por el tipo de dato que debe recibir "guess" (uint8), un número entero positivo que se pueda formar con 8 bits.
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest2" onclick="revelarSeccion(document.getElementById('testAEjecutar2'), 'botonRevelarTest2')">Revelar</button>
<div id="testAEjecutar2" style="display:none">
{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract } from "ethers";
import internal from "stream";

let guessTheSecretNumberContract: Contract;
let secretNumber;
let coincidencia: boolean;
let answerHash = "0xdb81b4d58595fbbbb592d3661a34cdca14d7ab379441400cbfa1b78bc447c365";

function averiguarValorPorKeccak() {
  for (let numero = 0; !coincidencia; numero++) {
    let numeroKeccakeado = ethers.utils.keccak256([numero]);
    if (numeroKeccakeado == answerHash) {
      coincidencia = true;
      console.log(`MATCH!! [✔] ---------------------> Numero: ${numero}`);
      return numero;
    } else {
      console.log(`Intento fallido [X] ---------------------> Numero: ${numero}`);
    }
  }
}

beforeEach(async () => {
  coincidencia = false;
  const guessTheSecretNumberFactory = await ethers.getContractFactory(
    "GuessTheSecretNumberChallenge"
  );
  guessTheSecretNumberContract = guessTheSecretNumberFactory.attach(
    "DIRECCION_DEL_CHALLENGE"
  );
});

describe("Guess The Secret Number", async () => {
  it("Resuelve el Lottery challenge - Guess The Secret Number", async () => {
    secretNumber = averiguarValorPorKeccak();
    console.log(`El numero encontrado por la función es: ${secretNumber}`);
    const tx = await guessTheSecretNumberContract.guess(secretNumber, {
      value: ethers.utils.parseEther("1"),
      gasLimit: 1e5,
    });
    expect(tx.hash).not.to.be.undefined;
  });
});

{% endhighlight %}
</div>

### Conclusión:
Siempre es interesante recordar que un atacante dispone de 2 cosas: tiempo y recursos infinitos. Pretender que una entrada "n" es imposible de hallar, cuando se comparte públicamente en la blockchain la lógica que aplicamos, es subestimar esos 2 elementos mencionados anteriormente.
<br>
<br>

<hr>
<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">3. Guess the random number</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Esta vez el número es generado basándose en un par de fuentes bastante aleatorias."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract GuessTheRandomNumberChallenge {
    uint8 answer;

    function GuessTheRandomNumberChallenge() public payable {
        require(msg.value == 1 ether);
        answer = uint8(keccak256(block.blockhash(block.number - 1), now));
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura3" onclick="revelarSeccion(document.getElementById('lectura3'), 'botonRevelarLectura3')">Revelar</button>
<div id="lectura3" style="display:none">
  <ul>
    <li style="white-space:nowrap;">
      La variable <i>answer</i> puede valer entre 0 y 255 (por ser de tipo <i>uint8</i>).
    </li>
    <li>
      Se utiliza nuevamente la función keccak.
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones3" onclick="revelarSeccion(document.getElementById('ideasSoluciones3'), 'botonRevelarIdeasSoluciones3')">Revelar</button>
<div id="ideasSoluciones3" style="display:none">
  <ul>
    <li>
      Si solo existen 256 valores posibles para la variable <i>answer</i>, ¿Podríamos probar una por una?.
    </li>
    <li>
      Si todo código y estado de un contrato es público en la blockchain, debe existir alguna forma de verlo, ¿No?.
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion3" onclick="revelarSeccion(document.getElementById('explicacion3'), 'botonRevelarExplicacion3')">Revelar</button>
<div id="explicacion3" style="display: none">
  <h4><i><u>Intentos uno por uno de los 256 valores</u></i></h4>
  Si revisamos la función <i><b>guess</b></i> veremos que inicialmente se necesita 1 ether para invocarla, lo cual resulta un inconveniente (en el peor de los casos gastaremos 256 ether). Además, pueden existir herramientas de monitoreo que detecten nuestros numerosos intentos.
  <br>

  <h4><i><u>Buscar valor público de <i>answer</i></u></i></h4>

  Toda la información en la blockchain, aún las variables privadas de un Smart Contract, son públicas. Tal vez no son posibles de leer desde un contrato, pero con librerías como ethers js o Web3 sí es posible, tal y como se demuestra en este ejercicio. Lo importante a entender acá es que el llamado <b>storage</b>, lugar donde se almacena el estado del contrato, es público y podemos acceder a él tanto mediante código, como a través de un exploradores de bloques como etherscan.
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest3" onclick="revelarSeccion(document.getElementById('testAEjecutar3'), 'botonRevelarTest3')">Revelar</button>
<div id="testAEjecutar3" style="display:none">
{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract, BigNumber } from "ethers";

let randomNumberContract: Contract;
const contractAddress: string = "DIRECCION_DEL_CHALLENGE";

beforeEach(async()=>{
  const randomNumberFactory = await ethers.getContractFactory("GuessTheRandomNumberChallenge");
  randomNumberContract = randomNumberFactory.attach(contractAddress);
});

describe("Guess The Random Number", async ()=>{
  it("Resuelve el Lottery challenge - Guess The Random Number", async ()=>{
    const randomNumber:BigNumber = BigNumber.from(await randomNumberContract.provider.getStorageAt(contractAddress,0));
    console.log(`El numero random buscado es: ${randomNumber}`);

    const tx = await randomNumberContract.guess(randomNumber,{
      value: ethers.utils.parseEther("1"),
      gasLimit: 1e5
    });
    expect(tx.hash).not.to.be.undefined;
    const isComplete:boolean = await randomNumberContract.isComplete();
    console.log(`El valor de isComplete es: ${isComplete}`);
    
    expect(isComplete).to.be.true;
  })
})

{% endhighlight %}

Sin embargo, no quiero dejar de remarcar la posibilidad de encontrar el <i>answer</i> simplemente observando la transacción en etherscan:
<br>
<br>
<b>Imagen</b> 1) Buscamos el address donde se deployó el contrato -> Internal Txns -> Elegimos la transacción con la que se creó.
<br>
<img src="/imagenes/02-Etherscan1.png">
<br>
<b>Imagen 2)</b> State -> Buscamos el cambio de estado de 0 ETH a 1 ETH, que sería cuando se creó el contrato y se modificó el state (variable answer) -> Observamos el storage y al hexadecimal del campo "After" le indicamos desde el desplegable que queremos verlo en formato "Number" -> Observamos el valor oculto y realizamos la llamada guess con el parámetro correspondiente.
<img src="/imagenes/02-Etherscan2.png">
</div>

### Conclusión:
Existen 3 tipos de visibilidad para las variables: public, internal y private. Sin embargo, cuando hablamos de esta visibilidad, nos estamos refiriendo a si otro contrato puede o no ver su contenido. Fuera de los contratos, cualquier persona puede analizar el estado del contrato, desde etherscan por ejemplo, y encontrar el valor actual de cierta variable. Todo es público y transparente, por eso amamos la web 3.0 😎

<script>
function revelarSeccion(HTMLElement, nombreBoton) {

  if (document.getElementById(nombreBoton).innerHTML == "Ocultar") {
    document.getElementById(nombreBoton).innerHTML = "Revelar";
    HTMLElement.style.display = "none";
  } else {
    document.getElementById(nombreBoton).innerHTML = "Ocultar";
    HTMLElement.style.display = ""
  }
}
</script>