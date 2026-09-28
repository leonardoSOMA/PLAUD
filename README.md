# PLAUD × Claude

Configuração do escritório para usar as gravações do **Plaud** com o **Claude**: reuniões com clientes viram ata, ficha tributária, hipóteses de planejamento, plano de ação e e-mail de follow-up.

## 1. Conectar o Plaud ao Claude (uma vez, cerca de 2 minutos)

1. Abra **https://claude.ai/customize/connectors** (no app: *Personalizar → Conectores*).
2. Procure **Plaud** no diretório de conectores e clique em **Conectar**.
3. Entre com a sua conta Plaud (o mesmo login do app) e clique em **Autorizar**.
4. Abra uma conversa nova, ou uma **nova sessão** do Claude Code, para o conector carregar.

A conexão vale para o Claude na web, no desktop, no celular e no Claude Code. Não precisa de programação nem de chave de API.

**Teste:** peça *"Liste minhas 5 gravações mais recentes do Plaud."*

## 2. Antes de usar

- O conector é **somente leitura**: ele lê o que já está no app Plaud. Sincronize o gravador e gere a **transcrição em português** no app antes de pedir análises.
- Dê títulos padronizados às gravações, como `AAAA-MM-DD · Cliente · Assunto`. Assim o Claude encontra a reunião certa pelo nome.
- Renomeie os falantes no app (Speaker 1 → nome da pessoa) para a ata sair com os nomes certos.

## 3. Usar

**Neste repositório (Claude Code):**

```
/reuniao-plaud                        processa a gravação mais recente
/reuniao-plaud 2026-09-25 Padaria     processa uma gravação específica
```

O kit traz: ficha da reunião, resumo executivo, ficha tributária (com o minuto de cada informação), hipóteses de planejamento tributário a validar, documentos a solicitar, plano de ação 5W2H, riscos, rascunho de e-mail e trechos-chave. No final, o Claude oferece criar o rascunho no Gmail, marcar os prazos no Google Agenda ou salvar no Google Drive.

**Em qualquer conversa do Claude (web, desktop, celular)**, peça em linguagem natural:

- "Pegue a gravação mais recente do Plaud e faça a ata, as pendências com responsável e prazo e um e-mail de follow-up para o cliente."
- "Monte a ficha tributária do cliente a partir da reunião de diagnóstico de ontem e marque o que ainda precisa ser confirmado."
- "Liste as reuniões desta semana com cliente, assunto, pendências abertas e próximo passo."

## 4. LGPD e sigilo profissional

- Avise o cliente e registre a concordância dele antes de gravar.
- Mencione no contrato ou na política de privacidade do escritório o uso de gravações e de ferramentas de IA, inclusive a transferência internacional de dados (LGPD, art. 33).
- Segundo o Plaud, o servidor do conector fica nos EUA e processa os dados só durante a consulta, sem armazená-los.
- Se você usa um plano individual do Claude, confira em *Configurações → Privacidade* se as conversas podem ser usadas para melhorar os modelos e ajuste como preferir.
- O sigilo profissional (NBC PG 01) continua valendo: não compartilhe transcrições fora dos canais do escritório.
- Para revogar o acesso, desconecte o Plaud em *Conectores*.

## 5. Se algo não funcionar

| Sintoma | O que fazer |
|---|---|
| O Claude diz que não tem acesso ao Plaud | Confira se o conector está conectado e ativo na conversa. No Claude Code, abra uma nova sessão. |
| Não encontra a gravação | Verifique se ela foi sincronizada e transcrita no app Plaud; busque pelo título ou pela data. |
| Nomes trocados na ata | Renomeie os falantes no app Plaud e peça de novo. |
| Quer outro formato de resumo | Peça ao Claude para montar a partir da transcrição, ou gere no app Plaud com outro modelo. |
| Conta errada | Desconecte e conecte de novo em *Conectores*. |

## Estrutura do repositório

```
CLAUDE.md                        instruções do projeto para o Claude
.claude/settings.json            libera as ferramentas de leitura do Plaud sem pedir confirmação
.claude/skills/reuniao-plaud/    comando /reuniao-plaud (kit de gestão da reunião)
privado/                         pasta local ignorada pelo git; dados de clientes nunca são versionados
```

## Fontes

- [Plaud: integração com ChatGPT e Claude](https://www.plaud.ai/blogs/news/plaud-mcp-for-chatgpt-and-claude)
- [Plaud: central de ajuda, Plaud MCP](https://support.plaud.ai/hc/en-us/articles/57751078986265-Plaud-MCP)
- [Plaud: documentação do MCP](https://docs.plaud.ai/plaud-mcp-cli/mcp)
