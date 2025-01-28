# Agente de Mídias Sociais

Este repositório contém um "agente" que pode processar uma URL e gerar publicações para Twitter e LinkedIn com base no conteúdo da URL. Ele utiliza um fluxo de "humano no loop" (HITL) para lidar com autenticação em diferentes plataformas de mídia social e permite que o usuário faça alterações ou aceite/rejeite a publicação gerada.

![Captura de tela do fluxo do agente de mídias sociais](./static/agent_flow.png)

## Tabela de Conteúdos

- [Introdução Rápida](#quickstart)
  - [Variáveis de Ambiente](#set-environment-variables)
  - [Servidor LangGraph](#set-environment-variables)
- [Configuração Completa](#advanced-setup)
  - [Variáveis de Ambiente](#set-environment-variables-1)
  - [Servidor LangGraph](#install-langgraph-cli-1)
  - [Autenticação no Twitter](#twitter-developer-setup-instructions)
  - [Autenticação no Arcade](#arcade-setup-instructions)
  - [Autenticação no LinkedIn](#setup-linkedin-authentication)
  - [Supabase](#setup-supabase)
  - [Slack](#setup-slack)
  - [GitHub](#setup-github)
- [Uso](#usage)
  - [Gerar Post de Demonstração](#generate-demo-post)
  - [Configurar Cron Jobs](#setup-crons)
  - [Scripts Pré-construídos](#prebuilt-scripts)
- [Configurar o Agent Inbox](#setup-agent-inbox)
  - [Usando o Inbox em Produção](#using-the-deployed-inbox)
  - [Usando o Inbox Local](#using-the-local-inbox)
- [Personalização](#customization)
  - [Prompts](#prompts)
  - [Estilo dos Posts](#post-style)

# Introdução Rápida

> [!NOTA]
> 🎥 Para um guia visual, confira nosso [tutorial em vídeo](https://youtu.be/TmTl5FMgkCQ) que explica o processo de configuração da conta e do projeto.

Esta introdução rápida cobre como configurar o Agente de Mídias Sociais em modo básico. Este é o método mais rápido para começar, mas carecerá de alguns recursos do modo completo. Consulte [aqui](#advanced-setup) para o guia de configuração completa.

<details>
<summary>O modo básico não incluirá os seguintes recursos:</summary>

- Processamento de conteúdo de URLs do GitHub, Twitter ou YouTube.
- Ingestão de dados do Slack ou envio de atualizações para o Slack.
- Seleção e upload de imagens.

</details>

Para começar, você precisará das seguintes chaves de API/softwares:

- [API da Anthropic](https://console.anthropic.com/) - LLM geral.
- [LangSmith](https://smith.langchain.com/) - Necessária para rodar o servidor LangGraph localmente (grátis).
- [API FireCrawl](https://www.firecrawl.dev/) - Web scraping. Novos usuários recebem 500 créditos grátis.
- [Arcade](https://www.arcade-ai.com/) - Autenticação de redes sociais para leitura e escrita.

## Instruções de Configuração

### Clone o repositório:

```bash
git clone https://github.com/langchain-ai/social-media-agent.git
