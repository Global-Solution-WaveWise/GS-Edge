# 🌊 WaveWise – Sistema de Alerta de Enchentes 🚨

> Prototipado com Arduino | Simulação via [Tinkercad/Wokwi]  
> **Projeto Acadêmico – Edge Computing & IoT**

---

## 📌 Identificação do Problema

Eventos climáticos extremos têm causado **enchentes frequentes em áreas urbanas e de risco**. Esses alagamentos colocam em perigo **vidas humanas, propriedades e infraestrutura pública**.  
Muitas comunidades **não possuem sistemas de alerta acessíveis e eficazes** para prever ou mitigar esses riscos.

---

## 🎯 Objetivo da Solução

Desenvolver um sistema físico baseado em Arduino capaz de:

- Monitorar **nível da água** e **temperatura ambiente**
- Classificar a severidade da situação (chuva fraca, moderada ou forte)
- Emitir **alertas visuais e sonoros** de forma clara e acessível
- Exibir dados em **tempo real via LCD**
- Simular decisões automatizadas em tempo de risco

---

## ⚙️ Visão Geral da Solução

### 🎥 Vídeo Demonstrativo

📺 **Assista ao funcionamento do sistema aqui:**  
https://fiapcom-my.sharepoint.com/:v:/g/personal/rm562098_fiap_com_br/Eee-wcSgGGlNqZwpitXJG6kB5_3SwzpXHR3sWKQFWZ8xyQ?e=0IFfKm

---

### 🖼️ Esquema do Circuito

![image](https://github.com/user-attachments/assets/51a3934a-2f50-48fd-8a48-c4b8fe07b421)


---

### 🛠 Componentes Utilizados

| Componente               | Quantidade | Função no Sistema                              |
|--------------------------|------------|-----------------------------------------------|
| Arduino Uno              | 1          | Microcontrolador principal                    |
| Potenciômetro            | 1          | Simula o sensor de nível de água              |
| Sensor de Temperatura (analógico) | 1    | Mede temperatura ambiente                     |
| LEDs (Vermelho, Amarelo, Azul) | 3     | Indicam a intensidade da chuva                |
| Buzzer Ativo             | 1          | Emite sons para alertas                       |
| Display LCD 16x2         | 1          | Mostra dados de temperatura e nível de água   |
| Resistores               | 3+         | Limitam corrente para os LEDs                 |

---

## 🧠 Lógica de Funcionamento

| Nível de Água (simulado) | Estado                  | LED       | Buzzer        | LCD                         |
|--------------------------|-------------------------|-----------|----------------|------------------------------|
| 0–72                     | Chuvas Fracas           | 🔵 Azul   | ❌ Desligado   | "Chuvas Fracas / Umid. 20%" |
| 73–183                   | Chuvas Moderadas        | 🟡 Amarelo| 🔊 750 Hz (3x) | "Chuvas Moderadas / 50%"    |
| >183                     | Chuvas Fortes (Risco)   | 🔴 Vermelho | 🔊 1500 Hz   | "Chuvas Fortes / 100%"      |

O potenciômetro simula o nível da água. O sistema reage com diferentes sinais (LEDs e sons) e exibe a **temperatura ambiente** atual no LCD.

---

## 💻 Como Simular

### ✅ Passos para rodar no Tinkercad/Wokwi:

1. Acesse o link do projeto abaixo:
   - 🔗 [Link do Projeto no Simulador](LINK_DO_PROJETO)
2. Clique em **"Start Simulation"**
3. Gire o potenciômetro para simular o aumento do nível da água
4. Observe o comportamento dos LEDs, buzzer e LCD

---

## 🧾 Código-Fonte

```cpp
#include <Adafruit_LiquidCrystal.h>
Adafruit_LiquidCrystal lcd_1(0);

int potenciometro = A0;
int SensorTempPino= A1;
int ledRed = 3;
int ledYellow = 2;
int ledBlue = 4;
int buzzer = 5;
float valor_lido = 0;

void setup(){
  Serial.begin(9600); 
  lcd_1.begin(16, 2);
  
  pinMode(potenciometro, INPUT);
  pinMode(ledBlue, OUTPUT);
  pinMode(ledYellow, OUTPUT);
  pinMode(ledRed, OUTPUT);
  pinMode(buzzer, OUTPUT);
}

void loop(){
  int SensorTempTensao = analogRead(SensorTempPino);
  float Tensao=SensorTempTensao*5;
	Tensao/=1024;
  float TemperaturaC=(Tensao-0.5)*100;
  
  valor_lido = map(analogRead(potenciometro), 0, 1023, 0, 255);

  lcd_1.setCursor(12,1);
  lcd_1.print(TemperaturaC);
  lcd_1.setCursor(10,1);
  lcd_1.print("C.");
  
  if (valor_lido >= 183) {
    analogWrite(ledRed, 255);
    analogWrite(ledYellow, 0);
    analogWrite(ledBlue, 0);
    lcd_1.setCursor(0,0);
    lcd_1.print("Chuvas Fortes    ");
    lcd_1.setCursor(0,1);
    lcd_1.print("Umid.100%");
    tone(buzzer, 1500);
    
  } else if (valor_lido >= 72 && valor_lido <= 183) {
    analogWrite(ledYellow, 255);
    analogWrite(ledBlue, 0);
    analogWrite(ledRed, 0);
    lcd_1.setCursor(0,0);
    lcd_1.print("Chuvas Moderadas");
    lcd_1.setCursor(0,1);
    lcd_1.print("Umid.50% ");
    delay(1000);
    tone(buzzer, 750);
    delay(1000);
    tone(buzzer, 750);
    delay(1000);
    
  } else {
    analogWrite(ledRed, 0);
    analogWrite(ledYellow, 0);
    analogWrite(ledBlue, 255);
    lcd_1.setCursor(0,0);
    lcd_1.print("Chuvas Fracas    ");
    lcd_1.setCursor(0,1);
    lcd_1.print("Umid.20% ");
    noTone(buzzer);
  }
}
