# Vencimentos — contas e orçamento (PWA)

App instalável no Android para lembrar contas fora do débito automático e controlar o orçamento mensal (contas fixas, gastos variáveis, aportes e parcela do apartamento). Funciona 100% offline; os dados ficam no aparelho (`localStorage`).

## Como publicar (GitHub Pages — grátis, 5 minutos)

PWA exige HTTPS, então precisa estar hospedado. O caminho mais simples:

1. Crie um repositório no GitHub (ex.: `vencimentos`), pode ser privado ou público (Pages gratuito exige público em conta free).
2. Envie os 5 arquivos desta pasta para a raiz do repositório:
   `index.html`, `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`
3. No repositório: **Settings → Pages → Source: Deploy from a branch → Branch: main / (root) → Save**.
4. Aguarde ~1 minuto. O app estará em `https://SEU-USUARIO.github.io/vencimentos/`.

## Como instalar no Android

1. Abra a URL no **Chrome** do celular.
2. Menu (⋮) → **"Adicionar à tela inicial"** / **"Instalar app"**.
3. Abra pelo ícone. Vá em **Ajustes → Ativar notificações** e permita.

## Como funciona o lembrete (Fase 1)

- Ao **abrir o app**, se houver contas vencendo nos próximos N dias (configurável) ou atrasadas, você recebe uma notificação (1x por dia, no máximo).
- O **ícone do app** mostra um badge com o número de pendências (Android/Chrome).
- A **régua do mês** no topo mostra todos os vencimentos de relance: verde = no prazo, âmbar = vence em breve, vermelho = atrasada, cinza = paga.

Limitação honesta: com o app **fechado**, o navegador não dispara notificações agendadas de forma confiável sem um servidor. Dica prática enquanto a Fase 2 não chega: crie o hábito de abrir o app junto com alguma rotina diária (ex.: café da manhã) — a notificação e o badge fazem o resto.

## Fase 2 — push real com o app fechado (projeto FastAPI)

O service worker (`sw.js`) já tem o listener de `push` pronto. O backend precisa de:

1. **FastAPI** com um endpoint `POST /subscribe` que recebe e guarda a `PushSubscription` do navegador (SQLite/PostgreSQL).
2. **Chaves VAPID** (gere com `vapid --gen` do pacote `py-vapid`).
3. Um **job diário** (cron/APScheduler) que consulta as contas com vencimento próximo e envia push via `pywebpush`.
4. No front, adicionar a chamada `registration.pushManager.subscribe({ userVisibleOnly: true, applicationServerKey: VAPID_PUBLIC_KEY })` e enviar ao backend.

Encaixa direto no seu roadmap: FastAPI + SQL + deploy. Quando chegar nessa etapa do estudo, me chama que montamos juntos.

## Backup

Os dados são locais. Use **Ajustes → Exportar backup** de tempos em tempos (gera um `.json` que pode ser reimportado em outro aparelho).
