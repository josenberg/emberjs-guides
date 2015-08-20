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

Which can be used like so:

```app/templates/posts.hbs
{{#post-list posts=headlinePosts}}
  Greatest post ever!
{{/post-list}}
```

And will result in the following HTML:

```html
<div id="ember123" class="ember-view">
  <h3>Tomster goes to town</h3>
  <p>Greatest post ever!</p>
  <h3>Tomster on vacation</h3>
  <p>Greatest post ever!</p>
</div>
```

But what use is it to just output the same thing over and over? Don't we want to customize our posts,
and display the right content? Sure we do. Lets explore the `{{yield}}` helper a bit.

### Data Down

To accomplish composability beyond simple templates, we need to pass data to those templates. This can be done with the `{{yield}}` helper that was introduced above.

The `{{yield}}` defines where the content we defined inside our component block will yield in the component's layout,
as we saw in the previous section. Apart from that, the yield helper also allows us to send data down by yielding params back to the scope the component was invoked in.

```hbs
{{yield}}
{{yield "post"}}
{{yield item}}
{{yield this "footer"}}
```

By default yield does not send any data, but you can provide an arbitrary number of parameters. Also, the yielded values are ordered sequentially by how they are defined, so you access them in the same order as the component exposes them.
Sending data down allows the consumer of our component to utilize that data.
For the consumer to get access to the data, she needs to know what data a component exposes,
this is where documentation is so crucial, and from there the yielded data can be accessed
with the `as` operator. Let's take the following template as an example:

```app/components/user-list/template.hbs
{{#each users as |user|}}
  {{yield user}}
{{/each}}
```

This `{{user-list}}` component will yield as many times as there are users.
We can consume it in the following way:

```app/users/template.hbs
{{#user-list users=users as |user|}}
  {{user-card user=user}}
{{/user-list}}
```

Now `{{user-card}}` has access to the current user, which changes as the `{{user-list}}` iterates over each user
since the `{{yield user}}` is inside the `{{each}}` block. Although this is nice, what if we used our component
without a block, i.e. `{{user-list users=users}}`? This would make the component pretty useless since nothing is yielding,
but the users are still being iterated. Let's mix in the `hasBlock` attribute and see if we can make it more useful.

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

This makes our component more useful due too it having sane defaults, but it also allows us to override those defaults. By using the `{{yield}}` helper we can pass
down our params for the consumer to utilize. This makes the concept of passing
data down very useful.

#### `{{component}}` Helper

To understand the `{{component}}` helper, we will add the following new features to the `{{user-list}}` component:

* For each type of user, we want to use different layout.
* The list can also be toggled between basic and detailed mode.

Let's add a new type of user, a superuser, which will have different behavior in our application. How would the consumer use this new data to show a different
UI for each type of user? This is where the `{{component}}` helper comes into play. This helper allows us to render a component chosen at runtime.

Since we have two user types, 'public' and 'superuser', and we want to render
a component for each type plus for the simple and detailed modes. We want to have the following component names:

* `basic-card-public`
* `basic-card-superuser`
* `detailed-card-public`
* `detailed-card-superuser`

We can create these names by using the `{{concat}}` helper in nested form.

```hbs
{{component (concat 'basic-card-' user.type)}}
```

Now that our component names are valid due to the use of dashed, we can put together the full template with the two modes.

```app/users/template.hbs
{{#user-list users=users as |user basicMode|}}
  {{#if basicMode}}
    {{component (concat 'basic-card-' user.type) user=user}}
  {{else}}
    {{component (concat 'detailed-card-' user.type) user=user}}
  {{/if}}
{{/user-list}}
```

Now we have `{{basic-card-superuser}}` and `{{detailed-card-public}}` rendering in their respective places
based on the `user.type` equaling 'superuser' and 'public' respectively. The use of the 'concat' helper in nested form allowed us to accomplish that. Nested helpers are evaluated before the parent helper
and then that helper can use the value returned by the nested helper. In this case it's just a concatenation of two values. You can
also experiment with other helpers in nested form, like the `if` helper.

Apart from the use of `{{component}}`, we also have the second yielded value for our basic/detailed mode feature,
which we've added to our yield, i.e. `{{yield user basic}}`.

The consumer can decide how to name the yielded values. We could have named our yields like so:

```app/users/template.hbs
{{#user-list users=users as |userModel isBasic|}}
  {{! something creative here }}
{{/user-list}}
```

### Actions Up

Now that we know how to send data down, we probably want to manipulate that data via some user interaction,
like changing a user's avatar. We can accomplish this by using actions.

We'll be looking at a `{{user-profile}}` component that implements a save action. By yielding the action as a parameter, we allow the consumer to use this component without having to know how to save, but still be able to trigger a save.

Here's our component's definition:

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
{{! most likely we have some markup here }}
{{yield profile (action "saveUser")}}
```

So now the consumer will have access to our data and the
action that we have defined. Lets see how this component could be consumed
in block form.

```app/templates/user/profile.hbs
{{#user-profile user=user as |profile saveUser|}}
  {{user-avatar url=profile.imageUrl onchange=saveUser}}
{{/user-profile}}
```

As you can see from this example, the consumer was able to hook into the
`{{user-profile}}` component's `saveUser` action, which we passed down
with the `(action "saveUser")` nested helper.  This helper reads a property off the actions hash of the current context, then creates a closure over that function and any arguments you pass.

We could also leverage this format to place actions on native HTML elements
like an input button:

```hbs
{{#user-profile user=user as |profile saveUser|}}
  <button type="button" onclick={{action saveUser}}>Save</button>
{{/user-profile}}
```

This is very useful since we are reusing the existing event hooks that
are provided on these elements. This makes components much more reusable due to how we can compose
small components that do one thing well.

### Wrapping Up

Composing components is all about isolating functionality into reusable
chunks that are easy to reason about and easy to combine together so that they work well together.
This also has the added benefit of easier testing since we try to stay away from
monolithic components that do everything, and can isolate small parts of functionality.
