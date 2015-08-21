Componentes permitem você definir controles que você pode reusar pela 
sua aplicação. Se eles forem genericos o suficiente, eles podem ser compartilhados
com outros e serem usadas em multiplas aplicações.

Para fazer um controle util, no entado, você precisa primeiro permitir que os usuarios 
da sua aplicação tenham interação com ele

Você pode fazer elementos no seu componente interagirem com seu componetne 
usando o helper `{{action}}`. Ele é [a mesmo helper `{{action}}` que você usa nos 
templates](../../templates/actions), mas ele tem apresenta diversas diferenças quando 
usado dentro de uma instancia  do `Ember.Component`.

Por exemplo, imagine o seguinte componetne que mostra o titulo de um post.
Quando o titulo for clicado o conteudo do post é exibido:

```app/templates/components/post-summary.hbs
<h3 {{action "toggleBody"}}>{{title}}</h3>
{{#if isShowingBody}}
  <p>{{{body}}}</p>
{{/if}}
```

```app/components/post-summary.js
export default Ember.Component.extend({
  actions: {
    toggleBody() {
      this.toggleProperty('isShowingBody');
    }
  }
});
```

O helper `{{action}}` pode aceitar argumentos, escutar por diferentes tipos de eventos,
controlar como as ações acontecem e muito mais.

Para detalhes sobre o helper `{{action}}` veja a [Sessão de actions](../../templates/actions) 
no capitulo de templates.