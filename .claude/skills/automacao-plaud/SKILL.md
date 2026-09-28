---
name: automacao-plaud
description: Rotina automática do escritório. Procura gravações novas no Plaud, analisa cada reunião, confere as falas técnicas do Leonardo, cria na Google Agenda a ata e os lembretes do que foi combinado e termina com um resumo curto para notificação. Use quando a automação agendada disparar ou quando o usuário pedir para processar agora as reuniões novas.
---

# Automação: reuniões novas do Plaud → conferência → agenda → resumo

A rotina agendada "Plaud: processar reuniões novas" roda este procedimento dentro da sessão "Automação Plaud", que tem acesso aos conectores do Plaud e da Google Agenda.

A rotina roda sozinha, sem ninguém acompanhando. Não faça perguntas: decida pelas regras abaixo e registre as dúvidas no resumo final. Responda em português do Brasil.

## Limites (valem sempre)

- Plaud: somente leitura.
- Google Agenda: **apenas criar** eventos. Nunca alterar nem apagar eventos existentes. Nunca adicionar convidados. Em todo evento: `visibility: "private"` e `notificationLevel: "NONE"`.
- Não enviar e-mails nem mensagens. Não alterar arquivos do repositório, não fazer commit nem push.
- Não inventar números, fatos ou datas. Transcrições erram números e nomes: na dúvida, sinalize.
- Não copiar para a agenda números de documentos (CPF, CNPJ), IDs de aparelhos, e-mails ou telefones citados na gravação.
- Se as ferramentas do Plaud ou da Google Agenda não estiverem disponíveis (carregue-as pelo ToolSearch), pare, avise pelo passo 6 e responda: "Automação Plaud: sem acesso ao <Plaud/Google Agenda>. Reconecte o conector em claude.ai/customize/connectors."

## 1. Encontrar gravações novas

- `list_files` com `date_from` = 7 dias atrás. Ignore as gravações de exemplo do Plaud ("Welcome to Plaud.ai", "How to use Plaud").
- `start_at` vem em UTC: converta para `America/Sao_Paulo` (UTC−3). Fim = início + `duration` (em milissegundos).
- Já processada? `list_events` no calendário principal com `fullText` = ID da gravação sem o prefixo `of_`, `startTime` = 1 dia antes do início e `endTime` = 1 dia depois. Se houver um evento "📝 Ata ·" com esse ID, pule a gravação.
- Sem transcrição ainda: não processe; anote como "aguardando transcrição". A próxima execução tenta de novo.
- Gravação curta (menos de 2 minutos, como uma nota de voz): trate como lembrete rápido. A ata pode ter uma linha só; se houver tarefa com data, crie o combinado.
- Gravações simultâneas ou sobrepostas podem cobrir o mesmo assunto: não duplique combinados entre elas.

## 2. Ler

- `get_transcript` com `limit` 500 (bloco padrão `transaction`, com falantes e tempos), seguindo `next_cursor` até o fim.
- `get_note`: resumo do Plaud, só como apoio. Se divergir da transcrição, vale a transcrição, e a divergência vira alerta.

## 3. Analisar

1. **Tipo:** reunião com cliente, reunião interna, atendimento de fornecedor/suporte ou outro. Se não for reunião com cliente, faça só um resumo curto e os combinados com data.
2. **Resumo:** até 6 linhas, com cliente, assunto, conclusão e números principais com `[mm:ss]`.
3. **Conferência das falas do Leonardo:**
   - Identifique as falas dele pelo nome do falante ou, se o rótulo for genérico, por quem explica as regras e orienta o cliente.
   - Liste as afirmações técnicas: regras, alíquotas, prazos, limites, cálculos e recomendações.
   - Classifique cada uma como `✅ consistente`, `⚠️ conferir` ou `❌ provável erro`, com uma linha de explicação e a fonte (dispositivo legal ou link confiável obtido com WebSearch).
   - Confira as contas (ex.: DAS ÷ faturamento = carga efetiva) e se o resumo do Plaud reproduz corretamente o que foi dito.
   - É apoio à revisão, não parecer: na dúvida, use ⚠️ e diga o que confirmar.
   - Considere a legislação vigente na data da reunião (Reforma Tributária do consumo: EC 132/2023 e LC 214/2025; lucros e dividendos e IRPF mínimo: Lei 15.270/2025) e confirme a vigência antes de afirmar.
4. **Combinados:** tarefa, responsável (escritório, cliente ou terceiro), data ou prazo citado e minuto.

## 4. Registrar na Google Agenda

Ordem obrigatória: a ata primeiro, porque é ela que marca a gravação como processada.

### 4.1 Ata (1 evento por gravação, calendário principal)

- Título: `📝 Ata · <cliente ou assunto>`.
- Horário da reunião, `timeZone` `America/Sao_Paulo`.
- `availability: AVAILABILITY_FREE`, `useDefaultReminders: false` e sem `overrideReminders` (sem alerta), `colorId: "8"`.
- Descrição em HTML simples: resumo, conferência das falas, combinados e eventos criados. Última linha: `plaud-id: <ID sem of_>`.

### 4.2 Combinados com data (1 evento por combinado, calendário principal)

- Antes de criar, procure com `list_events` (palavras-chave e nome do cliente, perto da data prevista) se já existe evento equivalente. Se existir, não duplique.
- Título: `<ação> · <cliente>`, por exemplo `Planejamento tributário 2º sem/2027 · Orquidário Lumani`.
- Datas:
  - dia e hora ditos: use-os, com 1 h de duração;
  - só o dia: 09:00–09:30;
  - só o mês ("em março"): primeiro dia útil do mês, 09:00–09:30, com "data sugerida pela automação" na descrição;
  - prazo ("até novembro"): lembrete no primeiro dia útil do mês do prazo, 09:00–09:30;
  - prazo relativo ("em 15 dias"): conte a partir da data da reunião.
- Considere os feriados nacionais ao escolher o dia útil. Não crie eventos em datas já passadas; registre-os no resumo.
- Lembretes (`overrideReminders`): popup 10080, popup 1440 e email 10080 minutos. Se o evento for em menos de 7 dias, use só popup 1440 (ou 60, se for no dia seguinte).
- `availability: AVAILABILITY_FREE` (lembrete não bloqueia a agenda) e `colorId: "10"`. Descrição: o que fazer, por quê, trecho `[mm:ss]` e, na última linha, `plaud-id: <ID sem of_>`.

### 4.3 Combinados sem data

Não crie evento. Liste no resumo como "sem data".

## 5. Resumo final

A última mensagem da execução fica registrada na sessão "Automação Plaud". Formato:

```
Plaud · N reunião(ões) nova(s)

1) <cliente/assunto> (<dd/mm>, <duração>)
<resumo em 2–3 linhas>
Falas: ✅ x · ⚠️ y · ❌ z. <alerta mais importante em 1 linha>
Agenda: <dd/mm/aaaa hh:mm> <título>; ...
Sem data: <itens>

Aguardando transcrição: <títulos>
Erros: <se houver>
```

Se não houver gravação nova nem pendente, responda apenas: `Nenhuma reunião nova.`

## 6. Aviso no celular

Só quando houver novidade (reunião processada, gravação aguardando transcrição ou erro):

1. Envie o resumo em uma linha, com até 200 caracteres, pelo `PushNotification`.
2. Se o `PushNotification` não for entregue, crie na Google Agenda um aviso `📬 Plaud · <resumo curto>`: começa daqui a 2 minutos e dura 15 minutos, `availability: AVAILABILITY_FREE`, `visibility: "private"`, sem convidados e com um único lembrete popup de 0 minuto. Na descrição, repita o resumo final.

Sem novidade, não avise.
