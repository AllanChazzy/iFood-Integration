# Introdução :wave:

- O presente documento é complementar às documentações de [Marketplaces](./Marketplaces.md) e [Vendas](./Vendas.md) e descreve a rotina de Relacionamento de Produtos do Marketplace com os Produtos do Sistema Ganso. Aplica-se à casos em que o Cliente já possui o Marketplace Tray em Produção.

# Requisitos Principais

- Efetuar uma Consulta na API de Produtos da Tray e retornar para uma Tabela Temporária, a lista de Produtos cadastrados na Plataforma.
- Criar uma Tela de Relacionamento de Produtos da Tray com o Sistema Ganso.

# Funções da Rotina

| #   | Nome                                 | Descritivo                                                                                                                                                                    | Regras de Negócio                                                                                                                                                 |
| :-- | :----------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| F01 | Atualizar a Lista de Produtos        | Função para Atualizar a Lista de Produtos efetuando uma Consulta na API do Marketplace e retornando todos os novos Produtos não inseridos na Tabela Temporária de Tratamento. | Ao acionar a função, sempre efetuar uma requisição à API de Produtos da Tray e inserir apenas os Produtos que não estão presentes na Lista de Produtos temporária |
| F02 | Reiniciar o Procedimento             | Função para Usuário reiniciar a vinculação dos Produtos como se o processo estivesse começado após acionamento da função.                                                     | Remover todos os vínculos não automáticos                                                                                                                         |
| F03 | Ignorar a vinculação de um Produto   | Função para Usuário selecionar individualmente, os produtos que não deseja vincular com um Produto Ganso.                                                                     | Solicitar [Chave de Acesso Restrito - AR01](#acessos-restritos)                                                                                                   |
| F04 | Utilizar Ref. Fabricante             | Parâmetro para permitir que o Sistema localize produtos através da Referência do Fabricante e Código de Barras padrão na tentativa de vínculação automática.                  | -                                                                                                                                                                 |
| F05 | Permitir Consulta `<F2>`             | Função para Usuário pesquisar um Produto através do Campo de Código do Produto através do Recurso padrão do Sistema.                                                          | -                                                                                                                                                                 |
| F06 | Permitir Alterar Campo para Consulta | Função para Usuário alterar a pesquisa no Campo de Código do Produto.                                                                                                         | Implementar a função da tecla `<Espaço>` para alterar entre **Código** e **Código de Barras Padrão**                                                              |
| F07 | Reprocessar Vínculos                 | Função para Usuário acionar a tentativa de vínculo automático do Sistema Ganso, utilizando as parametrizações definidas.                                                      | Não alterar os Produtos já vinculados, apenas reprocessar para os Produtos **Não Vinculados** e **Não Ignorados**                                                 |

