# Introdução

O presente documento objetiva descrever os requisitos básicos para implementação de um Recurso de Gerenciamento de Produtos a enviar para a API iFood. São descritos os métodos, regras de negócio e exemplos de tela para que a implementação desta integração, no ambito do Sistema Ganso.

# Roadmap

1. Implementar Parâmetros para comportamento da Rotina.
2. Criar uma Tela específica para comportar filtros e funções de envio de produtos.
3. Implementar recursos para gravação da lista de produtos enviados.
4. Implementar recursos para gravação da logs de envio.

# Requisitos

## Parâmetros

| Parâmetro                                   | Descritivo                                                                                                                                            | Regra de Negócio                                                                                                                                                                                                                                                                                                                                            |
| :------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Atualizar Estoques e Preços automaticamente | Opção para definir se ao movimentar Estoque dos Produtos ou atualizar Preço de Venda, uma atualização automática deve ser gerada para envio ao iFood. | Se ocorrer alterações manuais no Estoque ou Baixas automatizadas, disparar comando para envio da quantidade atualizada do Produto para a base do iFood. <br><br> Se ocorrer alterações de Preço de Venda manuais ou automatizadas, disparar comando para envio dos preços de venda atualizados para a base do iFood, observando a margem de lucro definida. |
| Política de Preço                           | Opção para definir a Política de Preço de Venda no iFood.                                                                                             | Deve existir três opções: <br> 1 - Preço Fixo (Preço de Venda Normal) <br> 2 - Preço Fixo + Promoção (Preço de Venda Normal ou da Promoção, quando ativa) <br> 3 - Preço Especial (Percentual sobre o Preço Normal ou de Promoção, quando ativa) <br> Quando opção 3, permitir que o usuário informe o Percentual.                                          |
| Estoque Padrão de Envio iFood               | Campo para definir o Código do Estoque a considerar para envio das Quantidades ao iFood.                                                              | Deve aceitar apenas códigos de estoque cadastrados em Arquivos > Almoxarifados, que correspondam a Filial configurada.                                                                                                                                                                                                                                      |
| Tipo de Estoque                             | Campo para definir qual tipo de Estoque a considerar para envio das Quantidades ao iFood.                                                             | Deve aceitar Valores entre "Físico" ou "Presumido". Se Físico, considerar a Quantidade Fisica total do Produto. Se Presumido, considerar Físico - Reservado - A Retirar (se ativado parâmetro)                                                                                                                                                              |

## Nova Tela - Produtos iFood

De modo a gerenciar os Produtos que devem ser enviados ao iFood, é necessário a criação de um Tela de Gerenciamento. Dentre os recursos, deve conter Filtros para consultar Produtos Cadastrados no Sistema, Exibir tais Produtos filtrados e obedecer Regras de Negócio específicadas.

### Filtros

| Grupo              | Descritivo                                       | Campos do Grupo                                                                                                                                                                                                                                                                                           |
| :----------------- | :----------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Segmentação        | Grupo de Filtros para Segmentação do Produto     | Marca, Seção, Grupo, Subgrupo, Ambiente de Utilização, Fornecedor Padrão, Agrupador de Preços                                                                                                                                                                                                             |
| Produto            | Grupo de Filtros para Características do Produto | Descrição, Referência Fabricante, Referência Auxiliar e Localização com critérios (Contém, Começa Com, Termina Com, Igual a), Status do Produto, Empresas, Período de Alteração de Preços (Log Preços), Apenas Produtos com Estoque Positivo, Produtos não Enviados ao iFood e Produtos Desativados iFood |
| Ações para Filtros | Botões de Ação para ambos grupos de filtros      | Botão Limpar Filtros e Botão Pesquisar.                                                                                                                                                                                                                                                                   |

### Grade de Dados

A Grade de Dados deve exibir os Produtos resultantes dos filtros aplicados. Os elementos necessários são:

| Campo/Função                             | Descritivo                                                                                                      | Regra de Negócio                                                                                        |
| :--------------------------------------- | :-------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| Caixa de Seleção                         | Caixa de Seleção do Item para indicar que o mesmo deve ser enviado                                              | Selecionado, Não Selecionado, Selecionar Todos                                                          |
| Código Interno                           | Código Interno do Produto                                                                                       | -                                                                                                       |
| Código de Barras                         | Código de Barras Padrão do Produto                                                                              | -                                                                                                       |
| Descrição                                | Descrição Completa do Produto                                                                                   | -                                                                                                       |
| Preço Normal                             | Preço de Venda Normal do Cadastro                                                                               | -                                                                                                       |
| Preço Promoção                           | Preço de Venda na Promoção quando ativa                                                                         | Considerar apenas Promoções da Modalidade Unitário                                                      |
| Preço Preço iFood                        | Preço de Venda para o iFood                                                                                     | Considerar parametrização da Gestão de Preço                                                            |
| Marca, Seção, Grupo, Subgrupo            | Segmentação do Produto                                                                                          | -                                                                                                       |
| Estoque Atual                            | Estoque Atual do Produto a enviar                                                                               | Considerar parametrização do Tipo de Estoque (Físico ou Presumido)                                      |
| Status iFood                             | Situação do Produto na Plataforma iFood (Disponível/Indisponível)                                               | Regra de Negócio da Plataforma. Se Estoque zerado, o Produto é considerado indisponível automaticamente |
| [F4] - Selecionar Todos/Inverter Seleção | Função para Selecionar todos os itens da Grade de Dados                                                         | -                                                                                                       |
| [F5] - Limpar Seleção                    | Função para limpar a seleção da Grade de Dados                                                                  | -                                                                                                       |
| Ação Desativar Selecionados              | Botão de Ação para Desativar/Remover Produtos selecionados da Base do iFood, quando Status igual a "Disponível" | Somente Produtos uma vez enviados para iFood que esteja "Ativos".                                       |
| Ação Enviar iFood                        | Botão de Ação para Enviar Produtos selecionados para o iFood                                                    | -                                                                                                       |

### Regras de Negócio

| Regra | Descrição | Tratativa |
| :---- | :-------- | :-------- |
| RN01  |

## Mensagens ao Usuário

| Ação                                     | Mensagem                                                                              | Tratativa                             |
| :--------------------------------------- | :------------------------------------------------------------------------------------ | :------------------------------------ |
| Clicar no Botão "Desativar Selecionados" | "Os Produtos Selecionados serão Desativados na Plataforma iFood. Deseja continuar ?"  | Enviar Desativação para a Plataforma. |
| Clicar no Botão "Enviar iFood"           | "Os Produtos Selecionados serão Enviados para a Plataforma iFood. Deseja continuar ?" | Enviar Produtos selecionados na Lista |

# Simulações