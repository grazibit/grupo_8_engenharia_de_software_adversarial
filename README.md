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
*(A Pessoa 3 vai preencher esta parte)*

### 3.4 Ameaças e riscos
*(A Pessoa 4 vai preencher esta parte)*