Dentro do **corpo de um e-mail**, o conteúdo normalmente é enviado em **MIME (Multipurpose Internet Mail Extensions)**.

Os tipos mais comuns são:

* **text/plain** → texto simples
* **text/html** → e-mail formatado (cores, imagens, links)
* **multipart/alternative** → contém versões *plain* e *HTML* do mesmo e-mail
* **multipart/mixed** → corpo do e-mail + anexos

Ou seja, o “arquivo” do corpo geralmente é **text/plain ou text/html** dentro de uma estrutura **MIME**.

------

O corpo do e-mail **não tem uma extensão própria**, porque ele faz parte da mensagem MIME.

Mas quando a mensagem é salva como arquivo, as extensões comuns são:

* **.eml** → formato padrão de e-mail (Outlook, Thunderbird, etc.)
* **.msg** → formato do Microsoft Outlook
* **.mbox** → formato usado por alguns clientes (Thunderbird, Apple Mail)

Ou seja, o corpo em si é **texto (plain ou HTML)** dentro de um arquivo **.eml / .msg / .mbox**.
