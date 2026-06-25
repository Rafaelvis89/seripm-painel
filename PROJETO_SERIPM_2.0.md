# 📋 RESUMO DETALHADO - PROJETO SERIPM 2.0

## 1. VISÃO GERAL DO PROJETO

**Nome:** SERIPM 2.0 - Sistema Integrado de Requisições de Iluminação Pública de Maricá  
**Objetivo:** Migrar do Google Sheets para um sistema independente com PHP + MariaDB  
**Status:** Pronto para iniciar desenvolvimento  
**Tempo estimado:** 2-3 dias  
**Stack:** PHP 7.4+ | MariaDB 10.5+ | Apache 2.4+ | HTML5 | CSS3 (Tailwind) | JavaScript Vanilla

---

## 2. CONTEXTO E MOTIVAÇÃO

### Problemas com o sistema atual (Google Sheets):
- ❌ Travamentos frequentes
- ❌ Respostas lentas (segundos vs milissegundos)
- ❌ Dados desincronizados
- ❌ Limites de requisições simultâneas
- ❌ Difícil escalabilidade

### Solução adotada:
- ✅ Backend PHP independente (roda no Apache local)
- ✅ Banco de dados MariaDB (está disponível)
- ✅ ngrok para compartilhar link público
- ✅ Frontend moderno e responsivo
- ✅ Reutilizar 80% do código HTML/CSS/JS atual

---

## 3. REPOSITÓRIO GIT

**GitHub:** `Rafaelvis89/seripm-painel`  
**Branch:** `main`  
**Commits recentes:**
- 8422914 - Fix: corrigir contagem de Total de Protocolos
- 6da3699 - Fix: contar Atrasados da aba URGENTE/ATRASADO real
- d4de193 - Fix: impedir alternância entre cards antigos e novos
- a82860d - Fix: remover auto-sync que causava atualização a cada 15 segundos

**Pasta local:** `d:\Users\rafael\Documents\Github e Google Sheets\`

---

## 4. ARQUITETURA DO NOVO SISTEMA

### 4.1 Estrutura de pastas

```
seripm-painel/
├── index.html                    # Login (será simplificado)
├── README.md
│
├── public/                       # Frontend (arquivos estáticos)
│   ├── dashboard/
│   │   ├── index.html           # Dashboard principal
│   │   ├── entrada.html         # Aba ENTRADA
│   │   ├── atendimento.html     # Atendimento presencial/telefone
│   │   ├── relatorios.html      # Relatórios e filtros
│   │   └── administrativo.html  # Gestão (ADMIN only)
│   │
│   ├── assets/
│   │   ├── css/
│   │   │   ├── main.css         # Estilos globais (Tailwind)
│   │   │   ├── cards.css        # Status cards
│   │   │   ├── modais.css       # Modais
│   │   │   ├── responsive.css   # Mobile-first
│   │   │   └── print.css        # Impressão
│   │   │
│   │   ├── js/
│   │   │   ├── api.js           # Chamadas HTTP para backend
│   │   │   ├── auth.js          # Autenticação e sessão
│   │   │   ├── app.js           # Lógica geral da aplicação
│   │   │   ├── modais.js        # Controle de modais
│   │   │   ├── mapa.js          # Integração Leaflet + OpenStreetMap
│   │   │   ├── validacao.js     # Validações de formulário
│   │   │   ├── impressao.js     # Print e PDF
│   │   │   ├── utils.js         # Funções auxiliares
│   │   │   └── config.js        # Configurações (URLs, etc)
│   │   │
│   │   ├── images/
│   │   │   ├── brasao-marica.png
│   │   │   ├── logo-iluminacao.png
│   │   │   ├── icons/
│   │   │   └── backgrounds/
│   │   │
│   │   └── fonts/               # Fontes personalizadas
│   │
│   └── libs/                    # Bibliotecas
│       ├── leaflet.css/js       # Mapa
│       ├── chart.min.js         # Gráficos
│       └── ...
│
├── api/                         # Backend PHP
│   ├── index.php               # Router principal (doPost equivalent)
│   ├── config.php              # Conexão MariaDB
│   ├── middleware/
│   │   ├── auth.php            # Validação de token/sessão
│   │   ├── cors.php            # CORS headers
│   │   └── logger.php          # Log de operações
│   │
│   ├── controllers/
│   │   ├── AuthController.php  # Login, logout, token
│   │   ├── ProtocolController.php # CRUD protocolos
│   │   ├── UserController.php  # Usuários (ADMIN)
│   │   ├── ReportController.php # Relatórios
│   │   ├── RSController.php    # Requisições de Serviço
│   │   ├── ExecutionController.php # Execuções
│   │   ├── FiscalController.php # Fiscalizações
│   │   └── DashboardController.php # Cards de status
│   │
│   └── models/
│       ├── Protocol.php        # Model de Protocolo
│       ├── User.php            # Model de Usuário
│       ├── RS.php              # Model de RS
│       ├── Execution.php       # Model de Execução
│       └── Fiscal.php          # Model de Fiscalização
│
├── database/
│   ├── schema.sql              # Estrutura das tabelas
│   ├── seeds.sql               # Dados iniciais (admin, perfis)
│   └── migrations.sql          # Futuras alterações
│
├── .env.example                # Variáveis de ambiente (copiar para .env)
├── .gitignore                  # Git ignore
└── composer.json (opcional)    # Dependências PHP
```

### 4.2 Fluxo de requisições

```
Frontend (HTML/JS)
    ↓
    ├─→ assets/js/api.js (fetch HTTP)
    ↓
Apache + PHP (localhost)
    ↓
    ├─→ api/index.php (router)
    ├─→ api/middleware/ (autenticação, validação)
    ├─→ api/controllers/ (lógica de negócio)
    ├─→ api/models/ (acesso ao banco)
    ↓
MariaDB (localhost)
    ↓
Resposta JSON → Frontend (atualiza UI)
```

---

## 5. BANCO DE DADOS (MariaDB)

### 5.1 Tabelas principais

```sql
-- Usuários e autenticação
usuarios (id, username, password, perfil, status, abas_permitidas, created_at)
perfis (id, nome, descricao, permissoes)

-- Protocolos
protocolos (id, numero, data, atendente, solicitante, cpf, telefone, 
            endereco, referencia, bairro, tipo_servico, detalhes, 
            observacoes, status, data_modificacao, modificado_por)

-- Campos adicionais de rastreamento
urgente_atrasado (id, protocolo_id, dias_atraso, motivo, created_at)

-- Gestão de serviços
requisicoes_servico (id, numero, protocolos_ids, empresa, responsavel, 
                     bairro, status, data_envio, data_conclusao)
execucoes (id, rs_id, protocolo_id, executor, data_exec, hora_inicio, 
           hora_fim, observacoes, fotos_url, status)
fiscalizacoes (id, rs_id, data_fiscal, fiscal, resultado, observacoes, 
               aprovado, motivo_devolucao, fotos_url, status)

-- Logs e auditoria
logs_atividade (id, usuario_id, acao, tabela, dados_antes, dados_depois, 
                timestamp, ip_address)
```

### 5.2 Campos de protocolo (mantém o mesmo formato)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| DATA | datetime | Data/hora da abertura |
| PROTOCOLO | varchar(20) | YYYYMMDD-XXX (ex: 20260623-001) |
| ATENDENTE | varchar(100) | Quem atendeu |
| SOLICITANTE | varchar(100) | Nome do solicitante |
| BAIRRO | varchar(100) | Bairro |
| ENDERECO | varchar(200) | Endereço completo |
| REFERENCIA | varchar(200) | Referência (próximo a...) |
| CPF | varchar(14) | CPF formatado |
| TELEFONE | varchar(15) | Telefone formatado |
| TIPO_SERVICO | varchar(100) | Tipo de serviço |
| DETALHES | text | Descrição do problema |
| OBSERVACOES | text | Observações adicionais |
| STATUS | enum | ABERTO, COM EMPRESA, RESOLVIDO, CANCELADO, etc |
| DATA_MODIFICACAO | datetime | Última alteração |
| MODIFICADO_POR | varchar(100) | Usuário que modificou |

---

## 6. FRONTEND - ESTRUTURA DE COMPONENTES

### 6.1 Páginas principais

1. **login.html** → Tela de autenticação
2. **dashboard.html** → 8 cards de status + gráficos
3. **entrada.html** → Lista de protocolos com filtros
4. **atendimento.html** → Interface para atendimento presencial/telefone
5. **relatorios.html** → Relatórios com múltiplos filtros
6. **administrativo.html** → Gestão de usuários e abas

### 6.2 Componentes reutilizáveis

```javascript
// Modais
Modal.open('novoProtocolo', dados)
Modal.close()
Modal.confirm('Deseja atualizar?')

// Cards de status (8 tipos)
renderStatusCards(counts)  // TOTAL, ABERTO, ATRASADO, COM_EMPRESA, RESOLVIDO, CANCELADO, PENDENTE, GARANTIA

// Tabela com checkboxes
renderTabela(dados, container)
selecionarTudo()
exportarSelecionados()

// Mapa
initMapa(containerElement)
buscarEndereco(rua, numero, bairro)  // Geocoding reverso

// Impressão e PDF
imprimirProtocolos(protocolos)
gerarPDF(protocolos)  // Com cabeçalho oficial
```

### 6.3 Design e responsividade

- **Framework CSS:** Tailwind CSS (CDN)
- **Icons:** Font Awesome 6.5.1
- **Breakpoints:** Mobile (< 768px), Tablet (768-1024px), Desktop (> 1024px)
- **Branding:** Cores de Maricá-RJ (definir paleta)
- **Acessibilidade:** WCAG 2.1 nível AA

---

## 7. BACKEND - ENDPOINTS API

### 7.1 Autenticação

```
POST /api/index.php?action=login
├─ Params: { username, password }
└─ Response: { success, token, role, user_id }

POST /api/index.php?action=logout
└─ Response: { success }

POST /api/index.php?action=refreshToken
└─ Response: { success, token }
```

### 7.2 Protocolos

```
GET /api/index.php?action=getProtocolos&aba=ENTRADA
├─ Params: aba, filtro (opcional)
└─ Response: { success, data: [...] }

POST /api/index.php?action=createProtocolo
├─ Params: { aba, solicitante, endereco, ... }
└─ Response: { success, protocolo: "20260625-XXX" }

POST /api/index.php?action=updateStatus
├─ Params: { protocolo_id, novo_status }
└─ Response: { success }

GET /api/index.php?action=getStatusCounts
└─ Response: { success, counts: { TOTAL: 2279, ABERTO: 253, ... } }
```

### 7.3 Usuários (ADMIN only)

```
GET /api/index.php?action=getUsuarios
└─ Response: { success, data: [...] }

POST /api/index.php?action=saveUsuario
├─ Params: { username, password, perfil, status, abas_permitidas }
└─ Response: { success }

DELETE /api/index.php?action=deleteUsuario&id=XXX
└─ Response: { success }
```

### 7.4 Dashboard

```
GET /api/index.php?action=getDashboardData
└─ Response: { success, cards: {...}, graficos: {...}, ultimos_protocolos: [...] }
```

---

## 8. SEGURANÇA

### 8.1 Implementações

- ✅ **Autenticação:** Token JWT ou Session PHP
- ✅ **Autorização:** Verificação de perfil por endpoint
- ✅ **Validação:** Input validation + sanitização
- ✅ **CORS:** Headers apropriados para ngrok
- ✅ **HTTPS:** ngrok fornece automaticamente
- ✅ **Logs:** Todas as ações registradas em banco
- ✅ **Proteção contra SQL Injection:** Prepared statements
- ✅ **Proteção contra XSS:** Escapar dados no frontend

### 8.2 Perfis e permissões

```
SUPER_ADMIN       → Acesso total
ADMINISTRATIVO    → Gerencia protocolos e relatórios
ATENDIMENTO       → Cria e consulta protocolos
EMPRESA           → Acessa aba "COM FUNCIONÁRIO"
FISCAL            → Faz fiscalizações
```

---

## 9. MAPA (Leaflet + OpenStreetMap)

### 9.1 Funcionalidades

```javascript
// Buscar localização pelo endereço (Nominatim - gratuito)
buscarCoordenadas("Rua X, 123, Bairro Y, Maricá-RJ")
  → { lat: -22.9123, lng: -42.8234 }

// Exibir pino no mapa
adicionarPino(lat, lng, "Título do local")

// Geocoding reverso (clique no mapa retorna endereço)
mapaClickListener(lat, lng)
  → "Rua X, 123, Maricá-RJ"

// Rotas (opcional)
tracarRota(origem, destino)
```

### 9.2 Implementação

- **Leaflet:** Biblioteca de mapa leve
- **OpenStreetMap:** Tiles gratuitas
- **Nominatim:** Geocoding gratuito (10 req/seg)
- **Biblioteca compatível:** leaflet-geosearch (opcional)

---

## 10. FUNCIONALIDADES PRINCIPAIS

### 10.1 Login
- Formulário simples
- Validação de credenciais
- Armazenamento de token/sessão
- Redirect para dashboard se já logado

### 10.2 Dashboard
- 8 cards de status (números em tempo real)
- Gráficos (Pizza, Barras, Linha)
- Últimos 10 protocolos
- Filtros rápidos

### 10.3 Abas de protocolo
- **ENTRADA:** Principal, todos os protocolos
- **ADMINISTRATIVO:** Administrativas
- **OBRA:** Obras
- **TELECOM:** Telecomunicações
- **GARANTIA:** Garantias
- **COM FUNCIONÁRIO:** Para empresas
- **IMPLANTAÇÃO:** Novos projetos

### 10.4 Atendimento (presencial/telefone)
- Interface simplificada para mobile
- Busca rápida de protocolo
- Criar novo protocolo
- Atualizar status
- Visualizar histórico

### 10.5 Relatórios
- Filtros: Data, Status, Bairro, Atendente
- Export: CSV, PDF, Impressão
- Gráficos dinâmicos
- Comparativos de período

### 10.6 Impressão/PDF
- Cabeçalho oficial (Brasão + SERIPM + Maricá)
- Dados do protocolo
- QR code (opcional)
- Formatação para imprimir

---

## 11. TECNOLOGIAS E VERSÕES

| Tecnologia | Versão | Motivo |
|------------|--------|--------|
| PHP | 7.4+ | Compatibilidade Apache |
| MariaDB | 10.5+ | Performance |
| Apache | 2.4+ | Disponível |
| HTML | 5 | Semântico |
| CSS | 3 (Tailwind CDN) | Moderno, sem build |
| JavaScript | ES6+ (Vanilla) | Sem dependências pesadas |
| Leaflet | 1.9.4 | Mapa leve |
| Chart.js | 4.x | Gráficos |
| Font Awesome | 6.5.1 | Icons |
| ngrok | Latest | Compartilhamento público |

---

## 12. FLUXO DE DESENVOLVIMENTO

### Fase 1: Banco de dados e API (1 dia)
- ✅ Criar schema.sql
- ✅ Criar conexão config.php
- ✅ Implementar controllers
- ✅ Testar endpoints com Postman/curl

### Fase 2: Frontend estrutura (0.5 dia)
- ✅ Desmembrar index.html em componentes
- ✅ Criar pasta public/ com assets
- ✅ Implementar js/api.js (chamadas HTTP)

### Fase 3: Funcionalidades core (1 dia)
- ✅ Login e autenticação
- ✅ Dashboard com cards
- ✅ Listar protocolos (ENTRADA)
- ✅ Criar protocolo
- ✅ Atualizar status

### Fase 4: Refinamentos (0.5 dia)
- ✅ Mapa + busca de endereço
- ✅ Print e PDF
- ✅ Relatórios
- ✅ Responsividade mobile

### Fase 5: Deploy e testes (0.5 dia)
- ✅ Testar em Apache local
- ✅ Ativar ngrok
- ✅ Testar em múltiplos navegadores/devices
- ✅ Push para GitHub

---

## 13. VARIÁVEIS DE AMBIENTE (.env)

```ini
# Banco de dados
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASS=sua_senha
DB_NAME=seripm_db

# API
API_URL=http://localhost/seripm/api/
API_TIMEOUT=30

# JWT (se usar)
JWT_SECRET=sua_chave_secreta_aqui
JWT_EXPIRY=86400

# Logs
LOG_LEVEL=info
LOG_PATH=./logs/

# CORS
CORS_ORIGIN=*

# Empresa
EMPRESA_NOME=Secretaria de Energia Renovável e Iluminação Pública
EMPRESA_CIDADE=Maricá
EMPRESA_ESTADO=RJ
```

---

## 14. GIT WORKFLOW

```bash
# Clonar ou sincronizar
git clone https://github.com/Rafaelvis89/seripm-painel.git
cd seripm-painel

# Criar branch para desenvolvimento
git checkout -b feat/php-migration

# Fazer commits atomizados
git add .
git commit -m "feat: criar schema.sql e config.php"
git commit -m "feat: implementar AuthController"
...

# Fazer push e PR
git push origin feat/php-migration

# Após merge, sincronizar local
git checkout main
git pull origin main
```

---

## 15. PRÓXIMOS PASSOS IMEDIATOS

### Quando começar:

1. ✅ **Confirmar paleta de cores** de Maricá-RJ
2. ✅ **Confirmar perfis de usuário** necessários
3. ✅ **Listar campos personalizados** adicionais (se houver)
4. ✅ **Definir URL base** para ngrok (ex: `https://seripm-marica.ngrok.io`)
5. ✅ **Preparar MariaDB** (criar database, user com permissões)
6. ✅ **Testar conexão PHP-MariaDB** localmente

### Ao iniciar desenvolvimento:

1. Criar pasta `/api/` com arquivos PHP
2. Criar `database/schema.sql`
3. Criar `public/` com estrutura HTML/CSS/JS
4. Testar conexão no localhost
5. Fazer primeiro commit

---

## 16. REFERÊNCIAS E RECURSOS

- **Documentação PHP:** https://www.php.net/docs.php
- **MariaDB:** https://mariadb.com/docs/
- **Leaflet:** https://leafletjs.com/
- **Tailwind CSS:** https://tailwindcss.com/
- **ngrok:** https://ngrok.com/docs
- **Nominatim (Geocoding):** https://nominatim.org/

---

**Este resumo é completo e detalhado. Use como referência durante todo o desenvolvimento!** 📌

Quando estiver pronto, me avisa e começamos com a Fase 1! 🚀
