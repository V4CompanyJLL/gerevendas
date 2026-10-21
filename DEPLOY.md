# Deploy do site Gere Vendas no EasyPanel

Instruções para o agente com acesso ao MCP do EasyPanel.

## O que é

Landing page estática do Gere Vendas: `index.html`, a pasta `assets/` (logos e favicon), `Dockerfile` (nginx 1.27-alpine) e `nginx.conf`.
O site escuta na porta **80** e responde `GET /healthz` com `ok`.

**Não alterar nenhum serviço existente do CRM** (app web, API, banco, workers).
Criar um serviço **novo** e separado apenas para o site.

## Passo a passo

1. Fazer commit da pasta `marketing/site/` e enviar para o GitHub, na branch usada pelo EasyPanel.
2. No EasyPanel, dentro do projeto do Gere Vendas, criar um novo serviço do tipo **App** chamado `site`.
3. Configurar a origem e o build:
   - **Source:** GitHub, no mesmo repositório e branch do passo 1.
   - **Build:** Dockerfile.
   - **Build path / context:** `marketing/site`.
   - **Dockerfile path:** `Dockerfile` (relativo ao contexto).
4. Fazer o deploy e conferir se o container fica saudável. Testar `/healthz` pela URL padrão do EasyPanel.
5. Configurar o domínio:
   - Na aba **Domains** do serviço `site`, adicionar `[DOMINIO]` e `www.[DOMINIO]`.
   - Usar porta **80**, path `/` e **HTTPS ligado** (Let's Encrypt).
   - Se o domínio principal estiver hoje apontado para o app do CRM, **confirmar com o Nathan antes de mover**.
     O app pode ficar em `app.[DOMINIO]`.
6. Configurar o DNS no provedor do domínio:
   - Registro `A` de `@` apontando para o IP do servidor do EasyPanel.
   - Registro `CNAME` de `www` apontando para `[DOMINIO]`.
   - Na Cloudflare, deixar o proxy desligado (nuvem cinza) até o certificado SSL ser emitido.
7. Validar:
   - `https://[DOMINIO]` carrega com cadeado.
   - Os botões levam aos destinos certos.
   - `/healthz` retorna `ok`.

## Links dos botões (preencher antes do deploy)

No fim do `index.html`, no objeto `CONFIG`:

| Chave | Uso | Exemplo |
|---|---|---|
| `signupUrl` | botão "Começar grátis" do fechamento | `https://app.[DOMINIO]/cadastro` |
| `loginUrl` | link "Entrar" no menu | `https://app.[DOMINIO]/login` |
| `whatsappUrl` | botão flutuante do WhatsApp | `https://wa.me/55DDDNUMERO?text=Quero%20conhecer%20o%20Gere%20Vendas` |
| `salesUrl` | links "Fale com vendas" | pode ser o mesmo WhatsApp |
| `demoUrl` | botão "Ver demonstração" | vídeo ou agenda |

Os demais botões "Começar grátis" rolam até a seção de fechamento da página.

## Pendências de conteúdo

- **Depoimentos:** estão com `[Nome do cliente]`, `[Cargo]`, `[Empresa]` e `[Depoimento...]`.
  Usar depoimentos reais ou remover a seção antes de divulgar.
- **Garantia:** o card diz "Garantia de [X] dias". Preencher com o prazo real ou remover.
