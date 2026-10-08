---
name: salvar-contexto
description: Grava um checkpoint leve da sessão na seção 🔄 Em Andamento do PROGRESSO.md, com o que foi feito, o que ficou em meio, o próximo passo e as decisões pendentes, para a próxima sessão retomar dali. Use antes de encerrar a sessão, antes de /compact, quando a conversa estiver longa ou quando o usuário pedir para salvar o contexto, salvar o progresso ou registrar onde parou.
---

# salvar-contexto

Checkpoint leve de trabalho **em meio** ou interrompido. Não fecha tarefa (isso é da `concluir-tarefa`), não valida build, não mexe em `CHANGELOG.md` nem em `ROADMAP.md` e não cria arquivos.

**Passo 0, antes de qualquer outro:** abrir com a ferramenta de leitura `~/.claude/skills/_compartilhado/docs-vivos.md` (seções 3, 5 e 6). Se não existir, avisar o usuário em uma linha ("arquivo compartilhado `_compartilhado/docs-vivos.md` não encontrado: sigo só com o que está nesta skill") e seguir com os passos abaixo, sem inventar as regras que estariam nele. **Esse aviso é obrigatório e vem na primeira linha da resposta final, antes de qualquer outro conteúdo**: sem ele, o usuário não sabe que a skill rodou incompleta.

## Passos

### 1. Estado barato

```bash
git status --short
git diff --stat
git log --oneline -5
```

Sem repositório git ou sem `PROGRESSO.md`: avisar em uma linha e terminar. Criar arquivos base é da `iniciar-sessao`.

`git diff` completo só se for indispensável para não registrar algo errado, e só no trecho necessário.

### 2. Ler o PROGRESSO.md

Conforme a seção 3 da referência. Guardar os itens que já estão em `🔄 Em Andamento`.

### 3. Sintetizar a sessão

A partir da conversa real, sem inventar:

- **Feito nesta sessão:** o que foi aprovado, implementado e validado (1 a 5 bullets).
- **Em meio de edição:** iniciado e não concluído, sem validação ou com dúvida. Sem certeza de validação, vai aqui e não em "feito".
- **Próximo passo:** uma frase específica.
- **Decisões pendentes:** perguntas sem resposta, trade-offs em aberto.
- **Documentação possivelmente afetada:** pela matriz da seção 5 da referência, os docs que o fechamento vai precisar revisar. Só registrar o risco; não auditar.

Cruzar com o `git status`: arquivo modificado sem commit é pista forte de trabalho em andamento; arquivo novo relevante é citado.

### 4. Montar o bloco

```markdown
## 🔄 Em Andamento

- [ ] <item formal que já estava aqui, preservado>

**Checkpoint salvo em YYYY-MM-DD HH:MM**

### Feito nesta sessão
- ...

### Em meio de edição
- ...

### Próximo passo
- ...

### Decisões pendentes
- ...

### Documentação possivelmente afetada
- ...
```

- **Preservar** os itens formais que já estavam em `🔄 Em Andamento`, acima do checkpoint.
- Se havia um checkpoint anterior, **substituí-lo** pelo novo (só um checkpoint por vez).
- Omitir seções vazias. Nada feito: não inventar entrega.
- Sem próximo passo claro: `- Revisar o PROGRESSO.md e confirmar a próxima prioridade com o usuário.`
- Preferir referência específica: arquivo, função, tela, fluxo.

Exemplo:

```markdown
## 🔄 Em Andamento

- [ ] Integração do PIX no checkout

**Checkpoint salvo em 2026-10-08 18:40**

### Feito nesta sessão
- Geração do QR Code do PIX em `src/pagamento/pix.ts`, com teste passando.

### Em meio de edição
- Webhook de confirmação em `src/pagamento/webhook.ts`: recebe o evento, ainda não valida a assinatura.

### Próximo passo
- Validar a assinatura do webhook com `PIX_WEBHOOK_SECRET` e escrever o teste de assinatura inválida.

### Documentação possivelmente afetada
- `DEPLOY.md` e `.env.example`: env vars novas do PIX.
```

### 5. Preview

Mostrar, nesta ordem: a linha **Regras compartilhadas:** <"lidas" ou "NÃO ENCONTRADAS: rodou só com o que está nesta skill">, o bloco e a pergunta:

> Esse é o checkpoint que vou gravar em `PROGRESSO.md`. Confirma?

Ajustes pedidos: refazer e mostrar de novo. Ler e coletar não precisa de aprovação; gravar precisa.

### 6. Gravar

Substituir só a seção `## 🔄 Em Andamento` pelo bloco aprovado. Se ela não existir, inserir logo após o título principal. Não tocar nas outras seções.

### 7. Encerrar

> Checkpoint salvo. Na próxima sessão, rode `iniciar-sessao` para retomar daqui.

## Limites

- Não fazer push. Não rodar build, testes, lint, scripts de banco ou migrations, nem consultar Context7.
- Não registrar histórico antigo: só o estado útil para retomar.
- Não registrar como concluído o que não foi validado.
- Não reorganizar o PROGRESSO.md nem sincronizar outros docs: só registrar o risco, que a `concluir-tarefa` ou a `revisar-contexto` resolvem.
- Não atualizar release notes nem comunicação ao usuário final.
