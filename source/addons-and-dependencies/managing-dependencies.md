Se você esta desenvolvendo seu aplicativo Ember, é provavel você encontrar um cenario que não faz parte do Ember, como autenticação, ou usar SASS para o css. Ember CLI nos fornece um formato padrão chamado [Ember Addons](#toc_addons) para distribuição e reuso de bibliotecas para resolver esses problemas. Além disso, talvez você precise adicionar mais alguma dependencia para o seu projeto, como por exemplo um framework CSS para um calendario que não é especifico do Ember. Ember CLI suporta a instalação desses pacotes pelo padrão [Bower package manager](#toc_bower). 

## Addons

Ember Addons são isntalados usando NPM (ex. `npm install --save-dev ember-cli-sass`). Addons
podem trazer outras dependencias modificando o arquivo `bower.json` automaticamente.

Você pode encontrar uma lista de addon em [Ember Observer](http://emberobserver.com).

## Bower

Ember CLI uses the [Bower](http://bower.io) como gerenciador de pacotes, fazendo ser facil 
você manter suas dependencias atualizadas. O arquivo de configuração do Bower, `bower.json`, esta localizado no
diretorio raiz da sua Aplicação Ember CLI, ele lista as dependencias para seu projeto. Executar `bower install` irá 
instalar todas as dependencias listadas no `bower.json`.

Ember CLI observa o `bower.json` por mudanças. Ele recarrega seu aplicativo se vocÊ 
instalar novas dependecias via `bower install <dependencies> --save`.

## Outros assets

Assets que não estiverem disponiveis como um addon ou pacote do Bower devem ser 
colocadas no diretorio `vendor` do seu projeto.

## Compilando os Assets

Quando você esta usando dependencias que não são incluidas em um addon, você
irá ter que configurar o Ember CLI para incluir seu assets na build. Isso é feito
usando um 'asset manifest', `ember-cli-build.js`. Você só deve tentar importar assets
localizados nas pastas `bower_components` and `vendor` folders.

### Javascript assets exportados como globais

Configurando um path do asset como primeiro e unico argumento.

```ember-cli-build.js
app.import('bower_components/moment/moment.js');
```

Fazendo isso você pode usa-los como globais na sua aplicação sem ter que ficar dando 
`import`. Você vai precisar adicionar `"moment": true` na sessão `prefef` no arquivo `.jshintrc`
para evitar que o JSHint fique acusando essas variaveis como `undefined`;

### Modulos AMD Javascript 

Configurar o path do asset como primeiro argumento, e uma lista dos modulos e exportações como segundo.

```ember-cli-build.js
app.import('bower_components/ic-ajax/dist/named-amd/main.js', {
  exports: {
    'ic-ajax': [
      'default',
      'defineFixture',
      'lookupFixture',
      'raw',
      'request',
    ]
  }
});
```

Você pode agora dar `import` neles no seu app. Por exemplo `import { raw as icAjaxRaw } from 'ic-ajax';`

### Assets Especificos para cada Ambiente

Se você precisa de um assets especifico para diferentes ambientes de desenvolvimento.
Especifique como objeto no primeiro parametro. A chave do objeto deve ser o nome do ambiente, 
e o seu valor deve ser qual asset usar nesse ambiente.

```ember-cli-build.js
app.import({
  development: 'bower_components/ember/ember.js',
  production:  'bower_components/ember/ember.prod.js'
});
```

Se você precisar importar um assets em apenas um ambiente você pode escrever `app.import` como se fosse um `if`.
Para assets necessarios durante o teste, você deve tambem usar a opção `{type: 'test'}` para ter certeza 
que eles estarão disponiveis no ambiente de teste.

```ember-cli-build.js
if (app.env === 'development') {
  // Only import when in development mode
  app.import('vendor/ember-renderspeed/ember-renderspeed.js');
}
if (app.env === 'test') {
  // Only import in test mode and place in test-supoprt.js
  app.import( app.bowerDirectory + '/sinonjs/sinon.js', { type: 'test' } );
  app.import( app.bowerDirectory + '/sinon-qunit/lib/sinon-qunit.js', { type: 'test' } );
}
```

### CSS

Coloque o asset como primeiro argumento:

```ember-cli-build.js
app.import('bower_components/foundation/css/foundation.css');
```

Todos os estilos adicionados dessa maneira serão concatenados como `/assets/vendor.css`.

### Other Assets

Todos os outros assets like imagens ou fontes tambem podem ser adicionados via `import()`. Por padrão eles serão ccompiados para
`dist/`.

```ember-cli-build.js
app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf');
```

Esse exemplo deve criar uma arquivo de font em `dist/font-awesome/fonts/fontawesome-webfont.ttf`.

Você tambem pode opcionalmente dizer `import()` para importar um arquivo que esteja em um path diferente.
O exemplo a seguir irá fazer uma copia desse arquivo para `dist/assets/fontawesome-webfont.ttf`.

```ember-cli-build.js
app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf', {
  destDir: 'assets'
});
```

Se você precisa carregar determinadas dependencias antes de outras, você pode adicionar a propriedade `prepend` para ser igual à `true` 
no segundo argumento do `import()`. Isso vai fazer a dependencia ser adiciona no começo, e não no fim do arquivo vendor. (que por padrão adiciona no fim).

```ember-cli-build.js
app.import('bower_components/es5-shim/es5-shim.js', {
  type: 'vendor',
  prepend: true
});
```
