# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | Português

## Por que isso existe

Cada token no seu system prompt e um imposto que voce paga em toda chamada de API. Um arquivo de instrucoes de 35.000 palavras significa cerca de 48.000 tokens de contexto consumidos antes do modelo ler uma unica mensagem do usuario. Multiplique isso por milhares de requisicoes por dia e voce esta queimando dinheiro com formatacao, redundancia e prosa que so um humano precisaria ler.

Eu construi isso para resolver esse problema. Lossless Prompt Compressor pega documentos de instrucoes para AI -- system prompts, arquivos CLAUDE.md, regras de agentes, especificacoes de produto, planos mestres -- e comprime a fundo. Nao resume. Comprime. Cada fato, cada nome de campo, cada restricao, cada referencia cruzada sobrevive intacta. O que e cortado e a embalagem: formatacao Markdown que o modelo nunca precisou, declaracoes duplicadas, andaimes de tutoriais, preenchimento motivacional e tecido conectivo gramatical que custa tokens sem carregar informacao.

Em uma especificacao de produto tipica, voce obtem 50-65% de reducao ao longo dos quatro passes. Isso e dinheiro real de volta no seu bolso em cada chamada de API, e janela de contexto real liberada para a conversa que realmente importa.

Este compressor tambem e o motor do passo de compressao dentro do [vibecheck](https://github.com/wallmage/vibecheck).

## O que "lossless" realmente significa

Isso nao e um resumo. Um resumo decide o que e importante e descarta o resto. Isso e perigoso para arquivos de instrucoes -- falta uma restricao e o modelo alucina uma funcionalidade que voce explicitamente excluiu.

Lossless significa: se o documento original diz "limitar os subsidios do tier gratuito a 3% da receita mensal", a versao comprimida diz exatamente a mesma coisa. Nenhum fato e eliminado, reformulado de forma ambigua ou silenciosamente fundido. Os numeros de secao permanecem fixos para que referencias cruzadas ("ver S8.7.3") nunca quebrem. A AI produz decisoes de codigo e arquitetura identicas a partir da versao comprimida e da original.

A garantia e aplicada por um portal de aprovacao -- cada edicao nao mecanica e proposta com um diff antes/depois e so e aplicada apos sua aprovacao.

## 23 tecnicas em 4 passes

O compressor opera em quatro passes progressivos. Cada um vai mais fundo. Voce decide onde parar.

### Pass 1 -- Limpeza mecanica (automatica, 10-25% de reducao)

Sem julgamentos. Este passo remove formatacao que os LLMs parseiam de forma identica esteja ela presente ou nao:

- **Remocao de Markdown**: cabecalhos `##`, `**negrito**`, marcadores `- `, pipes de tabela `| |`, cercas de codigo ` ``` `, citacoes `> `, linhas `---` -- tudo removido. O texto e a estrutura ficam; a decoracao vai embora.
- **Conversao de formato**: tabelas viram linhas separadas por ponto e virgula. Listas com marcadores viram itens inline separados por ponto e virgula. Sintaxe de link `[texto](url)` e reduzida a URL.
- **Varredura de verificacao**: apos a remocao, uma varredura completa detecta residuos de Markdown. Cores hexadecimais (`#FF0000`), globs de regex (`*.txt`), pipes de shell e frontmatter YAML sao reconhecidos como conteudo e preservados.

Tecnicas 1-2 do catalogo. Seguro para executar em qualquer documento. Economia tipica: 10-25% de reducao de palavras.

### Pass 2 -- Compressao que preserva fatos (requer aprovacao, +5-15%)

Aqui fica criativo. Cada edicao proposta e mostrada com a economia estimada e so e aplicada apos aprovacao:

- **Deduplicacao por referencias cruzadas** (Tecnica 3): fatos declarados em tres lugares sao declarados uma vez; os demais sao substituidos por um ponteiro como "conforme S6".
- **Compressao de blocos de codigo** (Tecnica 4): definicoes de struct/class viram descricoes inline compactas preservando todos os nomes de campo, tipos e enums. Excecao: Python/YAML/Makefile permanecem intactos porque espaco em branco e semantico.
- **Compressao de config JSON** (Tecnica 5): exemplos JSON completos viram descricoes de schema.
- **Corte de detalhamentos por versao** (Tecnica 6): se a tabela resumo e o detalhamento por versao dizem a mesma coisa, o detalhamento e removido.
- **Compressao de blocos motivacionais** (Tecnica 7): secoes "por que isso importa" sao comprimidas ao insight tecnico essencial.
- **Compressao de justificativas de design** (Tecnica 8): quatro paragrafos de argumentacao viram uma frase com as razoes principais.
- **Compressao de listas de casos de uso** (Tecnica 9): casos de uso verbosos de multiplos paragrafos viram uma linha cada.
- **Compressao de mockups ASCII** (Tecnica 10): arte ASCII cara em tokens vira uma descricao textual compacta.

Cada edicao deve passar por validacao rigorosa: todos os fatos presentes, a AI tomaria decisoes identicas, sem ambiguidade, cada fato rastreavel ate sua secao canonica.

### Pass 3 -- Remocao de conteudo para humanos (requer aprovacao, +15-25%)

A pergunta para cada candidato: "Se eu remover isso, a AI produzira output pior?" Se nao, e um alvo.

- **Tutoriais para iniciantes** (Tecnica 11): "Instale sua IDE pela App Store. Leva 30-60 minutos..." vira uma lista do que instalar, nao como.
- **Exercicios praticos e atividades de aprendizado** (Tecnica 12): a AI nao precisa dos exercicios de aprendizado de outra pessoa.
- **Cronogramas dia a dia** (Tecnica 13): horarios de acordar e pausas para cafe sao removidos. Nomes de funcionalidades e requisitos tecnicos permanecem.
- **Coaching e conteudo motivacional** (Tecnica 14): "Nao entre em panico" e "Comemore!" carregam zero informacao para o modelo.
- **Matrizes de validacao** (Tecnica 15): uma tabela de 30 linhas onde cada funcionalidade diz "SHIP" vira duas linhas.
- **Tabelas comparativas com concorrentes** (Tecnica 16): "Ganhamos em todas as dimensoes" motiva humanos, nao modelos.
- **Exemplos de workflow para humanos** (Tecnica 17): walkthroughs de "como e o seu dia".

Pass 3 preserva o QUE e o POR QUE. Remove o COMO-para-humanos.

### Pass 4 -- Reescrita telegrafica (opt-in explicito, +25-40%)

Uma reescrita completa do documento em taquigrafia ultra-densa. Cada palavra deve carregar informacao; se existe apenas para fazer a prosa fluir bem para um leitor humano, e eliminada. Para documentos onde a AI e o unico consumidor.

- **Eliminacao de frases ponte** (Tecnica 18): "Isso significa que", "o que permite", "a fim de", "e importante notar que" -- tudo eliminado.
- **Fragmentos separados por ponto e virgula** (Tecnica 19): frases completas viram fragmentos chave-valor. Artigos, "e/sao", sujeitos de preenchimento eliminados.
- **Condensacao de multiplas frases** (Tecnica 20): cinco frases explicando um conceito viram o fato essencial.
- **Eliminacao de sujeitos implicitos** (Tecnica 21): quando o titulo da secao diz "Funcao de busca", nao precisa comecar cada frase com "A funcao de busca".
- **Notacao compacta** (Tecnica 22): barras para opcoes (`immediate/batched/silent`), setas para fluxos (`offline -> local queue -> sync`), dois pontos para relacoes (`Auth: JWT + refresh`).
- **Compressao de padroes estruturais repetitivos** (Tecnica 23): descrever o padrao uma vez, depois usar taquigrafia.

LLMs modernos parseiam o estilo telegrafico com total fidelidade. Ponto e virgula, setas, opcoes separadas por barras -- nenhuma familia de modelos tem problemas com isso.

## Modos

Tres modos, cada um construindo sobre o anterior:

- **Strict Lossless** (padrao): Pass 1 + Pass 2. Cada fato sobrevive. O documento continua legivel por humanos. Ideal para arquivos que tanto voce quanto a AI consultam -- CLAUDE.md, regras de agente compartilhadas, especificacoes ativas. Seguro, conservador, economia real.

- **AI-Lossless**: Adiciona Pass 3. Remove tutoriais, coaching, cronogramas diarios, matrizes de validacao -- tudo que so ajuda um leitor humano. A AI recebe exatamente o mesmo sinal. Ideal para system prompts e instrucoes de agentes que a AI consome mas que voce raramente rele.

- **AI-Only**: Adiciona Pass 4. Reescrita completa em estilo telegrafico. Densidade maxima, leitura nao agradavel. Ideal para prompts de producao de alto volume onde cada token economizado se multiplica por milhares de chamadas diarias.

## Fluxo de trabalho

| Pass | O que faz | Aprovacao | Reducao tipica |
|------|----------|-----------|---------------|
| 1 | Remove formatacao Markdown, converte tabelas/listas | Automatica | 10-25% |
| 2 | Deduplica fatos, comprime codigo/JSON/justificativas | Por item | +5-15% |
| 3 | Remove tutoriais, coaching, cronogramas, andaime humano | Por item | +15-25% |
| 4 | Reescrita telegrafica completa, fragmentos em vez de frases | Opt-in explicito | +25-40% |

Os quatro passes em uma especificacao de produto tipica: **50-65% de reducao**. Em um documento de 35.000 palavras, sao aproximadamente 25.000-32.000 tokens a menos por chamada de API.

## Principios de design

- **Lossless por padrao**: Strict Lossless e o modo padrao. Para compressao mais agressiva e necessario opt-in explicito.
- **Numeros de secao sao sagrados**: ancoras de referencia cruzada nunca sao renumeradas, mesmo quando secoes ao redor sao removidas. "Ver S8.7.3" sempre funciona.
- **Portal de aprovacao**: cada edicao nao mecanica e proposta com diff e economia estimada. Nada muda sem sua aprovacao.
- **23 tecnicas com exemplos**: catalogo completo antes/depois em `references/techniques.md`.
- **12 casos limite cobertos**: documentos curtos, prompts com muito codigo, entradas ja comprimidas, textos nao ingleses/CJK, URLs embutidas, documentos multilingues, documentos sobre Markdown, prosa criativa/legal, documentos muito grandes, e mais.

## Estrutura

```
lossless-prompt-compressor/
├── SKILL.md              — Manual de operacao (~350 linhas)
└── references/
    └── techniques.md     — Catalogo completo de tecnicas com exemplos antes/depois
```

## Autor

[Wallny](https://github.com/wallmage)
