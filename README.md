# Ciclo de Sessão

Pacote com quatro skills que automatizam a rotina de toda sessão de desenvolvimento: abrir um projeto, trabalhar, registrar progresso e validar documentação. Funciona com Claude Code, OpenCode, Cursor e outros agentes de código.

> **Para que serve:** Manter o `PROGRESSO.md` e a documentação do projeto em dia entre sessões, automáticos, sem você ter que lembrar ou fazer à mão.

## O que cada skill faz

### 🟢 iniciar-sessao
**Quando:** abrir o agente num projeto sem saber onde parou

**O que faz:**
- Lê o estado atual (`PROGRESSO.md`, git, última migration)
- Mostra o que está em andamento, próximas prioridades (P0/P1/P2) e sugestão da próxima tarefa
- Avisa se documentação pode estar desatualizada
- Economiza tokens: só lê o necessário, não carrega tudo

**Exemplo:** você abre o Claude Code no projeto, digita "onde paramos?" e a skill retorna um briefing de 300 palavras com estado, pendências e o que fazer agora.

---

### 🔵 concluir-tarefa
**Quando:** terminou uma feature, correção, ajuste de fluxo, deploy ou qualquer trabalho

**O que faz:**
1. Valida build e testes do stack (npm, Python, Go, Rust, etc.)
2. Varre **toda** a documentação viva do projeto
3. Encontra trechos desatualizado (o que envelheceu, não só o que a tarefa tocou)
4. Move o item para ✅ Concluído no `PROGRESSO.md`
5. Abre desdobramentos automáticos em Próximos Passos ("qual era a próxima etapa?")
6. Propõe marco no `CHANGELOG.md` se houver efeito observável

**Resultado:** documentação sempre alinhada, nenhum achado perdido, próximas tarefas prontas pra iniciar.

---

### 🟡 salvar-contexto
**Quando:** vai parar no meio do trabalho, a conversa está longa ou antes de `/compact`

**O que faz:**
- Grava um checkpoint na seção "🔄 Em Andamento" do `PROGRESSO.md`
- Registra o que foi feito, o que ficou em meio, próximo passo e decisões pendentes
- Preserva a tarefa formal (não apaga)
- Próxima sessão retoma dali com contexto completo

**Resultado:** voltar daqui a dias ou semanas e saber exatamente onde parou, sem ler a conversa inteira.

---

### 🟣 revisar-contexto
**Quando:** fechou uma fase, antes de etapa grande, depois de dias parado ou desconfiado que docs estão desatualizado

**O que faz:**
- Confere coerência entre `CLAUDE.md`, `PROGRESSO.md`, `ROADMAP.md`, `CHANGELOG.md`
- Aponta divergências, lacunas e excesso
- Propõe itens novos em Próximos Passos só se forem trabalho real
- Não altera nada: só avisa o que precisa revisar

**Resultado:** achados baixos pendentes, conflitos de prioridade, docs obsoletas — tudo visível.

---

## Como funciona junto

**Rotina de uma sessão:**

```
1. Abre o agente no projeto
   → `/iniciar-sessao` (ou o agente dispara automaticamente)
   ↓
2. Trabalha a tarefa do dia

3. Terminou?
   → `/concluir-tarefa` (valida, atualiza docs, abre próximas)
   ↓
4. Próxima tarefa

5. Vai parar no meio da sessão?
   → `/salvar-contexto` (checkpoint com estado atual)
   ↓
6. Encerra

7. Próxima sessão
   → volta ao passo 1
```

**De vez em quando:**
- `/revisar-contexto` (ao fechar uma fase, antes de etapa grande)

As quatro se completam. Nenhuma substitui as outras.

---

## Instalação

Pré-requisito: [Node.js](https://nodejs.org) 18 ou superior (traz `npx`).

### Com npx (recomendado)

```bash
npx skills add nymbuslab/ciclo
```

O instalador pergunta em quais agentes instalar (Claude Code, OpenCode, Cursor, etc.) e se é global (todos os projetos) ou só este.

**Direto, sem perguntas:**

```bash
# Claude Code, todos os projetos
npx skills add nymbuslab/ciclo -g -a claude-code -y

# Claude Code e OpenCode ao mesmo tempo
npx skills add nymbuslab/ciclo -g -a claude-code -a opencode -y
```

| Opção | Efeito |
|---|---|
| `-g` | instala para o usuário (todos os projetos); sem ela, só este projeto |
| `-a <agente>` | escolhe o agente (`claude-code`, `opencode`, `cursor`...); pode repetir |
| `-y` | não faz perguntas |

### Manual

Copie a pasta `skills/` desta pasta para a pasta de skills do seu agente:

```bash
git clone https://github.com/nymbuslab/ciclo.git
cp -r ciclo/skills/* ~/.claude/skills/
```

| Agente | Pasta |
|---|---|
| Claude Code (todos os projetos) | `~/.claude/skills/` |
| Claude Code (só este projeto) | `.claude/skills/` |
| OpenCode | `~/.config/opencode/skills/` (também lê `~/.claude/skills/`) |
| Cursor | `~/.cursor/skills/` ou `~/.vscode/extensions/` |

Feche e abra o agente depois de instalar.

---

## Como usar

No agente, chame pelo nome ou com suas palavras:

```bash
/iniciar-sessao
/concluir-tarefa
/salvar-contexto
/revisar-contexto
```

Ou descreva:
- "onde paramos?" → `iniciar-sessao`
- "fecha essa tarefa" → `concluir-tarefa`
- "salva o contexto" → `salvar-contexto`
- "revisa os docs" → `revisar-contexto`

Cada skill pede confirmação antes de alterar arquivos.

---

## Exemplo: uma sessão completa

**Dia 1, manhã:**
```
$ claude
...
you: onde paramos?

[iniciar-sessao roda]
## Briefing de sessão

Projeto: Nymbus Pedidos
Stack: Node.js/Express + Postgres + HTML/CSS/JS
Branch: main — working tree limpo

### Em andamento
- Integração do PIX no checkout

### Pendências priorizadas
P0: Rollout desligável de Equipe
P1: Sprint 05 (Compras)

### Sugestão para iniciar
PIX (já 60% pronto) — a CNPJ e QR Code funcionam, falta o webhook.
```

**Dia 1, tarde (após 3 horas de código):**
```
you: salva o contexto, preciso parar

[salvar-contexto roda]
**Regras compartilhadas:** lidas
**Fechar:** Integração do PIX no checkout

**Checkpoint salvo em 2026-10-08 15:45**

### Feito nesta sessão
- Geração do QR Code dinâmico
- Teste do QR passando

### Em meio de edição
- Webhook de confirmação recebida, ainda sem validação de assinatura

### Próximo passo
- Validar assinatura e confirmar no banco de dados

### Documentação possivelmente afetada
- DEPLOY.md e .env.example: PIX pede env vars novas
```

**Dia 2, manhã:**
```
you: onde paramos?

[iniciar-sessao roda]
...mostra o checkpoint do dia anterior...

you: @tool salvar-contexto
[retoma dali: webhook incompleto, sabe exatamente o que falta]

you: [implementa a validação de assinatura + teste]

you: fecha essa tarefa

[concluir-tarefa roda]
✅ Build passou (npm run build)
✅ Testes passaram (npm test)
✅ Documentação viva (5 conferidos):
   - README.md: ok
   - DEPLOY.md: env vars atualizadas
   - CHANGELOG.md: marco adicionado

**Fechar:** Integração do PIX no checkout — 2026-10-08

**Próximos Passos novos abertos:**
- (P1) Integração do PIX — teste E2E entre restaurante e banco
- (P1) Rollout desligável de Equipe — era P0, agora PIX saiu
```

---

## Estrutura do pacote

```
ciclo/
├── README.md                    ← você está aqui
├── LICENSE                      ← MIT
├── skills/
│   ├── iniciar-sessao/
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   └── criar-arquivos-base.md
│   │   └── evals/
│   │       └── evals.json
│   ├── concluir-tarefa/
│   │   ├── SKILL.md
│   │   └── evals/
│   │       └── evals.json
│   ├── salvar-contexto/
│   │   ├── SKILL.md
│   │   └── evals/
│   │       └── evals.json
│   ├── revisar-contexto/
│   │   ├── SKILL.md
│   │   └── evals/
│   │       └── evals.json
│   └── _compartilhado/
│       └── docs-vivos.md        ← fonte única de regras (lido por todas as 4)
```

A pasta `_compartilhado` não é uma skill — é arquivo de referência que as quatro leem. Quando instalar com `npx`, ela vai para `~/.claude/skills/_compartilhado/`, no mesmo nível.

---

## Atualizar e remover

```bash
npx skills update ciclo
npx skills remove ciclo
```

---

## Limites e notas

- `iniciar-sessao` precisa de `PROGRESSO.md` no projeto. Se não houver, oferece criar um.
- `concluir-tarefa` roda build e testes do stack detectado. Se não tiverem testes, avisa.
- `salvar-contexto` grava só checkpoint, não altera PROGRESSO.md de verdade.
- `revisar-contexto` aponta problemas mas não corrige automaticamente.
- Todas as quatro respeitam o `CLAUDE.md` ou `AGENTS.md` local do projeto (leem antes de agir).

---

## Referência rápida

| Skill | Gatilho | Faz |
|---|---|---|
| `iniciar-sessao` | "onde paramos?", abrir sem contexto | briefing + estado + sugestão |
| `concluir-tarefa` | "fecha essa tarefa" | valida, atualiza docs, abre próximas |
| `salvar-contexto` | "salva o contexto", antes de sair | checkpoint em 🔄 Em Andamento |
| `revisar-contexto` | "revisa os docs", dúvida | aponta divergências em docs vivos |

---

## Licença

[MIT](LICENSE)

---

## Créditos

Desenvolvido por Pabllo Martins (nymbuslab), 2026.
