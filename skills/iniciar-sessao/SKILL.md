---
name: iniciar-sessao
description: Monta um briefing curto para retomar o trabalho num projeto com PROGRESSO.md, gastando poucos tokens. Traz estado atual, pendências priorizadas, divergências entre git e documentação, docs vivos possivelmente desatualizados e sugestão de próxima tarefa. Use quando o usuário abrir a sessão sem dizer o que fazer, perguntar "onde paramos?", "o que falta?" ou pedir para retomar o projeto.
---

# iniciar-sessao

Setup e briefing. Não implementa nada, não roda build, testes ou lint e não altera arquivos sem aprovação.

**Passo 0, antes de qualquer outro:** abrir com a ferramenta de leitura `~/.claude/skills/_compartilhado/docs-vivos.md` (seções 1 a 5). Se não existir, avisar o usuário em uma linha ("arquivo compartilhado `_compartilhado/docs-vivos.md` não encontrado: sigo só com o que está nesta skill") e seguir com os passos abaixo, sem inventar as regras que estariam nele. **Esse aviso é obrigatório e vem na primeira linha da resposta final, antes de qualquer outro conteúdo**: sem ele, o usuário não sabe que a skill rodou incompleta.

Esta skill faz **checagem por risco**: aponta documentos que podem estar desatualizados, sem auditá-los. A varredura completa é da `concluir-tarefa`.

## Passos

1. **Arquivos de contexto:** verificar quais existem (seção 1 da referência).
   - Falta `CLAUDE.md`/`AGENTS.md` ou `PROGRESSO.md`: avisar, dizer em uma linha para que serve, propor criação seguindo [references/criar-arquivos-base.md](references/criar-arquivos-base.md) e esperar aprovação.
   - Falta `ROADMAP.md` ou `CHANGELOG.md`: só recomendar; não bloqueia o briefing.
2. **Estado barato:** comandos da seção 3 da referência.
3. **PROGRESSO.md:** ler conforme a seção 3. `🔄 Em Andamento` vazia com arquivos modificados no git é divergência. Arquivo modificado que nenhum item explica é **alerta importante**: o próximo agente não sabe se aquilo está em andamento, concluído ou abandonado.
4. **CLAUDE.md / AGENTS.md:** o da ferramenta em uso, conforme a seção 3.
5. **ROADMAP.md:** visão, objetivo atual, P0/P1 e fora de escopo. Orienta a sugestão, não substitui o PROGRESSO.
6. **CHANGELOG.md:** não ler, salvo se o PROGRESSO estiver vazio ou desatualizado, houver divergência com o git o usuário perguntar o que já foi entregue ou for preciso entender o último marco real. Nesse caso, só os últimos marcos.
7. **Migrations:** só os nomes das 3 últimas, sem ler o conteúdo:

   ```bash
   for d in supabase/migrations prisma/migrations alembic/versions; do [ -d "$d" ] && ls "$d" | sort | tail -3; done
   find . -path '*/migrations/*.py' ! -name '__init__.py' ! -path '*/node_modules/*' 2>/dev/null | sort | tail -3
   ```

   Ler conteúdo só se o PROGRESSO indicar trabalho ativo em banco, houver divergência clara, o usuário pedir ou for preciso gerar o `CLAUDE.md` inicial.
8. **Docs em risco:** com arquivos modificados ou commits recentes, usar a matriz da seção 5 da referência para **listar** os docs vivos que podem precisar de revisão e por quê. Ler só se a divergência parecer concreta.
9. **Briefing** no formato abaixo.

## Formato da resposta

Máximo de 400 palavras, salvo quando houver proposta de criação de arquivos.

```markdown
## Briefing de sessão

**Projeto:** <nome e uma linha>
**Stack:** <frase curta>
**Branch:** <nome> — <N arquivos modificados ou "working tree limpo">
**Último commit:** <hash curto e mensagem>
**PROGRESSO.md atualizado em:** <data via git>
**Regras compartilhadas:** <"lidas" ou "NÃO ENCONTRADAS: rodou só com o que está nesta skill">

### Estado atual
<1 ou 2 parágrafos curtos: o que parece pronto e o que está ativo.>

### Em andamento
<itens de 🔄 Em Andamento, ou "Nada em andamento.">

### Pendências priorizadas
<P0/P1/P2, até 3 por grupo; "+N outros" se houver mais.>

### Sugestão para iniciar
<o item recomendado e o motivo, em uma frase.>

### Regras críticas do projeto
<3 a 5 regras específicas do CLAUDE.md local. Nunca regras globais.>

### Divergências
<só se houver>

### Documentação possivelmente desatualizada
<só se houver: doc e motivo>

### Arquivos de contexto ausentes
<só se houver: se bloqueia ou é recomendação>
```

Exemplo de trecho:

```markdown
### Em andamento
- Integração do PIX no checkout (iniciado em 2026-10-06)

### Divergências
- `src/pagamento/pix.ts` e `src/pagamento/webhook.ts` modificados sem commit; o PROGRESSO não diz se o webhook está pronto.

### Documentação possivelmente desatualizada
- `DEPLOY.md`: o PIX pede env vars novas (`PIX_CHAVE`, `PIX_WEBHOOK_SECRET`) que o `.env.example` ainda não lista.
```

## Limites

- Não inventar informação que não está nos arquivos, no git ou na estrutura.
- Não ler secrets nem valores de `.env`.
- Não fazer push nem rodar migrations.
- Não consultar documentação externa.
- Informação ambígua: declarar a ambiguidade em vez de supor.
- Conflito entre `CLAUDE.md` local e global: o local prevalece; sinalizar.
