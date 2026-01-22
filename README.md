# 🌬️ DevLog: Projetando o Aromatizador IoT Definitivo

Sempre quis um aromatizador automático, mas me deparei com dois problemas no mercado: ou os produtos comerciais usam refis proprietários caros, ou as soluções DIY envolvem bombas, mangueiras e vazamentos.

Como engenheiro, decidi resolver isso aplicando princípios de **Design Mecatrônico Robusto (KISS)**. Meu objetivo: criar um dispositivo universal, conectado e mecanicamente infalível.

Aqui está o registro do desenvolvimento e as decisões técnicas que tomei.

---

## 💡 O Conceito: Por que Mecânica Pura?

Minha primeira ideia foi usar bombas peristálticas ou diafragmas (hidráulica). Mas, analisando a complexidade, percebi que isso adicionava pontos de falha desnecessários: vedação, limpeza de tubos e risco de vazamento na eletrônica.

Decidi pivotar para uma abordagem **100% Mecânica**. Em vez de manipular o líquido, eu manipulo o frasco. O sistema funciona como um "dedo robótico" que aperta qualquer spray de farmácia (60ml).

### Pinhão cremalheira

* **Solução atual:** Optei por usar o pinhão cremalheira pela praticidade e maior controle da força aplicada no frasco
* **Solução antiga:** Um came oval. (Talvez ainda haja aplicação.)
    * **0º (Repouso A):** O came não toca no frasco.
    * **90º (Ataque):** O raio máximo aperta o spray.
    * **180º (Repouso B):** O came libera o frasco do outro lado.
    
* **A nova solução** permite que o borrifador do frasco seja pressionado tranquilamente. O deslocamento necessário para que ele seja pressionado até o fim, é de aproximadamente **5 mm**

---

## 🛠️ Hardware: As Escolhas

Para garantir confiabilidade, fugi dos componentes de brinquedo.

* **Cérebro: ESP32.** Escolhi pela conectividade Wi-Fi nativa. Quero configurar os intervalos pelo celular (Web Server), não ficar apertando botões físicos na parede.
* **Músculo: Servo MG996R.** Nada de servos azuis (SG90). Preciso de engrenagens de metal e torque de 10kg/cm para vencer a mola do spray sem esforço.
* **Segurança Elétrica:** Adicionei um capacitor de **1000uF** na linha de 5V. Servos potentes causam picos de corrente na partida que resetam o microcontrolador. O capacitor resolve esse *brownout*.

---

## 🧠 A Lógica: Máquina de Estados

No firmware, implementei uma lógica de controle baseada em estados para otimizar o desgaste mecânico. Não é apenas "ligar e desligar".

O sistema sabe onde o braço está (Lado A ou Lado B).
* Se preciso de **1 Spray**, o servo viaja de A para B (passando pela "lombada" central).
* Se preciso de **2 Sprays**, ele vai e volta.

Isso elimina movimentos mortos e torna o barulho de operação mínimo.

---

## 📋 Lista de Materiais (BOM)

Para quem quiser replicar meu setup:

1.  **ESP32** (Qualquer modelo, estou usando um DevKit V1).
2.  **Servo MG996R** (Metal Gear).
3.  **Fonte USB 5V 2A** (Carregador antigo de celular).
4.  **Capacitor Eletrolítico 1000uF/16V**.
5.  **Frasco Spray 100ml** (Genérico de viagem).
6.  **Case Impresso em 3D** (PLA/PETG).

---

## ⚠️ Análise de Riscos (O que pode dar errado?)

Durante o projeto, identifiquei três pontos críticos que tratei no design:

1.  **Stall do Motor:** Se o came for grande demais, o servo trava e queima. **Solução:** Projetei o suporte do motor com furos oblongos, permitindo ajuste fino da altura na hora da montagem.
2.  **Troca de Refil:** O usuário precisa trocar o frasco fácil. **Solução:** Na posição de repouso, o came não toca no botão, deixando o frasco livre para ser deslizado para fora.
3.  **Vazamento:** Embora raro, sprays vazam. **Solução:** O compartimento da eletrônica (ESP32) fica fisicamente isolado e acima da linha do líquido.

---

## 🚀 Próximos Passos

1.  Validar a impressão do Came com **100% de infill** (precisa ser sólido).
2.  Testar a interface Web para ajuste de timer.
3.  Montagem final e teste de stress da mola.

---
*Este projeto é Open Source. Sinta-se livre para usar o código e os STLs.*
