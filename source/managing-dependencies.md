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

If you need to use different assets in different environments, specify an object as the first parameter. That object's key should be the environment name, and the value should be the asset to use in that environment.

```ember-cli-build.js
app.import({
  development: 'bower_components/ember/ember.js',
  production:  'bower_components/ember/ember.prod.js'
});
```

If you need to import an asset in only one environment you can wrap `app.import` in an `if` statement.
For assets needed during testing, you should also use the `{type: 'test'}` option to make sure they
are available in test mode.

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

Provide the asset path as the first argument:

```ember-cli-build.js
app.import('bower_components/foundation/css/foundation.css');
```

All style assets added this way will be concatenated and output as `/assets/vendor.css`.

### Other Assets

All other assets like images or fonts can also be added via `import()`. By default, they
will be copied to `dist/` as they are.

```ember-cli-build.js
app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf');
```

This example would create the font file in `dist/font-awesome/fonts/fontawesome-webfont.ttf`.

You can also optionally tell `import()` to place the file at a different path.
The following example will copy the file to `dist/assets/fontawesome-webfont.ttf`.

```ember-cli-build.js
app.import('bower_components/font-awesome/fonts/fontawesome-webfont.ttf', {
  destDir: 'assets'
});
```

If you need to load certain dependencies before others, you can set the `prepend` property equal to `true` on the second argument of `import()`. This will prepend the dependency to the vendor file instead of appending it, which is the default behavior.

```ember-cli-build.js
app.import('bower_components/es5-shim/es5-shim.js', {
  type: 'vendor',
  prepend: true
});
```
