(Sobre a impossibilidade de deletar um volume - como o do node_modules, que fica preso a um container órfão)

Porque o Docker Desktop não é uma visão “forense” perfeita do estado interno do Docker.

O que costuma acontecer nesses casos:
- o container existe só como referência interna, parado ou quebrada
- ele foi recriado/substituído muito rápido e a UI não atualizou direito
- ele está fora do grupo visual esperado do Compose
- a interface mostra melhor containers “normais” e ativos do que restos órfãos ligados a volume
- às vezes o volume fica preso por metadata do daemon antes da UI refletir isso claramente

Em outras palavras: o Docker Desktop mostra uma visão amigável, mas o daemon do Docker é a fonte real de verdade. Se o daemon respondeu:

```text
volume is in use - [52a0...]
```

então havia sim um container referenciando esse volume, mesmo que a interface não exibisse isso.

Por isso, para diagnosticar volume preso, o mais confiável é sempre CLI:
```powershell
docker ps -a
docker inspect ID
docker volume inspect NOME_DO_VOLUME
```

Resumo curto:
- não apareceu no Docker Desktop porque a UI pode ocultar, agrupar mal ou não atualizar restos órfãos
- o daemon ainda mantinha a referência
- isso é relativamente comum com containers recriados e volumes anônimos/nomeados em ciclos de `up/down/build`

Se quiser, eu posso te passar um comando que mostra exatamente quais containers estão presos a um volume específico.