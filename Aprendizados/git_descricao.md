No Git via linha de comando, o **commit pode ter título (subject) e descrição (body)**.

### Forma mais comum

Use **duas opções `-m`**:

```bash
git commit -m "Corrige validação de CPF no cadastro" \
           -m "Ajusta a função de validação para aceitar CPFs com máscara.
Também adiciona teste unitário para o caso de CPF com espaços."
```

Resultado no commit:

**Título**

```
Corrige validação de CPF no cadastro
```

**Descrição**

```
Ajusta a função de validação para aceitar CPFs com máscara.
Também adiciona teste unitário para o caso de CPF com espaços.
```

---

### Outra forma (abrindo editor)

Se usar:

```bash
git commit
```

O Git abre o editor padrão:

```
Corrige validação de CPF no cadastro

Ajusta a função de validação para aceitar CPFs com máscara.
Também adiciona teste unitário para o caso de CPF com espaços.
```

Regra padrão de commits:

* **primeira linha:** título
* **linha em branco**
* **resto:** descrição

---

### Padrão muito usado em projetos

```
git commit -m "feat: adiciona exportação CSV" \
           -m "Permite exportar relatórios financeiros em CSV.
Adiciona botão na tela de relatórios."
```


-------------------------------------------

Não. **A `\` não é obrigatória.**

Ela serve apenas para **quebrar a linha no terminal**, deixando o comando mais legível.

### Sem `\` (mesmo resultado)

```bash
git commit -m "Corrige validação de CPF no cadastro" -m "Ajusta a função para aceitar CPF com máscara."
```

### Com `\` (apenas estética)

```bash
git commit -m "Corrige validação de CPF no cadastro" \
           -m "Ajusta a função para aceitar CPF com máscara."
```

O Git interpreta **exatamente igual**.

### Regra simples

* `-m` → cada um adiciona **um parágrafo** na mensagem do commit.
* `\` → apenas **continuação de linha no shell** (bash/zsh).
