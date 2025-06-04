# 💡 Detector de Luminosidade com ESP8266 e Raspberry Pi

Este projeto tem como objetivo desenvolver um sistema IoT simples e funcional para **monitoramento de luminosidade** com resposta visual automática (LED) e visualização de dados em tempo real através de um **dashboard hospedado em uma Raspberry Pi**.

---

## 🔧 Componentes Utilizados

- ESP8266 NodeMCU  
- Sensor de luminosidade LDR  
- LED + resistor de 220 ohms  
- Protoboard e jumpers  
- Raspberry Pi 3 B+  
- Conexão Wi-Fi  
- Broker MQTT (Mosquitto)  
- Dashboard (ex: Node-RED, Grafana, etc.)

---

## 📌 Objetivo

Criar um sistema que:

1. **Meça a luminosidade do ambiente**  
2. **Envie os dados para um broker MQTT**  
3. **Acione um LED automaticamente quando estiver escuro**  
4. **Exiba os dados de forma gráfica e em tempo real no dashboard**

---

## 📡 Tópicos MQTT

| Tópico                | Descrição                                | Exemplo de Payload |
|-----------------------|-------------------------------------------|--------------------|
| `sensor/light`        | Valor bruto do sensor LDR (0–1023)        | `724`              |
| `sensor/light_percent`| Luminosidade em porcentagem (0–100%)      | `71`               |
| `sensor/led_state`    | Estado do LED                             | `ON` ou `OFF`      |

---

## 🔌 Esquema de Montagem

- O **LDR** está ligado ao pino **A0** da ESP8266 e ao **GND**, com resistor pull-up.  
- O **LED** está conectado ao pino **D1**, com resistor de 220 ohms em série.  
- A alimentação do circuito vem da própria ESP8266 (3.3V e GND).

---

## 🧪 Trecho do Código Explicado

### Conexão com a rede Wi-Fi:

```cpp
WiFi.begin(ssid, password);
while (WiFi.status() != WL_CONNECTED) {
  delay(500);
  Serial.print(".");
}
```

Esse trecho conecta o ESP8266 à sua rede Wi-Fi. O while mantém o programa tentando até conseguir conexão.

### Leitura e publicação dos dados:
```cpp
int ldrValue = analogRead(ldrPin);
String lightPercent = String(map(ldrValue, 0, 1024, 0, 100));

client.publish("sensor/light", String(ldrValue).c_str());
client.publish("sensor/light_percent", lightPercent.c_str());
```

Aqui, o valor lido do sensor é convertido em uma porcentagem e enviado para o broker MQTT nos respectivos tópicos.

### Acionamento do LED conforme luminosidade:

```cpp
if (ldrValue < threshold) {
  digitalWrite(ledPin, HIGH);
  client.publish("sensor/led_state", "ON");
} else {
  digitalWrite(ledPin, LOW);
  client.publish("sensor/led_state", "OFF");
}
```
Se o valor lido for menor que threshold (ex: 500), o LED será aceso e o estado será publicado como ON.

## Dashboard

O dashboard foi construído na Raspberry Pi, que:

- Atua como servidor MQTT

- Escuta os tópicos sensor/light e sensor/light_percent

- Exibe os dados em tempo real, em formato gráfico

- Permite observar o comportamento da luz ambiente ao longo do tempo

- O gráfico se atualiza a cada 2 segundos, conforme as mensagens chegam do ESP8266

## Problemas Enfrentados

- Mal contato entre o sensor e a protoboard/ESP causou falhas de leitura no início.

- Foi necessário refazer e reforçar as conexões para garantir a estabilidade do sistema.

## Requisitos de Software

- Arduino IDE com suporte ao ESP8266

- Biblioteca PubSubClient instalada

- Mosquitto MQTT Broker rodando na Raspberry Pi

- Node-RED ou outra ferramenta de dashboard configurada para ler tópicos MQTT

## Como Rodar o Projeto

1. Suba o código para o ESP8266 usando a Arduino IDE.

2. Configure o broker MQTT na Raspberry Pi (por exemplo, com Mosquitto).

3. Inicie o dashboard (Node-RED ou similar).

4. Abra o monitor serial para verificar a conexão do dispositivo.

5. Observe a variação da luminosidade no gráfico e o LED acendendo/apagando automaticamente.

## Autores

- André Carvalho

- Arthur Lins

- Pedro Oliveira
