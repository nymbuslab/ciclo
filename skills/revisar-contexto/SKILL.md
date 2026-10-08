---
name: revisar-contexto
description: Revisa a coerência entre os documentos de contexto do projeto (CLAUDE.md/AGENTS.md, PROGRESSO.md, ROADMAP.md, CHANGELOG.md, README.md, DEPLOY.md, PRD.md, docs/ e design/) e aponta divergências, lacunas e excesso, sem mexer em código. Use ocasionalmente, ao fechar uma fase, antes de uma etapa grande, depois de dias parado ou quando houver suspeita de documentação desatualizada. Não use no dia a dia nem para fechar tarefa.
---

# revisar-contexto

Revisão **ocasional** da documentação de contexto. Não audita código.

- Início de sessão → `iniciar-sessao`.
- Checkpoint → `salvar-contexto`.
- Fechar tarefa com varredura completa → `concluir-tarefa`.

**Passo 0, antes de qualquer outro:** abrir com a ferramenta de leitura `~/.claude/skills/_compartilhado/docs-vivos.md` (seções 1 a 5). Se não existir, avisar o usuário em uma linha ("arquivo compartilhado `_compartilhado/docs-vivos.md` não encontrado: sigo só com o que está nesta skill") e seguir com os passos abaixo, sem inventar as regras que estariam nele. **Esse aviso é obrigatório e vem na primeira linha da resposta final, antes de qualquer outro conteúdo**: sem ele, o usuário não sabe que a skill rodou incompleta.

Esta skill faz **checagem por risco**: lê os documentos de contexto principais e só lê os demais docs vivos quando a matriz (seção 5 da referência) ou os sinais do git apontam risco. A varredura completa de todos os docs vivos é da `concluir-tarefa`.

## Quando usar

- uma fase importante terminou;
- o projeto ficou dias ou semanas parado;
- `PROGRESSO.md` ou `ROADMAP.md` parecem não acompanhar o que foi feito;
- há entregas sem marco no `CHANGELOG.md`;
- o `CLAUDE.md`/`AGENTS.md` local cresceu demais ou repete regras globais;
- antes de uma etapa grande e arriscada.

## Progresso

```
- [ ] 0. docs-vivos.md lido (ou ausência avisada)
- [ ] 1. Estado barato coletado
- [ ] 2. Arquivos de contexto verificados
- [ ] 3. Leitura econômica feita
- [ ] 4. Coerência verificada
- [ ] 5. Achados classificados
- [ ] 6. Ajustes e itens de Próximos Passos propostos
```

## 1. Estado barato

Rodar os comandos da seção 3 da referência. Não rodar build, testes, lint, `git diff` completo, Context7 nem busca ampla sem termo concreto.

## 2. Arquivos de contexto

Verificar quais existem (papéis e mínimos na seção 1 da referência). Faltando arquivo: sinalizar como lacuna, dizer em uma linha para que serve e propor criação mínima, sem criar.

## 3. Leitura

Seguir a tabela da seção 3 da referência para `CLAUDE.md`/`AGENTS.md`, `PROGRESSO.md` e `ROADMAP.md`. **Nesta skill o `CHANGELOG.md` é sempre lido**, só os últimos marcos (`tail -100 CHANGELOG.md`), porque a checagem CHANGELOG × entregas depende dele. Demais docs vivos: listar pela seção 2 e ler só os que a matriz da seção 5 aponta para mudanças recentes, ou os que o usuário pediu.

## 4. Coerência

- **ROADMAP × PROGRESSO:** o objetivo atual aparece nos Próximos Passos? Há P0 fora da fase? Item do roadmap sem tarefa nenhuma? Tarefa em andamento sem relação com a direção?
- **PROGRESSO × git:** arquivos modificados batem com `🔄 Em Andamento`? Último commit indica entrega não registrada? Andamento ativo com working tree limpo?
- **CHANGELOG × entregas:** marcos entregues registrados e em linguagem observável.
- **Docs vivos:** as checagens da seção 4 da referência, com buscas `rg` por termos reais da mudança. Ocorrência em documento histórico não é erro.
- **CLAUDE.md local × global:** o local repete regra global? Falta regra específica, comando, stack ou decisão? Env var com valor? Contradição com o global: o local prevalece, mas sinalizar.

## 5. Classificar

- **Crítico:** pode causar implementação errada, perda de contexto ou risco operacional.
- **Importante:** atrapalha continuidade, priorização ou manutenção.
- **Leve:** organização, clareza ou limpeza.

Sem exagero: se está coerente, dizer que está coerente.

## 6. Propor

**Ajustes de texto:** arquivo, seção, motivo e mudança em linguagem simples. Nada é aplicado sem aprovação.

**Itens de Próximos Passos:** achado que é **trabalho pendente** (decisão não tomada, lacuna recorrente, item de roadmap sem tarefa, divergência git × PROGRESSO que exige investigação) vira item proposto, com prioridade, seguindo a seção 6 da referência. Texto desatualizado é ajuste, não item. Conferir duplicidade antes.

## Formato da resposta

Máximo de 600 palavras:

```markdown
## Revisão de contexto

**Branch:** <branch> · **Git:** <limpo ou N modificados>
**Encontrados:** <arquivos> · **Ausentes:** <lista ou "nenhum">
**Regras compartilhadas:** <"lidas" ou "NÃO ENCONTRADAS: rodou só com o que está nesta skill">

### Resultado geral
<1 parágrafo: coerente, parcialmente coerente ou desalinhado.>

### Achados
#### Crítico
- ... (ou "Nenhum.")
#### Importante
- ...
#### Leve
- ...

### Sugestões de ajuste
- `<arquivo>` — <mudança e motivo>.

### Itens propostos para Próximos Passos
- (P1) <item> — <motivo>.
(ou: "Nenhum achado desta revisão virou item novo.")

### Docs conferidos
- `<arquivo>` — ok / precisa ajuste / histórico intencional.

### Próximo passo recomendado
<uma frase.>
```

Exemplo de trecho preenchido:

```markdown
### Achados
#### Importante
- `ROADMAP.md` lista "App do garçom" como P0, mas nenhum item do `PROGRESSO.md` trata disso.
- `README.md` manda rodar `npm run seed`; o script foi removido do `package.json` em 2026-09-20.
#### Leve
- `CLAUDE.md` local repete a regra global de não commitar sem pedir.

### Itens propostos para Próximos Passos
- (P1) Decidir se o "App do garçom" continua P0 ou sai do roadmap — o roadmap e o progresso discordam desde a fase 2.
```

Encerrar perguntando:

> Quer que eu prepare os blocos de alteração e os itens de Próximos Passos para você aprovar?

Depois da aprovação, aplicar só os blocos aprovados.

## Limites

- Não implementar código nem transformar a revisão em refatoração.
- Não ler migrations inteiras nem rodar build, testes ou lint.
- Não atualizar `CHANGELOG.md` automaticamente nem mover tarefas sem confirmação.
- Não transformar todo achado em item de Próximos Passos.
- Não inventar contexto que não está nos arquivos ou no git.
