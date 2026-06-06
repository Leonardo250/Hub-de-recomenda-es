.

🛸 HubA - Anime Recommendation API
O HubA é uma API REST desenvolvida em Java com Spring Boot que atua como um agregador e filtro inteligente de animes. O objetivo principal do projeto é consumir dados da API pública do Jikan (MyAnimeList) e aplicar regras de negócio customizadas para filtrar e recomendar apenas o "suprassumo" das produções (como clássicos dos anos 90 e 2000, como Cowboy Bebop), eliminando repetições de tropos modernos e genéricos.

🛠️ Tecnologias Utilizadas
Java 21 & Spring Boot 3

Spring WebFlux (WebClient) - Para consumo assíncrono e performático de APIs externas.

Lombok - Para redução de código boilerplate através de anotações como @Data e @Builder.

Java Records - Para representação imutável de sub-DTOs.

Postman - Para validação de endpoints e testes de integração.

🏗️ Arquitetura do Projeto
O projeto segue os padrões de clean code e separação de responsabilidades do ecossistema Spring:

Controller: Expõe o endpoint GET /api/v1/animes/recomendar-algo-bom e recebe os parâmetros de busca.

Client: Camada isolada responsável exclusivamente pela comunicação HTTP com a API externa.

Service: Onde reside a inteligência do projeto, responsável por receber a lista bruta de animes e aplicar os filtros de qualidade baseados na sinopse, gêneros e metadados.

DTOs: Estruturas de dados otimizadas para mapear o JSON externo e expor a resposta limpa para o cliente final.

🛑 Relato Técnico: O Desafio da API Externa & Resiliência
Durante a fase de testes integrados utilizando o Postman, a aplicação deparou-se com uma barreira de infraestrutura externa: a API do Jikan passou a retornar consistentemente o status 504 Gateway Timeout, acompanhado da mensagem:

{"status":504,"type":"BadResponseException","message":"Jikan failed to connect to MyAnimeList..."}

🔍 Diagnóstico do Problema
O erro não foi originado pelo código do HubA. A arquitetura local (rotas, injeção de dependências e mapeamento de DTOs) estava processando as requisições perfeitamente. O problema ocorreu devido à instabilidade nos servidores de upstream do MyAnimeList, que bloquearam ou limitaram as requisições vindas da API pública do Jikan naquele momento.

💡 Solução de Resiliência (Mocking Local)
Para não paralisar o ciclo de desenvolvimento e garantir a validação da camada de negócios (Service), foi aplicada uma estratégia de Mocking.

Utilizando o padrão @Builder do Lombok, a chamada real do WebClient foi temporariamente interceptada no JikanClient para injetar dados estáticos de teste controlados localmente (incluindo animes altamente recomendados e animes genéricos propositalmente inseridos para teste de expurgo).

Resultado: A aplicação alcançou o status 200 OK, processou o filtro com sucesso em memória e validou toda a pipeline do projeto de forma isolada e resiliente.
