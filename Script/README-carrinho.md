# Integração do carrinho

O carrinho funciona sem conta e salva apenas identificadores e quantidades em `localStorage`, na chave `ideiaTech.carrinho.v1`. Os dados permanecem no mesmo navegador e origem. Preços são definidos em centavos no catálogo; cores ainda não cadastradas aparecem como “Não informada”. Para adicionar variantes de cor, cadastre cada variante com um identificador próprio.

## Autenticação e pagamento

Carregue `fluxoAutenticacao.js` também nas futuras páginas de cadastro e no retorno da autenticação social. O parâmetro `next` e a chave de `sessionStorage` `ideiaTech.retornoLogin` preservam o destino durante o login/cadastro. O formulário de login recebe um campo oculto `next`; o servidor precisa validar que ele pertence ao site antes de redirecionar.

Depois de o servidor confirmar o login ou cadastro, chame:

```js
window.FluxoAutenticacao.concluirLogin();
```

Esse método apenas retorna à página de origem; ele não autentica o usuário. Não o chame apenas porque o formulário foi enviado. O carrinho permanece salvo e o usuário pode revisar a compra antes de continuar novamente.

Na página do carrinho, conecte as funções reais:

```js
window.FluxoAutenticacao.configurar({
    verificarSessao: async () => {
        // Consultar a sessão autenticada no servidor e devolver true ou false.
    },
    iniciarPagamento: async (itens) => {
        // Enviar [{ id, quantidade }] ao servidor e abrir o checkout confirmado.
    }
});
```

Por padrão, a sessão é considerada ausente e o botão direciona para `Pages/login.html`. Não há login fictício ou pagamento implementado. As rotas existentes `/login` (POST), `/register/cliente` e `/register/administrador` ainda dependem do backend. O servidor deverá verificar a sessão e recalcular preços, disponibilidade, quantidade e frete, sem confiar no navegador. Adicionar ao carrinho não reserva estoque.

## Verificação

Execute `node --test tests/carrinho.test.cjs`. Os testes cobrem adição sem conta, estoque, persistência, total, edição, remoção, falhas de armazenamento, login/retorno, bloqueio de destino externo e identificação dos oito produtos.
