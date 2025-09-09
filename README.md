# 🛰️ Projeto MQTT Revisado
📌 Descrição

Este projeto demonstra uma arquitetura de comunicação MQTT segura, composta por:

Broker MQTT: Eclipse Mosquitto 2.0.20, configurado com TLS, controle de acesso (ACL) e autenticação via password_file.

Sensor de Temperatura: Implementado em Python 3.12 com Paho MQTT, publicando dados de temperatura a cada 5 segundos.

Subscriber MQTT: Utiliza mosquitto_sub ou scripts personalizados para assinar tópicos e visualizar as mensagens recebidas.

O projeto é executado via Docker Compose, simplificando o setup e o deploy.


# ⚙️ Como Executar
1. Clonar o repositório
git clone https://github.com/paulotorresousa/mqtt-project-revisado.git
cd mqtt-project-revisado

2. Iniciar os containers
docker compose up -d --build

3. Visualizar logs do subscriber
docker compose logs -f mqtt-subscriber


Para a comunicação segura, o subscriber devem usar o certificado TLS do broker e fornecer usuário/senha válidos.

# 🔐 Segurança

Autenticação: Apenas clientes com credenciais válidas no password_file podem se conectar ao broker.

Controle de acesso (ACL): Cada usuário tem permissões específicas para publicar/assinar tópicos.

TLS/SSL: Toda comunicação entre clientes e broker é criptografada, garantindo segurança de dados em trânsito.

Sem acesso anônimo: allow_anonymous está desativado.

Essas medidas tornam o broker seguro para testes avançados ou pequenos ambientes de produção.

