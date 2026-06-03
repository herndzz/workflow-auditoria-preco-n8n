# SISTEMA DE AUDITORIA DE PREÇOS

Este é um sistema desenvolvido para facilitar o controle e a atualização de preços em supermercados ou distribuidoras. Em vez de usar cadernos de papel, a equipe utiliza um bot no Telegram (no celular) para enviar as atualizações. O sistema usa Inteligência Artificial para ler essas mensagens, organizar os dados e salvá-los de forma segura até serem aprovados. Tudo isso foi pensado para rodar num servidor caseiro simples (como um notebook antigo com o sistema Ubuntu).

## 1. TECNOLOGIAS UTILIZADAS
- Automação: n8n (plataforma que liga tudo e cria as regras de funcionamento).
- Banco de Dados: PostgreSQL 16 (onde ficam salvos os preços e os registros da equipe).
- Inteligência Artificial: Google Gemini 1.5 (para ler e entender as mensagens de texto).
- Interface: Telegram (aplicativo usado pelos funcionários na loja).
- Infraestrutura: Docker e Nginx Proxy Manager (para organizar os programas e garantir a segurança na conexão).

## 2. O QUE O SISTEMA FAZ
- Leitura Inteligente de Texto: O funcionário escreve uma mensagem normal (ex: "o preço da heineken subiu para 6.50") e a IA entende automaticamente qual é o produto e qual é o novo preço.
- Suporte a Várias Lojas: O banco de dados consegue separar e organizar as informações de diferentes lojas ou clientes ao mesmo tempo, sem misturar os dados de cada uma.
- Modo Inteligente: O sistema sabe diferenciar quando o usuário quer usar a IA para registrar algo ou quando está apenas navegando nos menus básicos.
- Aprovação na Prateleira: Qualquer novo preço entra como "Pendente". O funcionário precisa ir fisicamente até a prateleira e enviar um comando no celular para aprovar aquele preço. O sistema salva quem fez a aprovação e a que horas.

## 3. COMO FUNCIONA O SERVIDOR (EM CASA)
Este sistema roda num computador local (um notebook reaproveitado) com as seguintes configurações:
- Conexão Segura: Usa-se o Nginx Proxy Manager para garantir que as mensagens do Telegram cheguem com segurança (certificados HTTPS).
- Rede Privada: Os programas (o n8n e o banco de dados) conversam através de uma rede interna fechada criada pelo Docker. O banco de dados nunca fica exposto publicamente à internet.
- Acesso Local e Externo: Foram feitas regras no roteador de casa para que o acesso funcione perfeitamente, seja com o celular conectado no Wi-Fi ou usando o 4G/5G na rua.

## 4. COMO INSTALAR E EXECUTAR

Passo 1: Preparar a rede e as senhas
Crie a rede interna onde os programas vão se comunicar:
sudo docker network create rede_homelab

Crie o arquivo com as credenciais (.env) na pasta principal, usando o modelo de exemplo.

Passo 2: Iniciar os Programas (Containers)
Inicie os serviços do banco de dados e do sistema de segurança nas respectivas pastas:
cd infra/postgres
sudo docker compose up -d

cd ../npm
sudo docker compose up -d

Passo 3: Criar as Tabelas e Inserir Dados Iniciais
Acesse o banco de dados para criar as tabelas a partir do arquivo de estrutura (schema.sql):
sudo docker exec -it postgres_estoque psql -U admin -d sistema_distribuicao -f schema.sql

Para que o sistema permita registrar os preços, insira os dados da primeira empresa e o seu próprio usuário (substitua SEU_CHAT_ID pelo seu ID do Telegram):
sudo docker exec -it postgres_estoque psql -U admin -d sistema_distribuicao -c "INSERT INTO empresas (nome) VALUES ('Distribuidora Matriz');"
sudo docker exec -it postgres_estoque psql -U admin -d sistema_distribuicao -c "INSERT INTO usuarios_telegram (chat_id, nome, empresa_id, em_modo_ia) VALUES ('SEU_CHAT_ID', 'Admin', 1, false);"

Passo 4: Configurar o n8n (Automação)
- Importe o arquivo auditoria_workflow.json para o painel de controle do n8n.
- Configure as credenciais do banco de dados, do Telegram e do Google Gemini.
- Ative o fluxo de trabalho.
