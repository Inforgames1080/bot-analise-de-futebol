# ⚽ Bot Telegram de Análise de Futebol (FutOdds)

Este projeto implementa um **bot do Telegram** que consome a API **FutOdds** para listar jogos ao vivo e gerar **alertas automáticos (sinais)** baseados em regras simples de trading esportivo (ex.: Lay Correct Score).

---

## 📌 Funcionalidades

* 📡 Listar **jogos ao vivo** (`/live`)
* 🚨 Gerar **sinais automáticos** (`/sinais`)
* 🧠 Análise baseada em **minuto do jogo e placar**
* 🛡️ Tratamento seguro de dados inconsistentes da API
* 💾 Persistência simples (último update do Telegram)

---

## 🧱 Estrutura Geral do Código

O código é dividido em **7 blocos principais**:

1. Configurações
2. Banco de dados simples (JSON)
3. Integração com Telegram
4. Integração com FutOdds
5. Extração de dados do jogo
6. Análise e formatação
7. Loop principal do bot

---

## 1️⃣ Configurações

```python
TELEGRAM_TOKEN = "SEU_TOKEN"
FUTODDS_TOKEN = "SEU_TOKEN"
```

* `TELEGRAM_TOKEN`: token do BotFather
* `FUTODDS_TOKEN`: token da API FutOdds
* `TG_URL`: endpoint base da API do Telegram
* `FUTODDS_LIVE`: endpoint de jogos ao vivo

---

## 2️⃣ Banco de Dados Simples (JSON)

Usado apenas para **controlar o último update do Telegram**, evitando mensagens duplicadas.

### `load_db()`

```python
def load_db():
    try:
        with open(DB_FILE, "r") as f:
            return json.load(f)
    except:
        return {"last_update_id": 0}
```

* Se o arquivo existir → carrega
* Se não existir → cria estrutura inicial

### `save_db(db)`

Salva o estado atual no arquivo `football_db.json`.

---

## 3️⃣ Integração com Telegram

### `send_message(chat_id, text)`

Responsável por enviar mensagens para o usuário.

### `get_updates(offset)`

Busca novas mensagens recebidas pelo bot.

* `offset` evita reprocessar mensagens antigas

---

## 4️⃣ Integração com FutOdds

### `get_live_matches()`

```python
def get_live_matches():
    r = requests.get(FUTODDS_LIVE)
    data = r.json()
```

A API pode retornar:

* `{ "data": [...] }`
* `[ ... ]`
* Erros ou dados vazios

A função normaliza tudo para **uma lista de jogos**.

---

## 5️⃣ Extração Segura dos Dados do Jogo

### `extrair_info_jogo(j)`

Esta é a função **mais importante do projeto**.

Ela resolve o maior problema da API:

> ⚠️ `scores` frequentemente vem como `null`

```python
scores = j.get("scores")
if not isinstance(scores, dict):
    scores = {}
```

### Dados extraídos:

* ⏱ Minuto do jogo (`elapsed`)
* ⚽ Gols da casa
* ⚽ Gols do visitante

Tudo é protegido contra:

* `None`
* Chaves inexistentes
* Strings em vez de inteiros

---

## 6️⃣ Formatação e Análise

### `formatar_jogo(j)`

Responsável por gerar a mensagem exibida no Telegram:

```
⚽ Time A 0x0 Time B
🏆 Liga (País)
⏱ 23′
```

---

### `analisar_jogo(j)`

Aplica regras simples de trading:

| Regra   | Condição      |
| ------- | ------------- |
| Lay CS  | 20+ min e 0x0 |
| Pressão | 30+ min e 0x0 |
| HT      | 45+ min       |

Retorna **texto do alerta** ou `None`.

---

## 7️⃣ Loop Principal do Bot

### `main()`

Fluxo contínuo:

1. Lê mensagens do Telegram
2. Processa comandos
3. Consulta API FutOdds
4. Envia respostas
5. Salva estado
6. Aguarda 2 segundos

```python
while True:
    updates = get_updates(...)
    for u in updates:
        ...
    time.sleep(2)
```

---

## 🤖 Comandos Disponíveis

| Comando   | Descrição             |
| --------- | --------------------- |
| `/start`  | Mensagem inicial      |
| `/live`   | Lista jogos ao vivo   |
| `/sinais` | Mostra alertas ativos |

---

## 🛠️ Como Executar

```bash
pip install requests
python tipsbot.py
```

---

## 📁 Estrutura do Projeto

```
📂 bot-futebol-telegram
 ├── tipsbot.py
 ├── football_db.json
 └── README.md
```

---

## 🚀 Próximas Evoluções

* 🔔 Alertas automáticos a cada 5 minutos
* 🧠 Filtro por liga / país
* 💰 Estratégias avançadas (Over, Under, CS)
* 📊 Estatísticas por time
* ☁️ Deploy em VPS

---

## 📄 Licença

Projeto educacional – uso livre para estudo e adaptação.

---

## 👤 Autor

**Anderson Rodrigues**
Trader esportivo • Python • Data Science
