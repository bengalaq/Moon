---
layout: post
title: "Capture The Ether - Capítulo 1"
date: 2022-05-15
excerpt: "Capítulo 1 de la serie de desafíos en CTE"
tag: [cte, challenge, resolución]
comments: true
---

<img src="/imagenes/02-Titulo.png">

Antes de comenzar, quería aprovechar este breve párrafo para aclarar un poco la filosofía de este blog. Pese a estar indefectiblemente enseñando la solución a los problemas, **NO ES LO IDEAL EN ABSOLUTO** recurrir a esta explicación sin antes intentarlo por cuenta propia. No importa cuánto demores, muchos de estos desafíos enseñan, más allá del conocimiento, a ser pacientes con uno mismo y adquirir esa habilidad de <a href="https://www.youtube.com/watch?v=C0JbAJmf9eI&t=9s">*levantarse y seguir intentando*</a>. Dicho esto, si no entendiste el problema del todo o querés ver cómo lo resolvió otra persona, ¡Bienvenide! Estás en el lugar que soñabas 😎.

En este primer capítulo intentaré explicar los desafíos:

1. ***<span style="color:#0008ffb8">Guess the number</span>***
2. ***<span style="color:black">Guess the secret number</span>***
3. ***<span style="color:#009d9d">Guess the random number</span>***

# ***<span style="color:#3f45f5b8">1. Guess the number</span>***
### <span style="color:#ff0000c4">Problema:</span> *Estoy pensando en un número. Todo lo que tienes que hacer es adivinarlo.*

El contrato que atacaremos es el siguiente

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

### Datos de lectura rápida:
<button class="btn btn-info" id="botonRevelarLecturaRapida" onclick="revelarSeccion(document.getElementById('lecturaRapida'), 'botonRevelarLecturaRapida')">Revelar</button>
<p id="lecturaRapida"></p>

### Ideas para soluciones:
<button class="btn btn-info" id="botonRevelarIdeasSoluciones" onclick="revelarSeccion(document.getElementById('ideasSoluciones'), 'botonRevelarIdeasSoluciones')">Revelar</button>
<div id="ideasSoluciones"></div>

### Test a ejecutar:
<button class="btn btn-info" id="botonRevelarTest" onclick="revelarSeccion(document.getElementById('testAEjecutar'), 'botonRevelarTest')">Revelar</button>
<div id="testAEjecutar" style="display:none">
{% highlight typescript %}
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

### Explicación:
<button class="btn btn-info" id="botonRevelarExplicacion" onclick="revelarSeccion(document.getElementById('explicacion'), 'botonRevelarExplicacion')">Revelar</button>
<div id="explicacion" style="display: none">El número que debemos adivinar está practicamente frente a nuestras narices. No deberían subestimarnos tanto, ¿No creés?</div>

### Conclusión:
Leer código es SÚPER importante. Así, muchas veces encontraremos cosas que los desarrolladores olvidaron borrar o simplemente pensaron que no habría problema alguno en dejarlo ahí, a nuestro alcance. Conoce a tu enemigo y esas cosas, vos me entendés...

# *<span style="color:#008000d4">2. Guess the secret number</span>*

<div markdown="0"><a href="#" class="btn btn-success">Success Button</a></div>
<div markdown="0"><a href="#" class="btn btn-warning">Warning Button</a></div>
<div markdown="0"><a href="#" class="btn btn-danger">Danger Button</a></div>
<div markdown="0"><a href="#" class="btn btn-info">Revelar</a></div>
<div markdown="0"><a href="#" class="btn">Click to reveal</a></div>

<script>
function revelarSpoiler1() {
  document.getElementById("lecturaRapida").innerHTML = "<ul><li>El número 42 aparece a simple vista.</li><li>La función <i><b>guess</b></i> compara un número que le pasemos con el que posea la variable <i><b>answer</b></i>.</li></ul>";
  if (document.getElementById("botonRevelar1").innerHTML == "Ocultar") {
    document.getElementById("botonRevelar1").innerHTML = "Revelar";
    document.getElementById("lecturaRapida").innerHTML = "";
  } else {
    document.getElementById("botonRevelar1").innerHTML = "Ocultar";
  }
}

function revelarSpoiler2() {
  document.getElementById("ideasSoluciones").innerHTML = "<ul><li>Llamar a la función <i><b>guess</i></b> con el valor 42, el cual es el almacenado en la variable <i><b>answer</i></b></li></ul>";
  if (document.getElementById("botonRevelar2").innerHTML == "Ocultar") {
    document.getElementById("botonRevelar2").innerHTML = "Revelar";
    document.getElementById("ideasSoluciones").innerHTML = "";
  } else {
    document.getElementById("botonRevelar2").innerHTML = "Ocultar";
  }
}

function revelarSpoiler3() {
  document.getElementById("testAEjecutar").style.display = "";
  if (document.getElementById("botonRevelar3").innerHTML == "Ocultar") {
    document.getElementById("botonRevelar3").innerHTML = "Revelar";
    document.getElementById("testAEjecutar").style.display = "none";
  } else {
    document.getElementById("botonRevelar3").innerHTML = "Ocultar";
  }
}

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


<!-- //Boton spoiler
<details>
  <summary><span class="btn btn-info">Revelar</span></summary>

* El número 42 aparece a simple vista.
* La función ***guess*** compara un número que le pasemos con el que posea la variable ***answer***.
</details> -->