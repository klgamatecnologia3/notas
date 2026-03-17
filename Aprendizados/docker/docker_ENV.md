* `ENV` = variável padrão da imagem (existe em runtime)
* Pode substituir `.env` dentro do container

```dockerfile
ENV NODE_ENV=production
```

```bash
docker run meu-app                 # usa NODE_ENV=production
docker run -e NODE_ENV=dev meu-app # sobrescreve → NODE_ENV=dev
```

* Sem `ENV` (sem default):

```dockerfile
# sem ENV → depende de -e ou .env
```

* ⚠️ `-e` sempre sobrescreve `ENV`
* ✔ Use `ENV` para defaults e `-e` para configuração dinâmica


---

Runtime é o momento em que o container está **rodando de fato**.

👉 Build = criação da imagem
👉 Runtime = execução do container (`docker run` / `compose up`)
