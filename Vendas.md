# Introdução

O presente documento é complementar à documentação de [Requisitos Iniciais](./Marketplaces.md), e descreve a **segunda etapa** do processo de Integração com a Plataforma Tray onde descreve os requisitos para importação das Vendas.

# Roadmap

1. Implementar [parâmetros](#parâmetros) de controle relacionados à Venda.
2. Implementar alterações na [Tela de Venda Retaguarda](#venda-retaguarda) para comportar campos de controle e identificação de Vendas realizadas na Plataforma do Marketplace.
3. Implementar alterações em [Documentos Fiscais (DF-e)](#documentos-fiscais-df-e) para adequar a emissão da NF-e e inclusão na Plataforma.
4. Implementar alterações em [Relatórios Gerenciais](#relatórios) para incluir novos filtros para Marketplaces.

# Siglas Utilizadas

| Sigla | Descritivo                                |
| :---- | :---------------------------------------- |
| RNC   | Regra de Negócio para Clientes            |
| RNV   | Regra de Negócio para Vendas              |
| RNP   | Regra de Negócio para Formas de Pagamento |

# Parâmetros

Para controle dos processos de Venda, foram levantados novos parâmetros conforme descritivo abaixo.

| Elemento            | Nome                            | Posição                       | Descritivo                                                                                                                     | Regras de Negócio                                                                                                                                                                    |
| :------------------ | :------------------------------ | :---------------------------- | :----------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Caixa de Combinação | Tipo de Venda Retaguarda        | Integrações/Marketplaces/Tray | Campo para definir o Tipo de Venda Retaguarda a ser criada durante o processo de captura das Vendas realizadas no Marketplace. | Permitir selecionar apenas uma das opções: <br>- Orçamento Aberto<br>- Orçamento Fechado<br>- Condicional Aberto<br>- Condicional Fechado<br>- Pedido Aberto<br>- Pedido Fechado<br> |
| Campo               | CNPJ Intermediador da Transação | Integrações/Marketplaces/Tray | Campo para definir o CNPJ do Intermediador/Marketplace responsável por processar a Venda.                                      | **Obrigatório** para emissão da NF-e conforme [NT2020.006](https://microsig.my.site.com/a0e4w00000KEmlf?btdid=a0g4w00000JDgsH)                                                       |
| Campo               | Finalizador Padrão              | Integrações/Marketplaces/Tray | Campo para definir o Código do Finalizador Padrão para as Vendas realizadas no Marketplace                                     | **Não Obrigatório**, mas importante para a Conclusão da Venda no Sistema Ganso.                                                                                                      |
| Campo               | Caixa Padrão                    | Integrações/Marketplaces/Tray | Campo para definir o Código do Caixa Padrão para as Vendas realizadas no Marketplace                                           | **Não Obrigatório**, mas importante para a Conclusão da Venda no Sistema Ganso.                                                                                                      |
| Campo               | Vendedor Padrão                 | Integrações/Marketplaces/Tray | Campo para definir o Código do Colaborador/Vendedor Padrão para as Vendas realizadas no Marketplace                            | **Não Obrigatório**, mas importante para a Conclusão da Venda no Sistema Ganso.                                                                                                      |

Os novos parâmetros descritos acima estão ilustrados no exemplo a seguir:

![Novos Parâmetros](./Parameters02.png)

# Venda Retaguarda

Todas as Vendas com **Pagamento concluído** geradas no Marketplace devem ser capturadas e inseridas no Sistema Ganso obedecendo os [parâmetros](#parâmetros) de vendas definidos por padrão. Para que o Pedido criado no Marketplace seja identificado e controlado corretamente no Sistema Ganso, é necessário criar novos elementos, que são:

| Elemento | Nome                                        | Descritivo                                                                                          |        Campo do Retorno         | Regra de Negócio                                                                                                                       |
| :------- | :------------------------------------------ | :-------------------------------------------------------------------------------------------------- | :-----------------------------: | :------------------------------------------------------------------------------------------------------------------------------------- |
| Flag     | Criado via Marketplace (nome do Integrador) | Informação Identificadora                                                                           |                -                | Visível ao Usuário. Utilizar o mesmo campo que sinaliza Venda "Mobile". Não permitir alterações.                                       |
| Campo    | **Código do Pedido no Marketplace**         | Código Identificador do Pedido no Marketplace                                                       |           `Order.id`            | Visível ao Usuário. Não permitir alterações.                                                                                           |
| Campo    | **Código do Cliente no Marketplace**        | Código Identificador do Cliente no Marketplace                                                      |       `Order.customer_id`       | Não permitir alterações.                                                                                                               |
| Campo    | Data/Hora do Pedido no Marketplace          | Data e Hora de Criação do Pedido no Marketplace                                                     |   `Order.date` e `Order.hour`   | Não permitir alterações. Converter o formato da Data e Hora.                                                                           |
| Campo    | Data/Hora do Pagamento no Marketplace       | Data e Hora do Pagamento do Pedido no Marketplace                                                   |    `Payment.Payment.created`    | Não permitir alterações. Converter o formato da Data e Hora.                                                                           |
| Campo    | Data Estimada para Entrega                  | Data estimada para entrega, quando ocorrer                                                          | `Order.estimated_delivery_date` | Visível ao Usuário. Não permitir alterações. Converter o formato da Data.                                                              |
| Campo    | Código de Rastreio/Envio                    | Código de Rastreio ou Envio gerado pelo Marketplace, quando disponível dentro da própria Plataforma |      `Order.sending_code`       | Não permitir alterações.                                                                                                               |
| Campo    | Nome de Usuário                             | Nome do Usuário ou Identificação do Perfil do Vendedor no Marketplace                               |          `User.login`           | Informação obrigatória para NF-e (Campo **idCadIntTran**). Não permitir alterações. [Ver Requisito R03 da NT2020.006](./NT2020-006.md) |

## Relacionamento de Dados - Venda Retaguarda

Para as demais informações do Pedido, há campos no Sistema Ganso que devem ser utilizados (tanto para o Pedido quanto para os Itens do Pedido) conforme os relacionamentos abaixo.

| Ganso               | Tray                   | Tipo Retorno | Descritivo                                    | Regra de Negócio                            |
| :------------------ | :--------------------- | :----------: | :-------------------------------------------- | :------------------------------------------ |
| `desconto_valor`    | `Order.discount`       |  `decimal`   | Valor do Desconto do Pedido                   | Não permitir alterações.                    |
| `valor_frete`       | `Order.shipment_value` |  `decimal`   | Valor do Frete quando contratado pelo Cliente | Não permitir alterações.                    |
| `obs_nf`            | `Order.customer_note`  |   `string`   | Observação informada pelo Cliente             | Não permitir alterações.                    |
| `orcamento_bruto`   | `Order.partial_total`  |  `decimal`   | Valor Bruto do Pedido                         | Não permitir alterações.                    |
| `orcamento_liquido` | `Order.total`          |  `decimal`   | Valor Líquido do Pedido                       | Não permitir alterações.                    |
| `identificacao`     | -                      |   `string`   | Identificação de Venda no Marketplace         | Texto Fixo 'MKPL'. Não permitir alterações. |

## Relacionamento de Dados - Itens da Venda Retaguarda

| Ganso                                                       | Tray                                                                     | Tipo Retorno | Descritivo                                                                                              | Regra de Negócio                                       |
| :---------------------------------------------------------- | :----------------------------------------------------------------------- | :----------: | :------------------------------------------------------------------------------------------------------ | :----------------------------------------------------- |
| `codigo_produto` relativo a `mkpl_produtos.id_produto_mkpl` | `ProductsSold.ProductsSold.product_id`                                   |   `number`   | Código do Produto vinculado ao ID do Produto do Marketplace da tabela de relacionamento `mkpl_produtos` | -                                                      |
| `quantidade`                                                | `ProductsSold.ProductsSold.quantity`                                     |  `decimal`   | Quantidade Vendida no Marketplace                                                                       | -                                                      |
| `preco_unitario`                                            | `ProductsSold.ProductsSold.price`                                        |  `decimal`   | Preço Unitário do Produto no Marketplace                                                                | -                                                      |
| `valor_total`                                               | `ProductsSold.ProductsSold.price` x `ProductsSold.ProductsSold.quantity` |  `decimal`   | Valor Total do Produto no Marketplace                                                                   | -                                                      |
| `situacao_entrega`                                          | -                                                                        |      -       | Situação de Entrega no Sistema Ganso                                                                    | Definir como 'EL'                                      |
| `custo_reposicao`                                           | `ProductsSold.ProductsSold.cost_price`                                   |  `decimal`   | Preço de Custo do Produto no Marketplace                                                                | -                                                      |
| `pmz`                                                       | `ProductsSold.ProductsSold.cost_price`                                   |  `decimal`   | Preço de Custo PMZ do Produto no Marketplace                                                            | Utilizar a mesma informação gravada no Custo Reposição |
| `codigo_estoque`                                            | -                                                                        |   `number`   | Código do Estoque informado no Produto do Marketplace durante envio                                     | -                                                      |

## Regras de Negócio - Venda Retaguarda

| #     | Descritivo                                                   | Tratativas                                                                                                         |
| :---- | :----------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------- |
| RNV01 | Gravar Código do Vendedor Padrão em Vendas do Marketplace    | Utilizar o Código de Vendedor Padrão quando informado nos [parâmetros](#parâmetros) de vendas do Marketplace.      |
| RNV02 | Gravar Código do Finalizador Padrão em Vendas do Marketplace | Utilizar o Código do Finalizador Padrão quando informado nos [parâmetros](#parâmetros) de vendas do Marketplace.   |
| RNV03 | Gravar Código do Cliente Consumidor Final                    | Utilizar o Código do Consumidor Final se o **Planejamento** decidir não incluir a Gravação do Cadastro do Cliente. |

# Cadastro de Clientes

A API Tray retorna os dados principais do Cliente no detalhamento do Pedido Completo, contudo, é necessário **avaliar** a necessidade em capturar os Clientes cadastrados no Marketplace e seus endereços envolvidos. Inicialmente, havendo a captura dos Clientes, é necessário criar novos elementos, que são:

| Elemento | Nome                                            | Descritivo                                         | Regra de Negócio                                                                                                         |
| :------- | :---------------------------------------------- | :------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| Flag     | Cadastrado via Marketplace (nome do Integrador) | Informação identificadora da Origem do Cadastro    | Não permitir alterações.                                                                                                 |
| Campo    | **Código do Cliente no Marketplace**            | **Código Identificador do Cliente no Marketplace** | Informação mais importante requerida para captura dos Endereços do Cliente na API de Endereços. Não permitir alterações. |

## Relacionamento de Dados - Clientes

| Ganso             | Tray                         | Tipo Retorno | Descritivo                  | Regra de Negócio                                                                                                                    |
| :---------------- | :--------------------------- | :----------: | :-------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `nome`            | `Customer.name`              |   `string`   | Nome                        | Observar limite de tamanho no Sistema Ganso: 50 caracteres. Se maior, truncar a informação.                                         |
| `rg`              | `Customer.rg`                |   `string`   | RG                          | Observar limite de tamanho no Sistema Ganso: 15 caracteres. Se maior, truncar a informação. Eliminar a máscara do campo de retorno. |
| `cpf`             | `Customer.cpf`               |   `string`   | CPF                         | -                                                                                                                                   |
| `telefone`        | `Customer.phone`             |   `string`   | Telefone Fixo               | Observar limite de tamanho no Sistema Ganso: 13 caracteres. Se maior, truncar a informação.                                         |
| `celular`         | `Customer.cellphone`         |   `string`   | Telefone Celular            | Observar limite de tamanho no Sistema Ganso: 13 caracteres. Se maior, truncar a informação.                                         |
| `nascimento`      | `Customer.birth_date`        |    `date`    | Data de Aniversário         | Converter o Formato para 'DDMMAAAA'. A API retorna no Formato 'AAAADDMM'.                                                           |
| `sexo`            | `Customer.gender`            |   `number`   | Gênero/Sexo                 | Se 0 = 'Masculino'. Se 1 = 'Feminino'.                                                                                              |
| `email`           | `Customer.email`             |   `string`   | E-mail                      | Observar limite de tamanho no Sistema Ganso: 350 caracteres. Se maior, truncar a informação.                                        |
| `apelido`         | `Customer.nickname`          |   `string`   | Apelido                     | Observar limite de tamanho no Sistema Ganso: 60 caracteres. Se maior, truncar a informação.                                         |
| `observacao`      | `Customer.observation`       |   `string`   | Observações sobre o Cliente | Observar limite de tamanho no Sistema Ganso: 100 caracteres. Se maior, truncar a informação.                                        |
| `pessoa`          | `Customer.type`              |   `number`   | Tipo de Cliente             | Se 0 = 'Física'. Se 1 = 'Jurídica'                                                                                                  |
| `cnpj`            | `Customer.cnpj`              |   `string`   | CNPJ                        | Remover a máscara do retorno.                                                                                                       |
| `razao_social`    | `Customer.company_name`      |   `string`   | Razão Social                | Observar limite de tamanho no Sistema Ganso: 60 caracteres. Se maior, truncar a informação.                                         |
| `insc_estadual`   | `Customer.state_inscription` |   `string`   | Inscrição Estadual          | Observar limite de tamanho no Sistema Ganso: 20 caracteres. Se maior, truncar a informação.                                         |
| `endereco`        | `Customer.address`           |   `string`   | Endereço Principal          | Observar limite de tamanho no Sistema Ganso: 45 caracteres.                                                                         |
| `cep`             | `Customer.zip_code`          |   `string`   | CEP Principal               | Remover a máscara do retorno.                                                                                                       |
| `numero`          | `Customer.number`            |   `number`   | Número Principal            | Observar limite de tamanho no Sistema Ganso: 6 caracteres.                                                                          |
| `complemento`     | `Customer.complement`        |   `string`   | Complemento Principal       | Observar limite de tamanho no Sistema Ganso: 60 caracteres.                                                                         |
| `bairro`          | `Customer.neighborhood`      |   `string`   | Bairro Principal            | Observar limite de tamanho no Sistema Ganso: 25 caracteres.                                                                         |
| `cod_cidade`      | `Customer.city`              |   `string`   | Cidade Princial             | Localizar um Código de cidade, na tabela `CIDADE_VILA`, que corresponda a informação do retorno `city` e `state`.                   |
| -                 | `Customer.state`             |   `string`   | Sigla do Estado             | Informação Complementar da Cidade. Auxiliará identificação do Código da Cidade                                                      |
| `ent_endereco`    | `Customer.address`           |   `string`   | Endereço Entrega            | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |
| `ent_cep`         | `Customer.zip_code`          |   `string`   | CEP Entrega                 | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |
| `ent_numero`      | `Customer.number`            |   `string`   | Número Entrega              | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |
| `ent_complemento` | `Customer.complement`        |   `string`   | Complemento Entrega         | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |
| `ent_bairro`      | `Customer.neighborhood`      |   `string`   | Bairro Entrega              | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |
| `ent_cod_cidade`  | `Customer.city`              |   `string`   | Cidade Entrega              | Utilizar o endpoint `customer/adresses` de consulta de endereços e capturar os dados cujo campo `type=1` e campo `active=1`         |

## Regras de Negócio - Clientes

| #     | Descritivo                                                                                                            | Tratativas                                                                                                                                                                                                                                                                                     |
| :---- | :-------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RNC01 | Regra Geral para Clientes do Marketplace                                                                              | Não permitir alterações de dados de Cliente originados do Marketplace, **exceto** Endereço de Entrega.                                                                                                                                                                                         |
| RNC02 | Verificar se Cliente do Marketplace já possui cadastro no Sistema Ganso                                               | Verificar através do CPF ou CNPJ se Cliente do Marketplace já existe no Sistema Ganso. Se existir, verificar informações e atualizar endereço e demais dados importantes para conclusão da Venda e NF-e. \*                                                                                    |
| RNC03 | Definição de valores padrão de Cadastro para os campos **Situação**, **Tipo** e **Contribuinte ICMS**                 | Situação = Ativo<br>Tipo = Informal<br><br>Contribuinte ICMS:<br> Se `type="0"` então 'N'.<br>Se `type="1"` e `state_inscription <> "ISENTO"` então 'S'. <br> Se `type="1"` e `state_inscription="ISENTO"` então 'Isento'.                                                                     |
| RNC04 | Buscar todos os Endereços Ativos do Cliente e Relacioná-los em tabela específica (se rotina de captura implementada). | Buscar todos os Endereços do Cliente Ativos na API de Clientes, em `customer/addresses` e relacioná-los com o `id` do Cliente conforme regra:<br> Se `type=0` então **Endereço de Cobrança**. Se `type=1` então **Endereço de Entrega**. Se `type=2` então **Endereço Tipo Lista de Presente** |

> :bulb: Nota: Um Cliente cadastrado via Marketplace pode possuir mais que 3 endereços, definidos como Entrega ou Cobrança, diferenciando-os por uma descrição que o mesmo informa. (Discutir criação de tabela de relacionamento durante o planejamento)

# Formas de Pagamento

# Documentos Fiscais (DF-e)

## Requisito Obrigatório

Implementar Requisitos da [NT2020.006](./NT2020-006.md) para disponibilizar os campos necessários para informar o **Intermediador/Marketplace**.

# Relatórios

Além das alterações nas rotinas de Vendas, é necessário incluir novos filtros nos **Relatórios** de Gestão do Sistema para melhorar a experiência do Usuário. A tabela a seguir descreve os relatórios e recursos necessários.

| Relatório                                                                                                                                                                   | Novos Filtros                                                                                                                    | Regras de Negócio                                                                              |
| :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------- |
| **Vendas Geral**<br>**Vendas Geral por Produto**<br>**Vendas Geral por Marca**<br>**Vendas Geral por Seção**<br>**Vendas Geral por Grupo**<br>**Vendas Geral por Vendedor** | Opções: <br>[ ] Apenas Vendas de Marketplaces<br>[ ] Agrupar Vendas por Marketplace<br>[ ] Não exibir Vendas de Marketplaces<br> | Estas opções só deverão ser exibidas se houver pelo menos 1 (um) Marketplace Ativo no Sistema. |
| **Documentos Fiscais**                                                                                                                                                      | Opções: <br>[ ] Apenas DF-e de Marketplaces<br>[ ] Agrupar DF-e por Marketplace<br>[ ] Não exibir DF-e de Marketplaces<br>       | Estas opções só deverão ser exibidas se houver pelo menos 1 (um) Marketplace Ativo no Sistema. |

# Intercorrências

| #   | Descritivo                                                                                                                  | Tratativa                                                                              |
| :-- | :-------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------- |
| I01 | Usuário Ganso remover um Produto do Marketplace que possui Pedido com Pagamento efetuado não Sincronizado no Sistema Ganso. | Efetuar uma verificação na API de Pedidos antes de permitir a Exclusão do Produto.     |
| I02 | Cliente do Marketplace **cancelar** um Pedido com Pagamento efetuado, Sincronizado, com NF-e Emitida e não Enviado.         | Usuário deve proceder com a Rotina de Devolução de Vendas e emitir a NF-e de Devolução |

# Referências

- [Tray Developers](https://developers.tray.com.br/)
- [Nota Técnica - NF-e](https://developermercado.ifood.com.br/page/nt-2020006)
- [Tiny ERP](https://ajuda.tiny.com.br/hc/pt-br/articles/8289956453268-Integra%C3%A7%C3%A3o-Tiny-com-o-iFood-Mercado)
- [Consumer ERP](https://ajuda.programaconsumer.com.br/como-integrar-o-programa-consumer-ao-ifood/)
