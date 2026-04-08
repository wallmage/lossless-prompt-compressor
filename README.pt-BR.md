# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

Comprime documentos de instrução para AI, arquivos de regras, documentos de planejamento e especificações de produto. Sem perder nenhum fato.

Isso é compressão, não resumo. Fatos relevantes para a máquina ficam; formatação desnecessária, redundâncias e conteúdo exclusivo para humanos são cortados. Opcionalmente, reescreve tudo em taquigrafia ultradensa exclusiva para AI.

## Modos

- **Strict Lossless** (padrão): Limpeza mecânica (Pass 1) + edições aprovadas que preservam fatos (Pass 2). Cada fato sobrevive. Continua legível por humanos.
- **AI-Lossless**: Adiciona o Pass 3. Remove tutoriais, coaching, cronogramas — tudo que só um humano leria.
- **AI-Only**: Adiciona o Pass 4. Reescrita completa em estilo telegráfico para máxima densidade.

## Fluxo de trabalho

| Pass | Tipo | Aprovação | Redução típica |
|------|------|-----------|---------------|
| 1 | Limpeza mecânica | Automática | 10-25% |
| 2 | Compressão com preservação de fatos | Por item | +5-15% |
| 3 | Remoção de conteúdo humano | Por item | +15-25% |
| 4 | Reescrita telegráfica | Opt-in explícito | +25-40% |

O Pass 1 tira a embalagem. O Pass 2 sela a vácuo. O Pass 3 descarta o manual de instruções que só um humano precisaria. O Pass 4 converte tudo em taquigrafia.

## Design

- Strict Lossless como padrão: compressão, não resumo
- Números de seção seguros: âncoras de referência cruzada nunca são renumeradas
- Controle por aprovação: toda edição não mecânica precisa de confirmação do usuário
- 23 técnicas numeradas com exemplos antes/depois em `references/techniques.md`
- 12 casos limite cobrindo desde prompts com muito código até documentos multilíngues

## Estrutura

```
lossless-prompt-compressor/
├── SKILL.md              — Manual de operação (~350 linhas)
└── references/
    └── techniques.md     — Catálogo completo de técnicas com exemplos
```

## Autor

[Wallny](https://github.com/wallmage)
