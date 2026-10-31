# Doce Forno — Pastelaria online (site + app instalável)

## O que o projecto faz
1. **Cadastro obrigatório** antes de encomendar: email, número de celular (+258), senha e confirmação da senha. O cadastro só activa depois de confirmar o código enviado por email.
2. **Entrar** com número + senha.
3. **Finalizar pedido** em 3 passos:
   - **Entrega:** nome, cidade, bairro, rua/nº, ponto de referência, contacto e botão "Usar a minha localização" (GPS). A morada fica guardada para o próximo pedido.
   - **Pagamento:** **M-Pesa** (84/85), **e-Mola** (86/87) ou **Cartão** (validação Luhn, validade e CVV).
   - **Confirmar:** resumo com total (+ taxa de entrega) e pagamento.
4. **Os meus pedidos** na conta do cliente.
5. **Email:** código de verificação no cadastro; email interno para a pastelaria e confirmação/recibo para o cliente após registar o pedido.
6. **App instalável (PWA):** botão "Instalar app" no Android/Chrome; no iPhone, Partilhar → "Adicionar ao ecrã principal".

## Estrutura
```
index.html        página principal
css/style.css     estilos
js/app.js         lógica (conta, carrinho, checkout, pagamentos)
manifest.json     dados da app instalável
sw.js             service worker (funciona offline para o menu)
icons/            ícones da app
backend/          servidor Node/Express (esqueleto)
```

## Como testar já
Precisa de servidor (a app instalável não funciona abrindo o ficheiro direto):
```
cd doce-forno
python3 -m http.server 8000      # abre http://localhost:8000
```
Ou com o backend: `cd backend && npm install && npm start` (http://localhost:3000).

## ⚠️ Estado actual: modo demonstração
O que ainda está **simulado / por integrar**:
- **Pagamentos:** o site ainda não cobra dinheiro. O pedido é registado como recebido, mas o estado real do pagamento depende da integração com M-Pesa/e-Mola ou um gateway de cartões.
- **Persistência:** o backend usa `db.json` como armazenamento simples; antes de produção, migra para uma base de dados.
- **Fotos:** são carregadas de URLs do Unsplash e precisam de internet. Confirma as licenças/termos antes de usar comercialmente.
- **Pagamentos:** apenas simulam 3,5 s de espera e marcam "Pago (demo)". Nenhum dinheiro é cobrado.

## Para ir a produção
1. **M-Pesa:** pede acesso à Open API M-Pesa Moçambique (Vodacom) e implementa `POST /api/payments/mpesa` (pedido ao cliente via USSD push).
2. **e-Mola:** pede credenciais de comerciante à Movitel e implementa `/api/payments/emola`.
3. **Cartão:** usa um gateway com campos/página hospedados pelo fornecedor, para o número do cartão **nunca passar pelo teu servidor**. O front-end actual não guarda dados de cartão.
4. O estado do pedido passa a "Pago" **só** com a confirmação do operador (callback), nunca por indicação do browser. O servidor já recalcula o total do pedido.
5. Usa HTTPS, base de dados a sério (PostgreSQL/MySQL), `JWT_SECRET` forte e limite de pedidos (`express-rate-limit`).
6. Confirma a taxa de entrega em `CONFIG.DELIVERY_FEE` (js/app.js) e `DELIVERY_FEE` (backend/server.js): está em 100 MT como valor de exemplo.

## Levar para as lojas (opcional)
- **Android:** publicar a PWA como TWA com o [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap) ou empacotar com [Capacitor](https://capacitorjs.com).
- **iOS:** Capacitor (precisa de Mac e conta Apple Developer).


## Configurar verificação e recibos por email
1. Entra em `doce-forno/backend` e copia `.env.example` para `.env`.
2. Configura `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `MAIL_FROM` e `ORDER_EMAIL`. Para Gmail, usa uma palavra-passe de aplicação, não a palavra-passe normal.
3. Executa `npm install` e `npm start` dentro de `backend`. Abre `http://localhost:3000` para usar o site servido pelo backend. Não abras `index.html` directamente nem uses apenas `python -m http.server`, porque a verificação de email e o registo real de pedidos precisam da API Node.
4. Ao criar conta, o código de 6 dígitos expira em 10 minutos. Depois de um pedido registado, o servidor envia o detalhe para a pastelaria e um resumo/recibo para o email do cliente.

**Importante:** o pagamento continua em modo de demonstração. Não anuncies a encomenda como paga até integrar e validar a confirmação real do operador. Nunca coloques credenciais SMTP no front-end nem publiques o ficheiro `.env`.
