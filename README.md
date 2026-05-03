# Porto Açai - Sistema Integrado de Gestão (Full Stack PWA) 

Este repositório documenta a arquitetura e as decisões técnicas do ecossistema digital desenvolvido para o quiosque **Porto Açai**. O sistema foi projetado para transformar a operação física em um fluxo digital ágil e conectado em tempo real.

> **Confidencialidade:** O código-fonte deste projeto é de propriedade exclusiva do cliente. Este documento serve como memorial descritivo da stack tecnológica e das funcionalidades implementadas para fins de portfólio.

---

##  Visão Geral do Ecossistema
Diferente de um app de vendas comum, este projeto é um **ERP modular** que gerencia todas as pontas do negócio:

- **Módulo Cliente:** Interface PWA para seleção de produtos e personalização dinâmica de acompanhamentos.
- **Painel de Operações (Cozinha e Entregas):** Gestão de tickets em tempo real para preparação e logística de saída.
- **Módulo Ambulante:** Funcionalidade específica para vendas externas e mobilidade.
- **Painel Administrativo:** Controle de staff, segurança (Password Protector) e histórico geral de vendas.

##  Stack Tecnológica (Modern Web)
O projeto utiliza as tecnologias mais atuais do mercado, garantindo performance e escalabilidade:

- **Frontend:** `React 19` (versão mais recente) com `Vite` para um carregamento ultra-rápido.
- **Backend & Database:** `Supabase` (PostgreSQL) para gerenciamento de dados e autenticação em tempo real.
- **Comunicação:** `OneSignal` integrado para notificações push (alertas de novos pedidos para a equipe).
- **Roteamento:** `React Router Dom v7` para navegação entre os múltiplos painéis.
- **Utilidades:** `Date-fns` para precisão em cronogramas de entrega e `FontAwesome` para interface intuitiva.

## Soluções de Engenharia Aplicadas
1. **Sincronia Real-time:** Utilização do Supabase para que a cozinha receba pedidos instantaneamente, sem necessidade de atualização manual da página.
2. **Arquitetura de Componentes:** Estrutura modular (`App.jsx`, `VitorPainel.jsx`, `StaffLogin.jsx`) que facilita a manutenção e adição de novos módulos.
3. **PWA & Mobilidade:** Configurado com `Vercel` e lógica de instalação (`InstallApp.jsx`), permitindo o uso como um aplicativo nativo em tablets e celulares do quiosque.
4. **Segurança:** Implementação de variáveis de ambiente (`.env`) e validação de acesso para áreas críticas do sistema.

##  Impacto no Negócio
- **Agilidade:** Redução drástica no tempo entre a escolha do cliente e o início da preparação na cozinha.
- **Redução de Erros:** Sistema de comanda digital que elimina falhas de comunicação humana.
- **Gestão Centralizada:** Histórico de entregas e staff acessíveis em um único lugar para o proprietário.

---
**Desenvolvido por:** [João Davies]  
**LinkedIn:** [www.linkedin.com/in/joao-davies-133587229]
