# Tipos de Dados Algébricos

Tutorial 1 - Programação Guiada por Tipos

João Pedro Kayano Leal - 11202020271


## Definição
Tipos de dados algébricos, em inglês *algebraic data types*, são tipos gerados a partir da composição de outros tipos.

Em haskell um novo tipo de dado algébrico é criado seguindo a forma:

```haskell
data NovoTipo = ...
```
Cada tipo algébrico define um conjunto de valores, logo é possivel definir a cardinalidade de um tipo. A cardinalidade de um tipo é a quantidades de valores que ele pode assumir.

### Tipo Zero
Um tipo de cardinalidade zero é um conjunto vazio, logo uma variavel desse tipo não pode assumir nenhum valor. Ele é definido como:

```haskell
data Zero
```
### Tipo Um
Um tipo de cardinalidade um é um conjunto de um uníco elemento, logo uma variavel desse tipo só pode assumir valor. Ele é definido como:

```haskell
data Um = ()
```

Em Haskell Tipos compostos podem surgir de dois padrões, ou de uma combinação deles, tipo soma e tipo produto

## Tipo Soma
Um tipo pode ser formado da união de dois conjuntos diferentes, a cardinalidade desse novo tipo será igual a soma das cardinalidades dos tipos unidos, logo o nome tipo soma. Em Haskell um tipo soma pode ser gerado com `|` como separador entre os valores, ou com o `Either` para fazer a união disjunta dos conjuntos.

Ex.: Pessoa Fisicia ou Juridica

```haskell
-- utilizando |
data Pessoa = Fisica | Juridica
P0 = Juridica
P1 = Fisica

-- utilizando either
type Pessoa = Either () ()
P2 = Left () :: Pessoa
P3 = Right () :: Pessoa
```

Ex.: Boolean, o tipo booleano é a soma entre um tipo unitário de valor `True` e um tipo unitário de valor `False`

```haskell
data Boolean = True | False
```

## Tipo Produto
Um tipo pode ser formado do produto cartesiano de dois conjuntos diferentes, a cardinalidade desse novo tipo será igual ao produto das cardinalidades dos tipos unidos, logo o nome tipo produto.
Em Haskell um tipo produto pode ser gerado com um construtor, com o *record type*

Ex.: Endereço é um produto entre o logradouro e o numero

```haskell
-- utilizando constructor
data Endereco = MkEndereco String Int
E0 = MkEndereco "Av. dos Estados" 5001

-- utilizando record type
type Endereco = MkEndereco {logradouro :: String
   	        	,Numero :: Int
           	}
E1 = MkEndereco "Av. dos Estados" 5001
```

Além disso qualquer tupla também é um tipo produto

## Tipos Recursivos
Outra maneira de definir tipos é por meio da recursão. Isso ocorre quando um tipo é uma combinação de produto ou soma de, entre outros tipos, ele mesmo.

Ex.: Lista ligada 

Por definição uma lista ligada é uma sequência de n elementos do mesmo tipo, lgoo poderiam ser definidas pela soma de uma lista vazia, uma lista de um elemente, uma lista de dois elementos e assim infinitamente.
Por isso se utiliza a recursão para definilas

```haskell
data [a] = [] | a:[a]
```

Outra coisa que se pode perceber na lista é que são tipos parametricos, ou seja, sua definição varia dependendo de um tipo parametro `a`.

Ex.: Árvore 

```haskell
data Arvore a = Empty | No a [(Arvore a)]
```
Ex.: Árvore Binária

```haskell
data ArvoreBin a = Empty | No (ArvoreBin a) a (ArvoreBin a)
```

## Zippers

Leve em consideração o tipo parametrico da um lista

```haskell
data [a] = [] | a:[a]
```

Agebricamente podemos defini-lo como

$$L(a) = 1 + a*L(a)$$

$$L(a) = \frac{1}{1-a}$$

Ao derivar $L(a)$ em função de $a$ obtem-se seu zipper. Um zipper de um tipo representa uma menira de atravesá-lo em tempo constante

$$L'(a) = 0 + (aL'(a)) + (1L(a))$$

$$L'(a) = \frac{L(a)}{1-a}$$

$$L'(a) = L(a)*\frac{1}{1-a}$$

$$L'(a) = L(a)*L(a)$$

Logo o zipper de uma lista é o produto de duas listas, que pode ser implementado como:

```haskell
data ListZipper a = ListZipper [a] [a]

generateZipper :: [a] -> ListZipper a
generateZipper = xs -> ListZipper [] xs

-- mover para o próximo item da lista (para a direita)
nextList :: ListZipper a -> ListZipper a
nextList = (ListZipper behind (x:infront))  -> ListZipper (x:behind) infront


-- mover para o ultimo item da lista (para a esquerda)
lastList :: ListZipper a -> ListZipper a
lastList = (ListZipper behind (x:infront))  -> ListZipper behind (x:infront)

-- item em foco
focus :: ListZipper a -> a
focus (ListZipper _ []) = error "empty list"
focus (ListZipper _ (x:xs)) = x
```
Assim o zipper define uma maneira de se locmover em uma lista (ao redor de um ponto de foco) em tempo constante.


## Classes de Tipos
Além do que foi explicado anteriormente ainda é possivel definir classes de tipos. Classes tipos representam propriedades que todos os tipos pertencentes a classe possuem.
Um exemplo disso é a classe monoid definida como um conjunto com uma operação binária e um elemento neutro. Um Monoid, por exemplo, é a classe de string com a operação de concatenação.

```haskell
class Monoid a where
    mempty  :: a
    mappend :: a -> a -> a

    mconcat :: [a] -> a
    mconcat = foldr mappend mempty

-- definir que um tipo pertence a uma certa classe
instance Monoid [a] where
    mempty = []
    mappend = (++)
```
