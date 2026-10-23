# Segurança dinâmica do app mobile: o que é, o que falta e o que cuidar

Nota de estudo para entender depois. Escrita em 2026-10-22. Tudo o que está aqui sobre as ferramentas vem do meu conhecimento geral e **não foi verificado na documentação oficial desta versão**; confirmar antes de usar.

## 1. O que é "segurança dinâmica"

- **Estática:** olhar o código e o pacote do app (APK/IPA) **sem rodar**. Acha segredo escrito no código, permissão demais, biblioteca vulnerável. Os agentes `dev-security` e `dev-reviewer` fazem isso.
- **Dinâmica:** **rodar o app** e tentar atacá-lo: ver o que ele manda pela rede, o que guarda no aparelho, se dá para alterar o comportamento dele em execução. É o que um atacante real faz. **Nenhum agente do time faz isso para app mobile** (o `dev-pentest` é só de web e API).

Por que importa aqui: o app guarda sessão, fala com um parceiro de pagamento e dispara Pix. O binário é público e o aparelho pertence ao atacante.

## 2. As ferramentas citadas

| Ferramenta | Em uma frase | O que ela mostra neste projeto |
|---|---|---|
| **MobSF** | Analisa o app (estática e também dinâmica, rodando no emulador) e gera um relatório | Permissões, segredos no binário, configurações fracas, tráfego sem criptografia |
| **mitmproxy / Burp Suite** | Ficam "no meio" entre o app e a internet para você **ver e alterar o tráfego** | O que o app envia ao servidor; se o servidor aceita um valor ou destinatário alterado na mão |
| **Frida** | Injeta código no app **enquanto ele roda** (hooking) | Se dá para contornar biometria, a detecção de root ou a confirmação de pagamento |

Resumo: MobSF = "check-up geral"; mitmproxy/Burp = "o que passa pelo fio"; Frida = "mexer no app por dentro".

## 3. Pendências (o que ainda não existe)

1. **Ninguém executa isso hoje.** `dev-qa` só tem Chrome; `dev-pentest` é web/API. Está registrado como "não testado por agente" no `CLAUDE.md` do projeto.
2. **Não há app para testar ainda.** O projeto só tem protótipo HTML. Nada a atacar até existir um build.
3. **Ferramentas não instaladas.** Foram instalados só o Android SDK, o emulador (`gaspar_pixel`) e o `adb`. MobSF, mitmproxy/Burp e Frida **não estão instalados**.
4. **Teste em iOS impossível aqui.** Exige macOS; este ambiente é Linux. Quando houver iOS, precisa de um Mac ou serviço em nuvem.
5. **Falta decidir quem faz:** o resultado final deve ser de **especialista humano** (pentest mobile) antes de publicar um app financeiro. Falta definir orçamento, prazo e quem contratar.
6. **Falta o parceiro de pagamento definido.** Sem ele não dá para testar o fluxo real de consentimento e retorno (deep link), nem usar o sandbox dele.
7. **Falta o ambiente de teste do backend** (staging com dados fictícios). Teste dinâmico nunca deve ser contra produção.
8. **Falta o requisito de segurança escrito na spec** (por exemplo, "o app deve atender ao nível X do OWASP MASVS"). Hoje está só nos agentes.

## 4. Pontos de atenção

**Autorização e legalidade**
- Testar **somente o seu próprio app e o seu próprio backend**, em ambiente de teste. Interceptar tráfego ou injetar código em app de terceiros (inclusive o app do banco) é inaceitável.
- Se contratar um pentester, formalizar o escopo por escrito (o que pode testar, quando, em qual ambiente).

**Dinheiro e dados**
- Usar **só sandbox do parceiro e contas de teste**. Nunca rodar esses testes com dinheiro real nem com contatos reais da agenda de alguém.
- A interceptação de tráfego mostra **tokens e dados**. Guardar os resultados com cuidado e não colar em lugares públicos. Revogar qualquer token que aparecer em captura.

**Limitações técnicas esperadas**
- **Certificate pinning:** se o app usar (recomendado, com plano de rotação), o mitmproxy/Burp **não vão enxergar o tráfego** sem contornar o pinning. Contornar isso é justamente um dos testes (geralmente com Frida), e precisa de uma build de teste.
- **Emulador não é aparelho real:** o emulador facilita o teste, mas pode ter root e comportamento diferente. Detecção de root/emulador e atestação do aparelho só se validam em aparelho real.
- **Android 16 e proteções novas:** versões recentes do Android limitam alguns truques (ex.: certificados de usuário no tráfego). Pode ser necessário uma build de depuração configurada para teste. Detalhe a confirmar na documentação do Android.
- **iOS:** Frida e interceptação em iPhone costumam exigir aparelho com jailbreak ou build especial. Não é trivial.

**O que um resultado "limpo" NÃO prova**
- Ferramenta automática (MobSF) acha o óbvio. Falha de **lógica de negócio** (ex.: dá para pagar um valor diferente do mostrado?) exige testador humano.
- O teste mais importante deste app: **"o Pix sai sem a confirmação do usuário?"** e **"o servidor confia em valor/destinatário vindos do app?"**. As duas respostas esperadas são "não". Isso se testa alterando o tráfego e o app em execução.

## 5. O que o time de agentes cobre (e o que não)

| Item | Quem | Observação |
|---|---|---|
| Revisão do código do app (segredos, armazenamento, permissões) | `dev-security`, `dev-reviewer` | Estática; entra no fluxo normal |
| Análise estática do APK (MobSF, Semgrep) | `dev-security` | Pode rodar após instalar a ferramenta |
| Teste da **API/backend** (autorização entre usuários, IDOR, rate limit, webhooks) | `dev-pentest` | Em staging autorizado; **cobre o servidor, não o app** |
| Tráfego, Frida, contorno de proteções **do app** | **Ninguém** | Especialista humano |
| Teste manual em aparelho (voz, biometria, bolha) | Você, com roteiro do `dev-qa` | Funcional, não é segurança |

## 6. Sugestão de ordem (a decidir)

1. Escrever na spec o requisito de segurança do app (MASVS) com o `arquiteto-specs`.
2. Durante o desenvolvimento: análise estática contínua (`dev-security`).
3. Antes da publicação: `dev-pentest` no backend em staging, mais MobSF no APK.
4. Antes da publicação: **pentest mobile humano**, com o escopo acordado por escrito.
5. Depois de corrigir, **repetir o teste** nas falhas encontradas.

## 7. Perguntas em aberto

- Qual nível de segurança do app será exigido (e por quem: parceiro de pagamento, lojas)?
- Qual o orçamento para um pentest mobile humano?
- O parceiro de pagamento exige algum teste ou certificação de segurança antes de liberar produção? (a verificar com ele e na documentação do Open Finance)
- Haverá iOS no lançamento? Isso muda o custo e o ambiente do teste.
- Quem guarda e quem pode ver os relatórios (eles descrevem falhas reais)?
