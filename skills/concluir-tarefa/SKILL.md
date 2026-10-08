---
name: concluir-tarefa
description: Fecha uma tarefa terminada. Valida build e testes, varre toda a documentação viva do repositório contra o estado real e corrige o que ficou falso, move o item para ✅ Concluído no PROGRESSO.md, abre os desdobramentos em Próximos Passos e propõe marco no CHANGELOG.md quando há efeito observável. Use ao terminar uma feature, correção, ajuste de fluxo, deploy, schema ou mudança de UI, ou quando o usuário pedir para finalizar, fechar, encerrar ou concluir a tarefa.
---

# concluir-tarefa

Fecha trabalho **terminado**. Trabalho em meio ou interrompido é da `salvar-contexto`.

**Passo 0, antes de qualquer outro:** abrir com a ferramenta de leitura `~/.claude/skills/_compartilhado/docs-vivos.md` (inteiro). Se não existir, avisar o usuário em uma linha ("arquivo compartilhado `_compartilhado/docs-vivos.md` não encontrado: sigo só com o que está nesta skill") e seguir com os passos abaixo, sem inventar as regras que estariam nele. **Esse aviso é obrigatório e vem na primeira linha da resposta final, antes de qualquer outro conteúdo**: sem ele, o usuário não sabe que a skill rodou incompleta.

Esta skill faz a **varredura completa** da documentação viva: não só o que a tarefa tocou, mas tudo que hoje é falso, inclusive o que envelheceu em tarefas anteriores. O usuário não deve precisar pedir "confere se os docs estão alinhados". Todo item que vai para ✅ Concluído passa por aqui; mover à mão pula a varredura.

## Progresso

```
- [ ] 0. docs-vivos.md lido (ou ausência avisada)
- [ ] 1. Validação (build/testes)
- [ ] 2. Validação visual, se UI
- [ ] 3. Item a fechar identificado
- [ ] 4. Bloco de conclusão montado
- [ ] 5. Varredura completa dos docs vivos
- [ ] 6. Desdobramentos extraídos
- [ ] 7. Preview aprovado
- [ ] 8. PROGRESSO.md gravado
- [ ] 9. CHANGELOG.md (se aprovado)
- [ ] 10. Correções de docs aplicadas e validadas
```

## 1. Validação

Detectar o stack pelo manifesto na raiz e rodar build e testes existentes:

| Manifesto | Comandos |
|---|---|
| `package.json` | script `build` e `test` com o gerenciador do lockfile (npm, pnpm, yarn) |
| `pyproject.toml` / `requirements.txt` | `pytest` se houver `tests/` ou configuração |
| `Cargo.toml` | `cargo build` e `cargo test` |
| `go.mod` | `go build ./...` e `go test ./...` |
| outro | `README.md`, `CLAUDE.md`/`AGENTS.md` ou perguntar ao usuário |

- Projeto sem testes: pular e informar.
- Suíte E2E pesada: só com aprovação.
- **Build ou teste falhou: parar, mostrar o erro e não mover o item.**

## 2. Validação visual, se for UI

Com ferramenta visual (Playwright, navegador): golden path e 1 ou 2 casos de borda, em desktop e celular. Sem ferramenta: o item leva a ressalva `(build OK, UI não validada)`. Não deixar a tarefa presa por falta de ferramenta.

## 3. Identificar o item a fechar

Na seção `🔄 Em Andamento` do `PROGRESSO.md` (ler conforme a seção 3 da referência):

- um item claro → propor fechá-lo;
- vários → perguntar qual;
- nenhum, mas há trabalho concluído na conversa → propor descrição curta;
- só um checkpoint genérico → perguntar o que de fato foi concluído.

## 4. Bloco de conclusão

Formato da seção 6 da referência: descrição observável, data `YYYY-MM-DD` e ressalva se houver.

## 5. Varredura completa

A pergunta é "o que está escrito em algum doc vivo que hoje é falso?", e não "o que esta tarefa mudou?".

1. Listar vivos e históricos pela seção 2 da referência. Ler **todos os vivos**: os pequenos inteiros, os grandes pela seção 3.
2. Montar o estado real (seção 4): `✅ Concluído` desde o último fechamento feito por esta skill (na dúvida, as duas últimas semanas), `📋 Próximos Passos`, `git log --oneline -20 --stat`, última migration do repositório e, se der para conferir só lendo, a última aplicada, código quando o doc afirma algo verificável.
3. Aplicar as 7 checagens da seção 4 e a matriz da seção 5, para a tarefa atual e para as recentes.
4. Montar a lista de correções: arquivo, trecho atual e trecho corrigido. Corrigir **só o que ficou falso**, sem reescrever o doc. O trecho novo diz só o que PROGRESSO, git ou código comprovam; em dúvida, perguntar. Manter `CLAUDE.md`/`AGENTS.md` enxuto, com o detalhe em `docs/`. Menção ao passado marcada como histórico pode ficar: registrar no preview que foi vista e mantida.

Repositório muito grande (mais de ~40 docs vivos): ler inteiros os que a matriz aponta para as mudanças recentes e, nos demais, só as frases de estado e índices (checagens 1 e 4). Dizer no preview quais foram lidos assim.

## 6. Desdobramentos

Aplicar "Fechar uma etapa é abrir a seguinte" (seção 6 da referência) ao bloco do passo 4. Cada achado vira item em `📋 Próximos Passos`, com prioridade e motivo de estar parado. Sem desdobramento: dizer isso no preview.

## 7. Preview e confirmação

Mostrar antes de gravar:

```markdown
**Regras compartilhadas:** <"lidas" ou "NÃO ENCONTRADAS: rodou só com o que está nesta skill">
**Fechar:** <item que sai de 🔄 Em Andamento>
**Concluído:** - [x] <descrição> — 2026-10-08
**Próximos Passos novos:**
- [ ] (P1) <item> — <por que está aberto>
(ou: nenhum desdobramento)
**CHANGELOG.md:** <texto proposto, ou "sem efeito observável">
**Documentação viva (N conferidos):**
- A corrigir: `README.md` — diz "falta integrar o PIX"; PROGRESSO registra a integração em 2026-10-02 → trocar por "PIX integrado"
- Conferido e sem mudança: `CLAUDE.md`, `DEPLOY.md`, `docs/modelo-dados.md`
- Histórico, fora da varredura: `CHANGELOG.md`, `docs/sprintx/**`
```

Preview sem a lista de docs conferidos está incompleto. Perguntar:

> Confirma a conclusão? Abro os itens de Próximos Passos, registro no `CHANGELOG.md` e aplico as correções de documentação?

## 8. Gravar no PROGRESSO.md

Após aprovação: tirar o item de `🔄 Em Andamento`, junto com o checkpoint da `salvar-contexto` se ele tratava só dessa tarefa (vazia → `_(nada no momento)_`), acrescentar ao fim de `✅ Concluído`, remover de `📋 Próximos Passos` se estava lá e acrescentar os itens do passo 6. Não mexer no resto.

## 9. CHANGELOG.md (opcional)

Só se o usuário aprovou, o marco tem efeito observável e o arquivo já existe. Entrada curta, com data, em linguagem do usuário final, sem hash, ID de migration ou lista de arquivos. Sem `CHANGELOG.md` no projeto: não criar; sugerir.

## 10. Aplicar e validar as correções de docs

Aplicar **todas** as correções aprovadas no passo 7, inclusive as de tarefas anteriores. Depois:

```bash
git diff --check
rg -n "<nomes antigos da mudança>" $(git ls-files '*.md' '*.mdx' '*privacidade*.html')
```

Ocorrência que sobrar de propósito (histórico) é registrada na resposta final.

## 11. Encerrar

> Tarefa movida para ✅ Concluído. Abertos em Próximos Passos: [itens ou "nenhum desdobramento"]. Documentação viva: N conferidos, M corrigidos ([lista curta]). [Marco no CHANGELOG.md.] Quer iniciar a próxima da lista?

## Limites

- Não fazer push nem rodar migrations.
- Não criar `CHANGELOG.md` nem inventar marco.
- Não mover item se a validação falhou, nem fechar item ambíguo sem perguntar.
- `ROADMAP.md` só muda quando uma linha de trabalho de produto foi entregue, cancelada, repriorizada ou criada.
- Não reorganizar o `PROGRESSO.md` inteiro nem reescrever documentos inteiros.
- Não deixar para "uma revisão depois" o que já está desatualizado.
