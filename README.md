# 🌊 Monitor de Nível do Rio com Alerta Visual e Sonoro

Este projeto foi desenvolvido com o objetivo de **monitorar o nível de um rio de forma simples, eficiente e acessível** utilizando a plataforma Arduino. A proposta é oferecer uma solução de **baixo custo e fácil implementação** para auxiliar na **prevenção de enchentes**, especialmente em regiões vulneráveis. O sistema também pode ser adaptado para outras aplicações, como monitoramento de reservatórios ou controle de irrigação.

---

## 👥 Participantes

- Felipe Godoi   
- Raphael Taketa  
- Victor Guimarães  

---

## 📦 Descrição do Projeto

O sistema é composto por componentes eletrônicos fundamentais:

- **Sensor ultrassônico HC-SR04**: mede a distância entre o sensor e a superfície da água, permitindo calcular a **altura do nível do rio** em tempo real.
- **Display LCD 16x2 (com I2C)**: exibe a **altura da água em centímetros**, facilitando o acompanhamento visual.
- **Dois LEDs**:
  - **Verde**: indica que o nível está normal.
  - **Vermelho**: acende quando o rio atinge um nível crítico.
- **Buzzer**: emite um som contínuo quando o nível ultrapassa o **limite de segurança**, definido neste projeto como **2 metros de altura**.

### 🛑 Modo de Alerta

Quando o nível da água atinge ou ultrapassa os **2 metros**, o sistema entra em alerta:
- O **LED vermelho acende**.
- O **buzzer apita continuamente**.
- O **LCD exibe o valor atual** e uma **mensagem de perigo**.

---

## 🧰 Materiais

- 1x Arduino Uno  
- 1x Sensor ultrassônico HC-SR04  
- 1x Display LCD 16x2 com módulo I2C  
- 1x LED verde  
- 1x LED vermelho  
- 2x Resistores  
- 1x Buzzer  
- Jumpers  
- Protoboard  

---
## 📌 Considerações Finais

Este projeto demonstrou que é possível utilizar componentes simples e acessíveis da plataforma Arduino para criar uma ferramenta eficaz de monitoramento ambiental. O sistema pode ser facilmente replicado, adaptado para diferentes contextos (como cisternas, tanques, poços e reservatórios) e até mesmo expandido com funcionalidades como conexão Wi-Fi, armazenamento em nuvem ou envio de alertas via SMS.

Além disso, proporcionou um excelente aprendizado prático sobre sensores, lógica de programação, leitura de dados em tempo real e ações baseadas em condições ambientais.

---

## 📽️ Demonstração

▶️ **[Assista à demonstração no YouTube](https://www.youtube.com/)**  
💻 **[Acesse a simulação no Wokwi]([https://www.tinkercad.com/](https://wokwi.com/projects/432771420012238849))**  
📷 **[Imagem do projeto]**

---

![image](https://github.com/user-attachments/assets/672f4a8c-3691-4124-ae24-d07e878bac6f)


---

## 🧠 Código Arduino

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// LCD I2C
#define col 16
#define lin  2
#define ende 0x27
LiquidCrystal_I2C lcd(ende, col, lin);

// HC-SR04
const int trigPin = 9;
const int echoPin = 10;

// LED e buzzer
const int ledVermelho = 6;
const int ledVerde = 7;
const int buzzer = 5;

// Altura total do sensor (em cm)
const float alturaSensor = 300.0;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledVermelho, OUTPUT);
  pinMode(ledVerde, OUTPUT);
  pinMode(buzzer, OUTPUT);

  lcd.init();
  lcd.backlight();

  Serial.begin(9600);
}

void loop() {
  // Medição do HC-SR04
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duracao = pulseIn(echoPin, HIGH);
  float distancia = duracao * 0.034 / 2.0;
  float alturaRio = alturaSensor - distancia;

  // LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Altura: ");
  lcd.print(alturaRio, 1);
  lcd.print(" cm");

  // Alerta se altura >= 200 cm
  if (alturaRio >= 200) {
    digitalWrite(ledVermelho, HIGH);
    digitalWrite(ledVerde, LOW);
    tone(buzzer, 1000); 
    lcd.setCursor(0, 1);
    lcd.print("ALERTA: Cheia! ");
  } else {
    digitalWrite(ledVermelho, LOW);
    digitalWrite(ledVerde, HIGH);
    noTone(buzzer); 
    lcd.setCursor(0, 1);
    lcd.print("Nivel normal    ");
  }

  delay(1000);
}
