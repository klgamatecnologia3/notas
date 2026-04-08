**Comparação com React:**

* Vue → separa HTML + JS + CSS (Single File Component)
* React → tudo em JS (JSX)

👉 Vue é mais “HTML first”
👉 React é mais “JavaScript first”

---

**Vue:**

* Usa **Proxy (Vue 3)** para tornar dados reativos
* `ref()` e `reactive()`

**React:**

* Usa `useState` / `useReducer`

**Diferença chave:**

* Vue detecta mudanças automaticamente
* React precisa de setState manual

👉 Em Vue:

```js
count.value++
```

👉 Em React:

```js
setCount(count + 1)
```

---

Ambos usam Virtual DOM.

Mas:

Vue → mais otimizações automáticas (tracking fino)
React → re-render mais amplo (depende de memo/useMemo)

👉 Vue geralmente atualiza só o necessário
👉 React exige otimização manual em muitos casos

---

Vue:

Props (entrada)
Emits (saída)

React:

Props (entrada)
Callback functions (saída)

👉 Equivalência:

emit (Vue) ≈ função passada por props (React)

---

Regra simples:

Quem usa outro componente → é o pai
Quem é usado → é o filho

👉 Comunicação:

Pai → Filho → props
Filho → Pai → emit

---

Vue tem diretivas nativas:

v-if
v-for
v-model
v-bind
v-on

React:

Tudo é JS puro

👉 Vue:

<div v-if="show">Oi</div>

👉 React:

{show && <div>Oi</div>}

---

Dica da imagem: Não use v-if com v-for. Ex: 

    <li v-for="todo in todos" v-if="!todo.isComplete">

NÃO usar.

---

    v-on:submit.prevent="onSubmit"

    v-on é o Name.

        Começa com v- 
        Pode ser omitido quando usado shorthand

    submit é o Argument.

        Segue os : (dois pontos) ou símbolo shorthand.

    .prevent é um Modifier.

        Percebibo pelo . (ponto final) inicial.

    onSubmit é o Value.

        Interpretado como expressão JavaScript.

---

Two-way binding (v-model)

Vue tem nativo:

<input v-model="name">

React:

Sempre manual (controlled components)

👉 Vue simplifica formulários MUITO

---

Vue moderno usa:

setup()
ref, computed, watch

React:

Hooks (useState, useEffect)

👉 Equivalência direta:

ref ≈ useState
watch ≈ useEffect
computed ≈ useMemo

---

Computed vs Watch

Vue tem dois conceitos fortes:

computed → valor derivado (cache automático)
watch → efeito colateral

👉 React mistura isso com:

useMemo
useEffect

👉 Vue separa melhor responsabilidades

---

Ciclo de vida

Vue:

onMounted
onUpdated
onUnmounted

React:

useEffect

👉 Vue tem lifecycle mais explícito

---

Vue costuma ter:

/components
/views
/composables
/router
/store

👉 Muito parecido com React moderno

---

Roteamento
Vue usa: Vue Router
React usa: React Router

👉 Conceito igual:

rotas → componentes

---

Gerenciamento de estado
Vue: Pinia (moderno) / Vuex (antigo)
React: Context API / Redux / Zustand

👉 Pinia é MUITO mais simples que Redux

---

13. Performance (diferença prática)
Vue → mais otimizações automáticas
React → mais controle manual

👉 Resultado:

Vue = mais produtivo rápido
React = mais flexível em escala grande

---

🧭 Resumo direto (pra fixar)

    Conceito	    Vue	                    React
    
    Sintaxe	        HTML-like	            JS (JSX)
    Estado	        reativo automático	    manual
    Formulário	    fácil (v-model)	        verboso
    Hooks	        Composition API	        Hooks
    Performance	    automática	            manual
    Curva	        mais fácil	            mais técnica

🚀 Ordem ideal de estudo

    Fundamentos + template
    Reatividade (ref, reactive)
    Diretivas
    Componentes (props/emits)
    Composition API
    Computed + Watch
    Router
    Pinia