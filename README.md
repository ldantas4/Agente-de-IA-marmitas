# README - Fluxo N8N para Atendimento de Marmitaria

## Visão Geral
Este fluxo N8N foi projetado para simular o atendimento de um restaurante de marmitas via um agente de IA. O objetivo é permitir que os clientes escolham, personalizem e confirmem seus pedidos de marmitas, com o agente mantendo o foco no cardápio e nas personalizações. Ele também integra com o Redis para gerenciar sessões e o Google Sheets para registrar os pedidos.

## Componentes Principais do Fluxo

1. **Webhook**: O fluxo começa com um webhook que captura os dados do cliente (ID da sessão, mensagens, timestamp, etc.).
2. **Redis (Push e Get)**: Utilizado para armazenar e recuperar as mensagens e dados da sessão do cliente, garantindo a continuidade do atendimento.
3. **Switch1**: Responsável por decidir o fluxo com base nas condições dos dados recebidos, como o tempo de espera ou a verificação de mensagens.
4. **If**: Condicional que verifica a presença de dados de atendimento e decide se o fluxo deve continuar ou se o cliente precisa ser atendido novamente.
5. **OpenAI Chat Model**: Este nó integra o modelo de linguagem GPT-4o-mini para gerar respostas dinâmicas e personalizadas durante a interação com o cliente.
6. **Parser Chain**: Processa a resposta do modelo de IA, ajustando a formatação e preparando as mensagens para o envio.
7. **Google Sheets Integration**: Registra os pedidos confirmados em uma planilha do Google Sheets, armazenando os dados essenciais do pedido.

## Fluxo de Atendimento

1. **Recepção do Pedido**: O cliente escolhe uma das opções do cardápio, e o agente solicita possíveis personalizações, como adicionar ou substituir ingredientes.
2. **Personalização do Pedido**: O cliente pode modificar ingredientes, adicionar proteínas ou carboidratos, com preços fixos conforme o cardápio.
3. **Confirmação do Pedido**: O agente confirma os itens selecionados, personalizações e o valor total antes de finalizar.
4. **Armazenamento do Pedido**: Após confirmação, os dados do pedido são enviados ao Google Sheets, registrando todos os detalhes do pedido para rastreamento.

## Instruções de Teste

1. **Importação do Fluxo no N8N**: 
   - Importe o arquivo JSON para o N8N através do menu "Importar Fluxo".
   - Certifique-se de ter as credenciais do Redis e do Google Sheets configuradas corretamente.

2. **Configuração do Webhook**:
   - O webhook é responsável por capturar os dados de entrada. Teste o fluxo enviando um POST com o payload esperado no endpoint do webhook configurado.

3. **Testando o Fluxo**:
   - Ao ativar o fluxo, envie um pedido via API (ou manualmente via interface de teste) contendo informações como `sessionId`, `messages`, e `timestamp`.
   - O agente irá responder com o cardápio, permitir alterações e confirmar o pedido final. Os pedidos confirmados serão registrados na planilha do Google Sheets.

4. **Verificação no Google Sheets**:
   - Após a confirmação de um pedido, verifique se as informações estão sendo corretamente registradas no Google Sheets. A planilha deve conter os seguintes campos:
     - **ID do Pedido**
     - **Itens Escolhidos**
     - **Alterações Personalizadas**
     - **Valor Total**
     - **Status do Pedido**

5. **Ajustes no Fluxo**:
   - Se necessário, ajuste o fluxo para novos cenários de personalização ou alterações nas interações do agente, como mais opções de marmitas ou novos adicionais.

## Como Funciona a Integração com OpenAI

O modelo GPT-4o-mini é utilizado para gerar as respostas do agente de IA. As mensagens enviadas pelo cliente são processadas e formatadas antes de serem respondidas. O prompt do modelo foi configurado para garantir que o agente mantenha o foco no atendimento de pedidos e não saia do contexto.

### Detalhes do Prompt:
- O prompt define regras de atendimento, como:
  - **Foco no cardápio**: O agente só pode falar sobre as marmitas e personalizações.
  - **Preços fixos**: O agente só aceita pedidos com valores do cardápio.
  - **Limitação de adicionais**: O cliente pode adicionar até 2 ingredientes extras por marmita.

O fluxo de conversa segue o modelo abaixo:
1. O agente apresenta o cardápio.
2. O cliente escolhe e personaliza o pedido.
3. O agente confirma o pedido e o valor total.
4. O pedido é registrado e a confirmação é enviada.

## Ambiente de Teste no Lovable

Este agente de IA está vinculado à página de teste criada pela plataforma **Lovable**: [https://lovablen-meal-chat.lovable.app/](https://lovablen-meal-chat.lovable.app/). A página foi desenvolvida para testar a interação do agente em um ambiente controlado e simular o atendimento de um restaurante de marmitas.

A plataforma oferece duas abas principais:

1. **Chat**: Nessa aba, o cliente interage diretamente com o agente, fazendo pedidos e personalizando as marmitas. O agente apresenta o cardápio, aceita modificações nos pedidos e confirma as escolhas antes de finalizar o atendimento.

2. **Histórico**: Aqui, você pode visualizar as conversas anteriores, buscando por **ID da sessão**. Essa funcionalidade permite que você consulte o histórico de interações com o agente, facilitando o rastreamento e verificação dos pedidos feitos.

### Como Usar o Ambiente de Teste:

1. **Acessando o Chat**:
   - Na aba **Chat**, inicie uma conversa com o agente.
   - Envie um pedido com as informações necessárias, como o tipo de marmita desejado e quaisquer personalizações.
   - O agente irá confirmar o pedido e calcular o valor total.
   
2. **Consultando o Histórico**:
   - Na aba **Histórico**, você pode buscar por **ID de sessão**, que é fornecido ao iniciar a conversa.
   - Isso permite que você veja todos os detalhes da conversa anterior e verifique os dados registrados.

Este ambiente de teste serve como uma forma prática de validar o funcionamento do fluxo antes de integrá-lo a um sistema de produção.

## Considerações Finais

Esse fluxo é uma solução completa para a automação de pedidos em um restaurante de marmitas. A integração com o OpenAI permite uma interação fluida e natural com os clientes, enquanto o armazenamento de dados no Google Sheets facilita o controle e rastreamento dos pedidos.
