# ESTRAGOS — Experimentos de depuração

> Faça cada alteração separadamente, execute o app, anote o resultado real e RESTAURE o código original antes de ir ao próximo. Os campos **O que observei** abaixo estão em branco porque precisam registrar o comportamento observado por você no Android Studio.

## Experimento 1 — Construir ViewModel na tela

**Mudança:** em `ui/TelaGastos.kt`, trocar o valor padrão `viewModel()` por `GastosViewModel()`.

**Minha previsão:** ao construir o ViewModel diretamente na composição, ele não terá a retenção de instância associada ao `ViewModelStore`. A tela poderá perder os gastos e o estado, por exemplo após recomposição ou rotação.

**O que observei:** [PREENCHER APÓS EXECUTAR: cadastrei itens, digitei e girei a tela; ocorreu...]

**O que explica o resultado:** `viewModel()` gerencia a instância de acordo com o ciclo de vida da tela. Chamar diretamente o construtor dentro da função composable não oferece essa garantia. O resultado específico depende de quando ocorre recomposição.

**Como restaurei o código:** voltei a usar `vm: GastosViewModel = viewModel()`.

## Experimento 2 — Lista não observável

**Mudança:** em `viewmodel/GastosViewModel.kt`, substituir `mutableStateListOf<Gasto>()` por `mutableListOf<Gasto>()`.

**Minha previsão:** alterações na lista poderão deixar de provocar a atualização automática da interface. Alterações em outros estados observáveis ainda podem causar recomposição.

**O que observei:** [PREENCHER APÓS EXECUTAR: tentei adicionar/remover e observei...]

**O que explica o resultado:** `mutableStateListOf` é uma lista observável pelo Compose; `mutableListOf` é uma coleção Kotlin comum, que não informa por si só ao Compose quando seus elementos mudam.

**Como restaurei o código:** voltei a `mutableStateListOf<Gasto>()`.

## Experimento 3 — Total manual

**Mudança:** substituir temporariamente o cálculo derivado de `total` por uma variável atualizada somente ao adicionar, sem atualizar ao remover nem ao filtrar.

**Minha previsão:** o total poderá ficar incorreto depois de remover um gasto ou filtrar uma categoria.

**O que observei:** [PREENCHER APÓS EXECUTAR: descreva valores cadastrados, filtro/remoção e total exibido...]

**O que explica o resultado:** o valor armazenado manualmente pode ficar desatualizado, enquanto `gastosVisiveis.sumOf { it.valor }` sempre calcula o total dos itens atualmente visíveis.

**Como restaurei o código:** retornei à propriedade derivada `val total: Double get() = gastosVisiveis.sumOf { it.valor }`.

## Experimento 4 — Acoplamento à classe

**Mudança:** trocar o campo `repositorio: GastoRepository` por uma dependência direta de `GastoRepositoryMemoria`.

**Minha previsão:** a troca para `GastoRepositoryDemo` deixará de ser simples: será necessário modificar o ViewModel ou alterar seu tipo concreto, em vez de mudar somente a implementação fornecida ao contrato.

**O que observei:** [PREENCHER APÓS EXECUTAR: tentei trocar Memoria por Demo e observei...]

**O que explica o resultado:** o contrato `GastoRepository` abstrai a implementação. Tipar o campo diretamente como `GastoRepositoryMemoria` aumenta o acoplamento e dificulta a substituição.

**Como restaurei o código:** retornei ao tipo `GastoRepository` com implementação padrão `GastoRepositoryMemoria()`.

## Experimento 5 — Regra na tela

**Mudança:** mover temporariamente a validação da descrição para o `onClick` do botão, em vez de mantê-la concentrada no ViewModel.

**Minha previsão:** o comportamento de validação ficará dependente daquela tela e exigirá duplicação se outra interface também permitir adicionar um gasto.

**O que observei:** [PREENCHER APÓS EXECUTAR: tentei adicionar descrição vazia e observei...]

**O que explica o resultado:** a View deve exibir o estado e encaminhar eventos, enquanto o ViewModel centraliza regras de entrada e ações. Colocar a regra na tela reduz a reutilização e dificulta o teste isolado da regra.

**Como restaurei o código:** retirei a validação do `onClick` e mantive a validação original em `GastosViewModel.adicionar()`.

---

**Verificação final:** código original restaurado; aplicativo recompilado e testado após os cinco experimentos.
