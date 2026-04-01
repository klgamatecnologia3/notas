```js
// Vue
const loading = ref(false)
loading.value = true
```

Funciona porque `ref()` cria um valor reativo do Vue, e o Vue acompanha mudanças em `.value`.

```js
// JS puro
const loading = { value: false }
loading.value = true
```

Não vira reativo no Vue só por ter `.value`; é apenas um objeto comum do JavaScript.