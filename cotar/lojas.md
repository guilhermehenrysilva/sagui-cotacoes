# Sagui Cotações — Como coletar em cada loja

Use o **Claude in Chrome** (aba nova; feche ao final). Prefira `fetch` de dentro da página da própria loja
(mesma origem) + `DOMParser` — é rápido e coleta várias buscas de uma vez. Saída do `javascript_tool` é cortada
em ~1.500 caracteres: guarde em `window._x` e imprima por partes.

## Lojas no modo "geral"
- **Hardware/eletrônicos**: KaBuM, Pichau, Terabyte, Mercado Livre, Amazon.com.br.
- **Outros produtos**: Amazon.com.br, Mercado Livre, Magazine Luiza, Casas Bahia, KaBuM (se fizer sentido) e
  lojas especializadas brasileiras da categoria.
- **Nunca**: AliExpress, Shein, Temu, Banggood ou qualquer loja/vendedor da China ou "Compra Internacional".
- Shopee: só se acessível sem login e com vendedor nacional (tem muito vendedor internacional — na dúvida, pule).

## KaBuM
- Em `kabum.com.br`: `fetch('/busca/<termo-com-hifens>')`, extrair `<script id="__NEXT_DATA__">` →
  `props.pageProps.data` (pode ser string JSON; procure recursivamente o array de itens com `code`, `name`, `price`).
- Campos: `priceWithDiscount` = Pix; `price` = cartão; `maxInstallment` = "10x de R$ …"; `available`; `sellerName`
  (marketplace se ≠ "KaBuM!"). Link: `https://www.kabum.com.br/produto/<code>`.

## Pichau
- Em `pichau.com.br`: `fetch('/search?q=<termo>')`, parsear `a[data-cy="list-product"]`; nome no `h2`.
- Texto: `R$ X À vista` (Pix), `PIX R$ Y` (cartão), `Em até 12x de R$ Z`. **Números no formato 1,234.56.**
- Excluir "OPENBOX". Link: `https://www.pichau.com.br` + href.

## Terabyte
- Em `terabyteshop.com.br`: `fetch('/busca?str=<termo>')`, parsear `.product-item`.
- `por: R$ X` = Pix; `12x de R$ Z sem juros` (total = parcelas × valor). Excluir "open box", "indisponível".
- Link: `https://www.terabyteshop.com.br` + href de `a[href*="/produto/"]`.

## Mercado Livre
- `fetch` não funciona: **navegar** para
  `https://lista.mercadolivre.com.br/<termo-com-hifens>_ITEM*CONDITION_2230284_NoIndex_True` (filtro **Novo**),
  esperar ~2,5s (`await new Promise(r=>setTimeout(r,2500))`) e parsear `li.ui-search-layout__item`.
- Nome em `h3`/`[class*=title]`; preço atual em `.andes-money-amount` (fração + centavos); parcelas em
  `[class*=installments]` ("ou R$ X em outros meios" = preço no cartão).
- Excluir "internacional", "usado", "muito novo", "seminovo". Preferir links `/p/MLB…` (catálogo).

## Amazon.com.br
- Em `amazon.com.br`: `fetch('/s?k=<termo>')`, parsear `[data-component-type="s-search-result"]`
  (`h2`, `.a-price:not(.a-text-price) .a-offscreen`, `data-asin`). Link: `https://www.amazon.com.br/dp/<ASIN>`.
- Para os escolhidos, `fetch('/dp/<ASIN>')` e confirmar `#availability` (em estoque) e vendedor
  (`[offer-display-feature-name="desktop-merchant-info"]`) brasileiro. Pix = preço exibido.

## Magazine Luiza / Casas Bahia / outras
- Navegar para a busca da loja e usar `get_page_text` ou parsear os cards pelo DOM.
- Confirmar estoque na página do produto antes de registrar.
