# Porto Açaí — Sistema Operacional PWA para Gestão de Pedidos, Cozinha e Logística em Tempo Real

Este repositório documenta a arquitetura, as decisões de engenharia e o impacto operacional do ecossistema digital desenvolvido para o **Porto Açaí**.

O sistema foi projetado para transformar uma operação física baseada em papel, memória operacional e WhatsApp em um fluxo digital mais ágil, rastreável e conectado em tempo real.

> **Confidencialidade:** o código-fonte completo é de propriedade exclusiva do cliente. Este repositório funciona como um memorial descritivo da arquitetura, da stack tecnológica e dos desafios de engenharia resolvidos, para fins de portfólio técnico.

---

## Visão geral

O projeto não foi desenvolvido como um aplicativo genérico de vendas.

Ele foi criado como um sistema operacional sob medida para a rotina do cliente, conectando os principais pontos da operação:

* atendimento ao cliente;
* pedidos;
* cozinha;
* logística;
* vendedores ambulantes;
* caixas;
* administração;
* histórico operacional.

O sistema possui características de um ERP modular, mas foi desenhado com escopo específico para a operação real do Porto Açaí.

---

## Módulos principais

### 1. Módulo Cliente — PWA

Interface pública acessada pelo cliente para seleção de produtos, personalização de pedidos e envio da solicitação.

O módulo foi construído como PWA, permitindo uma experiência leve, acessível pelo navegador e adequada ao uso em dispositivos móveis.

### 2. Painel de Operações — Cozinha e Logística

Painel interno para acompanhamento dos pedidos em tempo real.

A cozinha visualiza tickets, aceita pedidos, acompanha o preparo e sinaliza mudanças de status. A logística acompanha a saída dos pedidos e organiza entregas.

### 3. Módulo Ambulante

Funcionalidade desenhada para vendedores em ambiente externo, especialmente em contexto de praia, onde a conexão pode ser instável e o uso precisa ser rápido.

O fluxo foi pensado para operar em dispositivos móveis e redes 3G/4G, reduzindo fricção durante o atendimento.

### 4. Painel Administrativo

Área administrativa para acompanhamento da operação, consulta de histórico, controle de equipe, gestão de permissões e apoio na resolução de conflitos com clientes.

---

## Objetivo do projeto

O objetivo principal foi reduzir o caos operacional e tornar o fluxo de pedidos mais rastreável.

Antes do sistema, parte da operação dependia de comunicação manual, memória, papel e mensagens dispersas.

Com o sistema, os pedidos passaram a ser organizados em um fluxo digital com:

* registro centralizado;
* atualização de status;
* histórico de pedidos;
* separação de funções;
* comunicação em tempo real;
* maior rastreabilidade;
* redução de retrabalho operacional.

---

## Cloud Economics e infraestrutura de baixo custo

Um dos objetivos de engenharia foi manter a infraestrutura simples, barata e adequada ao volume real da operação.

A arquitetura foi desenhada utilizando serviços serverless e BaaS, principalmente:

* Vercel para deploy;
* Supabase para banco de dados, autenticação, Row-Level Security e realtime.

No estágio atual da operação, o projeto utiliza os free tiers disponíveis dessas plataformas, mantendo o custo operacional inicial de cloud em R$ 0,00/mês dentro do volume atual de uso.

O setup inicial de produção e implantação teve custo aproximado de R$ 180,00, considerando registros, domínios e configurações essenciais.

> Observação: esse valor se refere ao custo de infraestrutura/cloud no estágio atual do projeto, não ao custo de desenvolvimento, suporte, manutenção evolutiva ou operação futura em escala maior.

---

## Desafios técnicos e decisões de arquitetura

### 1. Realtime, concorrência e tolerância a falhas

A operação exigia atualização rápida entre cliente, cozinha, caixa, ambulantes e logística.

Para isso, o sistema utiliza recursos de realtime do Supabase, conectados a eventos do PostgreSQL por meio de `postgres_changes`.

Isso permitiu criar um fluxo de atualização em tempo real sem depender de polling HTTP constante.

Principais decisões:

* uso de subscriptions realtime;
* atualização de tickets conforme mudanças no banco;
* separação de estados do pedido;
* redução de duplicidade operacional;
* ocultação de tickets já assumidos por outro operador;
* sincronização entre diferentes telas da operação.

### 2. Idempotência e controle de duplicidade

Em ambientes com conexão instável, especialmente no módulo ambulante, existe risco de duplicação de pedidos por múltiplos cliques, lentidão da rede ou reenvio acidental.

Para reduzir esse risco, o sistema utiliza chaves de idempotência geradas no frontend e validadas no banco por meio de restrições de unicidade.

Esse desenho ajuda a impedir que o mesmo pedido seja registrado mais de uma vez em situações de instabilidade.

### 3. Segurança com Row-Level Security

O sistema utiliza políticas de Row-Level Security do PostgreSQL/Supabase para separar permissões e reduzir exposição de dados.

O módulo público possui acesso restrito e controlado. As políticas foram desenhadas para permitir apenas operações necessárias ao fluxo, evitando acesso indevido a tabelas críticas ou dados de terceiros.

No desenho atual, as políticas de RLS impedem operações não autorizadas de leitura ou alteração em áreas protegidas do sistema.

### 4. Segurança operacional leve

A operação precisava de segurança, mas também exigia velocidade.

Por isso, o sistema combina mecanismos simples e adequados ao contexto:

* variáveis de ambiente para segredos;
* URLs internas não públicas;
* validações leves de acesso;
* separação de painéis;
* permissões por tipo de operação;
* restrição de ações sensíveis.

A decisão foi evitar autenticações pesadas demais em pontos operacionais de alta velocidade, sem abrir mão de uma separação mínima de responsabilidades.

---

## Integração com hardware

### Web Bluetooth e ESC/POS

O projeto inclui integração com mini impressoras térmicas usando Web Bluetooth API e protocolo ESC/POS.

Essa solução permite que o navegador se comunique diretamente com a impressora térmica, sem depender de aplicativo nativo ou API proprietária.

A cozinha opera principalmente em telas digitais. A impressão física é acionada sob demanda, quando necessário, especialmente no momento de saída ou organização do pedido.

Essa abordagem ajudou a reduzir custo de hardware e evitar dependência de impressoras mais caras ou infraestrutura local complexa.

---

## Modelagem de dados

O banco foi modelado com uma abordagem híbrida entre dados relacionais estruturados e campos JSON controlados.

A decisão foi evitar armazenar todo o pedido como um único payload genérico em JSON, pois isso dificultaria consistência, filtros, relatórios e análises futuras.

Foram mantidos como dados estruturados:

* status de pedido;
* horários;
* tempos de preparo;
* identificadores;
* vínculos operacionais;
* estados de fluxo;
* informações relevantes para relatórios.

Campos JSON foram usados de forma pontual para partes altamente variáveis, como personalizações de produtos, adicionais e combinações flexíveis.

Essa decisão preserva flexibilidade sem comprometer totalmente a consistência analítica do banco.

---

## Telemetria e histórico operacional

O sistema foi estruturado para registrar informações úteis sobre a operação, como:

* horário de criação do pedido;
* aceite pela cozinha;
* preparo;
* saída para entrega;
* conclusão;
* histórico de status;
* dados de personalização;
* responsáveis operacionais.

Esses dados permitem análises futuras de produtividade, gargalos, tempos de ciclo e desempenho operacional.

---

## Impacto no negócio

O sistema já intermediou quase 3.000 pedidos reais em produção.

Durante o uso observado, não houve registro de perda de dados em transições críticas de status de pedidos.

Além da parte técnica, o sistema ajudou a reorganizar a operação do cliente.

### Separação de funções

Antes, a operação dependia mais de comunicação manual e improviso.

Com o sistema, funções passaram a ser melhor separadas:

* vendedores focados em venda;
* cozinha focada em preparo;
* entregadores focados em logística;
* administração focada em controle e histórico.

Essa separação ajudou a tornar a operação mais previsível e menos dependente de memória individual.

### Rastreabilidade

O histórico de pedidos permitiu consultar informações específicas sobre montagem, status e detalhes de cada venda.

Isso reduziu conflitos operacionais e ajudou o cliente a lidar melhor com dúvidas, reclamações ou divergências.

### Comunicação assíncrona

O projeto também utiliza OneSignal para notificações operacionais.

As notificações foram usadas para apoiar comunicações internas, como alertas de falta de insumos ou chamadas operacionais entre equipe e gestão.

---

## Validação em uso real

Durante pico de temporada turística, o sistema foi utilizado em uma operação com múltiplos usuários simultâneos, incluindo:

* vendedores ambulantes em conexão móvel;
* cozinheiros;
* entregadores;
* caixas;
* administradores;
* clientes acessando o PWA público.

Em um dos cenários de maior uso observado, a operação contou com aproximadamente 13 operadores simultâneos, além do tráfego público do PWA.

Essa validação em produção ajudou a comprovar que a arquitetura era adequada para o volume e a realidade operacional do cliente naquele estágio.

---

## Stack tecnológica

### Frontend

* React 19;
* Vite;
* PWA;
* React Router v7.

### Backend e banco de dados

* Supabase;
* PostgreSQL;
* Row-Level Security;
* Realtime Subscriptions;
* `postgres_changes`.

### Hardware e integrações

* Web Bluetooth API;
* ESC/POS;
* OneSignal.

### Deploy

* Vercel.

---

## Principais aprendizados técnicos

Este projeto exigiu decisões práticas em várias camadas:

* arquitetura realtime;
* modelagem de dados para operação real;
* controle de concorrência;
* segurança com RLS;
* redução de custo de infraestrutura;
* integração com hardware simples;
* experiência de uso em ambiente com conexão instável;
* desenho de fluxo para equipe não técnica;
* transformação de processo físico em processo digital.

O maior desafio não foi apenas construir telas ou banco de dados.

O desafio foi entender o domínio da operação e traduzir esse domínio em software utilizável no ritmo real do negócio.

---

## O que este projeto demonstra

Este case demonstra experiência prática em:

* desenvolvimento full stack;
* sistemas em produção;
* arquitetura orientada a eventos;
* PWA;
* realtime;
* Supabase;
* PostgreSQL;
* RLS;
* integração com hardware via navegador;
* modelagem de dados operacional;
* cloud economics;
* produto sob medida para negócio real;
* engenharia com restrições reais de custo, rede e uso.

---

## Limitações e escopo

Este repositório é apenas um memorial técnico.

O código-fonte completo não está disponível publicamente por questões de confidencialidade e propriedade do cliente.

As informações descritas aqui representam a arquitetura, as decisões de engenharia e os resultados observados no contexto específico do Porto Açaí.

O projeto não é apresentado como uma solução universal para restaurantes ou food service, mas como um sistema sob medida que resolveu problemas reais de uma operação específica.

---

## Autor

Desenvolvido por **João Davies**.

Projeto full stack desenvolvido para operação real em produção, com foco em gestão de pedidos, realtime, logística, baixo custo de infraestrutura e rastreabilidade operacional.
**LinkedIn:** [www.linkedin.com/in/joao-davies-133587229]
