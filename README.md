# FORZY — PLN, Chatbots & Virtual Agents — Sprint 3 e 4

# Vídeo Yotube
https://youtu.be/-CT-UJQoxxA

## Equipe

- Geandro Dezordi — 562316
- Rickelmy Omine — 563286
- Roberto Eduardo — 564537
- Luccas Veronezi — 564202
- Davi Barboza — 564053

Solução completa de Processamento de Linguagem Natural para a plataforma Forzy, dividida em dois
notebooks Colab complementares, cobrindo os cinco entregáveis do projeto:

| Notebook | Entregáveis | Conteúdo |
|---|---|---|
| `sprint3_pln_alertas.ipynb` | 1 e 2 | Geração de resumos de alerta, classificação de eventos e relatório operacional |
| `sprint4_pln_rag.ipynb` | 3, 4 e 5 | Pipeline RAG, assistente conversacional de troubleshooting e avaliação |

## Como executar

1. Abra `sprint3_pln_alertas.ipynb` no Google Colab.
2. Faça upload de `History_3.csv` para o ambiente do Colab (ou deixe-o na mesma pasta do Drive).
3. Execute **Ambiente de execução → Executar tudo**. Ao final, o notebook gera dois arquivos na sessão:
   - `alertas_forzy_sprint3.csv` — episódios, resumos de alerta e classificação (artefato-ponte).
   - `relatorio_operacional_sprint3.txt` — relatório de estado operacional em texto.
4. Abra `sprint4_pln_rag.ipynb` **na mesma sessão/pasta** (para que ele encontre o artefato-ponte gerado
   no passo 3) e execute **Ambiente de execução → Executar tudo**.
   - Se quiser habilitar o modo de LLM real (em vez do modo offline/extrativo padrão), cadastre uma chave
     da Hugging Face Inference API no **Colab Secrets** (ícone 🔑 na barra lateral esquerda) com o nome
     `HF_TOKEN` — ou informe sua própria chave quando solicitado pelo `getpass()`. Sem chave cadastrada, o
     assistente funciona normalmente no modo offline/extrativo.

> **Execução independente:** `sprint4_pln_rag.ipynb` também pode ser executado sozinho, sem rodar a
> Sprint 3 antes. Se `alertas_forzy_sprint3.csv` não for encontrado na sessão, o notebook recalcula
> internamente uma versão condensada do pipeline de detecção de anomalias e geração de resumos, a partir
> de `History_3.csv`, e segue normalmente.

## Arquivo de dados necessário

- `History_3.csv` — histórico real de sensores IO-Link do sistema de monitoramento Forzy (dois pontos de
  medição; velocidade, aceleração e temperatura por ponto). Deve estar acessível no ambiente de execução
  (`/content/History_3*.csv` no Colab, ou na mesma pasta do notebook).

## Artefato-ponte entre os notebooks

Conforme o objetivo do projeto "a Sprint 4 constrói o assistente conversacional [...] com o contexto
operacional real gerado na etapa anterior", `sprint3_pln_alertas.ipynb` exporta seus resultados reais
(não apenas a descrição do processo) para que `sprint4_pln_rag.ipynb` os consuma de fato:

- **`alertas_forzy_sprint3.csv`**: episódio, horário, severidade, sensor responsável, estado operacional,
  score de anomalia, resumo de alerta gerado e categoria classificada usado como contexto operacional
  real injetado no assistente conversacional (Entregável 4) e nos três cenários de demonstração
  (Entregável 5).
- **`relatorio_operacional_sprint3.txt`**: o relatório de estado operacional em linguagem natural gerado
  na Sprint 3.

## Outros artefatos de teste exportados

- **`alertas_teste_referencia_rouge.csv`** (por `sprint3_pln_alertas.ipynb`): 6 alertas reais com resumo
  de referência escrito manualmente pela equipe, usado na avaliação ROUGE alerta a alerta.
- **`qa20_troubleshooting_referencia.csv`** / **`.json`** (por `sprint4_pln_rag.ipynb`): as 20 perguntas
  de troubleshooting com resposta e chunk(s) de referência (ground truth), usadas na avaliação de
  faithfulness, answer relevancy e context precision.

## Documentação complementar

- **`Requisitos.txt`** — requisitos funcionais e não-funcionais do projeto.
- **`Glossario.txt`** — termos técnicos usados nos notebooks e neste README.
- **`Corpus_Estruturado.txt`** — origem, composição e natureza (real/ilustrativa) de cada corpus textual
  usado no pipeline.
- **`Forzy_Relatorio_Operacional_PLN.docx`** — relatório com os resultados reais obtidos (métricas de
  classificação, ROUGE, Precision@k do RAG, métricas proxy de avaliação do assistente, cenários de
  demonstração e limitações).
- **`Forzy_Documento_Final_Unificado.docx`** — documento consolidado com templates de narrativa, critérios
  de classificação, arquitetura do assistente RAG, métricas obtidas e limites identificados.

## Transparência metodológica (RNF06)

A Forzy não disponibilizou manuais técnicos, datasheets ou logs de manutenção junto com `History_3.csv`.
Para viabilizar o pipeline RAG, a equipe escreveu um manual técnico ilustrativo (Seções 1 a 10), com
limiares numéricos calibrados a partir da distribuição estatística real dos dados, e posteriormente
incorporou uma folha de dados real do fabricante do motor monitorado (WEG, linha W22, código 13887610)
como Seção 11 a única seção com informação genuinamente fornecida por um fabricante. Toda decisão de
conteúdo não fundamentada diretamente em dados reais está explicitamente sinalizada nas células de
documentação dos notebooks. Os dois endpoints em tempo real (`get_s1`, `get_s2`) mencionados no enunciado
não estavam acessíveis durante o desenvolvimento; por isso, os notebooks trabalham exclusivamente com o
histórico estático `History_3.csv`.

## Segurança de credenciais (RNF08)

Nenhuma chave de API fica hardcoded em nenhum dos notebooks. A obtenção de credenciais para o modo de LLM
real do assistente conversacional segue a ordem: Colab Secrets (por usuário) → prompt interativo
(`getpass()`) → modo offline/extrativo — sem travar a execução caso nenhuma chave esteja disponível.
