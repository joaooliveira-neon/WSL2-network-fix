# Consertando Problema de Rede no WSL2

[Click here for English version](README.en.md)

## Problema

Se suas máquinas virtuais em WSL2 não conseguem se conectar à internet, pode ser por duas principais razões:

1. O DNS não está funcionando.
2. O WSL2 não consegue se conectar à internet de forma geral.

Para testar, dentro do WSL2 rode o comando `curl 142.250.189.206` (este é um IP de um servidor do Google). A saída deve ser algo como:

```
joao@NL-BD4V0N3:~$ curl 142.250.189.206
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

Se isso funcionou, o problema é com o DNS. Se não funcionou, o problema é com a rede em geral.

---

## Solução para problema de DNS

Caso o problema esteja no DNS, você pode precisar ajustar o arquivo `/etc/resolv.conf`. O `resolv.conf` que funciona para mim foi criado automaticamente pelo WSL2 e possui o seguinte conteúdo:

```
nameserver 10.255.255.254
search neon.local
```

Se não funcionar, aqui está outra configuração que foi gerada automaticamente para um colega aqui da Neon, essa também funciona:

```
nameserver 10.9.81.238
nameserver 10.9.50.245
nameserver 172.26.21.112
```

**Dica:**
Certifique-se de que o arquivo `/etc/wsl.conf` está configurado para evitar a substituição automática do `resolv.conf`. Adicione as seguintes linhas, se necessário:

```
[network]
generateResolvConf = false
```

Para mais informações, veja a [documentação oficial do WSL](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#wslconf).

---

## Solução para problemas de rede

Se nem o DNS nem o acesso direto via IP estiver funcionando, o problema pode estar relacionado à compatibilidade entre o WSL2 e algumas VPNs. Para resolver, você pode usar o projeto [wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit), que resolve esse problema criando uma interface de rede virtual dedicada para o WSL2.

### Como Configurar o wsl-vpnkit

1. **Baixe o arquivo mais recente:**
   Acesse a página de [lançamentos do wsl-vpnkit](https://github.com/sakai135/wsl-vpnkit/releases/latest) e baixe o arquivo `wsl-vpnkit.tar.gz`.

2. **Importe a distribuição do wsl-vpnkit:**
   Abra o terminal do PowerShell, vá até o diretório onde o arquivo `wsl-vpnkit.tar.gz` foi baixado e execute:
   ```
   wsl --import wsl-vpnkit --version 2 $env:USERPROFILE\wsl-vpnkit wsl-vpnkit.tar.gz
   ```
   Isso criará uma nova distribuição WSL2 chamada `wsl-vpnkit`.

3. **Inicie o wsl-vpnkit:**
   No terminal do PowerShell, execute:
   ```
   wsl.exe -d wsl-vpnkit --cd /app wsl-vpnkit
   ```
   O processo será iniciado e deve permanecer em execução. Você pode minimizar a janela ou rodar o processo em segundo plano.

4. **Teste a conexão:**
   Abra uma máquina WSL2 e verifique se a internet está funcionando.

---

## Observações

1. Estou utilizando o Ubuntu 24.04 no WSL2.
2. Meu arquivo `wsl.conf` está vazio (não precisei configurar nada nele).

## Sugestão

Faça um script ou algo do tipo pra rodar automaticamente quando alguma máquina WSL2 for iniciada.
