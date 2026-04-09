# 📦 PCP Embalagem - Sistema de Controle de Produção (Em desenvolvimento)

Sistema completo de Planejamento e Controle de Produção (PCP) desenvolvido para a indústria de embalagens. O sistema gerencia desde o relacionamento com o cliente (CRM) até o controle de estoque e logística, integrando dados do **Tiny ERP** e **ERP Alterdata**.

Desenvolvido com **React**, **TypeScript**, **Express**, **Firebase**, **SQLite**, **Python** e **OpenAI**.

---

## 🚀 Funcionalidades Principais

### 🏭 Produção e Dashboards (PCP)
- **Dashboard de Produção**: Painel principal com status em tempo real de todas as ordens de produção, setores e prioridades.
- **Pedidos Atrasados**: Relatório automatizado que extrai dados do ERP Alterdata (`REL_ATRASADOS.xls`) e exibe o status em tempo real por setor.
  - **Relatório Completo**: Impressão agrupada por setor com ordenação do mais atrasado ao menos atrasado.
  - **Sincronização**: Script Python automatizado (`sync-atrasados.py`) integrado ao Agendador do Windows.
  - **Fallback de Produção**: Em ambiente Docker/Cloud, o sistema consome dados diretamente via **Google Sheets API**.
- **📺 Painel TV por Setor**: Tela fullscreen dark mode para monitores de chão de fábrica, acessível via `/tv/:setor` (sem login). Exibe pedidos atrasados (com animação piscante) e próximos por setor. Auto-refresh a cada 30 segundos.
  - URLs: `/tv/impressao`, `/tv/guilhotina`, `/tv/plastificacao`, `/tv/corte-e-vinco`, `/tv/destaque`, `/tv/estoque`, `/tv/logistica`
- **Scanner de Produção**: Leitura de QR Code para registro de movimentações por setor (Guilhotina, Impressão, Verniz, Corte e Vinco, Destaque).
- **Ordens de Produção (OP)**: Fichas de acompanhamento com impressão otimizada e QR Code.
- **Relatórios de Produção**: Análises de performance por setor, operador e período.

### 🛒 ScuadraShop (E-commerce)
- **Integração Tiny ERP**: Módulo dedicado para operações de e-commerce da ScuadraShop.
  - Sincronização de pedidos com cache de 5min, filtros por status/canal/período.
  - Detalhamento completo de itens, observações internas e rastreio ML/Olist.
- **Gerador de Etiquetas**: Busca por SKU/nome com etiqueta no modelo ScuadraShop (logo, QR Code, data de fabricação).

### 👥 CRM (Customer Relationship Management)
- **Gestão de Clientes**: Cadastro completo com segmentação, histórico financeiro e sincronização com ERP Alterdata.
- **Pipeline Kanban**: 3 funis personalizados (Leads, Clientes/Contratos, Inativos/Perdidos) com drag-and-drop.
  - Cards exibem **Nome Fantasia** (`tradeName`) do cliente.
  - Busca global simultânea em todos os funis.
  - Relatório do cliente com botões **EMBALAGENS**, **HISTÓRICO** e **Nova Interação** no header.
  - Forma de Pagamento exibida no relatório impresso.
  - Excluir negócio com confirmação diretamente no modal.
- **Embalagens do Cliente**: Catálogo por cliente com Preço, Modelo, Tamanho, Litragem e Acabamento. Busca por nome ERP (`shortName`); dados sincronizados via **Sheets API** para evitar erros de fórmulas XLOOKUP no export Drive.
- **Caixa de Entrada**: Conexão IMAP/SMTP (Exchange/Office365) com envio, resposta e encaminhamento de e-mails.
- **Automação de Retomada**: Alerta automático para clientes inativos há mais de 90 dias, com ações rápidas via WhatsApp e E-mail.

### 🔍 Prospecção Inteligente (ScuadrAI)
- **Mineração de Leads**: 4 modos de busca — CNAE (Brasil.io), Google Maps (Apify), Manual (CNPJ) e Salvos.
- **Scoring por IA (OpenAI)**: Avaliação automática de aderência (0-100) com justificativa de vendas.
- **Mockup Canvas**: Geração de imagem personalizada com logo do prospect na embalagem Scuadra (quad-warp 3D).
- **WhatsApp Business Nativo**: Envio de mensagens de prospecção diretamente pelo sistema via Meta Cloud API.
- **Motor de Cadências**: Réguas de relacionamento automatizadas com tarefas diárias por canal (E-mail, WhatsApp, Ligação).

### 💬 WhatsApp Atendimento
- **Inbox Nativo**: Atendimento multiagente via Meta Cloud API com layout 3 painéis (lista / thread / contexto do cliente).
- **Chatbot IA 24h**: Respostas automáticas com GPT-4o-mini, FAQ configurável, horário comercial e handoff para humano por keywords.
- **Setores e Roteamento**: Criação de setores com cores e keywords, auto-roteamento na primeira mensagem, filtro e transferência manual.
- **Respostas Rápidas**: Atalhos com `/` no campo de resposta para textos pré-cadastrados.
- **Alertas sem Resposta**: Notificação em tempo real (SSE) quando conversas ficam sem resposta acima do threshold configurável. Criação automática de oportunidade CRM opcional.
- **Relatórios**: Métricas de atendimento por período — taxa de resolução, volume por dia, por operador e por setor.
- **Configuração do Bot (`/whatsapp/bot-config`)**: tela completa para configurar o assistente IA — ativar/desativar, horário comercial, dias da semana, prompt do sistema, mensagem de ausência, palavras-chave de handoff, FAQ (com importação de PDF/CSV/JSON) e detecção de frustração.
- **Bot — Retorno Dinâmico**: mensagem de ausência calcula automaticamente o próximo horário de atendimento disponível (horário de Brasília), informando hora ou dia da semana conforme o momento.
- **Bot — Detecção de Frustração**: identifica frustração por keywords configuráveis, CAPS LOCK, pontuação excessiva e repetição de mensagens. Transfere automaticamente para fila humana com alerta SSE para operadores.
- **Bot — Intenção de Compra**: detecta menções a dimensões, quantidades e tipos de embalagem; busca e pontua produtos relevantes no catálogo Flexy; injeta sugestões no contexto do GPT.
- **Bot — Transcrição de Áudio**: mensagens de voz transcritas via Whisper e processadas pelo bot normalmente.
- **Bot — Análise de Imagem**: imagens analisadas por GPT-4o Vision com sugestão de produtos similares do catálogo.
- **Tempo Real**: Server-Sent Events (SSE) para atualização instantânea sem polling.

### 📞 VoiceCRM
- **Agente de Voz IA (Nina)**: Prospecção ativa via Twilio + ElevenLabs com transcrição e resumo automáticos.
- **Discador**: Ligações outbound com status em tempo real e detecção automática de fim de chamada.
- **Análise Gerencial (IA)**: Avaliação pós-ligação com nota, qualificação e próxima ação recomendada.
- **Pipeline de Voz**: Kanban dedicado para gestão de leads de voz.

### 🤖 ScuadrAI — Consultor de Vendas (Site)
- **Widget Flutuante**: Chat IA integrado ao site público da Scuadra (62 páginas HTML). Botão verde, histórico em sessão, sem dependências externas.
- **API Pública**: `POST /api/public/chat` com autenticação via token, rate limiting (20 msg/IP/hora) e FAQ loader (PDF).
- **Cartões de Produto**: Resposta do bot acompanhada de cards visuais com foto, nome e tamanho dos produtos sugeridos.
- **Imagens Pinterest via Apify**: 40 boards do Pinterest mapeadas por segmento. No startup, busca até 6 pins por board. Cards exibem fotos reais do Pinterest quando o segmento bate; fallback automático para catálogo Flexy. Cache em `sheets/pinterest-cache.json` com refresh de 24h.
- **Handoff WhatsApp**: Após 2 trocas, botão "Continuar pelo WhatsApp". Visitante informa telefone → conversa salva no Firebase + mensagem disparada via UazAPI. Migração para Meta Cloud API planejada.

### 📦 Estoque e Inventário
- **Controle de Estoque**: Adição por Caixas (Qtd × Unidades/Caixa), agrupamento por localização e baixa inteligente via FIFO.
- **Parte da Embalagem**: Campos Tampa/Fundo marcados por padrão no cadastro (90% dos itens). Badges amarelos exibidos na lista.
- **Inventário via OCR (IA)**: Leitura automática de etiquetas usando **OpenAI GPT-4o**.
- **Etiquetas**: Gerador de etiquetas de produção com QR Code.

### 🚛 Logística
- **Mapa de Rastreamento**: Google Maps com GPS de motoristas em tempo real, polyline de trajeto e painel de status.
- **PWA do Motorista**: Página standalone para celular com GPS automático e confirmação de entregas.
- **Controle de Expedição**: Gestão de cargas e atribuição de entregas por motorista.

### ⚙️ Administração
- **Configurações**: Gerenciamento de usuários, e-mail IMAP/SMTP e integração Google Sheets (sincronização).
- **Logs do Sistema**: Painel de logs centralizados para diagnóstico.

---

## 🛠️ Stack Tecnológico

### Frontend
- **React 18** + **Vite** + **TypeScript**.
- **Tailwind CSS** + **Shadcn/UI** + **Framer Motion**.
- **TanStack Query** (Caching e Sync de dados).

### Backend
- **Node.js** + **Express** (API REST).
- **Firebase Firestore** (Dados persistentes).
- **SQLite** (Cache local de performance).
- **Python / Pandas** (Automação de ETL de relatórios legados).
- **Google Cloud APIs** (Sheets, Drive, Maps, Places, Identity).
- **OpenAI GPT-4o / GPT-4o-mini** (Scoring, análise, chat, transcrição).
- **Twilio** + **ElevenLabs** (VoiceCRM).
- **Meta Cloud API** (WhatsApp Business Nativo).

### Infraestrutura
- **Docker**: Containerização completa do ecossistema.
- **CI/CD**: GitHub Actions para build de imagens e push para GHCR.
- **Deploy**: Coolify (Self-hosted) com suporte a rollbacks.

---

## 🛡️ Backup e Segurança

- **Banco de Dados**: Exportação diária automática de 14 coleções do Firestore (clientes, estoque, pedidos, CRM, etc.).
- **Inventário**: Backup 3x/dia (06:30, 11:00, 15:00) com retenção de 30 dias.
- **Planilhas**: Scripts de backup local (.xlsx) para Google Sheets corporativos.
- **DR (Disaster Recovery)**: Manual de recuperação detalhado em `./docs/planejamento/DEPLOY_RUNBOOK.md`.

---

## 📁 Estrutura do Projeto

```
PCPembalagem/
├── client/                  # Frontend React (Vite)
│   └── src/pages/           # Dashboard, DelayedOrders, SectorTV, CRM, ScuadraShop...
├── server/                  # Backend Express
│   ├── routes.ts            # Rotas principais (Orders, Delayed, TV Dashboard)
│   ├── crm-routes.ts        # CRM, Clientes, Pipeline
│   ├── voice-crm-routes.ts  # VoiceCRM (Twilio + ElevenLabs)
│   ├── scuadrashop-routes.ts# Integração Tiny ERP
│   ├── stock-routes.ts      # Controle de Estoque
│   └── google-sheets.ts     # Serviço Google Sheets Admin
├── scripts/                 # Automações
│   ├── sync-atrasados.py    # ETL Alterdata XLS -> Sheets/CSV
│   └── sync-sheets.py       # Sync Google Sheets -> xlsx local (Drive + Sheets API)
├── shared/                  # Schemas Zod e Tipos
└── docs/planejamento/       # Documentação e Runbooks
```

---

## ⚙️ Configuração Local

1. `npm install`
2. Configure o `.env` (use `.env.example` como base).
3. `npm run dev` (Acesse `https://localhost:5080`).

---

## 📝 Histórico Recente (2026)

- **v1.28.x**: WhatsApp Business Nativo (Meta Cloud API) — inbox multiagente, chatbot IA 24h, setores, roteamento, relatórios, bot com contexto CRM, transcrição de áudio (Whisper), análise de imagem (Vision), detecção de frustração, retorno dinâmico, intenção de compra + catálogo Flexy, formatação nativa WhatsApp. ScuadrAI Widget (consultor de vendas IA no site) com cartões de produto, Pinterest via Apify e handoff WhatsApp. Estoque com badges Tampa/Fundo.
- **v1.27.x**: CRM Pipeline — botões EMBALAGENS, HISTÓRICO e Nova Interação no relatório; cards com Nome Fantasia; catálogo de embalagens com Preço/Modelo/Tamanho/Litragem/Acabamento; sync-sheets via Sheets API (resolve #NOME? de XLOOKUP); flag `--once` para Agendador de Tarefas.
- **v1.26.x**: Impressão CRM corrigida (react-to-print isolado via `data-crm-print`); busca global no pipeline; exclusão de negócios; funil Padrão removido automaticamente.
- **v1.25.x**: Painel TV por Setor, Relatório Completo de Atrasos, migração Google Sheets para Configurações.
- **v1.24.x**: Lançamento do ScuadraShop (Módulo Tiny ERP).
- **v1.23.x**: VoiceCRM, Mockup Canvas 3D, Cadências, WhatsApp UazAPI.
- **v1.22.x**: Módulo VoiceCRM (Twilio + ElevenLabs).
- **v1.21.x**: Prospecção IA (Mineração de Leads, Scoring OpenAI).
- **v1.18.x**: Módulo Pedidos Atrasados com automação Python.
- **v1.17.x**: Logística PWA + Mapa de Rastreamento.

Consulte o [CHANGELOG.md](./CHANGELOG.md) para a lista completa de alterações.

---

## 📄 Licença
Proprietário: Marcelo Prates. Todos os direitos reservados.
