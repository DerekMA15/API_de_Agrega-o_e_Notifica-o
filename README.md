# API de Notificação
- **A ideia:** Desenvolva um backend que consome dados de várias APIs de terceiros (por exemplo, notícias de tecnologia do `Hacker News`, vídeos novos de canais do YouTube, ou cotações de criptomoedas). O usuário poderia se cadastrar no seu sistema, escolher os tópicos de interesse e receber um "resumo" diário ou semanal por e-mail com as novidades.
    
- **O que você vamos aprender:**
    
    - **Consumo de APIs Externas:** Lidar com diferentes tipos de autenticação e formatos de dados de outras APIs.
        
    - **Tarefas Agendadas (Cron Jobs):** Criar rotinas que rodam automaticamente em horários específicos para buscar os novos dados.
        
    - **Caching:** Implementar uma camada de cache para não sobrecarregar as APIs de terceiros e servir os dados mais rapidamente.
        
    - **Envio de E-mails:** Integrar um serviço como `Nodemailer` para enviar os resumos para os usuários.
        
    - **Documentação de API:** Usar ferramentas como `Swagger` ou `OpenAPI` para documentar sua própria API, uma prática muito valorizada no mercado.
---

### Detalhando a: **API de Agregação e Notificação de Conteúdo**

#### 1. Conceito Central

O objetivo é criar um serviço que atua como um "curador" de conteúdo para o usuário. Em vez de ele ter que visitar 5 sites diferentes todos os dias, o nosso sistema faz isso por ele, junta as novidades dos tópicos que ele escolheu e entrega um resumo prático por e-mail.

---

#### 2. Funcionalidades Essenciais (para um MVP - Mínimo Produto Viável)

Para começar, vamos focar no essencial. Pense nestes blocos de construção:

- **Autenticação de Usuário:**
    
    - Rotas para `cadastro`, `login` e `logout`.
        
    - Uso de JWT (Tokens) para proteger as rotas e saber quem está fazendo as requisições.
        
- **Gestão de Fontes e Inscrições (Subscriptions):**
    
    - O sistema deve ter uma lista de fontes de dados pré-definidas (ex: um blog de tecnologia, um site de notícias sobre games, uma API de cotação de moedas).
        
    - O usuário logado pode ver as fontes disponíveis e se inscrever/desinscrever delas.
        
- **Coletor de Dados Automatizado (O "Crawler"):**
    
    - Um script que roda de forma agendada (ex: uma vez por dia).
        
    - Ele passa por cada fonte de dados cadastrada no sistema.
        
    - Faz uma chamada HTTP para a API da fonte.
        
    - Verifica os novos itens (artigos, vídeos, etc.) e os salva no seu banco de dados, evitando duplicatas.
        
- **Sistema de Notificação:**
    
    - Outro script agendado (ou o mesmo que o anterior) que verifica quais usuários devem receber o e-mail de resumo.
        
    - Para cada usuário, ele monta um e-mail personalizado com os novos itens das fontes em que ele está inscrito.
        
    - Envia o e-mail.
        

---

#### 3. Sugestão de Arquitetura e Tecnologias

Aqui está uma pilha de tecnologias (tech stack) moderna e eficiente para este projeto:

- **Linguagem/Ambiente:** **TypeScript** sobre **Node.js**.
    
- **Framework do Servidor:** **Express.js** ou **Fastify**. Ambos são excelentes, Fastify é mais moderno e focado em performance.
    
- **Banco de Dados:** **PostgreSQL**. É uma ótima escolha pela sua robustez e capacidade de lidar com dados relacionais (ex: a relação entre `Usuários` e suas `Inscrições`). Para interagir com ele, use um ORM como o **Prisma** ou **TypeORM**, que funcionam maravilhosamente bem com TypeScript.
    
- **Comunicação com APIs Externas:** **Axios**. É a biblioteca padrão do mercado para fazer chamadas HTTP de forma simples e poderosa.
    
- **Agendamento de Tarefas:** **node-cron**. Uma biblioteca super simples para configurar tarefas agendadas diretamente no seu código (ex: `cron.schedule('0 8 * * *', () => { ... });` para rodar todo dia às 8h).
    
- **Envio de E-mails:** **Nodemailer**. Use-o em conjunto com um serviço de e-mail transacional como **SendGrid**, **Mailgun** ou **Amazon SES**. Eles garantem que seus e-mails não caiam na caixa de spam.
    
- **Validação de Dados:** **Zod**. Uma biblioteca fantástica para validar os dados que chegam nas suas rotas da API, garantindo que tudo esteja no formato correto.
    

---

#### 4. Um Roteiro Simplificado para Começar

1. **Estrutura:** Inicie o projeto com Node.js, TypeScript e Express/Fastify.
    
2. **Banco de Dados:** Modele e crie as tabelas principais: `users`, `sources` (fontes de dados), `items` (artigos/conteúdos salvos) e `subscriptions` (tabela que liga os usuários às fontes).
    
3. **Autenticação:** Crie as rotas de cadastro e login.
    
4. **O Coletor:** Crie um script separado (ex: `collector.ts`) que use Axios para buscar dados de UMA API pública para começar (ex: a API do dev.to ou a Hacker News API). Faça ele salvar os dados na sua tabela `items`.
    
5. **Agendamento:** Use o `node-cron` para chamar a função principal do seu `collector.ts` uma vez a cada hora.
    
6. **O Notificador:** Crie a lógica para montar e enviar o e-mail usando Nodemailer.
    
7. **Junte Tudo:** Crie as rotas para o usuário gerenciar suas inscrições e conecte com a lógica do notificador.
    

Este mapa te dá uma visão bem clara da jornada! É um projeto desafiador, mas cada etapa vencida será uma grande adição às suas habilidades.

---
### 1. Sugestões de APIs Públicas (Ótimas para Começar)

Aqui estão algumas APIs gratuitas e de diferentes nichos. Escolhi opções com documentação clara para facilitar seu aprendizado.

1. **DEV.to API**
    
    - **Conteúdo:** Artigos de alta qualidade sobre programação e tecnologia. Você pode buscar por tags (ex: `javascript`, `typescript`, `node`).
        
    - **Nível de Dificuldade:** Fácil. Não precisa de chave de API (API Key) para a maioria das buscas públicas.
        
    - **Documentação:** [https://developers.forem.com/api](https://developers.forem.com/api)
        
2. **Hacker News API (via Algolia)**
    
    - **Conteúdo:** O clássico agregador de notícias de tecnologia. A API oficial é um pouco complexa, mas a versão mantida pela Algolia é super simples e poderosa para busca.
        
    - **Nível de Dificuldade:** Fácil. É uma API pública, sem necessidade de chaves.
        
    - **Documentação:** [https://hn.algolia.com/api](https://hn.algolia.com/api)
        
3. **NewsAPI**
    
    - **Conteúdo:** Notícias do mundo inteiro sobre qualquer assunto (negócios, entretenimento, esportes, etc.). É extremamente versátil.
        
    - **Nível de Dificuldade:** Médio. Requer uma chave de API gratuita, e o plano gratuito tem algumas limitações (como buscar notícias com mais de 24h), mas é perfeito para este projeto.
        
    - **Documentação:** [https://newsapi.org/](https://newsapi.org/)
        
4. **CoinGecko API**
    
    - **Conteúdo:** Dados sobre milhares de criptomoedas (preços, capitalização de mercado, etc.).
        
    - **Nível de Dificuldade:** Fácil. A maioria das informações públicas não precisa de chave de API.
        
    - **Documentação:** [https://www.coingecko.com/en/api/documentation](https://www.coingecko.com/en/api/documentation)
        

---

### 2. Modelagem do Banco de Dados (Exemplo com PostgreSQL)

A chave aqui é pensar nas "coisas" que nosso sistema tem e como elas se relacionam. Teremos **Usuários**, **Fontes** de conteúdo, os **Itens** de conteúdo que coletamos, e as **Inscrições** que conectam tudo.

A relação mais importante é entre `users` e `sources`: um usuário pode se inscrever em várias fontes, e uma fonte pode ter vários usuários inscritos. Isso é uma relação **Muitos-para-Muitos**, e a resolvemos com uma tabela no meio, que chamaremos de `subscriptions`.

Vamos desenhar as tabelas:

#### Tabela: `users`

Guarda as informações de quem se cadastrou.

- `id` (uuid ou serial) - Chave Primária
    
- `name` (texto)
    
- `email` (texto, único)
    
- `password_hash` (texto) - **Importante:** Jamais salve a senha em texto puro! Salve apenas o hash.
    
- `created_at` (timestamp)
    

#### Tabela: `sources`

O catálogo de APIs que o nosso sistema conhece.

- `id` (serial) - Chave Primária
    
- `name` (texto) - Nome amigável da fonte (ex: "Artigos de JavaScript do DEV.to").
    
- `identifier` (texto, único) - Um identificador único (ex: `devto_javascript`).
    
- `api_url` (texto) - A URL exata que nosso coletor vai chamar.
    
- `category` (texto) - Para agrupar (ex: 'TECNOLOGIA', 'FINANÇAS').
    

#### Tabela: `items`

Onde armazenamos cada artigo, notícia ou dado que coletamos.

- `id` (serial) - Chave Primária
    
- `source_id` (inteiro) - Chave Estrangeira que aponta para `sources.id`.
    
- `title` (texto)
    
- `url` (texto, único) - A URL original do item. Usar como "único" ajuda a não salvar o mesmo item duas vezes.
    
- `published_at` (timestamp) - A data de publicação original do item.
    
- `created_at` (timestamp) - Quando nosso sistema o salvou.
    

#### Tabela: `subscriptions` (A Tabela de Ligação)

É o coração da lógica de notificação. Ela diz "qual usuário quer receber notícias de qual fonte".

- `user_id` (uuid ou inteiro) - Chave Estrangeira que aponta para `users.id`.
    
- `source_id` (inteiro) - Chave Estrangeira que aponta para `sources.id`.
    
- `created_at` (timestamp)
    
- _(A Chave Primária aqui é a combinação de `user_id` e `source_id` para garantir que um usuário não se inscreva na mesma fonte duas vezes)._
    

**Como tudo se conecta?** Quando o notificador for rodar, ele fará uma lógica parecida com esta:

1. Para um `user_id` específico...
    
2. ...busque na tabela `subscriptions` todos os `source_id` associados a ele.
    
3. ...depois, busque na tabela `items` todos os itens recentes que tenham esses `source_id`.
    
4. ...pronto! Agora você tem a lista de conteúdo para enviar no e-mail daquele usuário.
