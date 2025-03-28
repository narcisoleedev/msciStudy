### **1. Problema & Contexto**
**Objetivo**: Otimizar processos de negócios (por exemplo, sistemas de gerenciamento de tíquetes de TI) encontrando a **sequência ótima de ações** para resolver tíquetes, evitando **gargalos** (por exemplo, "Ação Pendentes do Cliente").  
**Por que RL?** A mineração de processos tradicional exige análise manual de logs de eventos. RL automatiza isso, permitindo que um agente aprenda os melhores caminhos por tentativa e erro.

---

### **2. Conceitos-chave do seu conhecimento**
#### **a) Framework MDP**  
- **Estados**: Representam as etapas no ciclo de vida de um tíquete (por exemplo, "Aberto," "Atribuído," "Fechado").  
- **Ações**: Transições entre estados (por exemplo, mover de "Aberto" → "Atribuído").  
- **Recompensas**: Projetadas para penalizar gargalos e recompensar transições válidas (semelhante aos exemplos de gridworld).  

#### **b) Q-Learning & DQN**  
- **Tabela Q**: Usada no Q-learning para armazenar valores estado-ação.  
- **DQN**: Rede neural aproxima os valores Q para escalabilidade (embora aqui o espaço de estados fosse pequeno, então o Q-learning foi mais rápido).  
- **Equação de Bellman**: Atualiza os valores Q iterativamente:  
  \[
  Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]
  \]

#### **c) Exploração vs. Exploração**  
- **Epsilon-Greedy**: Decaimento adaptativo (começa com alta exploração e muda para exploração).  
- **Replay de Experiência** (DQN): Reduz o viés amostrando transições passadas.  

---

### **3. Pipeline & Metodologia**
#### **Etapa 1: Mineração de Processos & Configuração do Ambiente**  
- **Logs de Eventos**: 3.414 tíquetes do ServiceNow, com estados como "Aberto", "Atribuído", etc.  
- **Identificação de Gargalos**: "Ação Pendentes do Cliente" foi sinalizada como um **estado de absorção** (uma vez alcançado, não há saída).  
- **Probabilidades de Transição**: Calculadas a partir das frequências de eventos (por exemplo, "Atribuído" → "Investigando" ocorre 36,3% das vezes).  

#### **Etapa 2: Design de Recompensas**  
- **Q-Learning**:  
  - Transições inválidas/gargalos: Penalidade (-1).  
  - Transições válidas: Recompensa = probabilidade de transição (por exemplo, 0,36 para "Atribuído" → "Investigando").  
  - Estado alvo ("fim"): Recompensa alta (+10).  
- **DQN**:  
  - Recompensas simplificadas (por exemplo, +0,1 para transições válidas, +100 para alcançar "fim").  

#### **Etapa 3: Treinamento**  
- **Q-Learning**:  
  - Tabela Q inicializada com zeros.  
  - Decaimento de epsilon: \( \epsilon = \max(\epsilon_{\text{min}}, e^{-\epsilon_{\text{decay}} \cdot \text{episódio}}) \).  
  - Convergência em **2,04 segundos** (4000 episódios).  
- **DQN**:  
  - Rede neural (12 estados → 32 → 32 → 12 ações).  
  - Buffer de replay de experiência (tamanho=128) para estabilidade.  
  - Convergência mais lenta (**86,44 segundos**) devido ao custo computacional da rede neural.  

---

### **4. Resultados & Insights**
#### **Políticas Ótimas**  
- **Q-Learning**: Caminho encontrado: **Aberto → Atribuído → Resolvido → Fim**.  
- **DQN**: Caminho encontrado mais longo: **Aberto → Atribuído → Investigando → Resolvido → Fim**.  
- Ambos evitaram o estado de gargalo ("Ação Pendentes do Cliente").  

#### **Comparação de Desempenho**  
| Métrica          | Q-Learning      | DQN             |  
|------------------|-----------------|-----------------|  
| **Tempo de Treinamento** | 2,04 segundos    | 86,44 segundos   |  
| **Recompensa Média**  | 7,7             | 70              |  
| **Passos/Episódio** | 2,8             | 4,1             |  

**Por que o Q-Learning foi mais rápido**: O pequeno espaço de estados (12 estados) tornou os métodos tabulares mais eficientes. A rede neural do DQN adicionou custo computacional.  

---

### **5. Conexões com seu Conhecimento**
- **Estado de Absorção**: Semelhante a uma "armadilha" em gridworld (por exemplo, recompensa negativa e sem saída).  
- **Decaimento de Epsilon**: Equilibra exploração/exploração, como nos exemplos anteriores.  
- **Formação de Recompensas**: Penalizar gargalos reflete como você atribuiria recompensas negativas a estados indesejáveis (por exemplo, paredes em um labirinto).  
- **Aprendizado Off-Policy**: Tanto Q-learning quanto DQN usam **máximos valores Q** para atualizações, ignorando o ruído da exploração (por exemplo, escolhas ε-greedy).  

---

### **6. Trabalhos Futuros & Limitações**
- **Escalabilidade**: O DQN se destacaria em espaços de estado maiores (por exemplo, processos com mais de 1000 estados).  
- **Recompensas Baseadas em Tempo**: O artigo sugere adicionar penalidades de tempo (por exemplo, resoluções mais rápidas = maiores recompensas).  
- **Impacto no Mundo Real**: Reduzir gargalos melhora a eficiência do negócio (por exemplo, resolução mais rápida de tíquetes).  

---

### **Resumo**
Este artigo aplica **Q-learning** e **DQN** para otimizar processos de negócios evitando gargalos. Ele reflete os fundamentos do RL que você aprendeu:
- MDPs (estados, ações, recompensas).  
- Atualizações Bellman para convergência de valores Q.  
- Exploração vs. Exploração (epsilon-greedy).  
- Engenharia de recompensas para guiar o agente.  

Os resultados mostram a aplicabilidade do RL na otimização de processos do mundo real, com trade-offs entre métodos tabulares (Q-learning) e paramétricos (DQN).

---  

*Obs: Resumo feito usando o Deepseek.*