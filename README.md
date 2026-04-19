# Bot de Estoque

Automação de estoque com n8n + Telegram + Gemini + Google Sheets.

## Requisitos
- Docker e Docker Compose
- Token do Telegram
- API Key do Gemini
- Credenciais Google no n8n
- Planilha com abas:
  - Produtos
  - Movimentacoes
  - EstoqueAtual
  - HistoricoDePrecos

## Execução
1. Criar arquivo .env:
- N8N_PORT=5678
- N8N_HOST=localhost
- GENERIC_TIMEZONE=America/Sao_Paulo
- TELEGRAM_BOT_TOKEN=SEU_TOKEN
- GEMINI_API_KEY=SUA_CHAVE

2. Subir:
docker compose up -d

3. Abrir:
http://localhost:5678

## Configuração no n8n
1. Importar o workflow JSON
2. Configurar credenciais:
   - Telegram
   - Google Sheets
   - Google Gemini
3. Trocar placeholders:
   - REDACTED_GOOGLE_SHEET_ID
   - REDACTED_WEBHOOK_ID
   - REDACTED_INSTANCE_ID
4. Ajustar usuário autorizado no nó IF UsuarioAutorizado
5. Ativar workflow

## Entradas suportadas
- IA: texto livre
- Padronizado: PROD-001, 10, 12.50
- Consulta: consultar PROD-001

## Regras
- Só usuário autorizado executa
- Gravação só após confirmação
- Token de confirmação é único e expira
- Cancelamento não grava dados

## Publicação segura
Remover antes de publicar:
- Credenciais
- Tokens e chaves
- IDs sensíveis
- Dados pessoais