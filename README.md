# clean-code-javascript

Esta é apenas uma tradução do guia escrito por Ryan McDermot, que pode ser lido [aqui](https://github.com/ryanmcdermott/clean-code-javascript).
Note que algumas nomenclaturas foram mantidas em inglês. Isso é proposital: creio que alguns padrões do JavaScript simplesmente não
devem ser traduzidos, pois assim distancia a leitura dos nomes que os programadores em geral costumam usar para tratar de seus códigos.

## Tabela de conteúdos

1. [Introdução](#introdução)
2. [Variáveis](#variáveis)
3. [Funções](#funções)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Traduções](#traduções)

## Introdução

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

Princípios da engenharia de software, do livro
[_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), de Robert C. Martin,
adaptado para o JavaScript. Isso não é um style guide. É um guia para produzir softwares
[legíveis, reusáveis, e refatoráveis](https://github.com/ryanmcdermott/3rs-of-software-architecture) em JavaScript.

Nem todo princípio aqui deve ser seguido estritamente, e ainda menos devem ser universalmente concordados.
Estes são princípios-guia e nada mais, mas todos eles foram codificados através de muitos anos de experiência coletiva
dos autores da obra _Clean Code_.

Nosso trabalho de engenharia de software tem só um pouco mais de 50 anos de idade, e nós ainda estamos
aprendendo muito. Quando a arquitetura de software tiver quase a idade da arquitetura comum, então talvez ela
tenha regras mais estritas a se seguir. Por enquanto, deixemos que esses guias sejam apenas um caminho para
garantir a qualidade do código JavaScript que tanto você quanto a sua equipe irão produzir.

Mais uma coisa: saber isso tudo não vai te tornar um melhor engenheiro de software instantaneamente, e
trabalhar com elas por anos não vai garantir que você não vai cometer erros. Todo pedaço de código começa
com um desenho inicial, como argila molhada sendo modelada na sua forma final. Finalmente, nós raspamos
as imperfeições quando fazemos a revisão com nossos colegas. Não se abata por causa dos primeiros projetos
que precisarem de melhorias. Bata no código no lugar disso!

## **Variáveis**

### Use nomes significativos e pronunciáveis

**Errado:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Certo:**

```javascript
const dataAtual = moment().format("YYYY/MM/DD");
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Use o mesmo vocabulário para o mesmo tipo de variável

**Errado:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Certo:**

```javascript
getUser();
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Use nomes pesquisáveis

Nós vamos ler mais códigos do que escrever. Por isso, é importante que o código que escrevemos
seja legível e pesquisável. Ao _não_ nomear variáveis de forma significativa para o entendimento
do nosso programa, nós dificultamos a vida dos nossos leitores.

Por isso, faça nomes pesquisáveis. Ferramentas como [buddy.js](https://github.com/danielstjules/buddy.js) e
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
podem ajudar a identificar constantes sem bons nomes, por exemplo.

**Errado:**

```javascript
// Para que é que serve 86400000?
setTimeout(blastOff, 86400000);
```

**Certo:**

```javascript
// Declare as constantes com o nome em CAPS LOCK
const MILISSEGUNDOS_POR_DIA = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILISSEGUNDOS_POR_DIA);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Use variáveis explicativas:

**Errado:**

```javascript
const endereço = "One Infinite Loop, Cupertino 95014";
const cepDaCidadeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
salvarCepDaCidade(
  endereço.match(cepDaCidadeRegex)[1],
  endereço.match(cepDaCidadeRegex)[2]
);
```

**Certo:**

```javascript
const endereço = "One Infinite Loop, Cupertino 95014";
const cepDaCidadeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = endereço.match(cepDaCidadeRegex) || [];
salvarCepDaCidade(city, zipCode);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Procure não fazer mapeamentos mentais

Explícito é melhor que implícito.

**Errado:**

```javascript
const localizações = ["Austin", "New York", "San Francisco"];
localizações.forEach(l => {
  façaAquilo();
  façaIsso();
  // ...
  // ...
  // ...
  // Pera, para quê serve "l" mesmo?
  dispatch(l);
});
```

**Certo:**

```javascript
const localizações = ["Austin", "New York", "San Francisco"];
localizações.forEach(localização => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(localização);
});
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Não adicione contextos desnecessários.

Se o nome da sua classe/objeto já é significativo, não precisa repetir no nome da variável.

**Certo:**

```javascript
const Carro = {
  fabricanteDoCarro: "Honda",
  modeloDoCarro: "Accord",
  corDoCarro: "Blue"
};

function pintarCarro(car, cor) {
  car.corDoCarro = cor;
}
```

**Certo:**

```javascript
const Carro = {
  fabricante: "Honda",
  modelo: "Accord",
  cor: "Blue"
};

function paintCar(car, cor) {
  car.cor = cor;
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Use default arguments ao invés de short circuitings ou condicionais

Default argumentos normalmente são mais limpos do que short circuitings. Esteja ciente de que, se você usá-los,
sua função só vai disponibilizar valores para argumentos do tipo `undefined`. Outros valores que também são "falsy",
como `''`, `false`, `null`, `0` e `NaN`, **não** serão trocados pelo valor default.

**Errado:**

```javascript
function criarCervejaria(nome) {
  const nomeDaCervejaria = nome || "Hipster Brew Co.";
  // ...
}
```

**Certo:**

```javascript
function criarCervejaria(nome = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Funções**

### Argumentos das funções (dois ou menos, idealmente)

Limitar o número de parâmetros das funções é incrivelmente importante porque isso facilita e muito a testagem
das funções. Ter mais de três argumentos leva a uma explosão combinatória onde você tem de testar muitos casos
para cada um dos argumentos de sua função.

Por isso, utilizar apenas um ou dois argumentos é o ideal, e três deve ser evitado, se possível. Qualquer valor a mais
do que isso deveria ser refatorado. Normalmente, se você tiver mais de dois argumentos, é sinal de que a sua função
está fazendo mais coisas do que deveria. Em casos em que ela não está, na maioria das vezes passar um único objeto
como argumento será já o suficiente para resolver a situação.

Como JavaScript tem uma facilidade imensa para criar objetos, sem um caldeirão de classes (como ocorre com Java, por exemplo),
você poderá usar um objeto se você perceber que está precisando de vários argumentos.

Para tornar óbvias quais as propriedades de que a função precisa, você pode usar a sintaxe de destructuring do ES2015/ES6.
Isso traz algumas vantagens:

1. Quando alguém procura pela assinatura da função, fica imediatamente claro quais as propriedades que estão em uso;
2. Isso pode ser usado para emular parâmetros com nomes;
3. Destructuring também clona os valores de tipo primitivo do objeto passado como argumento para a função. Isso pode
ajudar a prevenir alguns efeitos colaterais. Note: objetos e arrays que são desestruturados do objeto-argumento NÃO são clonados.
4. O linter pode te avisar caso alguma propriedade não esteja em uso, o que não ocorreria caso você não fizesse destructuring.

**Errado:**

```javascript
function criarMenu(título, corpo, textoDoBotão, cancelável) {
  // ...
}

criarMenu("Foo", "Bar", "Baz", true);

```

**Certo:**

```javascript
function criarMenu({ título, corpo, textoDoBotão, cancelável }) {
  // ...
}

createMenu({
  título: "Foo",
  corpo: "Bar",
  textoDoBotão: "Baz",
  cancellable: true
});
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Funções devem fazer uma única coisa por vez.

**Essa é a regra mais importante na engenharia de software.** Quando funções fazem mais de uma coisa,
elas são mais difíceis de compôr, testar e entender.

Quando você consegue isolar uma função de modo que ela faça apenas uma única ação, ela pode ser refatorada mais facilmente
e o seu código se tornará muito mais limpo. 

É sério, se essa for **a única** coisa que você aprender neste guia, você já estará na frente de muitos desenvolvedores.

**Errado:**

```javascript
function mandarEmails(clientes) {
  clientes.forEach(cliente => {
    const dadosDoCliente = database.lookup(cliente);
    if (dadosDoCliente.ehAtivo()) {
      mandarEmail(cliente);
    }
  });
}
```

**Certo:**

```javascript
function mandarEmailParaClientesAtivos(clientes) {
  clientes.filter(ehClienteAtivo).forEach(mandarEmail);
}

function ehClienteAtivo(cliente) {
  const dadosDoCliente = database.lookup(cliente);
  return dadosDoCliente.ehAtivo();
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### O nome das funções deve dizer o que elas fazem

**Errado:**

```javascript
function adicionarNaData(date, month) {
  // ...
}

const data = new Date();

// É difícil de adivinhar, só pelo nome do função, o que é adicionado
adicionarNaData(date, 1);
```

**Certo:**

```javascript
function adicionarMesNaData(month, date) {
  // ...
}

const data = new Date();
adicionarMesNaData(1, date);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Funções só devem ter um único nível de abstração

Se sua função tiver mais de um nível de abstração, é sinal de que ela está fazendo mais coisas do que deveria.
Separar em funções diferentes gera uma maior reusabilidade e torna a testagem mais fácil.

**Errado:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Certo:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Remova código duplicado

Dê o melhor de si para evitar código duplicado. Fazer código duplicado é péssimo para você, porque,
caso você precise alterar alguma lógica, será necessário mudar o seu código em mais de um lugar.

Imagine que você é dono de um restaurante e está organizando o seu estoque: todo o seu tomate, cebola,
alho, temperos etc. Se você tiver várias listas para manter a contagem da quantidade de cada produto, você
terá que atualizar *todas* elas quando for servir um prato com tomates. Se você tiver uma só lista, só
precisará atualizar em um lugar por vez!

De vez em quando, você acaba escrevendo código duplicado por que você tem duas ou mais implementações que são só um pouco
diferentes, mas que têm muita coisa em comum, sendo que essas diferenças te obrigam a criar duas funções diferentes
que farão quase a mesma coisa. 

Remover código duplicado significa criar uma abstração que poderá cuidar dessas pequenas diferenças fazendo
uso de apenas uma função, módulo ou classe.

Fazer a abstração da forma correta é crítico, e é por isso que você deve seguir os princípios da SOLID que foram deixados
na seção _Classes_. 

Más abstrações podem ser piores do que código duplicado. Portanto, tome cuidado! Com isso dito, se você puder fazer
uma boa abstração, faça isso. Não se repita, do contrário você vai se ver atualizando vários lugares toda vez
que quiser mudar uma mesma coisa.

**Errado:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const salarioEsperado = developer.calcularSalarioEsperado();
    const experiencia = developer.getExperiencia();
    const githubLink = developer.getGithubLink();
    const informacoes = {
      salarioEsperado,
      experiencia,
      githubLink
    };

    render(informacoes);
  });
}

function showListaDeGerentes(gerentes) {
  gerentes.forEach(gerente => {
    const salarioEsperado = gerente.calcularSalarioEsperado();
    const experiencia = gerente.getexperiencia();
    const portfolio = gerente.getMBAProjects();
    const informacoes = {
      salarioEsperado,
      experiencia,
      portfolio
    };

    render(informacoes);
  });
}
```

**Certo:**

```javascript
function showListaDeEmpregados(empregados) {
  empregados.forEach(empregado => {
    const salarioEsperado = empregado.calcularSalarioEsperado();
    const experiencia = empregado.getexperiencia();

    const informacoes = {
      salarioEsperado,
      experiencia
    };

    switch (empregado.type) {
      case "gerente":
        informacoes.portfolio = empregado.getMBAProjects();
        break;
      case "developer":
        informacoes.githubLink = empregado.getGithubLink();
        break;
    }

    render(informacoes);
  });
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Crie objetos com valores default usando Object.assign

**Errado:**

```javascript
const menuConfig = {
  titulo: null,
  corpo: "Bar",
  textoDoBotao: null,
  cancelavel: true
};

function criarMenu(config) {
  config.titulo = config.titulo || "Foo";
  config.corpo = config.corpo || "Bar";
  config.textoDoBotao = config.textoDoBotao || "Baz";
  config.cancelavel =
    config.cancelavel !== undefined ? config.cancelavel : true;
}

criarMenu(menuConfig);
```

**Certo:**

```javascript
const menuConfig = {
  titulo: "Pedido",
  // Note que a chave 'corpo' não está presente
  textoDoBotao: "Send",
  cancelavel: true
};

function criarMenu(config) {
  let finalConfig = Object.assign(
    {
      titulo: "Foo",
      corpo: "Bar",
      textoDoBotao: "Baz",
      cancelavel: true
    },
    config
  );
  return finalConfig
  // config now equals: {titulo: "Order", corpo: "Bar", textoDoBotao: "Send", cancelavel: true}
  // ...
}

criarMenu(menuConfig);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Não use flags como parâmetros para funções

Flags dizem para o seu usuário que essa função faz mais de uma coisa. Funções devem fazer apenas uma coisa por vez.

Separe suas funções em duas se elas estão seguindo rotas diferentes de código baseadas em um boolean passado como parâmetro.

**Errado:**

```javascript
function criarArquivo(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Certo:**

```javascript
function criarArquivo(name) {
  fs.create(name);
}

function criarArquivoNoTemp(name) {
  criarArquivo(`./temp/${name}`);
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite funções com efeitos colaterais (parte 1)

Uma função produz efeito colateral se ela faz algo além de receber um valor e returnar outro valor.
Um efeito colateral poderia ser escrever um arquivo, modificar alguma variável global, ou acidentalmente
entregar todo o seu dinheiro para um estranho.

Perceba: de vez em quando você precisa que uma função tenha efeitos colaterais. Como no exemplo do tópico anterior,
pode ser que você tenha que escrever em um arquivo. O que você precisa saber é aprender como centralizar *onde*
você está fazendo isso. 

Ou seja: não é bom ter *muitas* funções que escrevam em um arquivo particular. Tenha **apenas um** serviço que faça
isso. Um e apenas um.

O ponto principal é evitar erros comuns como compartilhar estados entre objetos sem nenhuma estrutura,
usar tipos de dados mutáveis que podem ser escritos por qualquer coisa, e não centralizar onde seus
efeitos colaterais ocorrem. Se você souber fazer isso, você será mais feliz que a vasta maioria dos outros programadores.

**Errado:**

```javascript
// Variável global referenciada pela função a seguir.
// Se tivéssemos mais de uma função usando esse nome, agora isso se transformaria em um array, e toda a estrutura poderia quebrar
let nome = "Ryan McDermott";

function separarEmNomeESobrenome() {
  nome = nome.split(" ");
}

separarEmNomeESobrenome();

console.log(nome); // ['Ryan', 'McDermott'];
```

**Certo:**

```javascript
function separarEmNomeESobrenome(nome) {
  return nome.split(" ");
}

const nome = "Ryan McDermott";
const novoNome = separarEmNomeESobrenome(nome);

console.log(nome); // 'Ryan McDermott';
console.log(novoNome); // ['Ryan', 'McDermott'];
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite efeitos colaterais (parte 2)

Em JavaScript, alguns valores são imutáveis e outros são mutáveis. Objetos e arrays são dois tipos
de valores mudáveis, e, por isso, é importante usá-los com muito cuidado ao passá-los como parâmetros
de uma função. Uma função de JavaScript pode mudar as propriedades de um objeto ou alterar os conteúdos
de um array, o que pode facilmente causar bugs em outros lugars.

Suponha que tenha uma função que aceite um array como parâmetro, e que ele representa um carrinho de compras.
Se a função faz uma mundaça neste array - adicionando um item para a compra, por exemplo - então qualquer outra
função que usa o mesmo array `carrinho` será afetada por essa adição. Isso pode ser bom, mas também pode
ser ruim. 

Imaginemos uma situação em que isso não é bom:

O usuário clica no botão "Comprar", que chama a função `comprar`. Esta faz uma 
requisição, depois enviando o array `carrinho` para o servidor. Por conta de uma
má conexão de internet, a função `comprar` tem de continuar enviando a requisição.
E se, enquanto isso, o usuário acidentalmente clica no botão de "Adicionar ao carrinho"
em um item que ele não realmente quer antes que a requisição comece?

Se isso ocorre e a requisição começa, então essa função de compra vai, acidentalmente,
adicionar o novo item por conta do array `carrinho` ter sido modificado.

Uma boa solução seria se a função `adicionarItemAoCarrinho` sempre clonasse o
`carrinho`, editasse ele, e retornasse o clone. Isso iria garantir que as funções
que ainda estivessem usando o carrinho antigo não seriam afetadas pelas mudanças.

Temos duas ressalvas a se fazer antes de explorar ainda mais essa abordagem:

1. Podem haver casos em que você realmente quer modificar o objeto do input,
   mas, depois que você adotar essa prática de programação, você vai notar que esses casos são bastante raros. A maioria das coisas podem ser refatoradas
   para não ter efeitos colaterais!

2. Clonar grandes objetos pode ser algo bastante caro em termos de perfomance.
   Por sorte, essa prática não é um grande problema porque existem [ótimas bibliotecas](https://facebook.github.io/immutable-js/)
   que permitem que esse tipo de programação seja rápido e não consuma tanto 
   processamento quanto ocorreria se a clonagem de objetos e de arrays fosse feita
   manualmente.

**Errado:**

```javascript
const adicionarItemAoCarrinho = (carrinho, item) => {
  carrinho.push({ item, data: Date.now() });
};
```

**Certo:**

```javascript
const adicionarItemAoCarrinho = (carrinho, item) => {
  return [...carrinho, { item, data: Date.now() }];
};
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Não escreva para funções globais

Poluir funções globais é uma má prática em JavaScript porque você poderia 
atrapalhar o funcionamento de alguma biblioteca e o usuário da API não faria ideia do que
está acontecendo até que ele encontrasse uma falha na execução.

Pensemos em um exemplo: e se você quisesse extender o método nativo Array de JavaScript para
que ele tivesse um método `diff` que pode mostrar a diferença entre dois arrays? Você poderia
escrever a sua nova função para o `Array.prototype`, mas ele poderia depois se chocar com uma biblioteca
que tentasse fazer a mesma coisa.

E se essa outra biblioteca estivesse usando um método `diff` para encontrar a diferença entre apenas o primeiro
e o último elementos de um array? É por isso que é muito melhor sar as classes do ES2015/ES6 e simplesmente
extender o `Array` global.

**Errado:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Certo:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Prefira programação funcional a imperativa

JavaScript não é uma linguagem funcional como Haskell é, mas ela ainda tem algo de funcional em si.
Linguagens funcionais normalmente são mais limpas e mais fáceis de se testar. Dê preferência a esse estilo
de programação sempre que possível.

**Errado:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Certo:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Você deve encapsular condicionais

É sempre bom e útil que as suas condicionais estejam encapsuladas dentro de funções.
Dessa forma, será muito fácil para o leitor entender o seu código, além de permitir reutilizar
a mesma condicional e facilitar algum ajuste em caso de bugs.

**Errado:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Certo:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite condicionais negativas

É sempre confuso de se entender uma condicional que funciona caso o valor de uma expressão
booleana seja negativo. Evitar usá-las tornará o seu código muito mais simples de se ler.

**Errado:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Certo:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite condicionais

Essa parece ser uma tarefa impossível. À primeira vista, muitos vão dizer "como é que eu vou fazer
algo sem um `if`?" A resposta é que você pode usar polimorfismo para resolver esses problemas, na 
maioria dos casos.

A segunda pergunta, normalmente, é "bem, até aí tudo certo. Mas por que eu faria isso?" A resposta
é um conceito de clean code que aprendemos antes: uma função só deve fazer uma coisa.

Quando você tem classes e funções que têm `if`, você está dizendo ao usuário que essa função faz
mais de uma coisa. Lembre-se: faça só uma coisa por vez.

**Errado:**

```javascript
class Aviao {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Certo:**

```javascript
class Aviao {
  // ...
}

class Boeing777 extends Aviao {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Aviao {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Aviao {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite checar tipos (parte 1)

JavaScript não é uma linguagem que depende de tipos, o que quer dizer que sua função pode
receber qualquer tipo de argumento.

Às vezes, esse grau de liberdade acaba te prejudicando, e acaba se tornando tentador fazer checagem
de tipo em suas funções. Existem muitas formas de evitar ter que fazer isso. A primeira a se considerar 
é APIs consistentes.

**Errado::**

```javascript
function dirigirParaOTexas(veiculo) {
  if (veiculo instanceof Bicicleta) {
    veiculo.pedalar(this.localizacaoAtual, new Localizacao("texas"));
  } else if (veiculo instanceof Car) {
    veiculo.dirigir(this.localizacaoAtual, new Localizacao("texas"));
  }
}
```

**Certo:**

```javascript
function travelToTexas(veiculo) {
  veiculo.mover(this.localizacaoAtual, new Localizacao("texas"));
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Evite checar tipos (parte 2)

Se você estiver trabalhando com tipos primitivos como strings e integers e, por isso, não pode
usar polimorfismo, mas ainda assim você acha que precisa fazer checagem de tipos, você talvez 
deveria usar TypeScript.

É uma excelente alternativa para o JavaScript normal, já que ele te dá tipagem estática com uso
da sintaxe normal do JavaScript.

O problema de fazer a checagem de tipo manualmente no JavaScript normal é que fazê-lo bem feito
exige tanto código extra que a "segurança de tipo" recebida não compensa a perda de capacidade 
de leitura do seu código.

Mantenha o seu JavaScript limpo, escreva bons testes, e faça bons reviews no seu código. Ou então
você pode usar TypeScript no lugar disso tipo (o que, como dito antes, é uma ótima alternativa!).

**Errado:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Deve ser do tipo number ou integer");
}
```

**Certo:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Não tente otimizar demais

Navegadores modernos fazem muita otimização debaixo dos panos durante o tempo de execução.
Muitas das vezes, se você estiver tentando otimizar o seu código, então você está apenas perdendo
o seu tempo. 

[Existem alguns bons recursos](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
para te ajudar a ver onde estiver faltando otimizar algo. Coloque a sua mira neles, até que eles
sejam consertados, se possível.

**Errado:**

```javascript
// Em navegadores antigos, cada iteração de list.length sem cache vai ser custosa por conta da
// recomputação de `list.length`. Em navegadores modernos, isso já está otimizado.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Certo:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Remova código morto

Código morto é tão ruim quanto código duplicado. Não existe motivos para mantê-lo nos seus arquivos.
Se não estiver sendo usado, se livre! Ele ainda vai estar disponível no seu histórico de versões
se você precisar dele.

**Errado:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Certo:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Objects and Data Structures**

### Use getters and setters

Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:

- When you want to do more beyond getting an object property, you don't have
  to look up and change every accessor in your codebase.
- Makes adding validation simple when doing a `set`.
- Encapsulates the internal representation.
- Easy to add logging and error handling when getting and setting.
- You can lazy load your object's properties, let's say getting it from a
  server.

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Make objects have private members

This can be accomplished through closures (for ES5 and below).

**Bad:**

```javascript
const empregado = function(name) {
  this.name = name;
};

empregado.prototype.getName = function getName() {
  return this.name;
};

const empregado = new empregado("John Doe");
console.log(`empregado name: ${empregado.getName()}`); // empregado name: John Doe
delete empregado.name;
console.log(`empregado name: ${empregado.getName()}`); // empregado name: undefined
```

**Good:**

```javascript
function makeempregado(name) {
  return {
    getName() {
      return name;
    }
  };
}

const empregado = makeempregado("John Doe");
console.log(`empregado name: ${empregado.getName()}`); // empregado name: John Doe
delete empregado.name;
console.log(`empregado name: ${empregado.getName()}`); // empregado name: John Doe
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Classes**

### Prefer ES2015/ES6 classes over ES5 plain functions

It's very difficult to get readable class inheritance, construction, and method
definitions for classical ES5 classes. If you need inheritance (and be aware
that you might not), then prefer ES2015/ES6 classes. However, prefer small functions over
classes until you find yourself needing larger and more complex objects.

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Use method chaining

This pattern is very useful in JavaScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Prefer composition over inheritance

As stated famously in [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
   relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
   (Change the caloric expenditure of all animals when they move).

**Bad:**

```javascript
class empregado {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because empregados "have" tax data. empregadoTaxData is not a type of empregado
class empregadoTaxData extends empregado {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class empregadoTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class empregado {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new empregadoTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **SOLID**

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Interface Segregation Principle (ISP)

JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a
"fat interface".

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should
   depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
   abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Testing**

Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](https://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](https://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Async/Await are even cleaner than Promises

Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Error Handling**

Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Formatting**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](https://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization

JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Bad:**

```javascript
class PerformanceReview {
  constructor(empregado) {
    this.empregado = empregado;
  }

  lookupPeers() {
    return db.lookup(this.empregado, "peers");
  }

  lookupgerente() {
    return db.lookup(this.empregado, "gerente");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getgerenteReview();
    this.getSelfReview();
  }

  getgerenteReview() {
    const gerente = this.lookupgerente();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(empregado);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(empregado) {
    this.empregado = empregado;
  }

  perfReview() {
    this.getPeerReviews();
    this.getgerenteReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.empregado, "peers");
  }

  getgerenteReview() {
    const gerente = this.lookupgerente();
  }

  lookupgerente() {
    return db.lookup(this.empregado, "gerente");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(empregado);
review.perfReview();
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## **Comments**

### Only comment things that have business logic complexity.

Comments are an apology, not a requirement. Good code _mostly_ documents itself.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ voltar para o topo](#tabela-de-conteúdos)**

## Traduções

Este artigo também está disponível em outros idiomas:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![rs](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Serbia.png) **Serbian**: [doskovicmilos/clean-code-javascript/](https://github.com/doskovicmilos/clean-code-javascript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ voltar para o topo](#tabela-de-conteúdos)**
