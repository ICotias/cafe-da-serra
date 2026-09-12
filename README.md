# Café da Serra

Tema Shopify da loja Café da Serra. Construído em cima do
[Skeleton Theme](https://github.com/Shopify/skeleton-theme) da Shopify
(base: commit `a4f32d3`, de 2026-02-26), sem nenhum arquivo do Skeleton
alterado até a fatia 1.

- Loja: `cafe-da-serra-r3yomxrv.myshopify.com`
- Admin: https://admin.shopify.com/store/cafe-da-serra-r3yomxrv/themes
- Repositório: https://github.com/ICotias/cafe-da-serra

## Como o código chega na loja

O tema está **conectado ao GitHub** pelo admin da Shopify, na branch `main`.
A sincronização é nos dois sentidos:

- `git push` na `main` → a Shopify atualiza o tema conectado em cerca de um minuto.
- Salvar no editor de temas (Personalizar) → a Shopify faz um commit na `main`
  como `shopify[bot]`, mexendo em `config/settings_data.json` e em `templates/*.json`.

Por isso, quatro regras:

1. **Sempre `git pull` antes de começar a trabalhar.** Pode ter commit do editor esperando.
2. **Não use `shopify theme push` no tema conectado.** Quem entrega código pra ele é o git.
3. **Depois de salvar no editor, espere o commit do `shopify[bot]` chegar e mais uns
   2 minutos antes de fazer push que mexa no mesmo JSON.** Na fatia 1, um revert
   feito 49 s depois do commit do bot foi registrado como aplicado no log da
   Shopify, mas a loja ficou com a versão antiga até o push seguinte.
4. **JSON no git não é byte a byte o JSON na loja.** A Shopify reescreve
   `templates/*.json` e `config/settings_data.json` ao receber e descarta valores
   padrão. Pra conferir o que a loja tem de verdade, use
   `shopify theme pull --theme 188652159295 --only <arquivo> --path <pasta temporária>`.

Commit com Liquid quebrado não derruba a loja: a Shopify recusa o arquivo, a
loja fica no último commit bom, e o próximo commit bom sincroniza normalmente.
O log de cada sincronização fica em **Ver logs**, no card do tema.

O tema conectado se chama `cafe-da-serra/main` no admin e é o **tema
publicado**. Push na `main` muda a loja. Hoje isso é seguro porque a loja está
"Em desenvolvimento" e pede senha pra visitante. Quando a loja for lançada, o
fluxo muda: trabalho em branch, e só entra na `main` o que pode ir ao ar.

## Pré-requisitos

- [Shopify CLI](https://shopify.dev/docs/api/shopify-cli) 3.x ou mais novo
  (`shopify version`)
- Node.js (o CLI roda em cima dele)
- No VS Code: extensão
  [Shopify Liquid](https://shopify.dev/docs/storefronts/themes/tools/shopify-liquid-vscode)

## Comandos do dia a dia

Todos partindo da raiz do repositório.

Atualizar o local com o que a Shopify commitou:

```bash
git pull
```

Subir um tema de desenvolvimento (temporário, só seu, não toca no tema
conectado) e ver as mudanças ao vivo:

```bash
shopify theme dev --store cafe-da-serra-r3yomxrv
```

Rodar o lint do tema (tem que passar antes de qualquer commit):

```bash
shopify theme check
```

## Estrutura

```
.
├── assets          # CSS crítico, ícones e arquivos estáticos
├── blocks          # Blocos reutilizáveis e aninháveis (editáveis no editor)
├── config          # Schema e dados das configurações globais do tema
├── layout          # theme.liquid e password.liquid (o <html> de cada página)
├── locales         # Traduções (pt-BR entra aqui quando chegar a fatia)
├── sections        # Seções de largura total, com {% schema %}
├── snippets        # Fragmentos Liquid reutilizáveis, com {% doc %}
└── templates       # JSON que monta cada tipo de página a partir das seções
```

`AGENTS.md` (e o link `CLAUDE.md`) guardam as regras de arquitetura e Liquid
que o Skeleton traz. Leia antes de criar seção, bloco ou snippet.

## Convenções

- Uma fatia por vez: fecha, testa, commita, só então a próxima.
- Commit pequeno, mensagem no imperativo, uma fatia por commit.
- Nada é commitado sem passar no `shopify theme check` e no roteiro de teste
  manual da fatia.
- Todo texto visível pro cliente passa por `{{ 'chave' | t }}` e entra em
  `locales/`.
- `config/settings_data.json` e `templates/*.json` são editados pelo editor
  da Shopify. Mudança neles vem via commit do `shopify[bot]`; edite à mão só
  quando souber o que está fazendo.

## Roteiros de teste

- [Conexão do tema com o GitHub](docs/roteiro-conexao-github.md)

## Licença

O Skeleton Theme é MIT (ver [LICENSE.md](LICENSE.md)). O código específico
do Café da Serra segue a mesma licença.
