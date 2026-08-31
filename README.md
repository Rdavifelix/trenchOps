# TrenchOps — Funil de captação

Site estático, sem build e sem dependência. Dois arquivos HTML + assets.

## Páginas

| Arquivo | O que é |
|---|---|
| `index.html` | Landing de captação: headline + preço, preview com play, CTA verde. Clicar na foto ou no botão abre o **pop-up com quiz de 8 passos** (4 perguntas de qualificação → telefone → e-mail → nome → empresa). Ao enviar, redireciona para a página seguinte preservando os UTMs. |
| `we-will-call-you.html` | Página pós-quiz: "assista ao vídeo, nossa equipe vai te ligar". Player da VSL com placeholder automático enquanto o vídeo não existe. |
| `assets/logo-trenchops.svg` | Logo em vetor (ícone). |

## Rodar localmente

```bash
python3 -m http.server 8777
```

Abra `http://localhost:8777`.

## Antes de rodar tráfego

1. **`WEBHOOK_URL`** — constante no `<script>` do `index.html`. Apontar para o inbound webhook do CRM (GoHighLevel: Automations → Workflow → trigger "Inbound Webhook"). O payload já sai com as 8 respostas + UTMs + timestamp.
2. **Pixel do Meta** — slot comentado no `<head>` das duas páginas. O quiz já dispara `fbq('track','Lead')` no envio.
3. **VSL** — colocar o vídeo em `assets/vsl.mp4`. Enquanto não existir, a página mostra um placeholder escuro no lugar do player.
4. **Depoimentos** — as seções estão comentadas nos dois HTML esperando depoimentos **reais** de clientes (vídeo + frase + nome). Não publicar depoimento inventado.
5. **Thumbnail** — o bloco `.mock` do `index.html` é um preview ilustrativo desfocado; trocar por um print real do site quando ele existir.

## Publicar (GitHub Pages + domínio próprio)

O site é servido pelo GitHub Pages a partir da branch `main`.
Para usar domínio próprio: Settings → Pages → Custom domain, e no DNS do registrador:

- **Subdomínio** (ex.: `go.seudominio.com`): registro `CNAME` → `rdavifelix.github.io`
- **Domínio raiz** (`seudominio.com`): registros `A` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`

Depois de propagar, marcar **Enforce HTTPS**.
