Por padrão, todo componente é inserido dentro de um elemento `<div>`. Se vocÊ olhar para
o componente renderizado no developer tools você verá que ele é representado por algo
parecido com isso:

```html
<div id="ember180" class="ember-view">
  <h1>Meu componente</h1>
</div>
```

Você pode costumizar qual tipo de elemento o Ember gera para o seu componetne
incluindo seus atributos e classes, você só precisa criar uma subclasse do 
`Ember.Component` no seu JavaScript.

### Costumizando o Elemento

Para usar uma classe diferente de `div`, você precisa criar uma subclasse de `Ember.Component`
e edita-la com uma nova `tagName` property. Essa propriedade é uma string e 
pode ser qualquer tag HTML5.

```app/components/navigation-bar.js
export default Ember.Component.extend({
  tagName: 'nav'
});
```

```app/templates/components/navigation-bar.hbs
<ul>
  <li>{{#link-to 'home'}}Home{{/link-to}}</li>
  <li>{{#link-to 'about'}}About{{/link-to}}</li>
</ul>
```

### Costumizando as Classes

Você tambem pode especificar quais classes serão aplicadas no elemento 
configurando a propriedade `classNames` (que por acaso, é um array of strings).

```app/components/navigation-bar.js
export default Ember.Component.extend({
  classNames: ['primary']
});
```

Se você quiser que as classes sejam determinadas por propriedades do componente
você pode usar bindings. Se você der bind em uma propriedade booleana, a classe
será adicionada ou removida dependendo de seu valor:

```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['isUrgent'],
  isUrgent: true
});
```

Esse componente seria renderizado da seguinte maneira:

```html
<div class="ember-view is-urgent"></div>
```

Se `isUrgent` for mudado para `false`, então a `is-urgent` seria removida.

Por padrão o nome das propriedades booleanas deve utilizar um hifen. 
Você pode costumizar o nome da classe delimitando ela com um acento de dois pontos `:`:


```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['isUrgent:urgent'],
  isUrgent: true
});
```

Isso iria renderizar no HTML:

```html
<div class="ember-view urgent">
```

Alem de especificar qual classe usar quando o valor for `true`, você 
tambem pode especificar qual classe usar quando o valor for igual à `false`:

```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['isEnabled:enabled:disabled'],
  isEnabled: false
});
```

Isso irá renderizar no HTML:

```html
<div class="ember-view disabled">
```

VocÊ pode especificar a classe que só será aplicada caso a propriedade for 
`false` declarando o `classNameBindings` desse jeito:

```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['isEnabled::disabled'],
  isEnabled: false
});
```

Iso irá renderizar esse HTML:

```html
<div class="ember-view disabled">
```

Se a propriedade `isEnabled` for `true`, ele não irá renderizar nenhuma classe, e ficaria como no exemplo abaixo:

```html
<div class="ember-view">
```

Se o valor essa propriedade for uma string, seu valor será adicionado como classe, sem nenhuma modificação:

```app/components/todo-item.js
export default Ember.Component.extend({
  classNameBindings: ['priority'],
  priority: 'highestPriority'
});
```

E o HTML renderizado seria:

```html
<div class="ember-view highestPriority">
```

### Customizing Attributes

Você pode fazer bind dos atributos para o DOM que representa o componente utilizando o `attributeBindings`:

```app/components/link-item.js
export default Ember.Component.extend({
  tagName: 'a',
  attributeBindings: ['href'],
  href: "http://emberjs.com"
});
```

Você tambem pode dar bind para qualquer tipo de propriedade do elemento:

```app/components/link-item.js
export default Ember.Component.extend({
  tagName: 'a',
  attributeBindings: ['customHref:href'],
  customHref: "http://emberjs.com"
});
```
