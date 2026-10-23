# Pendências e decisões em aberto (retomar daqui)

Atualizado em 2026-10-22, após a spec v0.1 (`docs/specs/2026-10-22-gasparzinho-spec-v0.md`, rascunho de descoberta, ainda não revisada por humano). Os códigos PEND-xx e RN/RNF/CA são os da spec.

Legenda: **Sugestão** = recomendação do assistente, não decisão. Nada abaixo foi decidido pelo usuário, salvo o que estiver em "Decisões já tomadas" do `CLAUDE.md`.

## Perguntas 5 a 11 (prioridade para a próxima sessão)

### 5. Plataforma e público (PEND-05)
- **Pergunta:** quem é o público e qual plataforma sai primeiro? Confirma Android primeiro, com chat dentro do app no MVP e bolha + atalho na barra na fase 1b?
- **Contexto:** bolha flutuante e atalho na barra de notificações existem só no Android; no iOS há atalhos, widget e Siri. A bolha tem risco de revisão da loja e de microfone em segundo plano.
- **Sugestão (agente de specs):** Android primeiro; iOS na fase 2.
- **Bloqueia:** ADR de stack (dev-arquitetura), escopo do MVP, orçamento do build iOS (exige macOS).

### 6. Modelo de receita e teto de gasto de IA (PEND-09, RNF-07)
- **Pergunta:** como o app ganha dinheiro? Quem paga a tarifa do parceiro de pagamento e o custo de IA e voz? Qual o teto de gasto de IA por requisição, por usuário/mês e global/mês?
- **Contexto:** cada comando usa a API do modelo e, se for em nuvem, reconhecimento de voz; sem teto, um abuso gera custo ilimitado.
- **Bloqueia:** limites de uso (rate limit), degradação quando o teto estoura, proposta/precificação (orcamentos-propostas).

### 7. Fluxo manual sem IA como modo degradado (PEND-12)
- **Pergunta:** aceita já no MVP um fluxo manual (escolher contato, digitar valor, confirmar) que funcione quando a IA ou a voz falharem?
- **Sugestão:** sim. Reduz risco de indisponibilidade e de custo, e reaproveita a tela de confirmação, que já é obrigatória.
- **Bloqueia:** desenho das telas (dev-uiux) e dos modos de falha (RNF de degradação).

### 8. Nível do MASVS e pentest mobile humano (PEND-08, PEND-20)
- **Pergunta:** qual nível do OWASP MASVS será o alvo, e há orçamento para um pentest mobile feito por pessoa?
- **Contexto:** os níveis do MASVS estão `[A CONFIRMAR]` na spec (só os 8 grupos de controle foram conferidos). Teste dinâmico do app não é coberto por agente: ver `docs/seguranca/seguranca-dinamica-mobile.md`.
- **Bloqueia:** critérios de segurança do app (dev-security), plano de pré-entrega, data de publicação.

### 9. Atendimento e política de perda (PEND-10, PEND-21)
- **Pergunta:** qual canal e prazo de atendimento ao usuário? Qual a política se alguém perder dinheiro por golpe ou erro?
- **Contexto:** prazos e mecanismos de devolução/contestação do Pix vão para profissional habilitado (advogado/especialista); aqui é a decisão de negócio e de comunicação.
- **Relacionada:** pergunta 3 (quem decide e fala com parceiro e usuários em incidente financeiro, PEND-06).
- **Bloqueia:** RN-ATD/RN-INC, termos de uso, plano de incidentes (lgpd-specialist).

### 10. Biometria, push e notificação na tela bloqueada (PEND-11, PEND-13)
- **Pergunta:** a senha do aparelho pode substituir a biometria na confirmação? O push entra no MVP? A notificação mostra valor e nome na tela bloqueada?
- **Sugestão:** confirmação só com biometria do sistema, com a senha do aparelho como alternativa apenas se o usuário não tiver biometria cadastrada (**a validar** com dev-security e dev-auth). Notificação na tela bloqueada sem valor nem nome (privacidade).
- **Contexto:** se push entrar, dev-notifications passa a ser usado (FCM/APNs).
- **Bloqueia:** fluxo de confirmação (CA-PIX), RN-SEG, perfil de agentes do projeto.

### 11. Aparelhos de referência e versões mínimas (PEND-18)
- **Pergunta:** quais aparelhos de referência e quais versões mínimas de Android e iOS?
- **Contexto:** define matriz de teste manual em aparelho real (não coberta por agente), uso de biometria/voz/overlay e tamanho do público alcançado.
- **Bloqueia:** ADR de stack, roteiro de teste de aparelho (dev-qa), metas de desempenho e tamanho (RNF).

## Demais perguntas (1 a 4 e 12)

1. **Parceiro de pagamento (ITP) e bancos cobertos** (PEND-01): há conversa ou contrato em andamento?
2. **Consulta a advogado/especialista regulatório antes de construir** (PEND-02): o desenvolvedor precisa ser ou operar sob uma ITP? **Bloqueia a fase 1.** O agente `regulatorio-fintech` não foi criado por decisão do usuário.
3. **Quem decide e fala com o parceiro e os usuários em incidente financeiro** (PEND-06).
4. **Valores de risco** (PEND-07): teto por transação e por dia, teto e período para destinatário novo, janela noturna, velocidade, falhas de biometria. Todas as RN-RSK estão com valor `[PENDENTE]`.
12. **Consulta de marca no INPI** (PEND-22): quem faz, e há nome de reserva? "Gasparzinho" é provisório.

Dependem de documentação do parceiro/Open Finance e do dev-arquitetura, não do usuário: PEND-03 (jornada sem redirecionamento) e PEND-04 (voz no aparelho ou na nuvem).

## Ordem sugerida dos próximos passos

1. Responder as perguntas 5 a 11 (e, quando possível, 1 e 2).
2. dev-arquitetura: ADR de stack (precisa das respostas 5 e 11).
3. dev-uiux: prototipar Ajustes e Onboarding (não depende de nada) e a tela do fluxo manual (depende da 7).
4. Revisão humana da spec v0.1 e das RN-RSK com os valores da pergunta 4.
5. lgpd-specialist: inventário e RIPD; dev-security: modelo de ameaças do app.

## Pendências técnicas e de ambiente (não são perguntas)

- Protótipo `prototipo/index.html` nunca foi aberto em navegador (visual não verificado); faltam as telas de Ajustes e Onboarding.
- Gaps do protótipo registrados na spec: "quanto gastei" soma só Pix feitos pelo app; notificação mostra valor e nome na tela bloqueada; "Quando: agora" sem agendamento.
- Maestro, Node, MobSF, mitmproxy/Burp e Frida não estão instalados. Android SDK e AVD `gaspar_pixel` estão instalados (`~/Android/Sdk`, JDK em `~/.local/jdk`).
- Os agentes novos e as edições (dev-mobile e extensões) só valem após reiniciar a sessão do Claude Code.
- Snapshot do RAG mobile: atualizar com `cd ~/.claude/mcp/mobile-docs && npm run sync` (~7 min).
- O projeto não é repositório git; nenhum commit foi feito.
- Itens não cobertos por agente: iOS (precisa de macOS), voz/biometria/overlay em aparelho real, segurança dinâmica do app (pentest humano), enquadramento regulatório e contrato com o parceiro (profissional humano).
