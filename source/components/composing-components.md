Components realmente brilham quando você alcança seu potencial maximo, isso é, compondo um utilizando outro.
Pegue por exemplo o elemento `<ul>`, e o fato que o apenas elementos `<li>` são filhos apropriados. 
Se nos queremos o mesmo tipo de comportamento, nos temos que compor nossos Components.

Do mesmo jeito que compomos elementos HTML nos podemos compor Components do ember.


```app/templates/application.hbs
{{#user-list users=activeUsers sortBy='name' as |user|}}
  {{user-card user=user}}
{{/user-list}}
```

### Blocos Component

Components podem ser usados de duas formas, algo bem parecido com elementos HTML.

**Forma Inline**

```app/templates/application.hbs
{{user-list users=activeUsers}}
```

Isso é a forma basica de um Component, dessa forma o 'consumer' desse 
Component não tem habilidade de inserir seu proprio conteudo nele.
Pode ser possivel que determinado componente só possa ser usado dessa forma.
Verifique a documentação do componente para ver em quais formas ele esta disponivel

**Forma em bloco**

```app/templates/application.hbs
{{#user-list users=activeUsers}}
  {{!-- custom template here --}}
{{/user-list}}
```

Para compor componentes, é necessario que eles estejam em forma de bloco, 
É preciso verificar em qual forma ele esta sendo usado,
Nos podemos fazer isso utilizando a propriedade `hasBlock`.


```app/templates/components/user-list.hbs
{{#if hasBlock}}
  {{yield}}
{{else}}
  <p>No Block Specified</p>
{{/if}}
```

Uma vez que verificado se esse component esta sendo usado na forma de bloco, nos podemos 
'yield' nos podemos mostrar qualquer coisa para o usuario sempre que quisermos utilizando o 
`{{yield}}` helper.

Esse `{{yield}}` helper pode ser usado no template do componente diversas vezes.
Assim nos permitindo criar um compomente que funciona como uma lsita, onde 
controlamos o cabeçalho e o body de cada item (ou post, como no exemplo).

```app/templates/components/post-list.hbs
{{#each posts as |post|}}
  <h3>{{post.title}}</h3>
  <p>{{yield}}</p>
{{/each}}
```

Que pode ser usado como:

```app/templates/posts.hbs
{{#post-list posts=headlinePosts}}
  Greatest post ever!
{{/post-list}}
```

E o resultado será o seguinte HTML:

```html
<div id="ember123" class="ember-view">
  <h3>Tomster goes to town</h3>
  <p>Greatest post ever!</p>
  <h3>Tomster on vacation</h3>
  <p>Greatest post ever!</p>
</div>
```

Mas porque usar ele só para só para mostrar a mesma coisa? Nos não queremos costumizar nossos posts,
e mostrar o conteudo correto? É claro que queremos. Vamos entender mais como funciona o `{{yield}}`

### Data Down

Para conseguir compor melhor por tras de templates simples, nos vamos precisar passar dados para esse template.
Isso de ser feito com o helper `{{yield}}` que vai ser explicado logo abaixo.

O `{{yield}}` define onde o conteudo que definimos será inserido dentro do layout do nosso compoonente,
assim como vimos na sessão anterior. Alem disso, o helper yield tambem nos permite enviar usar data down passando os
parametros de volta para o escopo onde ele foi invocado.

```hbs
{{yield}}
{{yield "post"}}
{{yield item}}
{{yield this "footer"}}
```

Por padrão yield não envia nenhum dado, mas nos podemos providenciar isso com um numero arbitrario de parametros. Alem disso, os valores que o yield utilizar serão ordenados em uma ordem sequencial baseada em como ele foram definidos, então você pode acessa-los na mesma ordem que o componente.
Utilizar data down permite ao consumidor do nosso componente à utilizar esse dado.

Para o consumidor acessar esse dato, ele precisa saber qual é a informação for exposta pelo componente,
aqui é onde documentação se revela crucial, e os dados enviados podem ser acessados com um operador `as`.
Vamos utilizar o template abaixo como exemplo:


```app/components/user-list/template.hbs
{{#each users as |user|}}
  {{yield user}}
{{/each}}
```

Esse componente `{{user-list}}` irá se repetir quantas vezes foram necessarias.
Nos vamos consumir ele da seguinte maneira:

```app/users/template.hbs
{{#user-list users=users as |user|}}
  {{user-card user=user}}
{{/user-list}}
```

Agora `{{user-card}}` tem acesso ao usuario atual, que muda de acordo com a `{{user-list}}` que passa por todos os usuarios, já que 
`{{yield user}}` esta dentro do bloco `{{each}}`. Embora isso seja bacana, nos tambem poderiamos usar nosso componente sem um bloco?
(ex. `{{user-list users=users}}`) Isso faria nosso compomente inutil uma vez que nada esta utilizando o yield,
mesmo que os usuarios continuem sendo intinerados. Vamos fazer um mix dentro do atributo `hasBlock` e ver como 
podemos fazer esse componente mais util.

```app/components/user-list/template.hbs
{{#if hasBlock}}
  {{#each users as |user|}}
    {{yield user}}
  {{/each}}
{{else}}
  {{#each users as |user|}}
    <section class="user-info">
      <h3>{{user.name}}</h3>
      <p>{{user.bio}}</p>
    </section>
  {{/each}}
{{/if}}
```

Isso faz nosso compomente mais util graças aos seus valores default serem melhor definidos,
mas tambem é possivel alterar esses valores. Utilizando o helper `{{yield}}` nos podemos passar para baixo
os parametros que o consumidor irá usar. Isso faz o conceito de 'passar dados para baixo' muito util.

#### `{{component}}` Helper

Para entender o  helper `{{component}}`, nos vamos precisar adicionar novas features no componente `{{user-list}}`:

* Para cada tipo de usuario, nos queremos um layout diferente.
* As listas podem ser exibidas tanto como display basico e detalhado.

Vamos adicionar um novo tipo de usuario, o superusuario, ele irá tem um comportamento diferente na nossa aplicacão. 
Como o consumidor irá utilizar esses dados para mostrar esse uma interface diferente para cada usuario? É ai que o 
`{{component}}` entra na jogada. Esse helper nos permite escolher qual template renderizar em pleno tempo de execução.

Nos temos dois tipos de usuarios, o tipo 'public' e 'superuser', nos queremos renderizar um componente
para cada tipo, alem dos modos basico e detalhado. Nos vamos construir os seguintes componentes:

* `basic-card-public`
* `basic-card-superuser`
* `detailed-card-public`
* `detailed-card-superuser`

Nos vamos criar esses nomes usando o helper `{{concat}}` em sua nested form (forma aninhada).

```hbs
{{component (concat 'basic-card-' user.type)}}
```

Agora o nome dos nossos componentes são validos graças ao uso do traço, nova vamos colocar o templete completo de duas formas.

```app/users/template.hbs
{{#user-list users=users as |user basicMode|}}
  {{#if basicMode}}
    {{component (concat 'basic-card-' user.type) user=user}}
  {{else}}
    {{component (concat 'detailed-card-' user.type) user=user}}
  {{/if}}
{{/user-list}}
```

Nos temos o `{{basic-card-superuser}}` e o `{{detailed-card-public}}` sendo renderizados em seus respectivos lugares baseando-se no `user.type` sendo igual 'superuser' e 'public' respectivamente. O uso do helper 'concat' é aninhado para cumprir isso. Helpers aninhados são avaliadso antes do helper pai e esse helper
pode ser usar o valor retornado pelo helper aninhado. Nesse caso é só a concatenação de dois valores. vocÊ pode tentar tambem com mais helpers aninhados,
como o `if` por exemplo.

A partid do uso do `{{component}}`, nos tambem devemos ter um segundo valor yield para nossa feature "basic/detailed mode",
que nos iremos adicioanr no nosso yield, ex. `{{yield user basic}}`.

O consumidor pode decidir como nomear esses valores yield. Nos vamos nome-los da seguinte maneira:

```app/users/template.hbs
{{#user-list users=users as |userModel isBasic|}}
  {{! use sua criatividade aqui }}
{{/user-list}}
```

### Actions Up

Agora que nos sabemos como funciona o  "data down", nos queremos tambem, manipular os dados inseridos pelo usuario,
como a mudança do avatar ou nome. Nos conseguimos fazer isso usando actions.

Nos iremos olhar no componente `{{user-profile}}` que implementa uma ação de save. Usando yield e passando a action como parametro, é permitido que o consumidor use esse componente esses dados

Aqui a definição do nosso componente:

```app/components/user-profile/component.js
export default Ember.Component.extend({
  actions: {
    saveUser() {
      var user = this.get('user');

      user.save()
        .then(() => {
          this.set('saved', true);
        })
        .catch(error => {
          this.set('saveError', error);
        });
    }
  }
});
```

```app/components/user-profile/template.hbs
{{! provavelmente existiria algum markup aqui }}
{{yield profile (action "saveUser")}}
```

Então agora o consumidor irá ter acesso à nossa data e a action que definimos. 
Vamos ver como esse componente pode ser consumido em forma de bloco;


```app/templates/user/profile.hbs
{{#user-profile user=user as |profile saveUser|}}
  {{user-avatar url=profile.imageUrl onchange=saveUser}}
{{/user-profile}}
```

Como você pode ver nesse exmplo, é permitido que o consumidor olhe dentro do componente 
`{{user-profile}}` e da action `saveUser`, que foi passada para baixo com o helper  `(action "saveUser")` 
Esse helper le as propriedade do contexto dessa action em forma de hash. E então cria um closure em cima de qualquer argumento que você passe.

Nos vamos fazer de um modo que a formação das ações sobre o HTML seja como um input button:


```hbs
{{#user-profile user=user as |profile saveUser|}}
  <button type="button" onclick={{action saveUser}}>Save</button>
{{/user-profile}}
```

Isso é muito util, uma vez que estamos reusando um evento que já é nativo do elemento.
Isso faz com que componetnes sejam mais reusaveis, uma vez que eles você usar pequenos componentes que 
tem especificamente um comportamento.

### Wrapping Up

Compor componentes é sobre isolar funcionalidades para modos reusaveis de um jeito que seja facil
combina-los e faze-los trabalhar juntos
Isso tambem aumenta a facilidade de testes uma vez que podemos ficar longe de compomentes gigantes que fazem tudo
nos podemos isolar pequenas partes funcionalidades.