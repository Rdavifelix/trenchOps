# TrenchOps — Funil de captação

Site estático, sem build e sem dependência. Dois arquivos HTML + assets.

## Páginas

| Arquivo | O que é |
|---|---|
| `cadastro/index.html` | **A LP** (`/cadastro/`). Landing de captação: headline + preço, preview com play, CTA verde. Clicar na foto ou no botão abre o **pop-up com quiz de 8 passos** (4 perguntas de qualificação → telefone → e-mail → nome → empresa). Ao enviar, redireciona para a página seguinte preservando os UTMs. |
| `drforms/index.html` | **VSL + formulário** (`/drforms/`), inspirada na página de VSL da Stryker Digital. Oferta do relatório do Bodhi (20/10/2026): serviço mensal completo, **sem preço na página**. Formulário próprio (orçamento em faixas → `fit` premium/sistema) envia para o Inbound Webhook do GHL; confirmação na própria página. noindex. |
| `vsl/index.html` | **Página de VSL** (`/vsl/`). Entre "método em prática" e o FAQ há a seção **Depoimentos** (`.depo`, modelo da "Real Client Results" da Stryker: trilho de vídeos 9:16, nome e empresa, botão) — por enquanto um card provisório (`.ph`) esperando o vídeo real em `assets/vsl/depo-1.mp4`. Primeira dobra = hero da `/drforms/` + VSL do **VTurb** + botão, cabendo na tela do PC e do celular. Depois: "Como ajudamos" em 5 passos (Meta Ads no lugar de LSA), "Veja nosso método em Prática" com grades de posição de **clientes da Trench Ops** (nome e endereço cobertos em `assets/vsl/grid-*.webp`, os clientes não autorizaram o nome), botão, FAQ com as perguntas do FAQ da Stryker e avisos. Contrato mês a mês e preço "a partir de US$ 999/mês" confirmados em 26/10. Formulário em pop-up **uma pergunta por tela**, todas obrigatórias, no modelo do formulário da Stryker: tem Home Service? → tempo de empresa → gasto com marketing → o que tentou em 12 meses (texto) → parceiro de longo prazo ou serviço único → faturamento mensal → US$ 999/mês → nome → e-mail → telefone. Envia uma vez só, no fim, para o mesmo workflow do GHL da `/drforms/`, com `origem: "vsl"`; campos novos: `home_service`, `years_in_business`, `marketing_spend`, `tried_last_12m`, `looking_for`, `monthly_revenue` (além de `budget`/`fit`). |
| `vsl/obrigado/index.html` | **Obrigado da VSL** (`/vsl/obrigado/`). Para onde o formulário da `/vsl/` manda depois do envio (com as UTMs; o primeiro nome vai pelo `sessionStorage`). Próximos passos (a gente liga → marca a conversa por vídeo → olha o Google Maps) e botão verde **Furar fila** para o WhatsApp oficial +1 (607) 454-1762, com mensagem pronta e evento `Contact` do pixel. noindex. |
| `privacidade/index.html` | Política de privacidade (`/privacidade/`), linkada no rodapé das duas páginas. **Descreve campo a campo o que o quiz envia — se o funil mudar, atualize aqui também.** |
| `index.html` | Redirecionador da raiz → `/cadastro/`, preservando a querystring (UTMs). |
| `lp/index.html` | **LP longa** (`/lp/`), em construção e com `noindex`: hero + VSL, formulário em 4 passos (envia para `WEBHOOK_URL` com o mesmo payload da `/cadastro/` e redireciona para `/we-will-call-you/`), faixas cruzadas, depoimentos em vídeo, prints, carta do fundador, método em 3 passos, FAQ e pop-up de saída. Os textos entre [colchetes] são placeholders. |
| `lp-form/index.html` | **LP sem VSL** (`/lp-form/`), com `noindex`: hero com texto + formulário na primeira dobra, depois as mesmas seções da `/lp/` mais "para quem é". Carta e FAQ em rascunho (revisar); depoimentos ainda são placeholders. Mesmo `WEBHOOK_URL` (constante própria no arquivo). |
| `agencia/index.html` | **Página institucional** (`/agencia/`), com `noindex`: nav fixa, hero, fundadores, 2 serviços, resultados em vídeo, método em 4 fases + linha do tempo de 180 dias, time, comparação "outras agências × TrenchOps", FAQ e CTA final com o mesmo formulário em passos (mesmo `WEBHOOK_URL`). Números, fotos, depoimentos, contrato e preço são placeholders entre [colchetes]. |
| `we-will-call-you/index.html` | **A pos-quiz** (`/we-will-call-you/`). Página pós-quiz: "assista ao vídeo, nossa equipe vai te ligar". Player da VSL com placeholder automático enquanto o vídeo não existe. |
| `assets/logo-trenchops.svg` | Logo em vetor (ícone). |

## Rodar localmente

```bash
python3 -m http.server 8777
```

Abra `http://localhost:8777` — a raiz redireciona para `/cadastro/`.

## Antes de rodar tráfego

1. **`WEBHOOK_URL`** — constante no `<script>` do `cadastro/index.html`. Apontar para o
   **Inbound Webhook do GoHighLevel** (Automations → Workflows → novo workflow → trigger
   "Inbound Webhook" → copiar a URL). Enquanto estiver vazio, **nenhum lead é enviado**.

   O payload vai achatado (um nível só), para o workflow acessar cada campo como
   `{{inboundWebhookRequest.campo}}`:

   `partial` · `name` · `first_name` · `last_name` · `phone` (E.164) · `phone_formatted` ·
   `email` · `company` · `contracting_work` · `business_status` · `fix_timeline` · `revenue` ·
   `utm_source` · `utm_medium` · `utm_campaign` · `utm_content` · `fbclid` · `page` · `ts`

   **Captura parcial:** o lead é enviado assim que o telefone é validado e de novo a cada
   passo seguinte, não só no fim. Quem abandona o quiz no meio não se perde. Por isso o
   mesmo contato chega várias vezes — deixe a deduplicação ligada no GHL
   (Settings → Business Profile → "Allow Duplicate Contact" **desmarcado**) e use
   `partial` no workflow para separar quem terminou (`false`) de quem parou no meio (`true`).
2. **Pixel do Meta** — slot comentado no `<head>` das duas páginas. O quiz já dispara `fbq('track','Lead')` no envio.
3. **VSL** — `assets/vsl.mp4` (720p30, H.264 + AAC, `+faststart` para tocar sem baixar tudo), com poster em `assets/vsl-poster.jpg`. A moldura do navegador foi cortada da gravação original.
4. **Depoimentos** — as seções estão comentadas nos dois HTML esperando depoimentos **reais** de clientes (vídeo + frase + nome). Não publicar depoimento inventado.
5. **Thumbnail** — o bloco `.mock` do `cadastro/index.html` é um preview ilustrativo desfocado; trocar por um print real do site quando ele existir.

## Publicar (GitHub Pages + domínio próprio)

O site é servido pelo GitHub Pages a partir da branch `main`.
Para usar domínio próprio: Settings → Pages → Custom domain, e no DNS do registrador:

- **Subdomínio** (ex.: `go.seudominio.com`): registro `CNAME` → `rdavifelix.github.io`
- **Domínio raiz** (`seudominio.com`): registros `A` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`

Depois de propagar, marcar **Enforce HTTPS**.
