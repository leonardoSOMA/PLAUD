---
name: reuniao-plaud
description: Transforma uma gravação do Plaud (reunião com cliente ou reunião interna) em um kit de gestão para escritório contábil, com resumo executivo, ficha tributária do cliente, hipóteses de planejamento tributário a validar, documentos a solicitar, plano de ação 5W2H, riscos e rascunho de e-mail de follow-up. Use quando o usuário pedir para processar, resumir, analisar ou fazer a ata de uma reunião ou gravação do Plaud.
argument-hint: "[título, cliente ou data da gravação; vazio = a mais recente]"
---

# Kit de gestão a partir de uma reunião do Plaud

Pedido do usuário: $ARGUMENTS

Siga as regras de qualidade e de confidencialidade do `CLAUDE.md` do projeto.

## 1. Encontrar a gravação

1. Sem argumento: use a gravação mais recente (`list_files`).
2. Com argumento: busque por título, nome do cliente ou data (AAAA-MM-DD).
3. Mais de uma candidata: mostre até 5 (data, título, duração) e pergunte qual usar.
4. Se as ferramentas do Plaud não existirem nesta sessão, oriente a conexão em https://claude.ai/customize/connectors e a abertura de uma nova sessão, e ofereça usar uma transcrição exportada do app Plaud, anexada ou colada.

## 2. Ler o conteúdo

- `get_transcript` é a fonte principal (falantes e minutos).
- `get_note` (resumo do Plaud) serve só de apoio. Se divergir da transcrição, vale a transcrição, e a divergência deve ser apontada.
- Sem transcrição: pare e explique que é preciso transcrever no app Plaud, porque o conector é somente leitura.
- Falantes genéricos ("Speaker 1"): deduza o papel pelo contexto quando for óbvio (quem fala "o meu faturamento" é o cliente) e marque como dedução; senão, mantenha o rótulo.

## 3. Montar o kit (sempre nesta ordem)

### 3.1 Ficha da reunião

Data · duração · cliente/empresa · participantes e papel de cada um · tipo (diagnóstico, planejamento, fechamento, consultoria, interna).

### 3.2 Resumo executivo

Até 6 linhas: contexto, o que foi decidido, o que ficou pendente.

### 3.3 Ficha tributária do cliente

Tabela `Item | Informação | Status | Minuto`. Status: confirmado, estimativa ou a confirmar. Percorra os itens abaixo; o que não apareceu na reunião entra como "não abordado", o que já serve de pauta para a próxima conversa.

- Regime atual (MEI, Simples Nacional e anexo, Lucro Presumido, Lucro Real) e desde quando
- Atividades/CNAE, natureza (serviço, comércio, indústria), municípios e UFs de atuação
- Faturamento: RBT12, média mensal, sazonalidade, projeção
- Folha, pró-labore e Fator R (no Simples, anexos III e V)
- Sócios, participação e distribuição de lucros (valores por sócio e por mês)
- Margem, lucro contábil e escrituração (contabilidade completa ou não)
- Operações interestaduais, substituição tributária, DIFAL, importação e exportação
- Incentivos fiscais, créditos a recuperar, parcelamentos, débitos, certidões
- Contratos de longo prazo e formação de preço (impactos da transição para CBS/IBS)
- Grupo econômico, holding, imóveis, sucessão (se citados)

### 3.4 Hipóteses de planejamento tributário

Tabela `Hipótese | De onde surgiu (minuto) | Base legal (a confirmar) | Dados necessários | Risco | Próximo passo`.

- Tudo é hipótese a validar; nada de conclusão sem documentos.
- Quantifique apenas com números ditos na reunião e mostre a conta. Se faltar dado, diga qual.
- Quando o assunto aparecer, confira temas atuais: transição para CBS/IBS (EC 132/2023 e LC 214/2025), inclusive a possibilidade de optantes do Simples Nacional apurarem IBS/CBS pelo regime regular; retenção de IR sobre lucros e dividendos e IRPF mínimo (Lei 15.270/2025); Fator R; comparação entre regimes. Confirme a regra vigente antes de afirmar alíquota, prazo ou limite.
- Aponte o que traria risco de autuação (falta de propósito negocial ou de substância).

### 3.5 Documentos e informações a solicitar

Checklist `- [ ]` só com o necessário para validar as hipóteses (ex.: PGDAS-D dos últimos 12 meses, balancete e DRE, resumo da folha, contrato social, relação de notas fiscais).

### 3.6 Plano de ação 5W2H

Tabela `O quê | Por quê | Quem | Onde | Quando | Como | Quanto`.

- Quem: nome citado na reunião, ou "Escritório" / "Cliente".
- Quando: a data combinada na reunião. Se não houve, escreva "a definir" e não invente prazo.

### 3.7 Riscos e pontos de atenção

Prazos legais próximos, obrigações acessórias, exposição fiscal, dependências do cliente.

### 3.8 Rascunho de e-mail ao cliente

Assunto e corpo curto: agradecimento, resumo em linguagem simples, documentos pendentes, próximos passos e prazos. Sem juridiquês.

### 3.9 Trechos-chave

De 3 a 8 citações curtas com `[mm:ss]` que sustentam os números e as decisões.

## 4. Entrega

- Entregue o kit na conversa, em Markdown.
- No final, ofereça em uma linha, sem executar: criar o rascunho do e-mail no Gmail, marcar os prazos no Google Agenda, salvar o kit no Google Drive ou publicar uma página formatada.
- Execute só o que o usuário pedir. E-mail: apenas rascunho, nunca enviar.
- Não salve nada no repositório. Se precisar de arquivo local, use `privado/`.
