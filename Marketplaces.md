# Introdução :wave:

O presente documento objetiva descrever os requisitos básicos para implementação de Recursos para Gerenciamento de Produtos a enviar para Marketplaces. Em suma, o recurso atenderá a maioria das integrações e os requisitos são genéricos de preparação do Sistema Ganso.

![Integração Tray](./Integration02.png)

## Cadastro de Produtos :label:

Nesta Seção são descritos os recursos necessários a implementar no Cadastro de Produtos para controle.

| Elemento  | Descrição                 | Regra de Negócio                                                                                                                                                                                                                                                                                                                                                  |
| :-------- | :------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Parâmetro | Não Vender em Marketplace | Parâmetro do Produto que restinge o Envio do mesmo ao Marketplace. Ativando este parâmetro, o Produto não deve ser listado na Tela de Envio/Gerenciamento por padrão, e não deve ser enviado por outros meios. Se por decisão do usuário o produto precisar ser enviado, é necessário desativar este parâmetro ou gerar o envio através da Tela de Gerenciamento. |
| Campo     | Enviado para Marketplace  | Campo para identificar se o Produto pertence à Lista de Produtos do Marketplace. Esta informação deve ser exibida em local de fácil visualização, e será utilizada como filtro na Tela de Envio/Gerenciamento de Produtos.                                                                                                                                        |
| Campo     | Status Marketplace        | Campo para identificar se o Produto está Ativo ou Inativo no Marketplace. Esta informação deve ser exibida em local de fácil visualização, e será utilizada como filtro na Tela de Envio/Gerenciamento de Produtos.                                                                                                                                               |

**:bulb: Nota:** Como Regra de Negócio primária, os campos acima só devem ser exibidos e gerenciados se houver **Integração** ativada com algum **Marketplace**.

[Voltar ao Início](#introdução-wave)

## Nova Tela - Gerenciamentos de Produtos do Marketplace :package:

Para que o Usuário obtenha melhor experiência de controle dos Produtos que deseja vender em Marketplaces, é necessário a criação de uma Tela de Gerenciamento.
Esta Tela de Gerenciamento, assemelha-se à Tela de Agrupamento de Promoções Individuais, cuja principal função é compor uma **Lista de Produtos**, com possibilidade de utilizar Filtros.

### Recursos da Tela Principal :desktop_computer:

A Tela Principal deve exibir a Lista de Produtos já enviados ao Marketplace para permitir que o Usuário faça a Gestão através de determinadas ações. Deve conter os elementos descritos a seguir.

1. Incluir uma _Grid_ nomeada de **Produtos do Marketplace** que deve exibir os Produtos já contidos na Lista com os seguintes dados:

| Campo                         | Descritivo                                                    | Regra de Negócio                                                                                                               |
| :---------------------------- | :------------------------------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------- |
| Caixa de Seleção              | Caixa de Seleção do Item                                      | Selecionado, Não Selecionado                                                                                                   |
| Código Interno                | Código Interno do Produto                                     | -                                                                                                                              |
| Código de Barras              | Código de Barras Padrão do Produto                            | -                                                                                                                              |
| Descrição                     | Descrição Completa do Produto                                 | -                                                                                                                              |
| Preço Normal                  | Preço de Venda Normal                                         | Preço de Venda Normal do Produto.                                                                                              |
| Preço Promoção                | Preço de Venda de Promoção                                    | Preço de Promoção enviado, se houve promoção durante o envio.                                                                  |
| Margem de Lucro %             | Margem de Lucro do Produto                                    | Margem de Lucro enviada. Permitir digitação na própria colunas. Solicitar [Chave de Acesso Restrito AR04](#acessos-restritos). |
| Preço Marketplace             | Preço de Venda para o Marketplace                             | Preço de Venda enviado conforme o calculado pela Margem e que está atualmente na Plataforma.                                   |
| Estoque Atual                 | Estoque Atual do Produto                                      | Quantidade em Estoque enviada.                                                                                                 |
| Status Marketplace            | Situação do Produto na Plataforma Marketplace (Ativo/Inativo) | Status Atual do Produto na Plataforma Marketplace.                                                                             |
| Marca, Seção, Grupo, Subgrupo | Segmentação do Produto                                        | Dados Recomendados, mas não são obrigatórios.                                                                                  |

2. Acima da _Grid_ anterior, incluir um campo para **Inserção Rápida** de Produto com as funcionalidades de:

   - Atalho de Teclado para ativar a Função, por exemplo: **[F1] - Inserção Rápida**.
   - Digitação e adição do Produto ao teclar `<Enter>`.
   - Opção de troca de filtro entre "Código" e "Código de Barras" através da tecla `<Espaço>`.
   - Permitir utilização da Consulta `<F2>` padrão do Sistema.
   > O Produto digitado deve ser inserido na lista com as Configurações de Estoque e Margem de Lucro padrões dos Parâmetros.

3. Abaixo da _Grid_, incluir legenda para as ações que podem ser executadas nos itens:

| Elemento                       | Descritivo da Ação                                      | Regra de Negócio                                                            |
| :----------------------------- | :------------------------------------------------------ | :-------------------------------------------------------------------------- |
| Atalho [F3] - Pesquisar        | Acionar a Pesquisa na coluna selecionada                | Exibir um diálogo com um campo para usuário digitar o critério de pesquisa. |
| Atalho [F4] - Selecionar Todos | Inverter a Seleção ou Selecionar todos os itens da Grid | -                                                                           |
| Atalho [F5] - Limpar Seleção   | Limpar a Seleção da Grid                                | -                                                                           |
| Atalho [F6] - Excluir          | Excluir os Itens Selecionados na Grid                   | Solicitar Confirmação e [Chave de Acesso Restrito AR01](#acessos-restritos) |
| Atalho [F7] - Ativar/Desativar | Ativar ou Desativar Itens Selecionados na Grid          | Solicitar Confirmação e [Chave de Acesso Restrito AR02](#acessos-restritos) |
| Caixa de Seleção               | Aplicar alteração as demais selecionados \*             | Solicitar Confirmação e [Chave de Acesso Restrito AR06](#acessos-restritos) |
| Texto                          | Exibição do Número de Itens selecionados do Total       | Exibir no formato x de y registros. Ex.: Selecionados: 10 de 18 registros.  |

4. No rodapé da Tela, incluir um grupo de Funções Rápidas para **Reprocessar Estoque e Preços** dos Itens da Grid, e ações padrão que são:

| Elemento            | Descritivo              | Regra de Negócio                                                                                                                                                                                                                                                                         |
| :------------------ | :---------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Campo               | Estoque Padrão          | Campo para permitir alterar o Estoque aplicado aos itens                                                                                                                                                                                                                                 |
| Campo               | Margem de Lucro         | Campo para permitir alterar a Margem aplicada aos itens                                                                                                                                                                                                                                  |
| Caixa de Combinação | Precisão de Preço       | Opções para configurar a Precisão do Preço de Venda Marketplace, como por exemplo, arredondamento de casas decimais. Disponibilizar opções como: <br>Final 9 - Ex.: 4,77 > 4,79 <br>Final 0 - Ex.: 4,77 > 4,70 <br>Próx. Inteiro - Ex.: 4,77 > 5,00 <br>Valor Inteiro - Ex.: 4,77 > 4,00 |
| Botão de Ação       | Aplicar                 | Botão para Aplicar as configurações de Estoque, Margem de Lucro e Precisão de Preço aos itens **selecionados** na Grid.                                                                                                                                                                  |
| Botão de Ação       | Editar/Gravar           | Botão para Editar e Gravar alterações na Lista de Produtos.                                                                                                                                                                                                                              |
| Botão de Ação       | [F10] - Lançar Produtos | Botão para Acionar a Tela de Consulta e Lançamento de Produtos.                                                                                                                                                                                                                                     |

Ao final da implementação dos recursos, o resultado será semelhante a imagem abaixo:

![Protótipo de Tela](./Main-Screen02.png)
