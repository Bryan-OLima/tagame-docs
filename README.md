# Documentação do TAGAME

TAGAME é um iniciador local de jogos por NFC, hospedado no CasaOS. Este repositório reúne a documentação pública e o estudo de caso do projeto. A implementação será mantida separadamente nos repositórios `tagame-server` e `tagame-agent`.

## Estrutura física e implantação

A implantação inicial foi planejada em três partes:

```text
Tag NFC
   ↓
Celular Android ou iOS
   ↓ requisição pela rede local
Notebook executando CasaOS
   ↓ evento autenticado de início ou encerramento
PC executando a Steam e o agente do TAGAME
```

- **Celular:** lê a tag NFC e envia uma requisição autenticada ao TAGAME.
- **Notebook com CasaOS:** hospeda a aplicação web, a API, a distribuição de eventos, os registros e o banco de dados.
- **PC:** executa a Steam, recebe os eventos validados por meio do agente do TAGAME e inicia ou encerra o jogo configurado.

O celular funciona como leitor NFC, o notebook como servidor local e o PC como máquina de execução. Eles podem ser aparelhos físicos separados, conectados à mesma rede doméstica.

## Decisões de tecnologia

### Banco de dados: SQLite

O TAGAME utilizará **SQLite** em sua implantação inicial.

O SQLite é adequado porque a aplicação prioriza o funcionamento local, será executada em um único notebook com CasaOS, receberá poucos eventos NFC e não precisa de um servidor de banco de dados separado. O banco será acessado pelo backend do TAGAME na mesma máquina. Celulares e computadores se comunicarão exclusivamente com a API, nunca diretamente com o banco.

Requisitos iniciais do banco de dados:

- habilitar `journal_mode = WAL`;
- habilitar `foreign_keys = ON`;
- configurar um `busy_timeout` para operações de escrita;
- utilizar migrações desde a primeira implementação;
- armazenar UUIDs internos como `TEXT` no formato canônico de 36 caracteres;
- armazenar `steam_appid` como identificador externo inteiro;
- manter o arquivo do banco em um volume local do CasaOS, e não em um sistema de arquivos de rede;
- realizar cópias de segurança usando um procedimento seguro para SQLite, incluindo qualquer estado WAL ativo.

O PostgreSQL permanece como uma possível migração futura caso o TAGAME passe a exigir vários servidores de aplicação, uma concorrência de escrita consideravelmente maior ou acesso direto ao banco por várias máquinas. O modelo relacional lógico descrito em `DB.md` deverá permanecer portável o suficiente para permitir essa evolução.

## Mapa da documentação

Todos os documentos oficiais estão em `docs/`:

| Documento | Finalidade |
|---|---|
| [`PROJECT_VISION.md`](docs/PROJECT_VISION.md) | Propósito, objetivos e vocabulário do produto |
| [`ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Componentes, comunicação e limites de confiança |
| [`API.md`](docs/API.md) | Contrato inicial da API, autenticação e eventos do agente |
| [`UX.md`](docs/UX.md) | Arquitetura da informação, páginas, fluxos e estados da interface |
| [`DB.md`](docs/DB.md) | Estrutura e relacionamentos do banco de dados |
| [`RULES.md`](docs/RULES.md) | Regras de negócio, segurança e ciclo de vida |
| [`CURRENT_STATE.md`](docs/CURRENT_STATE.md) | Decisões aprovadas e questões ainda não resolvidas |
| [`ROADMAP.md`](docs/ROADMAP.md) | Etapas planejadas de entrega |
| [`TASKS.md`](docs/TASKS.md) | Índice atual de tarefas |
| [`COMMIT_CONVENTIONS.md`](docs/COMMIT_CONVENTIONS.md) | Formato obrigatório das mensagens de commit |
| [`AGENTS.md`](docs/AGENTS.md) | Instruções para agentes de IA que trabalham no projeto |

## Repositórios relacionados

- `tagame-server` — aplicação do CasaOS que contém interface web, API, integração com o banco e distribuição de eventos.
- `tagame-agent` — aplicação em segundo plano instalada nos computadores que executarão os jogos.

## Direção tecnológica inicial

- **Servidor no CasaOS:** aplicação web completa com frontend Angular, backend Express com TypeScript, SQLite, Prisma, Zod, migrações e distribuição de eventos.
- **Autenticação:** usuários do painel web usarão JWT Bearer; leitores e agentes usarão credenciais próprias, revogáveis e armazenadas apenas como hashes no servidor.
- **Comunicação:** o leitor NFC e o agente do PC se comunicarão exclusivamente por meio da API hospedada no CasaOS; eles não conversarão diretamente entre si.
- **Agente no PC:** aplicação C#/.NET para Windows, com uma janela simples de configuração em Windows Forms e interface na área de notificação por meio de `NotifyIcon`.
- **Aparelhos leitores:** integrações para Android e iOS que leem as tags NFC e enviam requisições autenticadas.

O agente deverá iniciar com a sessão do usuário do Windows, disponibilizar configuração e estado da conexão na área de notificação e retornar para essa área após a configuração. Um Windows Service separado poderá ser considerado futuramente caso seja necessário operar antes da entrada do usuário no Windows.

## Padrão das tags NFC

O TAGAME utilizará tags **NXP NTAG213** autênticas, em formato de cartão ou adesivo.

A NTAG213 foi escolhida por ser uma tag NFC Forum Type 2 comum, compatível com os fluxos de leitura planejados para Android e iOS, ter baixo custo e oferecer os recursos necessários ao projeto: UID físico estável, armazenamento NDEF, bloqueio de escrita e proteção de escrita opcional. A especificação oficial da NXP informa **144 bytes de memória de usuário**, retenção de dados por 10 anos e até 100 mil ciclos de escrita.

A capacidade de 144 bytes é mais do que suficiente para o TAGAME. Um UUIDv4 em seu formato textual canônico utiliza 36 caracteres ASCII:

```text
550e8400-e29b-41d4-a716-446655440000
```

Mesmo com o prefixo da aplicação, a carga permanece curta:

```text
tagame:550e8400-e29b-41d4-a716-446655440000
```

Isso representa aproximadamente 43 bytes antes da pequena sobrecarga do registro NDEF, deixando espaço suficiente dentro dos 144 bytes disponíveis. A tag armazenará apenas esse identificador opaco. Nome do jogo, Steam AppID, permissões e regras de execução permanecerão no banco de dados do CasaOS.

NTAG215 e NTAG216 não são necessárias para a primeira versão porque sua capacidade adicional não oferece benefício relevante ao TAGAME. A NTAG424 DNA poderá ser avaliada no futuro caso autenticação forte contra clonagem se torne um requisito, mas adicionaria complexidade criptográfica desnecessária à implantação local inicial.

Referência: [especificação das NXP NTAG213/215/216](https://www.nxp.com/products/NTAG213_215_216).

## Organização das tarefas

```text
tagame-docs/
├── README.md
└── docs/
    ├── PROJECT_VISION.md
    ├── AGENTS.md
    ├── TASKS.md
    ├── .agent/
    │   └── CURRENT_TASK.md
    └── tasks/
        ├── backlog/
        ├── active/
        └── archive/
            └── TASK_ARCHIVE.md
```

## Identificadores das tarefas

O prefixo descreve a área do trabalho, não se a alteração é uma funcionalidade ou uma correção:

```text
F001    Frontend
UX001   Experiência do usuário e arquitetura da informação
UI001   Identidade visual e design system
B001    Backend
DB001   Banco de dados
OPS001  DevOps e infraestrutura
M001    Integração mobile e leitor NFC
PC001   Agente para PC
QA001   Garantia de qualidade e testes
DOC001  Documentação
SEC001  Segurança
```

A parte numérica nunca será reutilizada. Uma tarefa concluída ou cancelada continuará representada no histórico.

## Trabalhando no TAGAME

Os agentes devem ler `AGENTS.md`, selecionar uma tarefa elegível em `TASKS.md`, manter `.agent/CURRENT_TASK.md` atualizado e seguir os critérios de aceite da tarefa. Pessoas podem consultar `TASKS.md` e o histórico arquivado para compreender o que está planejado e o que já foi entregue.
