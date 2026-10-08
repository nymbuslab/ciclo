# Criação assistida de arquivos base

Usado pela `iniciar-sessao` quando falta `CLAUDE.md`/`AGENTS.md` ou `PROGRESSO.md`. Nunca criar direto: mostrar a proposta e esperar aprovação.

## Quando falta CLAUDE.md / AGENTS.md

Explorar o mínimo para preencher com informação real:

1. Manifesto de dependências (`package.json`, `requirements.txt`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `Gemfile`, `composer.json`).
2. `README.md`, se existir.
3. Estrutura de pastas com limite:

   ```bash
   find . -maxdepth 2 -type d ! -path '*/node_modules/*' ! -path '*/.git/*' ! -path '*/dist/*' \
     ! -path '*/build/*' ! -path '*/.next/*' ! -path '*/__pycache__/*' ! -path '*/*venv/*' | sort | head -80
   ```

4. Até 2 arquivos-chave pequenos para entender a arquitetura.
5. `.env.example` ou similar: **só os nomes** das variáveis.
6. Stack de testes e CI pela presença de arquivos, sem leitura profunda.

Usar o nome da ferramenta principal (`CLAUDE.md` no Claude Code, `AGENTS.md` no Codex) ou propor os dois quando o projeto alterna entre agentes. Onde faltar informação, escrever `(a preencher)`.

### Template

````markdown
# <Nome do Projeto>

<Uma linha: para quem é e o que resolve.>

> Este projeto herda as regras globais da ferramenta em uso. As regras abaixo são específicas deste projeto e prevalecem em caso de conflito.

## Stack

- **Linguagem/Runtime:** (a preencher)
- **Frontend:** (a preencher)
- **Backend / Banco:** (a preencher)
- **Testes / CI:** (a preencher)
- **Outras dependências relevantes:** (a preencher)

## Variáveis de ambiente

Só nomes e descrições, nunca valores.

- `EXEMPLO_URL` — (descrição)

## Estrutura do Projeto

```text
(a preencher)
```

## Schema do Banco

(a preencher; remover se não houver banco)

## Integrações externas

- (a preencher)

## Padrões de Código

- Idioma de identificadores: (a preencher)
- Nomenclatura: (a preencher)
- Tipagem: (a preencher)
- Estrutura de componentes/módulos: (a preencher)

## Regras de Desenvolvimento específicas do projeto

1. (a preencher)

## Comandos Úteis

```bash
# Dev / Build / Testes / Migrations
(a preencher)
```

## Skills específicas deste projeto

- `nome-da-skill` — (quando usar)

## Bibliotecas para consulta no Context7

- (a preencher)

## Regras de negócio / decisões de arquitetura

- (a preencher conforme o projeto evolui)
````

## Quando falta PROGRESSO.md

Propor o template mínimo da seção 6 de [docs-vivos.md](../../_compartilhado/docs-vivos.md) e esperar aprovação.

## Quando falta ROADMAP.md ou CHANGELOG.md

Só recomendar, sem bloquear:

> Recomendo criar `ROADMAP.md` e/ou `CHANGELOG.md` para separar direção futura e histórico de marcos entregues. Posso criar uma estrutura mínima se você aprovar.
