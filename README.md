# Análise de Sistema Adversarial: Agendamento de Consultas

## 3. Desenvolvimento

### 3.1 Descrição do sistema adversarial
*(A Pessoa 1 vai preencher esta parte)*

### 3.2 Modelo estratégico estático

**Definição dos Jogadores e Ações**
* **Jogador A (Adversário):** Usuário com intenções maliciosas querendo uma quebra de agenda.
  * **Ação A1 (Normal):** Agenda apenas consultas necessárias (segue o protocolo correto).
  * **Ação A2 (Reservas Abusivas):** Tenta agendar vários horários para bloquear a agenda.
* **Jogador B (Defensor):** O sistema de Agendamento.
  * **Ação B1 (Controle Normal):** Permite agendamentos sem limites rígidos.
  * **Ação B2 (Controle Reforçado):** Aplica limites estritos, verificação de identidade e bloqueios por comportamento.

**Matriz de Decisão**
Os valores representam a ordem de preferência de 0 (pior cenário) a 3 (melhor cenário). A ordem no par é **(Payoff do Jogador A, Payoff do Jogador B)**.

| Jogador A \ Jogador B | Ação B1 (Controle Normal) | Ação B2 (Controle Reforçado) |
| :--- | :--- | :--- |
| **Ação A1 (Normal)** | (2, 3) | (1, 2) |
| **Ação A2 (Reservas Abusivas)** | (3, 0) | (0, 1) |

**Justificativa dos Resultados:**
* **(2, 3) - Regular vs Normal:** O adversário age como um usuário comum e consegue sua consulta facilmente (2). O sistema atinge seu estado ideal: máxima disponibilidade com excelente usabilidade (3).
* **(3, 0) - Abusivo vs Normal:** O adversário consegue explorar a falta de controles e monopoliza a agenda com sucesso (3). O sistema sofre o pior impacto possível, ficando indisponível para usuários legítimos (0).
* **(1, 2) - Regular vs Reforçado:** O adversário desiste do ataque e tenta um uso normal, mas sofre com a lentidão e burocracia dos controles de segurança (1). O sistema está seguro, mas penaliza a experiência dos usuários com atritos desnecessários (2).
* **(0, 1) - Abusivo vs Reforçado:** O ataque do adversário é detectado e bloqueado (0). O sistema sobrevive e mitiga a ameaça, mas opera em estado de alerta e consumindo recursos de processamento (1).

**Análise Estratégica:**
* **Melhores respostas:**
  * Se o Sistema escolhe B1, a melhor resposta do Adversário é A2, pois 3 > 2.
  * Se o Sistema escolhe B2, a melhor resposta do Adversário é A1, pois 1 > 0.
  * Se o Adversário escolhe A1, a melhor resposta do Sistema é B1, pois 3 > 2.
  * Se o Adversário escolhe A2, a melhor resposta do Sistema é B2, pois 1 > 0.
* **Estratégia Dominante:** Nenhum dos jogadores possui uma estratégia dominante. A melhor escolha de um depende estritamente do que o outro fizer.
* **Equilíbrio (Nash):** Neste cenário, não existe um equilíbrio estático (nenhum resultado onde ambos não queiram mudar). Trata-se de um ciclo (semelhante ao jogo de "polícia e ladrão"): se o sistema relaxa (B1), o adversário ataca (A2). Se o adversário ataca (A2), o sistema reforça (B2). Se o sistema reforça (B2), o adversário recua (A1). Se o adversário recua (A1), o sistema relaxa para melhorar a usabilidade (B1).
* **Impacto no Sistema e Usuários Legítimos:** O fato de não haver um equilíbrio perfeito mostra que a segurança tem um custo. Quando o sistema é forçado a ir para B2 (Controle Reforçado) para conter o adversário, os usuários legítimos pagam o preço da mitigação por meio de verificações extras, lentidão e bloqueios acidentais (falsos positivos).

### 3.3 Modelo estratégico dinâmico

O modelo dinâmico acompanha a disputa pela disponibilidade de horários. O adversário mantém o objetivo de ocupar a agenda e dificultar o acesso de outros pacientes, mas muda sua forma de reservar quando encontra uma restrição. O defensor procura conter o abuso e permitir reservas legítimas, ajustando seus controles conforme observa as tentativas.

As rodadas representam **hipóteses de análise**, não vulnerabilidades comprovadas no sistema de agendamento. Os limites, as confirmações e a análise de padrões descritos abaixo são controles propostos. O cenário considera que o adversário consegue operar mais de uma conta e observar as respostas às suas próprias solicitações. O defensor observa os registros de conta, horário escolhido, instante da solicitação e situação da reserva; a semelhança entre solicitações é um indício, não uma prova de que as contas pertencem à mesma pessoa.

#### Rodadas de ação, resposta, observação e adaptação

| Rodada | Ação do participante | Resposta do sistema ou defensor | O que se torna observável? | Adaptação para a rodada seguinte |
| --- | --- | --- | --- | --- |
| **1 — Concentração em uma conta** | O adversário tenta reservar vários horários pela mesma conta, buscando reduzir as opções disponíveis para outros pacientes. | Ao observar a concentração, o defensor aplica um limite de reservas ativas por conta às novas solicitações. Reservas pendentes de confirmação também entram nessa contagem. | O adversário vê que novas reservas passam a ser recusadas naquela conta e infere a existência de um limite. O defensor observa o volume de tentativas e os horários ocupados. | O adversário passa a distribuir as solicitações entre várias contas. O defensor mantém o limite por conta e passa a considerar a distribuição das reservas entre contas. |
| **2 — Distribuição entre contas** | O adversário usa várias contas, respeitando o limite individual, mas solicita horários da mesma agenda em intervalos próximos. | O defensor mantém o limite anterior e compara padrões de tempo e de horários entre contas. Solicitações com sinais de concentração passam por confirmação adicional antes de serem efetivadas. | O adversário observa quais solicitações exigem confirmação e suspeita que o conjunto das tentativas está chamando atenção. O defensor percebe que o total de horários ocupados pode crescer mesmo com cada conta abaixo do limite. | O adversário continua usando várias contas, mas espaça as tentativas. O defensor conserva os registros e amplia o período de observação, pois o volume por conta não explica sozinho a ocupação da agenda. |
| **3 — Distribuição ao longo do tempo** | O adversário usa as contas da rodada anterior para reservar com intervalos maiores, tentando reduzir o sinal de concentração imediata. | O defensor mantém os controles anteriores e analisa o histórico de reservas ativas, confirmações e cancelamentos em um período maior. Solicitações suspeitas exigem confirmação; reservas provisórias têm prazo de expiração para não reter horários indefinidamente. | O adversário observa que algumas solicitações ainda exigem confirmação e se os horários provisórios voltam a ficar disponíveis quando o prazo acaba. O defensor relaciona a ocupação persistente ao histórico, mas também observa casos legítimos submetidos à verificação. | O adversário pode reduzir o volume ou tentar cancelar e reservar novamente para renovar a retenção de horários. O defensor passa a considerar a repetição desse fluxo no histórico e ajusta os critérios de confirmação com atenção aos bloqueios indevidos. |

#### Como uma rodada altera a seguinte

A rodada 2 surge porque o limite aplicado na rodada 1 torna menos útil concentrar reservas em uma única conta. A rodada 3 mantém as múltiplas contas da rodada 2 e altera o intervalo entre as solicitações em resposta às confirmações adicionais. O histórico de tentativas, reservas e decisões permanece disponível ao defensor; os controles anteriores continuam ativos.

O limite por conta restringe novas reservas, mas não libera automaticamente horários já confirmados. Da mesma forma, observar padrões semelhantes entre contas não identifica com certeza quem as controla. A sequência mostra aprendizado e mudança de estratégia, sem presumir que uma defesa encerra a disputa.

### 3.4 Ameaças e riscos
*(A Pessoa 4 vai preencher esta parte)*
