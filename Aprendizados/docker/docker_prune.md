Diferença direta:

**`docker image prune`**
→ remove **apenas imagens “dangling”**
(imagens sem tag, tipo `<none>:<none>`, geralmente sobras de build)

**`docker image prune -a`**
→ remove **todas as imagens não utilizadas por nenhum container**
(inclui as dangling + imagens com nome/tag que não estão em uso)

Resumo prático:

* `prune` → limpeza leve (só lixo de build)
* `prune -a` → limpeza pesada (apaga tudo que não está sendo usado)
