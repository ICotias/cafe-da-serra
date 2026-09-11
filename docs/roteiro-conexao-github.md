# Roteiro de teste: conexão do tema com o GitHub

Fatia 1. Prova que a `main` do repositório e o tema conectado na Shopify
(`cafe-da-serra/main`, o tema publicado) estão sincronizando nos dois
sentidos, e que a loja segue protegida por senha.

Todos os comandos partem de `/Users/iagocotias/cafe-da-serra`.
Admin dos temas: https://admin.shopify.com/store/cafe-da-serra-r3yomxrv/themes

## Caminho feliz

| # | Ação | Comando completo | Resultado esperado |
|---|------|------------------|--------------------|
| 1 | Sincronizar o local antes de mexer | `git pull` | `Already up to date.` ou traz commit do `shopify[bot]` sem conflito |
| 2 | Confirmar a conexão no admin | Abrir o admin dos temas | O tema publicado (card do topo) é `cafe-da-serra/main`, com ícone do GitHub e a branch `main`. `shopify theme list --store cafe-da-serra-r3yomxrv` mostra `cafe-da-serra/main [live]` |
| 3 | Fazer uma mudança visível na home | `sed 's/Hello, World!/Café da Serra/' sections/hello-world.liquid > /tmp/hello-world.liquid && grep -c 'Café da Serra' /tmp/hello-world.liquid && mv /tmp/hello-world.liquid sections/hello-world.liquid` | Imprime `1` e o arquivo final tem `<h1>Café da Serra</h1>` |
| 4 | Lint antes de commitar | `shopify theme check` | `39 files inspected with no offenses found` |
| 5 | Commitar e subir | `gaa && gcmsg "feat: troca titulo da home para Café da Serra" && gp` | Push aceito na `origin/main` |
| 6 | Ver o GitHub → Shopify | Esperar até 1 min e recarregar o admin dos temas | O card do tema conectado mostra o commit novo (hash ou mensagem do passo 5) |
| 7 | Conferir a renderização | No card do tema publicado, **Ver loja** (ou `shopify theme pull --store cafe-da-serra-r3yomxrv --theme 188652159295 --only sections/hello-world.liquid --path <pasta temporária>`) | A home mostra o título `Café da Serra` no lugar de `Hello, World!` |
| 8 | Fazer uma mudança pelo editor | No card do tema conectado, **Personalizar** → na home, **Adicionar seção** → `Custom section` → **Salvar** | O editor salva sem erro |
| 9 | Ver o Shopify → GitHub | Abrir https://github.com/ICotias/cafe-da-serra/commits/main | Aparece um commit novo do autor `shopify[bot]` mexendo em `templates/index.json` |
| 10 | Trazer o commit do editor pro local | `git pull` | Fast-forward. `templates/index.json` ganhou uma seção `custom-section` |
| 11 | Desfazer pelo git e provar que volta na loja | `git revert --no-edit HEAD && gp` | Após 1 min, **Ver loja** mostra a home sem a `Custom section` |

## Casos negativos

| # | Ação | Comando completo | Resultado esperado |
|---|------|------------------|--------------------|
| 12 | Push em outra branch não sincroniza | `git checkout -b teste-sync && sed 's/Café da Serra/NAO DEVE APARECER/' sections/hello-world.liquid > /tmp/hello-world.liquid && mv /tmp/hello-world.liquid sections/hello-world.liquid && gaa && gcmsg "test: branch fora da sincronizacao" && git push -u origin teste-sync` | Após 1 min, o card do tema conectado continua no commit do passo 11 e **Ver loja** ainda mostra `Café da Serra` |
| 13 | Limpar a branch de teste | `git checkout main && git branch -D teste-sync && git push origin --delete teste-sync` | Branch removida local e remota; `git status` limpo na `main` |
| 14 | Liquid quebrado não derruba o tema | `printf '\n{%% if quebrado %%}\n' >> sections/hello-world.liquid && gaa && gcmsg "test: liquid quebrado de proposito" && gp` | Após 1 min, o card do tema conectado mostra aviso de erro de sincronização. **Ver loja** continua mostrando a home do passo 11, intacta |
| 15 | Consertar e provar que o erro some | `git revert --no-edit HEAD && gp` | Após 1 min, o aviso de erro some e o card mostra o commit do revert |
| 16 | Loja continua protegida por senha | Abrir `https://cafe-da-serra-r3yomxrv.myshopify.com` numa janela anônima | Aparece a tela de senha, não a home. Nada dos passos 1 a 15 ficou visível pra visitante |

## Limpeza

Ao final, a `main` deve ter, além dos commits da fatia 1:
`feat: troca titulo da home`, o commit do `shopify[bot]`, o revert dele,
o commit do Liquid quebrado e o revert dele. Nenhum arquivo modificado
sem commit:

```bash
git status --short
```

Resultado esperado: saída vazia.
