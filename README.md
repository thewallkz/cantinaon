# CantinaOn

SaaS para pedidos antecipados em cantina escolar com controle parental integrado.

## Stack base
- **Frontend:** React
- **Backend:** Node.js
- **Banco:** PostgreSQL

## Conteúdo inicial deste repositório
- `docs/cantinaon-spec.md`: especificação funcional e técnica consolidada.
- `database/schema.sql`: modelo inicial relacional em PostgreSQL.
- `docs/api-draft.md`: rascunho de endpoints REST para o MVP.
- `docs/canteen-express-integration-plan.md`: plano inicial de integração do frontend externo `canteen-express` com este backend.

## Backend MVP (implementado)
Foi adicionado um backend inicial em `backend/` com Express e PostgreSQL como banco padrão, cobrindo os endpoints principais do rascunho:
- Auth (`/auth/register`, `/auth/login`, `/auth/refresh`)
- Cardápio e catálogo (`/menu/today`, `/products/:id`, `/allergens`)
- Pedidos (`/orders`, `/orders/:id`, `/orders/my`, `/orders/:id/cancel`)
- Pagamento (`/payments/checkout`, `/payments/webhooks/mercadopago`, `/wallet/pay`)
- Operação (`/ops/online-status`)
- Funcionário (`/staff/orders/paid`, `/staff/orders/:id/confirm-pickup`)
- Controle parental (`/parental/...`)
- Carteira (`/wallet/students/...`)

Também inclui regras centrais de negócio para:
- Reserva atômica e devolução de estoque em cancelamento/expiração.
- Timeout de pagamento (8 minutos) com expiração automática.
- Geração de código de retirada de 4 dígitos após pagamento.
- Função de recálculo de estoque online por regra FIXO ou PERCENTUAL.

## Como executar
```bash
cd backend
npm install
npm start
```

Servidor padrão: `http://localhost:3000`

Ou, a partir da raiz do projeto, você pode iniciar o backend com:
```bash
npm start
```

### Usuários
Como o fluxo de autenticação está 100% em PostgreSQL, não há fallback local para login.
Crie usuários usando `POST /auth/register` antes de autenticar com `POST /auth/login`.

Para resetar dados em ambiente local, execute `database/seed.sql`.
Esse seed usa `TRUNCATE ... RESTART IDENTITY CASCADE` e recria uma massa completa de simulação (usuários, produtos, estoque, alérgenos, carteira, vínculos parentais e pedidos).
Credenciais padrão (com senha armazenada em bcrypt):
- `admin@cantinaon.local` / `admin123`
- `staff@cantinaon.local` / `staff123`
- `maria.resp@cantinaon.local` / `resp123`
- `joao.aluno@cantinaon.local` / `aluno123`
- `ana.aluna@cantinaon.local` / `aluno123`


## PostgreSQL (padrão do backend)
O backend depende de PostgreSQL como configuração padrão de execução.

Variáveis obrigatórias:
- `DATABASE_URL`: string de conexão PostgreSQL usada na inicialização do pool.

Endpoints já ligados ao PostgreSQL:
- `POST /auth/register`
- `POST /auth/login`
- `GET /menu/today`
- `GET /products/:id`
- `GET /allergens`
- `GET /health` (retorna status do banco)

Exemplo com arquivo de ambiente (recomendado):
```bash
cd backend
npm run dev
```

> Observação: o restante dos fluxos (pedidos, carteira, parental e operações) ainda está em memória e será migrado na próxima etapa.

## Frontend React (mobile-first)
Foi adicionado um frontend inicial em `frontend/index.html` usando React (via CDN) integrado ao backend do MVP.

### Fluxo disponível
- Login (`/auth/login`)
- Consulta de cardápio (`/menu/today`)
- Criação de pedido (`/orders`)
- Pagamento por carteira (`/wallet/pay`)

### Como executar frontend
Em um terminal, suba o backend:
```bash
cd backend
npm install
npm start
```

Em outro terminal, inicie o frontend:
```bash
npm run dev
```

A aplicação abre em `http://localhost:5173`.


## Plano de integração com `canteen-express`
Foi adicionada uma trilha inicial em `docs/canteen-express-integration-plan.md` com:
- fases de execução por fluxo (discovery, MVP, staff e avançados);
- checklist de kickoff técnico;
- riscos e mitigação para integração incremental.

## Próximos passos recomendados
1. Persistir dados em PostgreSQL (trocar store em memória por repositórios SQL).
2. Implementar autenticação JWT com hash seguro de senha.
3. Integrar Mercado Pago real (checkout + webhook assinado).
4. Criar suíte de testes automatizados de API.
5. Conectar frontend React ao backend.
