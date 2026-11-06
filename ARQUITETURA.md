# ARQUITETURA — Meus Gastos

## 1. Se a fonte de dados mudar de memória para outra implementação, que partes podem permanecer iguais? Quais partes ainda podem precisar de adaptação?

A interface visual (`TelaGastos`, `FormularioGasto`, `LinhaGasto`, `ResumoTotal` e `Etiqueta`) pode permanecer igual porque recebe estado e callbacks do `GastosViewModel`, sem conhecer o repositório. O `GastosViewModel` depende do contrato `GastoRepository`, então é possível trocar `GastoRepositoryMemoria` por `GastoRepositoryDemo` sem editar a View.

Se a fonte mudar para um banco de dados real, como Room, será necessário implementar e configurar a nova fonte. O ViewModel e o contrato também podem precisar de adaptações para operações assíncronas e observação de dados, conforme a implementação escolhida. O tutorial demonstra diretamente apenas a troca entre dois repositórios em memória.

## 2. Por que `total` e `quantidade` são calculados a partir da lista visível? Descreva um erro concreto que surgiria se fossem atualizados manualmente.

`total` usa `gastosVisiveis.sumOf { it.valor }` e `quantidade` usa `gastosVisiveis.size`. Por isso, ambos correspondem aos itens mostrados pelo filtro atual. Calcular esses valores a partir da lista evita ter que sincronizar variáveis separadas em cada operação.

Exemplo: se o app tiver um gasto de R$ 20,00 em Alimentação e outro de R$ 10,00 em Transporte, o total geral é R$ 30,00. Ao filtrar apenas Transporte, o resumo correto é R$ 10,00 e 1 gasto. Se o total fosse uma variável atualizada somente ao adicionar, continuaria mostrando R$ 30,00, mesmo com apenas o gasto de Transporte visível.

## 3. O que os componentes sem estado conseguem fazer nos previews que seria menos simples com dependência direta de um ViewModel?

Os componentes sem estado recebem os dados e callbacks por parâmetros. Assim, um `@Preview` pode mostrar, por exemplo, um formulário vazio, um formulário com mensagem de erro, uma etiqueta ativa ou um resumo com valores fictícios sem precisar iniciar um ViewModel, simular um repositório ou executar validações de verdade. Isso facilita testar e visualizar cada componente isoladamente.

## Resumo do fluxo MVVM

Um clique em **Adicionar** na View chama `vm.adicionar()`. O ViewModel valida a descrição e o valor, pede ao `GastoRepository` que adicione o item e chama `sincronizar()`. A lista observável é atualizada e o Jetpack Compose redesenha as partes da interface que leem esse estado. A View não acessa diretamente a fonte de dados.
