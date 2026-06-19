# Porto Açaí - Ecossistema ERP & PWA (Real-time Management System)

Este repositório documenta a arquitetura, as decisões de engenharia e o impacto de negócio do ecossistema digital desenvolvido para o *Porto Açaí*. O sistema foi projetado para transformar uma operação física caótica (baseada em papel, memória e WhatsApp) em um fluxo digital ágil, assíncrono e conectado em tempo real.

> **Confidencialidade:** O código-fonte completo é de propriedade exclusiva do cliente. Este documento serve como memorial descritivo da arquitetura de software, da stack tecnológica e dos desafios de engenharia resolvidos, para fins de portfólio.

---

## 🏗️ Visão Global do Ecossistema

Diferente de um simples aplicativo de vendas, este projeto é um **ERP modular** que gerencia todos os pontos críticos do negócio:
* **Módulo Cliente (PWA):** Interface pública para seleção de produtos e personalização dinâmica de acompanhamentos.
* **Painel de Operações (Cozinha e Logística):** Gestão de tickets em tempo real para preparação e saída de entregas.
* **Módulo Ambulante:** Funcionalidade desenhada para vendas na praia, operando sob redes 3G instáveis.
* **Painel Administrativo:** Controle de staff, segurança, histórico de vendas e resolução de litiges (conflitos com clientes).

---

## 💰 Cloud Economics & Infraestrutura de Baixo Custo

Um dos maiores diferenciais deste projeto é a otimização extrema de recursos em nuvem e a mentalidade de Engenharia de Produto focada em viabilidade financeira:
* **Custo Zero de Manutenção:** A arquitetura foi desenhada aproveitando ao máximo serviços Serverless (Vercel) e BaaS (Supabase). O custo operacional mensal para manter o sistema no ar e escalável é de R$ 0,00.
* **Setup Inicial Otimizado:** O gasto total de produção e implantação de infraestrutura foi de **apenas R$ 180,00 (pagos uma única vez)**, englobando registros, domínios e configurações essenciais.

---

## ⚙️ Desafios Técnicos & Soluções de Arquitetura

### 1. Tolerância a Falhas e Sincronização (Realtime & Concurrency)
* **Arquitetura Orientada a Eventos:** Sincronização imediata entre o Módulo Cliente e a Cozinha via *hooks* (`useEffect`) conectados aos canais do PostgreSQL (`postgres_changes` via Supabase). A latência é quase nula, eliminando a necessidade de *polling* HTTP.
* **Idempotência e Bloqueio de Estado:** Para evitar duplicação de pedidos (comum em redes 3G lentas na praia) e concorrência na cozinha, o sistema utiliza chaves de idempotência geradas no frontend e validadas por *Unique Constraints* no banco. Quando um cozinheiro aceita uma comanda, o estado do banco oculta esse ticket instantaneamente para os outros operadores.

### 2. Segurança Multi-camadas (Defense in Depth)
* **Princípio do Menor Privilégio (RLS):** O módulo de cardápio público é isolado. Graças às políticas de *Row-Level Security* (RLS) do PostgreSQL, terminais públicos possuem apenas permissão de escrita (`INSERT`) com limites lógicos de volume (evitando ataques de negação de serviço ou spam). É matematicamente impossível realizar `SELECT` ou `UPDATE` em pedidos de terceiros ou tabelas críticas.
* **Segurança Operacional Fluida:** Combinação de senhas globais ocultas (`.env`) para administração e validações leves no cliente (PINs e URLs ofuscadas) para barrar acessos não autorizados nos tablets do balcão, sem prejudicar a velocidade da operação com autenticações pesadas de rede.

### 3. Integração de Hardware de Baixo Nível (Web Bluetooth & ESC/POS)
* **Impressão Direta sem API Nativa:** Integração de uma biblioteca de conversão hexadecimal permitindo que o navegador (via Web Bluetooth API) comunique-se diretamente com mini impressoras térmicas utilizando o protocolo ESC/POS.
* **Fluxo Sob Demanda:** A cozinha opera 100% em telas (digital). A impressão física é assíncrona e acionada manualmente pelo entregador com baixíssima latência (em torno de 2 metros de distância via Bluetooth) apenas quando o pedido está finalizado, economizando hardware e papel.

### 4. Modelagem Híbrida e Inteligência de Dados (PostgreSQL / Supabase)
* **Modelagem Relacional Avançada vs. JSON:** Diferente de abordagens genéricas que transformam todo o payload em JSON (o que penaliza a performance e quebra a consistência), o banco de dados foi modelado de forma híbrida. Dados matemáticos, estados temporais rígidos (métrica de tempo de preparo, horários de entrega) e status de fluxo são tipos de dados nativos estruturados. Campos JSON foram estritamente isolados apenas para dados altamente mutáveis de personalização de produtos (ex: combinações infinitas de adicionais).
* **Análise e Telemetria Operacional:** O banco foi estruturado de forma analítica, retendo dados precisos de tempos de ciclo (pedido gerado -> aceito -> preparado -> entregue) para viabilizar relatórios futuros de inteligência de negócio (BI) e projeções de produtividade.
---

## 📈 Impacto no Negócio & Teste de Carga

* **Confiabilidade Extrema (Zero Data Loss):** A plataforma já intermediou **quase 3.000 pedidos reais em produção**, apresentando **0% de perda de dados** ou falhas em transições de status crítico de pedidos, provando a estabilidade e a resiliência da infraestrutura sob stress de uso contínuo.
  
O desenvolvimento foi guiado por um profundo entendimento do domínio (Domain Knowledge), reestruturando a lógica da empresa:

* **Especialização de Cargos (Escalabilidade Humana):** O sistema isolou as funções. Vendedores de alta performance agora ficam fixos na praia (onde o dinheiro está), enquanto a logística é feita por entregadores guiados pelas coordenadas exatas do sistema.
* **Rastreabilidade:** O histórico imutável reduziu prejuízos com "clientes problemáticos", fornecendo provas exatas e imediatas sobre a montagem de cada pedido.
* **Comunicação Assíncrona via Push:** Implementação do OneSignal para que o dono gerencie a equipe via notificações sonoras (ex: chamadas para almoço) e a cozinha sinalize falta de insumos em tempo real ("acabou o morango").
* **Stress Test em Produção:** No pico da temporada turística, a arquitetura sustentou de forma fluida **13 operadores simultâneos** (3 entregadores, 3 cozinheiros, 2 administradores, 3 vendedores ambulantes no 3G, 2 caixas de balcão), além de todo o tráfego público do PWA dos clientes.

---

## 💻 Stack Tecnológica

* **Frontend:** React 19, Vite (HMR), PWA, React Router v7.
* **Backend & Banco de Dados:** Supabase, PostgreSQL (RLS, Realtime Subscriptions).
* **Hardware & APIs:** Web Bluetooth API, OneSignal (Push Notifications).
* **Deploy:** Vercel.
**Desenvolvido por:** [João Davies]  
**LinkedIn:** [www.linkedin.com/in/joao-davies-133587229]
