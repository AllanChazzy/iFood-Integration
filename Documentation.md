# Introdução :wave:

O presente documento objetiva descrever os requisitos básicos para implementação de um Recurso de Gerenciamento de Produtos a enviar para a API iFood. São descritos os métodos, regras de negócio e exemplos de tela para que a implementação desta integração, no ambito do Sistema Ganso.

# Roadmap :rocket:

1. Implementar Parâmetros para comportamento da Rotina.
2. Criar uma Tela específica para comportar filtros e funções de envio de produtos.
3. Implementar recursos para gravação da lista de produtos enviados.
4. Implementar recursos para gravação da logs de envio.

# Requisitos

## Parâmetros :gear:

Na Tela Principal de Parâmetros do Sistema Ganso, criar uma aba **Integrações** e uma sub-aba **iFood** para organizar os Parâmetros descritos na tabela a seguir.

| Parâmetro                     | Descritivo                                                                                                                                            | Regra de Negócio                                                                                                                                                                                                                                                                                                                                            |
| :---------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ativar Integração iFood       | Opção para Ativar Integração iFood                                                                                                                    | -                                                                                                                                                                                                                                                                                                                                                           |
| Atualizações Automáticas      | Opção para definir se ao movimentar Estoque dos Produtos ou atualizar Preço de Venda, uma atualização automática deve ser gerada para envio ao iFood. | Se ocorrer alterações manuais no Estoque ou Baixas automatizadas, disparar comando para envio da quantidade atualizada do Produto para a base do iFood. <br><br> Se ocorrer alterações de Preço de Venda manuais ou automatizadas, disparar comando para envio dos preços de venda atualizados para a base do iFood, observando a margem de lucro definida. |
| Política de Preço             | Opção para definir a Política de Preço de Venda no iFood.                                                                                             | Deve existir três opções: <br> 1 - Preço Fixo (Preço de Venda Normal) <br> 2 - Preço Fixo + Promoção (Preço de Venda Normal ou da Promoção, quando ativa) <br> 3 - Preço Especial (Percentual sobre o Preço Normal ou de Promoção, quando ativa) <br> Quando opção 3, permitir que o usuário informe o Percentual.                                          |
| Estoque Padrão de Envio iFood | Campo para definir o Código do Estoque Padrão a considerar para envio das Quantidades ao iFood.                                                       | Deve aceitar apenas códigos de estoque cadastrados em Arquivos > Almoxarifados, que correspondam a Filial configurada.                                                                                                                                                                                                                                      |
| Tipo de Estoque               | Campo para definir qual tipo de Estoque a considerar para envio das Quantidades ao iFood.                                                             | Deve aceitar Valores entre "Físico" ou "Presumido". Se Físico, considerar a Quantidade Fisica total do Produto. Se Presumido, considerar Físico - Reservado - A Retirar (se ativado parâmetro). Sempre enviar a Quantidade disponível, mesmo que zero ou negativa.                                                                                          |

_**Nota: :thinking: Conforme documentação do iFood, o envio de atualizações deve obedecer o Rate Limit de 60 minutos**_

## Cadastro de Produtos :label:

No Cadastro de Produtos são necessários criar recursos para controlar os Produtos que podem ser enviados, se já foram enviados e qual a situação do mesmo na Plataforma iFood.

| Tipo      | Descrição           | Regra de Negócio                                                                                                                                                                             |
| :-------- | :------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Parâmetro | Não Vender no iFood | Parâmetro do Produto que restinge o Envio do mesmo ao Integrador iFood. Ativando este parâmetro, o Produto não será listado na Tela de Envio.                                                |
| Campo     | Enviado para iFood  | Campo para identificar se o Produto foi enviado para iFood. Esta informação deve ser exibida em local de fácil visualização. Também será utilizada como filtro na Tela de Envio de Produtos. |
| Campo     | Status iFood        | Campo para identificar se o Produto está Ativo ou Inativo na Plataforma iFood. Também será utilizada como filtro na Tela de Envio de Produtos.                                               |

## Nova Tela - Produtos iFood :package:

Para que o Usuário obtenha facilidade de controle de Produtos que deseja vender no iFood, é necessário a criação de uma Tela de Gerenciamento. Dentre os principais recursos, deve ser possível listar vários produtos através de filtros, obedecendo às Regras de Negócio especificadas.

### Filtros :mag:

Os filtros necessários foram classificados em dois grupos para melhor experiência do usuário e clareza de informações em tela.

| Grupo       | Descritivo                                       | Campos do Grupo                                                                                                                                                                                                                                                                                           |
| :---------- | :----------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Segmentação | Grupo de Filtros para Segmentação do Produto     | Marca, Seção, Grupo, Subgrupo, Ambiente de Utilização, Fornecedor Padrão, Agrupador de Preços                                                                                                                                                                                                             |
| Produto     | Grupo de Filtros para Características do Produto | Descrição, Referência Fabricante, Referência Auxiliar e Localização com critérios (Contém, Começa Com, Termina Com, Igual a), Status do Produto, Empresas, Período de Alteração de Preços (Log Preços), Apenas Produtos com Estoque Positivo, Produtos não Enviados ao iFood e Produtos Desativados iFood |

Além dos Grupos de Filtros, são necessárias duas ações principais para esta etapa:

1.  Pesquisar - Utilizando os filtros informados, que inclusive deve ser permitido combiná-los.
2.  Limpar Filtros - Função que facilita novas pesquisas.

### Grade de Dados :open_file_folder:

A Grade de Dados deve exibir os Produtos resultantes dos filtros aplicados. Os elementos necessários para exibição e controle de dados são:

| Campo/Função                             | Descritivo                                                             | Regra de Negócio                                                                                        |
| :--------------------------------------- | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| Caixa de Seleção                         | Caixa de Seleção do Item para indicar que o mesmo deve ser enviado     | Selecionado, Não Selecionado, Selecionar Todos                                                          |
| Código Interno                           | Código Interno do Produto                                              | -                                                                                                       |
| Código de Barras                         | Código de Barras Padrão do Produto                                     | -                                                                                                       |
| Descrição                                | Descrição Completa do Produto                                          | -                                                                                                       |
| Preço Normal                             | Preço de Venda Normal do Cadastro                                      | -                                                                                                       |
| Preço Promoção                           | Preço de Venda na Promoção quando ativa                                | Considerar apenas Promoções da Modalidade Unitário                                                      |
| Preço iFood                              | Preço de Venda para o iFood                                            | Considerar parametrização da Gestão de Preço                                                            |
| Marca, Seção, Grupo, Subgrupo            | Segmentação do Produto                                                 | -                                                                                                       |
| Estoque Atual                            | Estoque Atual do Produto a enviar                                      | Considerar parametrização do Tipo de Estoque (Físico ou Presumido)                                      |
| Status iFood                             | Situação do Produto na Plataforma iFood (Ativo/Inativo)                | Regra de Negócio da Plataforma. Se Estoque zerado, o Produto é considerado indisponível automaticamente |
| [F4] - Selecionar Todos/Inverter Seleção | Função para Selecionar todos os itens da Grade de Dados                | -                                                                                                       |
| [F5] - Limpar Seleção                    | Função para limpar a seleção da Grade de Dados                         | -                                                                                                       |
| Contagem de Produtos selecionados        | Texto informativo sobre a quantidade de Produtos selecionados na Grade | -                                                                                                       |

### Ações :pushpin:

| Função                      | Descritivo                                                                                                                           | Regra de Negócio                                                    |
| :-------------------------- | :----------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------ |
| Ação Reativar Selecionados  | Botão de Ação para Reativar Produtos selecionados da Base do iFood, quando Status igual a "Inativo" e o Usuário desejar reativá-los. | Somente Produtos uma vez enviados para iFood que esteja "Inativos". |
| Ação Desativar Selecionados | Botão de Ação para Desativar Produtos selecionados da Base do iFood, quando Status igual a "Ativo"                                   | Somente Produtos uma vez enviados para iFood que esteja "Ativos".   |
| Ação Enviar para iFood      | Botão de Ação para Enviar Produtos selecionados para o iFood                                                                         | -                                                                   |

### Regras de Negócio :lock:

| Regra | Descrição                                                                 | Tratativa                                                                                                    |
| :---- | :------------------------------------------------------------------------ | :----------------------------------------------------------------------------------------------------------- |
| RN01  | Não listar Produtos de "Aplicação de Direta"                              | Verificar o Parâmetro "Aplicação Direta" do Cadastro de Produtos                                             |
| RN02  | Não listar Produtos do Tipo "Kit", "Fracionável" ou "Matéria Prima"       | Verificar o campo Tipo do Produto do Cadastro de Produtos                                                    |
| RN03  | Não Permitir enviar mais que 10.000 Produtos em um único pacote de envio. | Se a seleção do usuário ultrapassar 10.000 Produtos, gerar um novo pacote e informar o Usuário sobre a ação. |

## Mensagens ao Usuário :incoming_envelope;

| Ação                                                    | Mensagem                                                                                                                          | Tratativa                                                               |
| :------------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| Clicar no Botão "Desativar Selecionados"                | "Os Produtos Selecionados serão Desativados na Plataforma iFood. Deseja continuar ?"                                              | Enviar Desativação para a Plataforma através do comando correspondente. |
| Clicar no Botão "Reativar Selecionados"                 | "Os Produtos Selecionados serão Reativados na Plataforma iFood. Deseja continuar ?"                                               | Enviar Reativação para a Plataforma através do comando correspondente.  |
| Clicar no Botão "Enviar para iFood"                     | "Os Produtos Selecionados serão Enviados para a Plataforma iFood. Deseja continuar ?"                                             | Enviar Produtos selecionados na Lista                                   |
| Selecionar e Enviar mais que 10.000 Produtos em um Lote | "A iFood recomenda que sejam enviados apenas 10.000 itens por Pacote. O Sistema irá gerar um novo pacote a cada limite atingido." | Gerar um novo pacote a cada 10.000 Produtos selecionados para envio.    |

# Simulações

| Cenário de Homologação                              | Resultado esperado                                                                                        |
| :-------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| Carga de Produtos do tipo EAN                       | Recebimento dos produtos                                                                                  |
| Atualização de Status do Produto do tipo EAN        | Recebimento de produtos com alteração de status (Ativo / Inativo)                                         |
| Atualização de Preços do Produto do tipo EAN        | Atualização de preço de produtos saindo de A para B                                                       |
| Atualização de Preços De / Por do tipo EAN          | Atualização de preço de produtos saindo de A para B tendo o DE/POR                                        |
| Atualização de Estoque do tipo EAN                  | Atualização do valor de estoque de A para B                                                               |
| Atualização da descrição do produto do tipo EAN     | Atualização do nome do produto saindo de A para B                                                         |
| Carga de Produtos do tipo PRÓPRIO                   | Recebimento dos produtos                                                                                  |
| Atualização de Status do Produto do tipo PRÓPRIO    | Recebimento de produtos com alteração de status (Ativo / Inativo)                                         |
| Atualização de Preços do Produto do tipo PRÓPRIO    | Atualização de preço de produtos saindo de A para B                                                       |
| Atualização de Preços De / Por do tipo PRÓPRIO      | Atualização de preço de produtos saindo de A para B tendo o DE/POR                                        |
| Atualização de Estoque do tipo PRÓPRIO              | Atualização do valor de estoque de A para B                                                               |
| Atualização da descrição do produto do tipo PRÓPRIO | Atualização do nome do produto saindo de A para B                                                         |
| Atualização geral dos itens                         | Recebimento da atualização somente dos itens que houve alteração do lado do ERP ao invés da base completa |
| Tempo de Atualização                                | Validação da atualização da base (Com update) com tempo de 120 minutos                                    |
