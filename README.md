# Agente de Mídia Social

Este repositório contém um 'agente' que pode receber uma URL e gerar uma postagem para Twitter e LinkedIn com base no conteúdo da URL. Ele usa um fluxo com humano no circuito (HITL - human-in-the-loop) para lidar com autenticação em diferentes plataformas de mídia social e permitir que o usuário faça alterações ou aceite/rejeite a postagem gerada.

![Captura de tela do fluxo do agente de mídia social](./static/agent_flow.png)

## Sumário

- [Início Rápido](#inicio-rapido)
  - [Variáveis de ambiente](#configurar-variaveis-de-ambiente)
  - [Servidor LangGraph](#configurar-variaveis-de-ambiente)
- [Configuração Completa](#configuracao-avancada)
  - [Variáveis de ambiente](#configurar-variaveis-de-ambiente-1)
  - [Servidor LangGraph](#instalar-cli-langgraph-1)
  - [Autenticação Twitter](#instrucoes-de-configuracao-do-desenvolvedor-twitter)
  - [Autenticação Arcade](#instrucoes-de-configuracao-arcade)
  - [Autenticação LinkedIn](#configurar-autenticacao-linkedin)
  - [Supabase](#configurar-supabase)
  - [Slack](#configurar-slack)
  - [GitHub](#configurar-github)
- [Uso](#uso)
  - [Gerar Post de Demonstração](#gerar-post-de-demonstracao)
  - [Configurar Crons](#configurar-crons)
  - [Scripts Prontos](#scripts-prontos)
- [Configurar Caixa de Entrada do Agente](#configurar-caixa-de-entrada-do-agente)
  - [Usando a caixa de entrada implantada](#usando-a-caixa-de-entrada-implantada)
  - [Usando a caixa de entrada local](#usando-a-caixa-de-entrada-local)
- [Personalização](#personalizacao)
  - [Prompts](#prompts)
  - [Estilo de Post](#estilo-de-post)

# Início Rápido

> [!NOTA]
> 🎥 Para um guia visual, confira nosso [tutorial em vídeo passo a passo](https://youtu.be/TmTl5FMgkCQ) que mostra todo o processo de configuração de conta e configuração do projeto.

Este início rápido cobre como configurar o Agente de Mídia Social no modo básico. Esta é a maneira mais rápida de começar, porém faltarão alguns recursos do modo de configuração completa. Veja [aqui](#configuracao-avancada) o guia de configuração completa.

<details>
<summary>Executar no modo de configuração básica não terá os seguintes recursos:</summary>

- Análise de conteúdo de URLs do GitHub, Twitter ou YouTube
- Ingestão de dados do Slack ou envio de atualizações para o Slack  
- Seleção e upload de imagens

</details>

Para começar, você precisará das seguintes chaves de API/software:

- [API Anthropic](https://console.anthropic.com/) - LLM geral
- [LangSmith](https://smith.langchain.com/) - Chave de API LangSmith necessária para executar o servidor LangGraph localmente (gratuito)
- [API FireCrawl](https://www.firecrawl.dev/) - Web scraping. Novos usuários recebem 500 créditos grátis
- [Arcade](https://www.arcade-ai.com/) - Autenticação de mídia social para leitura e escrita

## Instruções de Configuração

### Clone o repositório:
```bash
git clone https://github.com/langchain-ai/social-media-agent.git
cd social-media-agent
```

### Instale as dependências:
```bash
yarn install
```

### Configure as variáveis de ambiente.

Copie os valores do arquivo `.env.quickstart.example` para `.env`, depois adicione os valores:
```bash
cp .env.quickstart.example .env
```

Depois de concluído, certifique-se de ter as seguintes variáveis de ambiente configuradas:
```bash
# Para rastreamento LangSmith (opcional)
LANGCHAIN_API_KEY=
LANGCHAIN_TRACING_V2=true

# Para gerações LLM
ANTHROPIC_API_KEY=

# Para web scraping
FIRECRAWL_API_KEY=

# Chave de API Arcade - usada para buscar Tweets e agendar posts no LinkedIn/Twitter
ARCADE_API_KEY=
```

### Instale o CLI do LangGraph
```bash
pip install langgraph-cli
```

Em seguida, execute o seguinte comando para verificar se o CLI está instalado:
```bash
langgraph --version
```

Clique [aqui](https://langchain-ai.github.io/langgraph/cloud/reference/cli/) para ler as instruções completas de download do CLI do LangGraph.

### Inicie o servidor LangGraph:

Primeiro, certifique-se de que você tem o Docker instalado e em execução. Você pode verificar isso executando o seguinte comando:
```bash
docker ps
```

Em seguida, execute o seguinte comando para iniciar o servidor LangGraph: (certifique-se de ter o `LANGGRAPH_API_KEY` exposto em seu PATH, ou inclua-o inline quando executar o comando)
```bash
yarn langgraph:up
```
ou
```bash
LANGCHAIN_API_KEY="lsv2_pt_..." yarn langgraph:up
```

Na primeira vez que você executar este comando, levará alguns minutos para iniciar. Quando estiver pronto, você pode executar o seguinte comando para gerar um post de demonstração:
```bash
yarn generate_post
```

Isso iniciará uma nova execução para gerar um post sobre um [post do blog LangChain](https://blog.langchain.dev/customers-appfolio/).

Para ver a saída, inspecione-a no LangSmith ou use a Caixa de Entrada do Agente.

> [!NOTA]
> Siga estes passos para configurar a Caixa de Entrada do Agente: [Guia de Configuração da Caixa de Entrada do Agente](#configurar-caixa-de-entrada-do-agente)

# Configuração Avançada

![Captura de tela do grafo do agente de mídia social](./static/graph_screenshot.png)

Para usar todos os recursos do Agente de Mídia Social, você precisará do seguinte:

- [API Anthropic](https://console.anthropic.com/) - LLM geral
- [Google Vertex AI](https://cloud.google.com/vertex-ai) - Para lidar com conteúdo de vídeo do YouTube
- [LangSmith](https://smith.langchain.com/) - Chave de API LangSmith necessária para executar o servidor LangGraph localmente (gratuito)
- [API FireCrawl](https://www.firecrawl.dev/) - Web scraping
- [Arcade](https://www.arcade-ai.com/) - Autenticação de mídia social
- [Conta de Desenvolvedor Twitter](https://developer.twitter.com/en/portal/dashboard) - Para upload de mídia para o Twitter
- [Conta de Desenvolvedor LinkedIn](https://developer.linkedin.com/) - Postagem no LinkedIn
- [API GitHub](https://github.com/settings/personal-access-tokens) - Leitura de conteúdo do GitHub
- [Supabase](https://supabase.com/) - Armazenamento de imagens
- [Conta de Desenvolvedor Slack](https://api.slack.com/apps) (opcional) - ingestão de dados de um canal do Slack

[Continua com instruções detalhadas para cada serviço...]

# Personalização

## Prompts

Este agente está configurado para gerar posts para o LangChain, usando produtos LangChain como contexto. Para usar o agente em seu próprio caso de uso, você deve atualizar os seguintes prompts/seções de prompt dentro da pasta [`prompts`](./src/agents/generate-post/prompts/index.ts):

- `BUSINESS_CONTEXT` - Contexto a ser usado ao verificar se o conteúdo é relevante para seu negócio/caso de uso.
- `TWEET_EXAMPLES` ([`prompts/examples.ts`](./src/agents/generate-post/prompts/examples.ts)) - Uma lista de exemplos de posts que você gostaria que o agente usasse como guia ao gerar o post final.
- `POST_STRUCTURE_INSTRUCTIONS` - Um conjunto de instruções de estrutura para o agente seguir ao gerar o post final.
- `POST_CONTENT_RULES` - Um conjunto de diretrizes gerais de estilo/conteúdo de escrita para o agente seguir ao gerar um post.

O prompt para o relatório de marketing está localizado no arquivo [`generate-post/nodes/generate-report/prompts.ts`](./src/agents/generate-post/nodes/generate-report/prompts.ts). Provavelmente você não precisa atualizá-lo, pois já está estruturado para ser geral.

## Estilo de Post

Existem dois prompts principais para modificar o estilo dos posts:

1. Instruções de estrutura do post (`POST_STRUCTURE_INSTRUCTIONS`). Estas são as instruções que o LLM seguirá sobre como estruturar as gerações de post. Este NÃO deve ser o lugar onde você especifica tom ou estilo de escrita. Este prompt é usado para definir a estrutura que cada post deve seguir. Por padrão, ele é programado para incluir três partes: `Cabeçalho`, `Corpo`, `Chamada para ação`.

2. Exemplos de poucos disparos (`TWEET_EXAMPLES`). Estes são os exemplos dados ao LLM que ele deve usar como exemplos de estilo, conteúdo, tom e estrutura. Este é possivelmente um dos aspectos mais importantes do prompt. Atualmente, estes são definidos com alguns Tweets de contas populares focadas em IA no Twitter. Você DEFINITIVAMENTE deve atualizar estes se quiser gerar Tweets não focados em IA, usando exemplos de Tweets/posts sobre seu conteúdo alvo.

3. "Contexto de negócio" (`BUSINESS_CONTEXT`). Este prompt é usado amplamente em todo o agente para fornecer contexto sobre seu objetivo principal com o agente de mídia social. Para nós na LangChain, este prompt é usado para descrever os diferentes produtos e serviços LangChain. O prompt padrão é focado em conteúdo de IA, mas deve ser atualizado/editado para corresponder ao seu caso de uso. Este prompt é usado para verificar se o conteúdo é relevante para você, gerar relatórios de marketing e gerar tweets.