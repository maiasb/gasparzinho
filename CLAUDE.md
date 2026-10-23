# Gasparzinho (nome provisório)

App mobile (Android e iOS) com um agente de IA que **prepara** Pix por voz ou texto, a partir de um chat flutuante (Android), da barra de notificações ou do app. O usuário **sempre confirma** (biometria) antes do envio; a confirmação automática está fora do escopo atual. Também paga contatos da agenda. Histórico das transações, regras de segurança e LGPD desde o início.

## Decisões já tomadas

- **Caminho B:** o app inicia o Pix por API (Open Finance, via instituição iniciadora de pagamento autorizada), sem ler nem controlar o app do banco. Proibido usar serviço de acessibilidade ou automação de tela para agir em outros apps.
- **Modelo de IA:** só ferramentas de preparação; a execução do pagamento sai exclusivamente da confirmação do usuário na tela.
- **Agenda e voz:** resolver contatos no aparelho; enviar ao servidor e ao modelo apenas o necessário. Detalhes no inventário LGPD.
- **Nome e mascote:** não usar o personagem Casper/Gasparzinho (direitos de terceiros). Mascote é original. O nome "Gasparzinho" é provisório até consulta de marca (INPI) com profissional habilitado.
- **Protótipo de telas:** `prototipo/index.html`.

## Agentes deste projeto

- **Stack:** proposta preliminar, **a decidir em ADR do dev-arquitetura**: React Native + Expo + TypeScript (app); Node + TypeScript (backend); API da Anthropic com tool use (agente).
- **Banco:** não decidido (padrão da equipe: MySQL).
- **Usa:** arquiteto-specs, dev-arquitetura, dev-mobile, dev-uiux, dev-react (backend Node/TypeScript), dev-ai-agents, dev-integrations, dev-auth, dev-database, dev-security, lgpd-specialist, dev-tests, dev-reviewer, dev-devops, dev-observability, dev-debugger, dev-infra, docs-handoff.
- **Não usa:** dev-dotnet, dev-performance-seo, copywriter, prospeccao-clientes, candidaturas, whatsapp. (dev-notifications entra só se o push for adotado.)
- **Fluxo:** Descoberta (arquiteto-specs) → Stack (dev-arquitetura + dev-infra) → Fundação (dev-devops, dev-database, dev-auth) → Construção por módulo (dev-uiux → dev-mobile/dev-react → dev-tests → dev-reviewer) → Pré-entrega (dev-security, lgpd-specialist) → Entrega (docs-handoff).
- **Pendências:**
  - `regulatorio-fintech`: **não criado por decisão do usuário**. Enquanto isso, enquadramento regulatório (ITP/Open Finance/Banco Central), contrato com o parceiro, termos de uso e marca são levados a **advogado ou especialista regulatório humano**.
  - dev-qa e dev-pentest não cobrem app em aparelho nem teste dinâmico de app: tratar como **não testado por agente** e planejar teste manual em Android e iOS reais e teste de segurança por especialista humano antes de publicar.
  - dev-notifications: entra se houver push (FCM/APNs).
  - Requisitos de risco e desempenho a registrar na spec (arquiteto-specs): regras antifraude, orçamento de latência, disponibilidade, picos, teto de custo de IA, atendimento e resposta a incidente financeiro.
  - Certificados e autenticação entre servidores com o parceiro (Open Finance): exigência a confirmar na documentação; rotação e alerta de vencimento a desenhar.
  - Teste de carga: dev-tests escreve, dev-devops executa em staging; ainda sem cenário nem meta.
  - Monitoramento de crash e ANR do app (dev-observability): ferramenta a escolher, com LGPD.
  - Segurança dinâmica do app: ver `docs/seguranca/seguranca-dinamica-mobile.md`.

## Estado atual

Spec v0.1 em `docs/specs/2026-10-22-gasparzinho-spec-v0.md` (rascunho, sem revisão humana). Perguntas em aberto, pendências técnicas e ordem dos próximos passos: `docs/pendencias-decisoes.md` (retomar pelas perguntas 5 a 11).

## Perguntas em aberto (antes da spec)

(Lista inicial, anterior à spec; a lista atual está em `docs/pendencias-decisoes.md`.)

1. Parceiro de iniciação de pagamento e bancos cobertos (verificar documentação do Open Finance Brasil e do Banco Central).
2. Se a jornada de pagamento sem redirecionamento existe e está disponível (hipótese não verificada).
3. Limites por transação e por dia; política de destinatário novo.
4. Prazo de retenção do histórico (obrigação legal de guarda: confirmar com profissional habilitado).
5. Plataformas no lançamento: Android primeiro (bolha e atalho existem só nele) ou as duas juntas.
