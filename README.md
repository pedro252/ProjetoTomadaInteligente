# ProjetoTomadaInteligente

#include <ESP8266WiFi.h>
#include <WiFiClient.h>

const char* ssid = "SEU_SSID";      // SSID da sua rede Wi-Fi
const char* password = "SUA_SENHA";  // Senha da sua rede Wi-Fi
const int tomadaPin = 8;            // Pino de controle da tomada

// Endereço do servidor e porta para enviar notificações
const char* serverAddress = "SEU_SERVIDOR";
const int serverPort = 80;

void setup() {
  pinMode(tomadaPin, OUTPUT);
  digitalWrite(tomadaPin, HIGH);  // Inicialmente, a tomada está ligada
  Serial.begin(9600);

  // Conecte-se à rede Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Conectando ao Wi-Fi...");
  }
  Serial.println("Conectado ao Wi-Fi");

}

void loop() {
  // Leitura do nível da bateria do celular (simulado)
  int nivelBateriaCelular = leituraNivelBateriaCelular();

  if (nivelBateriaCelular <= 20) {
    digitalWrite(tomadaPin, HIGH); // Liga a tomada
    Serial.println("Tomada ligada. Bateria do celular menor ou igual a 20%.");

    // Envie uma notificação para o celular
    sendNotification("Bateria fraca. Conecte o celular para carregar.");
  } else {
    digitalWrite(tomadaPin, LOW); // Desliga a tomada
    Serial.println("Tomada desligada. Bateria do celular igual ou superior a 20%.");
  }

  delay(10000); // Aguarda 10 segundos antes de verificar novamente
}

int leituraNivelBateriaCelular() {
  // Simulando a leitura do nível da bateria do celular (valor aleatório entre 0 e 100).
  int nivelBateria = random(0, 101);
  return nivelBateria;
}

void sendNotification(const char* message) {
  WiFiClient client;

  if (client.connect(serverAddress, serverPort)) {
    client.println("GET /send_notification?message=" + String(message) + " HTTP/1.1");
    client.println("Host: " + String(serverAddress));
    client.println("Connection: close");
    client.println();
    client.stop();
  } else {
    Serial.println("Falha ao conectar ao servidor de notificações.");
  }
}
