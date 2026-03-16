Não. **Container não é um SO completo.**

Ele usa o **kernel do host**. O que ele tem é um **filesystem isolado**.

Estrutura simplificada:

```
Host OS (kernel)

 └─ Docker
     └─ Container
         ├─ filesystem próprio (imagem Linux)
         ├─ processos isolados
         └─ rede isolada
```

Dentro do container parece um Linux:

```
/bin
/etc
/usr
/var
```

Mas isso é só **user space da imagem**.
O **kernel é sempre do host**.

Resumo:

> Container = processo isolado + filesystem próprio, usando o kernel do host.
