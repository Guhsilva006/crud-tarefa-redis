# Gerenciador de Tarefas — CRUD com Python e Redis

Sistema de gerenciamento de tarefas feito em Python que guarda os dados em um banco **Redis**.
Funciona de duas formas, usando a **mesma camada de dados**: uma interface de **linha de comando**
e uma interface **web** (Flask + HTML). Projeto da atividade *Exercício CRUD Redis*.

## Funcionalidades

- **Criar** tarefas com título, descrição e status inicial (Pendente, Em Progresso ou Concluída)
- **Ler** uma tarefa pelo ID ou **listar** todas, ordenadas por ID
- **Atualizar** título, descrição ou status
- **Excluir** tarefas, com confirmação
- Interface web com filtros por status, contadores, janela de edição e tema claro/escuro automático
- Validações: título obrigatório, status restrito aos três valores válidos, IDs numéricos
- Tratamento de erros de conexão (servidor fora do ar, senha errada, tempo esgotado)

## Tecnologias

Python 3 · [redis-py](https://github.com/redis/redis-py) · Flask · HTML, CSS e JavaScript puros · Redis (Redis Cloud ou local)

## Como os dados são guardados no Redis

Cada tarefa é um **hash** na chave `tarefa:{id}`:

| Campo | Conteúdo |
|---|---|
| `titulo` | Título da tarefa |
| `descricao` | Descrição |
| `data_criacao` | Data e hora no formato `dd/mm/aaaa hh:mm:ss` |
| `status` | `Pendente`, `Em Progresso` ou `Concluída` |

O ID é gerado automaticamente pelo contador `contador:tarefa` (comando `INCR`).

## Estrutura

```
├── app.py                    # Servidor web (Flask) e rotas da API
├── crud_redis.py             # Camada de dados (TarefaRepository) + interface de linha de comando
├── config_redis.example.py   # Modelo de configuração da conexão
├── requirements.txt
└── templates/
    └── index.html            # Interface web
```

## Como executar

1. **Clone** o repositório e entre na pasta que contém o `app.py`.
2. **Instale as dependências:**
   ```bash
   pip install -r requirements.txt
   ```
3. **Configure a conexão:** copie `config_redis.example.py` para `config_redis.py` e preencha a `REDIS_URL`:
   ```
   redis://default:SUA_SENHA@HOST:PORTA
   ```
   Para um Redis local: `redis://localhost:6379/0`. O arquivo `config_redis.py` está no `.gitignore`
   e **nunca deve ser enviado ao GitHub**.
4. **Rode:**
   - Versão web: `python app.py` (abre o navegador em `http://127.0.0.1:5000`)
   - Linha de comando: `python crud_redis.py`

## API REST

| Método | Rota | Descrição |
|---|---|---|
| GET | `/tarefas` | Lista todas as tarefas |
| GET | `/tarefas/<id>` | Busca uma tarefa |
| POST | `/tarefas` | Cria (`titulo`, `descricao`, `status`) |
| PUT | `/tarefas/<id>` | Atualiza um campo (`campo`, `valor`) |
| DELETE | `/tarefas/<id>` | Exclui a tarefa |

## Observações

- O cliente usa o protocolo **RESP2** (`protocol=2`), pois o RESP3 padrão do `redis-py` 8.x demorou
  cerca de 16 segundos para responder no banco usado no desenvolvimento.
- A senha do Redis fica só no `config_redis.py` (ou na variável de ambiente `REDIS_URL`).
