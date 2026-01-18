# Relatório Técnico: Projeto Aromatizador Mecatrônico (Came Simétrico)

Como Projetista Mecatrônico Sênior, validei nossa solução. Chegamos a um design otimizado (DFMA - Design for Manufacturing and Assembly) que equilibra robustez mecânica com simplicidade de controle.

A escolha do **Came Simétrico ("Lombada")** acoplado a uma **Máquina de Estados** transforma um problema analógico complexo em uma operação digital binária e confiável.

Abaixo segue a documentação técnica final para a execução do projeto.

---

## 1. Lista de Materiais (BOM - Bill of Materials)

### Eletrônica e Atuação
* **Microcontrolador:** 1x ESP32 (DevKit V1 ou S3/C3).
* **Atuador:** 1x Servo Motor **MG996R** (Engrenagens Metálicas). *Não aceite SG90 ou servos de plástico.*
* **Gerenciamento de Energia:**
    * 1x Capacitor Eletrolítico **1000uF / 16V** (Essencial para filtrar o pico de corrente na partida do motor e evitar reset do ESP32).
    * Fonte de Alimentação USB 5V (Carregador de celular de **mínimo 2A** reais).
    * Cabo Micro-USB de boa qualidade (baixa resistência).

### Mecânica e Hardware
* **Reservatório:** 1x Frasco Spray Genérico 100ml (modelo de viagem/farmácia, cilíndrico).
    * *Requisito:* O botão do spray deve ter um curso suave e retorno rápido.
* **Fixação:** 4x Parafusos M3 ou M4 com porcas (para fixar o servo no case).
* **Material de Impressão:** PLA ou PETG (A Bambu Lab A1 Mini dá conta tranquilamente).

---

## 2. Plano de Ação (Execução em Fases)

### Fase 1: Engenharia Reversa e CAD (Fusion 360)
Antes de imprimir, precisamos das cotas de controle.
1.  **Metrologia:** Meça o **Curso (Stroke)** do botão do spray (Ex: 12mm). Adicione +2mm de tolerância (Total: 14mm).
2.  **Modelagem do Came (A "Lombada"):**
    * Desenhe o perfil no Fusion.
    * Ângulo 0º e 180º: Raio $R_{min}$ (Repouso).
    * Ângulo 90º: Raio $R_{max} = R_{min} + \text{Curso} + 2mm$.
    * Faça transições suaves (Splines) entre os pontos.
3.  **Modelagem do Case:**
    * Crie os **Rasgos Oblongos** no suporte do servo para permitir ajuste vertical (calibração física).
    * Desenhe os trilhos para o frasco deslizar e travar na posição correta.

### Fase 2: Eletrônica e Firmware
1.  **Montagem de Bancada:** Conecte o ESP32 + Servo + Capacitor na protoboard.
    * *Ligação:* Servo VCC direto no pino VIN (5V da USB). **Nunca** no 3.3V.
2.  **Coding (A Máquina de Estados):**
    * Implemente a lógica de ir de 0º para 180º (Spray A) e 180º para 0º (Spray B).
    * Implemente o WebServer simples para input do timer.
3.  **Teste de Stress:** Deixe rodando na bancada por 1 hora para garantir que não há aquecimento ou *memory leaks* no código.

### Fase 3: Integração e Validação
1.  **Impressão 3D:** Imprima o Came com **100% de Infill** (sólido) para não deformar com a força da mola.
2.  **Montagem Final:** Parafuse o servo, encaixe o came, deslize o frasco.
3.  **Calibração Mecânica:**
    * Solte os parafusos do servo.
    * Coloque o servo em 90º (ponto de máxima extensão).
    * Desça o corpo do servo até o came apertar totalmente o botão.
    * Aperte os parafusos do servo.

---

## 3. Pontos de Atenção (Análise de Riscos - FMEA)

Como sênior, aqui estão os detalhes onde os projetos amadores costumam falhar:

* **1. O Risco de "Stall" (Travamento):**
    * *Problema:* Se o came tentar empurrar o botão mais do que ele aguenta (fim de curso físico da mola), o servo vai travar, puxar corrente máxima (2.5A), aquecer e pode queimar o ESP32 ou a si mesmo.
    * *Mitigação:* A regulagem dos rasgos oblongos é crítica. Ajuste para que o ponto máximo do came (90º) coincida exatamente com o fim de curso do spray, sem forçar além.

* **2. O "Brownout" (Queda de Tensão):**
    * *Problema:* O MG996R é um monstro. Na partida, ele derruba a tensão da USB. Se cair abaixo de 2.8V, o ESP32 reinicia.
    * *Mitigação:* **Não ignore o Capacitor de 1000uF.** Coloque-o o mais próximo possível dos pinos de alimentação do Servo.

* **3. Fadiga do Material (Creep):**
    * *Problema:* Plástico PLA sob pressão constante deforma.
    * *Mitigação:* Projete o Came para que, na posição de repouso (0º e 180º), ele **não toque** no botão. Deve haver uma folga de 0.5mm a 1mm. Assim, o plástico não fica sob tensão o tempo todo.

* **4. Isolamento de Fluidos:**
    * *Problema:* Vazamento eventual do spray.
    * *Mitigação:* Projete o case com a eletrônica (ESP32) fisicamente separada do compartimento do frasco (ex: uma "parede" de plástico entre o servo e o microcontrolador). Se o frasco vazar, escorre para fora, não para a placa.
