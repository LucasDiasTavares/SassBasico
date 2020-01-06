# Anotações de estudo SASS

### Variaveis em normal css

```scss

:root{
    --primary-color: #227722;
}

body {
    background-color: var(--primary-color);
}
```
### Variaveis em normal Scss

```scss

$primary-color: #227722;

body {
    background-color: $primary-color;
}
```

### Usando map

Básicamente eu crio uma tupla com uma chave e um valor e depois reutilizo ela usando o `map-get($map: , $key: );` onde o map é o nome da minha tupla e a key é a chave que eu quero utilizar.

```scss

$font-weights: ("regular": 400,
    "medium": 500,
    "bold": 700);

body {
    font-weight: map-get($font-weights, bold);
}

```


### Usando nested

Usando css eu iria utilizar assim:

```css

.main .main-p{
    /* colocar quaisquer atributos que eu deseje */
}

```

Já em Scss faço assim:

```scss 

.main {
    width: 80%;
    margin: 0 auto;

    #{&}-p {
        font-weight: map-get($font-weights, bold);

        &:hover{
            color:pink;
        }
    }
}

```

#{&}-p é a mesma coisa de eu digitar main-p, como é um filho do main principal e começa com o mesmo nome posso utilizar dessa forma para 'gastar' menos tempo. Já o & neste caso está apenas funcionando na classe #{&}-p então só colocando o & já é o suficiente.

#### IMPORTANTE: CUIDADO AO USAR ESTE MÉTODO DE UMA FORMA GLOBAL!

### Utilizando @import

Em projetos médios/grande, geralmente são utilizados vários arquivos .scss, para isso no começo do meu arquivo estou importanto o arquivo resets que tem algumas informações básicas de reset de página `@import './resets';` e `@import './variables';` que contém todas as minhas variáveis de scss.

### Utilizando funções (function)

São bem parecidas com as functions do javascript... 

```scss
@function weight($weight-name) {
    @return map-get($font-weights, $weight-name);
}
```
Entendendo o código a cima: `@function weight` é o nome da minha function, `($weight-name)` parametro que irá receber, ` @return map-get($font-weights, $weight-name)` retorno da minha function.

#### Utilizando a minha function

È bem simples de ser utilizada:

Código antes.

```scss
#{&}-p {
    font-weight: map-get($font-weights, bold);
    color: red;
    }
```

Código depois.

```scss
#{&}-p {
    font-weight: weight(bold);
    color: red;
    }
```

Deste modo consigo ter um código mais simples e facil de ler, com uma facildiade gigantesca caso queira fazer alguma alteração futura.


### Utilizando Mixins

Muitas das vezes eu tenho que colocar estes atributos em diversos elementos: 
`display: flex;justify-content: center;align-items: center;` ou criar uma classe e ficar colocando nos onde necessitam, porem com Sass eu posso utilizar um mixin:

```scss
@mixin flexCenter {
    display: flex;
    justify-content: center;
    align-items: center;
}
```

E para utilizar:

```scss
.main {
    @include flexCenter;
    width: 80%;
    margin: 0 auto;
}
```

Similar as functions eu posso passar parametros para elas:

```scss
@mixin flexCenter($direction) {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: $direction;
}
```

```scss
.main {
    @include flexCenter(column);
    width: 80%;
    margin: 0 auto;
```

Exemplo um pouco mais complexo de uso de mixins, para que haja uma alteração de cores caso o tema do site seja light.

```scss
@mixin theme($lightTheme: true) {
    @if $lightTheme {
        background: lighten($primary-color, 100%);
        color: darken($text-color, 100);
    }
}
```

Crio uma classe de light:
```scss
.light {
    @include theme(true);
}
```

Depois uso ela no meu html: `<body class="light">`

#### Também posso utilizar mixins para me ajudar a deixar responsivo de uma forma mais fácil e menas repetitiva

Crio um mixin normal, que irá receber o tamanho a largura máxima pela variavel `$mobile`.

```scss
@mixin mobile {
    @media (max-width: $mobile) {
        @content;
    }
}
```

`$mobile` vem do meu arquivo _variables.css que contém o seguinte código: `$mobile: 800px;`

`@content;` ele irá 'pegar' todas as informações padrões e irá colocar dentro desse mixin.

Depois eu utilizo esse código dentro do meu main:

```scss
@include mobile() {
        @include flexCenter(column);
    }
```
`@include flexCenter(column)` é a minha function que decide se a vai ser mostrado os meus paragrafos em colunas(column) ou em linha(row). Tudo reutilizavel!

Exemplo do código completo:

```scss
.main {
    @include flexCenter(row);
    width: 80%;
    margin: 0 auto;

    #{&}-p {
        font-weight: weight(bold);
        color: red;

        &:hover {
            color: pink;
        }
    }

    @include mobile() {
        @include flexCenter(column);
    }
}
```

### Utilizando extenções (extends)

Dentro do meu .main eu quero que o meu segundo paragrafo, seja tenha as mesmas propriedades do meu primeiro paragrafo, porém quero que a cor do hover seja diferente.

#### IMPORTANTE: ESTÉ CÓDIGO ESTA DENTRO DO .main{}

```scss
 #{&}-p2 {
        @extend .main-p1;

        &:hover {
            color: purple;
        }
    }
```

Muito útil, em elementos que tem várias propriedades já setadas, neste caso Sass pode, te ajudar muito a não ter que ficar digitando todo o código novamente ou usando copiar e colar.

### Utilizando cálculos

Algumas vezes são necessárias utilizar algum calculo em css, por exemplo: `width: calc(80% - 40%)`. Que ficaria assim em Sass: `width: 80% - 40%`.

#### IMPORTANTE: EM SASS SÓ POSSO CALCULAR SE FOREM DO MESMO TIPO, EXEMPLO: % - %, px - px ou rem - rem, etc AO UTILIZAR % - px, DA ERRO!

