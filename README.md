# Projeto MQTT – Segurança e Correção de Vulnerabilidades

Este projeto tem como objetivo explorar e corrigir vulnerabilidades em um broker MQTT (Mosquitto), aplicando boas práticas de autenticação, controle de acesso e criptografia TLS.


---


# 🔎 Vulnerabilidades Identificadas

Acesso anônimo habilitado → Permitindo conexões sem autenticação.

Ausência de autenticação de usuário → Nenhum controle de acesso configurado.

Falta de criptografia TLS → Comunicação vulnerável a interceptação.



---

# ✅ Correções Implementadas

1. Bloqueio de conexões anônimas

Configurado allow_anonymous false no mosquitto.conf.



2. Criação de usuários e senhas

Usuário USUARIO adicionado em passwordfile (hash seguro).



3. Configuração de ACL (Access Control List)

aclfile configurado para restringir permissões.

Exemplo: apenas USUARIO pode publicar/assinar no tópico sensor/#.



4. Habilitação de TLS

Certificados e chaves gerados (server.crt, server.key, ca.crt).

TLS configurado no mosquitto.conf para conexões seguras na porta 8883.



5. Segregação de portas

1883 → comunicação interna (sem TLS, apenas testes/sensores locais).

8883 → comunicação externa (TLS + autenticação).





---

# ⚙️ Passo a Passo de Configuração

1. Criar diretórios

mkdir -p mosquitto/{config,data,log,certs}

2. Gerar certificados TLS

cd mosquitto/certs

# CA
openssl genrsa -out ca.key 4096

openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 \
  -subj "/CN=MyTestCA" -out ca.crt

# Servidor
openssl genrsa -out server.key 4096

openssl req -new -key server.key -subj "/CN=mqtt-broker" -out server.csr

openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key \
  -CAcreateserial -out server.crt -days 365 -sha256

3. Criar usuário no broker

mosquitto_passwd -c mosquitto/config/mosquitto.passwordfile USUARIO

4. Definir ACL (mosquitto/config/aclfile)

user USUARIO
topic readwrite sensor/#

5. Subir o broker com Docker Compose

docker compose up -d --build


---

# 🧪 Testes

Publicar mensagem (porta 1883 – sem TLS):

mosquitto_pub -h localhost -p 1883 -t 'sensor/temperature' -m '27.5'

Assinar tópico (porta 8883 – com TLS):

mosquitto_sub -h localhost -p 8883 --cafile ./mosquitto/certs/ca.crt \
  -t 'sensor/#' -v --tls-version tlsv1.2 -u USUARIO -P <SENHA>


---

# 📌 Resumo das alterações

Acesso anônimo desabilitado.

Usuário USUARIO criado com autenticação por senha.

ACLs implementadas para controle de permissões.

TLS configurado para proteger comunicação externa.

Portas segregadas:

1883 → uso interno/testes.

8883 → uso externo seguro.




---

Este projeto demonstra como corrigir falhas comuns de segurança em brokers MQTT, garantindo confidencialidade, autenticação e controle de acesso de forma prática e funcional.





