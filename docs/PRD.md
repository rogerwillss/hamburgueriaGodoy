# PRD - Aplicativo de Pedidos da Hamburgueria

## 1. Visão geral

O projeto é um aplicativo mobile feito com Expo, React Native, Expo Router,
NativeWind e Zustand. O objetivo principal é permitir que clientes visualizem o
cardápio de uma hamburgueria, escolham produtos, montem um carrinho e enviem o
pedido com endereço de entrega.

Atualmente o cardápio é local, definido no código, e o envio do pedido grava os
dados no Supabase nas tabelas `orders` e `order_items`.

## 2. Objetivo do produto

Facilitar o recebimento de pedidos digitais da hamburgueria, reduzindo atrito no
processo de escolha, montagem do carrinho e registro do pedido.

## 3. Público-alvo

- Clientes da hamburgueria que desejam fazer pedidos pelo celular.
- Equipe da hamburgueria que precisa receber pedidos com endereço, itens,
  quantidades e valor total.

## 4. Problema

Pedidos feitos manualmente por conversa ou ligação podem gerar erros de item,
quantidade, endereço e valor total. O aplicativo centraliza o fluxo do pedido e
padroniza os dados enviados.

## 5. Proposta de solução

Oferecer uma experiência simples de cardápio digital com:

- Listagem de produtos por categoria.
- Tela de detalhe com imagem, descrição, preço e ingredientes.
- Carrinho persistente no dispositivo.
- Cálculo automático de quantidade e total.
- Campo obrigatório de endereço.
- Registro do pedido no Supabase.

## 6. Escopo atual

### 6.1 Tela inicial

O cliente deve conseguir:

- Ver o título da tela e a marca da hamburgueria.
- Navegar pelas categorias do cardápio.
- Visualizar produtos agrupados por categoria.
- Abrir a tela de detalhe de um produto.
- Ver o ícone do carrinho quando houver itens adicionados.
- Ver a quantidade total de itens no carrinho.

### 6.2 Tela de produto

O cliente deve conseguir:

- Ver imagem de capa do produto.
- Ver nome, preço, descrição e ingredientes.
- Adicionar o produto ao carrinho.
- Retornar ao cardápio.

### 6.3 Carrinho

O cliente deve conseguir:

- Ver todos os produtos adicionados.
- Ver a quantidade de cada produto.
- Remover uma unidade de um produto do carrinho.
- Remover completamente um produto quando sua quantidade chegar a zero.
- Ver o total do pedido formatado em reais.
- Informar endereço de entrega.
- Enviar o pedido.
- Voltar ao cardápio.

### 6.4 Persistência local

O carrinho deve ser persistido localmente com AsyncStorage por meio do Zustand,
usando a chave `hamburguer:cart`.

### 6.5 Persistência remota

Ao enviar um pedido válido, o aplicativo deve:

1. Criar um registro em `orders` com endereço e preço total.
2. Criar registros em `order_items` com os produtos do pedido.
3. Limpar o carrinho após sucesso.
4. Voltar para a tela anterior após sucesso.

## 7. Fora do escopo atual

- Login de cliente.
- Pagamento online.
- Cálculo de frete.
- Cupom de desconto.
- Status em tempo real do pedido.
- Área administrativa para gerenciar cardápio.
- Integração com WhatsApp.
- Edição de quantidade diretamente no carrinho.

## 8. Requisitos funcionais

### RF01 - Exibir cardápio

O sistema deve exibir produtos agrupados por categoria.

Critérios de aceite:

- As categorias devem refletir os grupos definidos em `MENU`.
- Cada produto deve exibir imagem, nome e descrição na lista.
- A seleção de categoria deve rolar a lista para a seção correspondente.

### RF02 - Exibir detalhe do produto

O sistema deve abrir uma tela de detalhe ao selecionar um produto.

Critérios de aceite:

- Produto válido deve exibir capa, nome, preço, descrição e ingredientes.
- Produto inexistente deve redirecionar o usuário para a tela inicial.

### RF03 - Adicionar produto ao carrinho

O sistema deve permitir adicionar produtos ao carrinho.

Critérios de aceite:

- Ao adicionar um produto novo, ele deve entrar com quantidade `1`.
- Ao adicionar um produto já existente, sua quantidade deve ser incrementada.
- Após adicionar, o app deve retornar para a tela anterior.

### RF04 - Exibir resumo do carrinho

O sistema deve exibir os itens do carrinho e o valor total.

Critérios de aceite:

- O total deve considerar `price * quantity` de cada item.
- O total deve ser exibido em formato monetário brasileiro.
- Carrinho vazio deve exibir mensagem de estado vazio.

### RF05 - Remover produto do carrinho

O sistema deve permitir remover produtos do carrinho.

Critérios de aceite:

- Ao confirmar remoção, a quantidade do produto deve diminuir em `1`.
- Se a quantidade chegar a `0`, o produto deve sair do carrinho.
- Ao cancelar a confirmação, o carrinho não deve ser alterado.

### RF06 - Validar endereço

O sistema deve exigir endereço antes de enviar o pedido.

Critérios de aceite:

- Endereço vazio ou apenas com espaços deve bloquear o envio.
- O usuário deve receber alerta solicitando o endereço.

### RF07 - Registrar pedido

O sistema deve salvar pedido e itens no Supabase.

Critérios de aceite:

- O pedido deve ser salvo em `orders`.
- Os itens devem ser salvos em `order_items` vinculados ao `order_id`.
- Em caso de erro ao salvar pedido, os itens não devem ser enviados.
- Em caso de erro ao salvar itens, o app deve informar falha ao usuário.
- Em caso de sucesso, o carrinho deve ser limpo.

## 9. Requisitos não funcionais

- O app deve funcionar em Android, iOS e web via Expo sempre que as dependências
  suportarem a plataforma.
- O carrinho deve continuar disponível após fechar e reabrir o aplicativo.
- A interface deve manter leitura adequada em telas pequenas.
- Operações de pedido devem tratar erros de rede ou Supabase sem quebrar a tela.
- Chaves do Supabase devem vir de variáveis de ambiente públicas do Expo.

## 10. Modelo de dados esperado

### orders

Campos esperados:

- `id`: identificador do pedido.
- `address`: endereço de entrega.
- `total_price`: valor total numérico do pedido.
- `created_at`: data de criação, recomendada no banco.

### order_items

Campos esperados:

- `id`: identificador do item, recomendado.
- `order_id`: referência ao pedido.
- `product_id`: identificador do produto no cardápio.
- `product_title`: nome do produto no momento do pedido.
- `quantity`: quantidade comprada.
- `price`: preço unitário no momento do pedido.

## 11. Indicadores de sucesso

- Pedido pode ser criado sem intervenção manual.
- Carrinho mantém quantidades corretas.
- Valor total salvo no banco bate com o valor exibido no app.
- Usuário não consegue enviar pedido sem endereço.
- Falhas de Supabase são comunicadas com clareza.

## 12. Riscos e observações técnicas

- O cardápio é local; qualquer alteração de produto exige atualização do app.
- Algumas strings aparecem com encoding corrompido no código, por exemplo
  `FaÃ§a`, `AtenÃ§Ã£o` e `cardÃ¡pio`. Recomenda-se corrigir para UTF-8.
- O arquivo `src/services/supabase.ts` imprime partes das variáveis do Supabase
  no console. Recomenda-se remover esses logs antes de produção.
- A função `handleOder` parece ter erro de digitação no nome; o ideal seria
  renomear para `handleOrder`.
- O componente `Button` usa a classe `"5-12"`, que parece ser erro de digitação.
  Provavelmente deveria ser uma classe de altura, como `h-12`.
- O app importa `Linking` no carrinho, mas não usa esse recurso atualmente.

## 13. Testes unitários recomendados

### 13.1 `formatCurrency`

Arquivo alvo: `src/utils/functions/format-currency.ts`

Casos:

- Deve formatar `24.9` como moeda brasileira.
- Deve formatar `0` corretamente.
- Deve preservar centavos em valores quebrados.
- Deve retornar string com prefixo de real conforme locale `pt-BR`.

Exemplo de nome de teste:

- `formatCurrency should format values as BRL`

### 13.2 Helper de carrinho: adicionar produto

Arquivo alvo: `src/stores/helpers/cart-in-memory.ts`

Casos:

- Deve adicionar produto novo com quantidade `1`.
- Deve incrementar quantidade quando produto já existe.
- Deve manter os demais produtos sem alteração.
- Deve retornar um novo array, sem mutar o array original.

Exemplos de nomes de teste:

- `add should add a new product with quantity one`
- `add should increment quantity for an existing product`
- `add should not mutate current products`

### 13.3 Helper de carrinho: remover produto

Arquivo alvo: `src/stores/helpers/cart-in-memory.ts`

Casos:

- Deve decrementar quantidade quando produto tem quantidade maior que `1`.
- Deve remover produto quando quantidade é `1`.
- Deve manter outros produtos no carrinho.
- Deve retornar a mesma lista quando o `productId` não existir.
- Deve retornar array vazio ao remover o único produto com quantidade `1`.

Exemplos de nomes de teste:

- `remove should decrement quantity when greater than one`
- `remove should delete product when quantity reaches zero`
- `remove should keep unrelated products`

### 13.4 Store do carrinho

Arquivo alvo: `src/stores/cart-store.ts`

Casos:

- Estado inicial deve ter `products` vazio.
- `add` deve adicionar produto ao estado.
- `remove` deve remover ou decrementar produto no estado.
- `clear` deve limpar todos os produtos.
- Persistência deve usar a chave `hamburguer:cart`.

Observação:

- Para esse teste, mockar `@react-native-async-storage/async-storage`.

### 13.5 Dados do cardápio

Arquivo alvo: `src/utils/data/products.ts`

Casos:

- `CATEGORIES` deve ser derivado dos títulos de `MENU`.
- `PRODUCTS` deve conter todos os produtos de todas as categorias.
- Todos os produtos devem ter `id`, `title`, `price`, `description`,
  `thumbnail`, `cover` e `ingredients`.
- Todos os `id` de produto devem ser únicos.
- Preços devem ser maiores que zero.

### 13.6 Tela de produto

Arquivo alvo: `src/app/product/[id].tsx`

Casos:

- Deve renderizar dados do produto quando `id` é válido.
- Deve chamar `cartStore.add` ao pressionar "Adicionar ao pedido".
- Deve chamar `navigation.goBack` após adicionar produto.
- Deve redirecionar para `/` quando `id` não existir.

Observação:

- Mockar `expo-router`, `useCartStore` e imagens.

### 13.7 Header

Arquivo alvo: `src/components/header.tsx`

Casos:

- Deve renderizar título recebido.
- Deve ocultar ícone do carrinho quando quantidade for `0` ou indefinida.
- Deve exibir ícone e badge quando quantidade for maior que `0`.
- Badge deve exibir o número informado.

### 13.8 Produto na lista

Arquivo alvo: `src/components/products.tsx`

Casos:

- Deve renderizar título e descrição.
- Deve renderizar quantidade quando `quantity` existir.
- Não deve renderizar quantidade quando `quantity` for ausente.
- Deve disparar `onPress` quando pressionado.

### 13.9 Categoria

Arquivo alvo: `src/components/category-button.tsx`

Casos:

- Deve renderizar o título da categoria.
- Deve aplicar estilo de selecionado quando `isSelected` for verdadeiro.
- Deve disparar `onPress` quando pressionado.

### 13.10 Fluxo de envio do pedido

Arquivo alvo: `src/app/cart.tsx`

Casos:

- Deve bloquear envio com endereço vazio.
- Deve calcular `total_price` corretamente.
- Deve inserir registro em `orders`.
- Deve inserir itens em `order_items` com `order_id`.
- Deve limpar carrinho quando pedido e itens forem salvos com sucesso.
- Deve não limpar carrinho se salvar pedido falhar.
- Deve não limpar carrinho se salvar itens falhar.

Observação:

- Esse teste exige mocks para `supabase`, `Alert`, `useNavigation` e
  `useCartStore`. Se ficar pesado para unitário, pode ser dividido extraindo a
  lógica de montagem do pedido para uma função pura testável.

## 14. Priorização dos testes

Prioridade alta:

- `cart-in-memory.ts`
- `format-currency.ts`
- cálculo e envio do pedido no carrinho

Prioridade média:

- `cart-store.ts`
- `products.ts`
- `product/[id].tsx`

Prioridade baixa:

- componentes visuais simples como `Header`, `Product`, `Input`, `Button` e
  `CategoryButton`, salvo se forem usados como base de muitos fluxos.

## 15. Sugestão de estrutura de testes

```text
src/
  utils/
    functions/
      format-currency.test.ts
    data/
      products.test.ts
  stores/
    helpers/
      cart-in-memory.test.ts
    cart-store.test.ts
  components/
    header.test.tsx
    products.test.tsx
    category-button.test.tsx
  app/
    cart.test.tsx
    product/
      product-detail.test.tsx
```

## 16. Ferramentas recomendadas

O projeto já possui Jest e `jest-expo`. Para testes de componentes React Native,
recomenda-se adicionar:

- `@testing-library/react-native`
- `@testing-library/jest-native`, se quiser matchers extras

Comando sugerido:

```bash
npm install --save-dev @testing-library/react-native @testing-library/jest-native
```

## 17. Próximos passos recomendados

1. Corrigir textos com encoding quebrado.
2. Remover logs sensíveis do Supabase.
3. Implementar primeiros testes unitários para `cart-in-memory.ts`.
4. Implementar teste de `formatCurrency`.
5. Extrair a lógica de criação de pedido para facilitar teste unitário.
6. Adicionar testes do fluxo de envio com mocks do Supabase.
