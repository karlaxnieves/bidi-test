---
title: transfer-test
deprecated: false
hidden: false
metadata:
  robots: index
---
O acesso à API de transferências é realizado através de requisições para os endpoints que ramificam do endpoint base `/v1/companies/`{`companyId`}`/transfers`. As páginas que detalham cada operação indicarão se há a possibilidade de filtragem por meio de `query paramethers` e quais serão esses parametros.

| Endpoint                                                            | Descrição                                               | Verbos      |
| ------------------------------------------------------------------- | ------------------------------------------------------- | ----------- |
| `/v1/companies/`{`companyId`}`/transfers`                           | Inicia e lista transferências                           | POST, GET   |
| `/companies/`{`companyId`}`/transfers/schedule`                     | Obtém a lista de transferências agendadas               | GET         |
| `/v1/companies/`{`companyId`}\`/transfers/approvals/`{approvalId}\` | Cancela uma pendência por um Id.                        | DELETE      |
| `v1/companies/`{`companyId`}`/transfers/approvals`                  | Obtém a lista de transferências pendentes de aprovação. | GET         |
| `v1/companies/`{`companyId`}\`/transfers/`{transferId}\`            | Cancela e lista uma transferência especifica            | DELETE, GET |
| `v1/companies/`{`companyId`}`/transfers/``{transferId}\```/receipt` | Obtém o PDF do comprovante em base64                    | GET         |

Corpo da `transferência`

| Campo           | Tipo   | Descrição                 |
| --------------- | ------ | ------------------------- |
| `amount`        | number | Quantia a ser transferida |
| `scheduledDate` | Date   | Data de agendamento       |
| `reason`        | string | Mensagem do comprovante   |
| `type`          | string | Tipo de transferência     |
| `debitParty`    | object | Parte debitada            |
| `creditParty`   | object | Parte creditada           |
| `tags`          | object | Tag                       |

Objeto `debitParty`

| Campo        | Tipo   | Descrição                     |
| ------------ | ------ | ----------------------------- |
| `branchCode` | string | Código identificador do banco |
| `number`     | string | Número da parte debitada      |

Objeto `creditParty`

| Campo         | Tipo   | Descrição                          |
| ------------- | ------ | ---------------------------------- |
| `accountData` | object | Dados da conta creditada           |
| `pixData`     | object | Informações de pix caso seja usado |

Objeto `accountData`

| Campo         | Tipo   | Descrição                     |
| ------------- | ------ | ----------------------------- |
| `bankCode`    | string | CÓdigo bancario               |
| `branchCode`  | string | Código identificador do banco |
| `number`      | string | Número                        |
| `accountType` | string | Tipo da conta                 |
| `taxId`       | string |                               |
| `name`        | string | Nome da conta                 |

Objeto `pixData`

| Campo     | Tipo   | Descrição         |
| --------- | ------ | ----------------- |
| `keyType` | string | Tipo da chave pix |
| `pixKey`  | string | Chave pix         |

Objeto `tags`

| Campo        | Tipo   | Descrição                  |
| ------------ | ------ | -------------------------- |
| `externalId` | string | Identificador da transação |