# 🥐 Doce Forno — Pastelaria online

Site de encomendas para pastelaria, com app instalável (PWA) e servidor em Node.js.

## Funcionalidades
- Catálogo com categorias, fotos e carrinho
- Cadastro com **código de verificação enviado por email**
- Login com número de celular (+258) e senha
- Checkout em 3 passos: entrega (com GPS), pagamento e confirmação
- Pagamento por **M-Pesa**, **e-Mola** ou **cartão** (em integração)
- Emails automáticos: código de verificação e confirmação do pedido
- "Os meus pedidos" na conta do cliente
- App instalável no telemóvel (PWA)

## Tecnologias
HTML, CSS e JavaScript no front-end. Node.js, Express e Nodemailer no back-end.

## Como correr localmente
```bash
cd backend
npm install
```
Cria o ficheiro `backend/.env` a partir do `.env.example` e preenche os dados de SMTP:
```env
PORT=3000
JWT_SECRET=um-segredo-longo-e-aleatorio
ORDER_EMAIL=email-da-pastelaria@exemplo.com
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=teuemail@gmail.com
SMTP_PASS=palavra-passe-de-app
MAIL_FROM="Doce Forno <teuemail@gmail.com>"
```
Testa o email e arranca o servidor:
```bash
npm run test-email
npm start
```
Abre http://localhost:3000

## Estado do projecto
- ✅ Site, carrinho, cadastro com verificação por email, pedidos
- 🔧 Pagamentos M-Pesa, e-Mola e cartão: por ligar (requerem credenciais dos operadores)
- 🔧 Base de dados: usa um ficheiro JSON; para produção deve passar para PostgreSQL ou similar

## Segurança
- Nunca publiques o ficheiro `.env` (está no `.gitignore`)
- Os totais dos pedidos são sempre recalculados no servidor
- Dados de cartão nunca passam pelo servidor

## Licença
Todos os direitos reservados © 2026 Doce Forno.
