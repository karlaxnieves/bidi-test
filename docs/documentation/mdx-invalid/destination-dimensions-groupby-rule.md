---
title: Destination Dimensions - GroupBy Rule
deprecated: false
hidden: false
metadata:
  robots: index
---
<HTMLBlock>{`
<iframe class=\"vidyard_iframe\" title=\"Dimensions Module - GroupBy Rule\" src=\"//play.vidyard.com/FtAru3qxpKZuUrzvS6aGFD.html?\" width=\"800\" height=\"400\" scrolling=\"no\" frameborder=\"0\" allowtransparency=\"true\" allowfullscreen referrerpolicy=\"no-referrer-when-downgrade\"></iframe>"
`}</HTMLBlock>

<br />

<br />

## GroupBy Rule Overview

In many cases, customers have tag values they want to drive Dimension elements.  They don’t want to define the logic per element, they just want to use the values from the tag(s).  This is where the “GroupBy” rule is used.  “GroupBys” are generally used for 1 of 3 scenarios:

1. To normalize the values in the tag using a CloudZero transform (see full Transform documentation <a href="https://docs.cloudzero.com/docs/cfdl-reference#transforms" target="_blank">here</a>).  Since tags represent user generated content, you often find different capitalizations and special characters used in the tag values.
2. To collapse multiple tags into 1 Dimension a user would interact with.  Since tags represent user generated content, you often find multiple tag keys for the same concept: Environment, Environment, env, etc.
3. \[LESS COMMON] - To concatenate the values together from multiple sources

### Scenarios

#### Scenario 1 - Normalize Tag Value

```yaml
  Environment:
    Name: Environment
    Rules:
      - Type: GroupBy
        Source: Tag:Environment
        Transforms:
          - Type: Lower
```

**Logic** - Take all the values from Tag:Environment, lowercase them, and use them as Dimension elements

#### Scenario 2 - Normalize Tag Keys

```yaml
  Environment:
    Name: Environment
    Rules:
      - Type: GroupBy
        Source: 
          - Tag:Environment
          - Tag:Enviromnent
          - Tag:env
        Transforms:
          - Type: Lower
        CoalesceSources: True
```

**Logic**

> 📘 Remember that Dimension logic is run against each billing line item where each line item has a date, resource and all the metadata associated with that resource’s usage including the the possibility of all three tags reference above.

Evaluate all the values from Tag:Environment, Tag:Enviromnent, and Tag:env (in that order), lowercase the first non-null value, and use it as the Dimension elements.  Note that if a billing line item has values for all the sources, the only value brought into the Dimension will be the value from the first source in the list (hence the CoalesceSource: True).

Coalesce Definition - To come together as a recognizable whole or entity

**Data Example Using Scenario 2 CostFormation**

| Resource   | Tag:Environment | Tag:Enviromnent | Tag:env    | Dimension Element Created |
| :--------- | :-------------- | :-------------- | :--------- | :------------------------ |
| Resource A | PROD            | Production      | Production | prod                      |
| Resource B |                 |                 | prod       | prod                      |
| Resource C |                 | DEVELOPMENT     | Dev        | development               |
| Resource D | Stg             | Staging         | Stage      | stg                       |
| Resource E | Development     |                 | Dev        | development               |
| Resource F | Staging         | prod            | prod       | staging                   |

> When listing multiple sources with “GroupBy”, most of the desired use cases want to collapse or coalesce the values from the different sources together.  If you do not include the CoalesceSource: True, CloudZero will concatenate the values from the different sources together.

#### Scenario 3 - Concatenate Sources (LESS COMMON)

```yaml
  Tenant:
    Name: Tenant
    Rules:
      - Type: GroupBy
        Source: 
          - Tag:TenantID
          - Tag:TenantName
        Format: '{0} - {1}'
```

If you leave CoalesceSource: True off of your “GroupBy” rule or use CoalesceSource: False, CloudZero will concatenate the values from the source dimensions together.  This is most commonly used when you have different dimensions with IDs and names for some reason, and you want to create a final version of the Dimension that has the combination of both elements.  It is almost always paired with the Format option since we rarely want to concatenate the values together without any spacing or separator in between the element values.

**Data Example Using Scenario 3 CostFormation**

| Resource   | Tag:TenantID | Tag:TenantName | Dimension Element Created |
| :--------- | :----------- | :------------- | :------------------------ |
| Resource A | 12           | Oracle         | 12 - Oracle               |
| Resource B | 23           | Microsoft      | 23 - Microsoft            |
| Resource C | 62           | Apple          | 62 - Apple                |
| Resource D | 45           | Google         | 45 - Google               |

<br />

### Exercises

Before beginning the exercises, you should have VSCode and CloudZero’s extension installed and be Authenticated to the Platform.  For each exercise, you should use the extension's validation functionality to ensure you’re creating well formed CostFormation (you can validate by saving your file locally).  These exercises are somewhat contrived so that they will validate and publish in most customer’s environments.  Because environments are so varied, the results of publishing will be varied.  You may see the logic referencing things that don’t exist in your environment slightly smiling face

Note - If you have any questions about publishing your dimensions you can see the documentation <a href="https://docs.cloudzero.com/docs/publish-definitions#publishing-your-definitions" target="_blank">here.</a>

#### Exercise 1 - You're a new customer to CloudZero and trying to clean up some of your messy tags.  You want to start with Product.

You noticed you have 3 different product tags that you'd like to collapse into a single dimension that end users can interact with (you should replace "product" with something that might be relevant in your environment.  If you don't have this problem, you can choose arbitrary tags).  You use CloudZero's "Tagged Spend Overview" dashboard to determine which tags have the most coverage and prioritize them accordingly:

1. Tag:Product
2. Tag:Products
3. Tag:product

**Exercise 1 Solution**

<a href="https://downloads.cloudzero.com/documentation/resources/academy/GroupBy_Exercise1.cz.yml"> GroupBy\_Exercise1.cz.yml </a>

#### Exercise 2 - After building your product dimension, you realize that one of the products recently got renamed.  While there are a few examples of the correct name, most of the tagged values are the old, "bad", name.  You want to correct this in your CostFormation by renaming all the old values to the new value.

For Exercise 2 and the "Product" dimension, let's assume the "old" product name was Acme and the "new' product name is "Pied Piper".  You can replace this with real examples if you're using tags from your infrastructure.

**Exercise 2 Solution**

<a href="<https://downloads.cloudzero.com/documentation/resources/academy/GroupBy_Exercise2.cz.yml"> GroupBy\_Exercise2\_cz.yml </a>