# Documentos vivos e PROGRESSO.md

Fonte única usada por `iniciar-sessao`, `salvar-contexto`, `concluir-tarefa` e `revisar-contexto`. Cada skill diz quais seções ler. Esta pasta não é uma skill.

Os comandos estão em sintaxe Bash (Git Bash no Windows). Em PowerShell, use o equivalente.

## Conteúdo

1. Papéis dos documentos
2. Vivos e históricos
3. Leitura econômica
4. Checagens de documento desatualizado
5. Matriz: o que mudou → o que revisar
6. Regras do PROGRESSO.md

---

## 1. Papéis dos documentos

| Documento | Papel | Tempo |
|---|---|---|
| `CLAUDE.md` / `AGENTS.md` | regras e contexto específicos do projeto: stack, comandos, padrões, decisões de arquitetura, índice de `docs/`. Nunca repete regra global | permanente |
| `PROGRESSO.md` | o que está em andamento, próximos passos e concluídos operacionais | presente |
| `ROADMAP.md` | direção, fases, prioridades e escopo planejado | futuro |
| `CHANGELOG.md` | marcos entregues com efeito observável, em linguagem do usuário final | passado |
| `README.md` | entrada operacional: setup, comandos, testes, visão rápida | permanente |
| `DEPLOY.md` | infraestrutura, env vars (só nomes), secrets, domínios, deploy | permanente |
| `PRD.md` | escopo de produto atual e fora de escopo | permanente |
| `docs/**`, `design/**` | documentação viva por área: arquitetura, fluxos, modelo de dados, UI | permanente |

Mínimo para continuidade: `CLAUDE.md` ou `AGENTS.md`, e `PROGRESSO.md`. Recomendados: `ROADMAP.md`, `CHANGELOG.md`, `README.md`, `DEPLOY.md`, `PRD.md`. `CLAUDE.md` e `AGENTS.md`: usar o da ferramenta em uso (Claude Code → `CLAUDE.md`, Codex → `AGENTS.md`); se os dois existirem e divergirem, sinalizar.

## 2. Vivos e históricos

Descobrir pela lista do git, nunca por memória:

```bash
git ls-files '*.md' '*.mdx' '*privacidade*.html'
```

- **Vivos** (ensinam o estado atual ou guiam a próxima tarefa): `CLAUDE.md`, `AGENTS.md`, `README.md`, `DEPLOY.md`, `PRD.md`, `ROADMAP.md`, `PROGRESSO.md`, `docs/**` exceto pastas históricas, `design/**`, documentação de LGPD e política de privacidade pública, manuais de instalação, `.env.example`.
- **Históricos** (registram o passado; não se corrige história): `CHANGELOG.md`, planos e artefatos de método (`docs/sprintx/**`, `docs/manutencao/**`, `docs/relatorios/**`, `docs/superpowers/**`), specs e relatórios com data no nome, skills de terceiros versionadas no repositório.

Na dúvida, um documento com data no nome ou que descreve "o que foi feito" é histórico; um que descreve "como é" ou "como fazer" é vivo. Menção ao passado dentro de doc vivo pode ficar se estiver marcada como histórico ("removido em <data>").

## 3. Leitura econômica

| Arquivo | Até 250 linhas | Acima de 250 linhas |
|---|---|---|
| `PROGRESSO.md` | inteiro | `🔄 Em Andamento` inteira, os 10 primeiros itens de `📋 Próximos Passos`, as últimas 20 linhas de `✅ Concluído` |
| `CLAUDE.md` / `AGENTS.md` | inteiro | seções de stack, env vars, estrutura, schema, integrações, padrões, regras de desenvolvimento, comandos, skills do projeto, Context7 e decisões de arquitetura |
| `ROADMAP.md` | inteiro | visão, objetivo atual, fases, prioridades P0/P1, fora de escopo, decisões de produto |
| `CHANGELOG.md` | não ler por padrão | quando precisar, só os últimos marcos: `tail -100 CHANGELOG.md` |
| demais docs vivos | inteiro | seções de estado, índice e as que tratam da área mudada |

Comandos baratos de estado:

```bash
git status --short
git log --oneline -5
git branch --show-current
git log -1 --format=%cd --date=short PROGRESSO.md
wc -l CLAUDE.md AGENTS.md PROGRESSO.md ROADMAP.md CHANGELOG.md README.md DEPLOY.md PRD.md 2>/dev/null
```

Fora de repositório git: seguir com os arquivos existentes e avisar que o histórico não pôde ser usado.

Não fazer por padrão: `git diff` completo, leitura de migrations, leitura de `.env` com valores, busca ampla sem termo concreto.

## 4. Checagens de documento desatualizado

Referência do estado real, nesta ordem de confiança: `PROGRESSO.md` (o mais recente) → `git log` e arquivos tocados → código (rota, função, tabela, comando, env var) → nomes das migrations. `README.md`, `ROADMAP.md` e textos longos em `docs/` costumam ficar para trás: nunca usá-los como verdade sem cruzar.

Para cada doc vivo:

1. **Frases de estado que envelhecem:** "estado atual", "onde estamos", "pendente", "falta", "ainda não", "em breve", "próximo passo", "conferido em", contagens (N testes, N funções, N clientes), "última migration". Se o PROGRESSO diz que aconteceu, o doc não pode dizer que falta.
2. **Resumo × texto completo:** quando `CLAUDE.md`/`AGENTS.md` resume e aponta para `docs/`, os dois dizem a mesma coisa.
3. **Nomes que mudaram:** rota, tela, componente, função, tabela, coluna, env var ou comando renomeado ou removido. Buscar o nome antigo em todos os vivos: `rg -n "<nome antigo>" $(git ls-files '*.md')`.
4. **Índices e mapas:** árvore de pastas e índice de docs do `CLAUDE.md`, lista de documentação do `README.md`. Arquivo novo sem entrada, entrada para arquivo apagado.
5. **Roadmap:** linha de trabalho entregue, cancelada ou criada refletida nas prioridades; item concluído não segue listado como prioridade.
6. **CLAUDE.md local:** só contexto do projeto, sem repetir regra global; env vars só com nome e descrição, nunca valor.
7. **Changelog:** marcos em linguagem observável, sem hash de commit, ID de migration ou lista de arquivos.

Divergência entre git e PROGRESSO (arquivo modificado sem item em andamento, commit de entrega sem registro) é **algo a confirmar**, não erro automático.

## 5. Matriz: o que mudou → o que revisar

| O que mudou | Revisar |
|---|---|
| comandos, testes, setup local, scripts de CI | `README.md` e doc de testes do projeto |
| deploy, host, domínio, env var, secret, webhook, infra | `DEPLOY.md`, `.env.example`, doc da integração |
| escopo de produto, promessa, público, plano comercial | `PRD.md`, `ROADMAP.md`; `CHANGELOG.md` se observável |
| feature planejada entregue, cancelada ou repriorizada | `ROADMAP.md`, estado atual do `README.md`, `PROGRESSO.md` |
| arquitetura, regra durável, convenção, stack | `CLAUDE.md`/`AGENTS.md` **e** o texto completo em `docs/` para onde ele aponta |
| modelo de dados, schema, migration, tabela, coluna | doc de modelo de dados, doc da feature, estado das migrations; LGPD se envolver dado pessoal |
| fluxo de usuário ou regra de negócio | doc da feature em `docs/**`; resumo no `CLAUDE.md` se for essencial |
| UI, layout, padrão visual, componente reutilizável | docs de design system e de UI em `design/**` ou `docs/**`, doc da tela |
| dado pessoal, finalidade, retenção, subprocessador, base legal, exclusão | documentação de LGPD do projeto e política de privacidade pública |

Os nomes exatos dos documentos variam por projeto: localizar pela lista do git (seção 2) e pelo índice do `CLAUDE.md`.

## 6. Regras do PROGRESSO.md

Três seções, nesta ordem:

```markdown
## 🔄 Em Andamento
## 📋 Próximos Passos
## ✅ Concluído
```

- **🔄 Em Andamento:** o que está sendo feito agora, escrito **antes** de começar. Vazia: `_(nada no momento)_`.
- **📋 Próximos Passos:** planejado e não iniciado, com prioridade `(P0)`, `(P1)` ou `(P2)`. Nunca contém item já concluído.
- **✅ Concluído:** ordem cronológica, mais recente por último. Formato:

  ```markdown
  - [x] <descrição observável> — YYYY-MM-DD
  - [x] <descrição observável> — YYYY-MM-DD (build OK, UI não validada)
  ```

**Fechar uma etapa é abrir a seguinte.** Ao mover um item para Concluído, cada coisa que ele deixou em aberto vira item próprio em Próximos Passos:

- etapa seguinte de trabalho em partes ("2/4" implica 3/4 e 4/4);
- frases como "próxima fase", "falta", "fica para depois", "adiado";
- o que não deu para validar (ambiente indisponível, fluxo não visto rodando, teste não escrito);
- decisão levantada e não resolvida;
- limpeza ou refatoração que a tarefa tornou possível e não fez.

Cada item novo é entendível por quem abre o projeto daqui a um mês, sem ler o concluído, e diz **por que está parado** quando houver empecilho. Citar a continuação dentro do item concluído não é registro: pode ficar como contexto, mas o item em Próximos Passos é aberto mesmo assim. Se não houver desdobramento, dizer isso explicitamente. Linha de trabalho grande (feature em fases) também pede entrada no `ROADMAP.md`.

Antes de abrir item, conferir se já existe equivalente em Próximos Passos.

Template mínimo de `PROGRESSO.md`:

```markdown
# Progresso do Projeto

> Detalhes de implementação ficam em `CLAUDE.md`/`AGENTS.md`. Aqui entram apenas etapas.

## 🔄 Em Andamento

_(nada no momento)_

## 📋 Próximos Passos

- [ ] (P0) <primeira tarefa>

## ✅ Concluído

_(nada ainda)_
```
