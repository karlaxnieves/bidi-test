---
title: Destination Dimensions - Group Rule
deprecated: false
hidden: false
metadata:
  robots: index
---
<HTMLBlock>{`
<iframe class="vidyard_iframe" title="Dimensions Module - Group Rule" src="//play.vidyard.com/vwL7Sid3MwuVJdvvXnYAhJ.html?" width="800" height="400" scrolling="no" frameborder="0" allowtransparency="true" allowfullscreen referrerpolicy="no-referrer-when-downgrade"></iframe>
`}</HTMLBlock>

<br />

***

## Group Rule Overview

The “Group” rule lets you define the exact logic you want to drive a particular Dimension element. It’s probably the most commonly used rule both because of it’s usefulness and simplicity.

If we revisit the example from the Dimension Introduction section, you’ll see a number of “Group” rules.  The topmost example being:

```yaml
  Lineitem_Type:
    Name: Lineitem Type
    Rules:
      - Type: Group
        Name: Support
        Conditions:
          - Source: Service
            Equals: OCBPremiumSupport
```

```Text LOGIC EQUIVALENCY
IF Source:Service = “OCBPremiumSupport” THEN “Support”
```

Every “Group” rule you create will match the syntax above, albeit the element you’re creating and the the logic will change.  The most common problems people run into, beyond syntax, are:

1. YAML spacing issues (the spacing must look like the above)
2. How to define more complex logic; the most common need is to define an AND

When defining more complex logic within the “Conditions” section, you need to remember that the AND/OR/NOT needs to come before/above the logic it’s describing.  Let’s say I wanted to implement the following logic:

```Text LOGIC EQUIVALENCY
IF Source:Service = “OCBPremiumSupport” AND Source:Account = “123456789012” THEN “Support”
```

You see how when I write it out (or speak it) the AND comes between the two conditions?  You need to be careful because that’s **not** how it’s implemented in the configuration:

```yaml
  Lineitem_Type:
    Name: Lineitem Type
    Rules:
      - Type: Group
        Name: Support
        Conditions:
          - And:
            - Source: Service
              Equals: OCBPremiumSupport
            - Source: Account
              Equals: 123456789012
```

What if we wanted the following logic:

```Text LOGIC EQUIVALENCY
IF Source:Service = “OCBPremiumSupport” AND Source:Account != “123456789012” THEN “Support”
```

```yaml
  Lineitem_Type:
    Name: Lineitem Type
    Rules:
      - Type: Group
        Name: Support
        Conditions:
          - And:
            - Source: Service
              Equals: OCBPremiumSupport
            - Not:
              - Source: Account
                Equals: 123456789012
```

> 📘 The condition always comes before/above the logic it’s describing!

### Scenarios

#### Scenario 1 - Your finance team has a spreadsheet where they map account to products for reporting

Here’s a sample from the spreadsheet:

| Account      | Product   |
| :----------- | :-------- |
| 931830288929 | Product A |
| 061190967865 | Product B |
| 618180337335 | Product C |
| 975482736046 | Product B |
| 767428711162 | Product B |

You can easily build a Product dimension in CloudZero using the “Group” rule:

```yaml
  Product:
    Name: Product
    Rules:
      - Type: Group
        Name: Product A
        Conditions:
          - Source: Account
            Equals: 931830288929
      - Type: Group
        Name: Product B
        Conditions:
          - Source: Account
            Equals: 061190967865
      - Type: Group
        Name: Product C
        Conditions:
          - Source: Account
            Equals: 618180337335
      - Type: Group
        Name: Product B
        Conditions:
          - Source: Account
            Equals: 975482736046
      - Type: Group
        Name: Product B
        Conditions:
          - Source: Account
            Equals: 767428711162
```

> 📘 Whenever you’re referencing “Account” as a source with AWS data, you need to remember that all data in CloudZero is represented as a string (even though spreadsheets will convert numeric data to numbers).  AWS Accounts are always padded to 12 digits with leading 0’s.  In the example above,  “61190967865” becomes “061190967865”

The CostFormation above can be optimized in 2 ways:

1. If you find yourself referencing the same source over and over again, CostFormation allows you to define one source in the header section to be the default for the dimension
2. If there are different rules for the same element, these can often be collapsed (assuming it still achieves the logic you want)

An optimized form of the dimension above would be:

```yaml
  Product:
    Name: Product
    Source: Account
    Rules:
      - Type: Group
        Name: Product A
        Conditions:
          - Equals: 931830288929
      - Type: Group
        Name: Product B
        Conditions:
          - Source: Account
            Equals: [061190967865, 767428711162, 975482736046]
      - Type: Group
        Name: Product C
        Conditions:
          - Equals: 618180337335
```

#### Scenario 2 - You want to create an environment dimension that cleans up your “env” tag

After an  analysis in the CloudZero platform, you see that the “env” tag has the following values:

* Production
* production
* producion
* PROD
* acme-prd
* development
* DEV
* staging
* stage
* acme-staging
* stg

You might create the following CostFormation:

```yaml
  Environment:
    Name: Environment
    Rules:
      - Type: Group
        Name: Production
        Conditions:
          - Source: Tag:env
            Equals:
              - Production
              - production
              - producion
              - PROD
              - acme-prd
      - Type: Group
        Name: Staging
        Conditions:
          - Source: Tag:env
            Equals:
              - staging
              - stage
              - acme-staging
              - stg
      - Type: Group
        Name: Development
        Conditions:
          - Source: Tag:env
            Equals:
              - development
              - DEV
```

The CostFormation above can be optimized in 4 ways:

1. If you find yourself referencing the same source over and over again, CostFormation allows you to define one source in the header section to be the default for the dimension
2. Whenever you’re sourcing a tag, you often want to “transform” the tag values before evaluating them against some values (because tags represent user generated values…you can’t control the casing and values).  You can find the full documentation on transforms <a href="https://docs.cloudzero.com/docs/cfdl-reference#transforms" target="_blank">here.</a>
3. Instead of using the “Equals” conditional, you can instead us the “Contains” conditional to make the logic more robust.  This might not work in every case depending on what your desired logic is, but for something like Environment it works well.
4. Instead of listing multiple values on different lines, you can collapse these to “array notation” to make your CostFormation more readable.

An optimized form of the dimension above would be:

```yaml
  Environment:
    Name: Environment
    Source: Tag:env
    Transforms:
      - Type: Lower
    Rules:
      - Type: Group
        Name: Production
        Conditions:
          - Contains: prod
      - Type: Group
        Name: Staging
        Conditions:
          - Contains: [staging, stage, stg]
      - Type: Group
        Name: Development
        Conditions:
          - Contains: dev
```

### Exercises

Before beginning the exercises, you should have VSCode and CloudZero’s extension installed and be Authenticated to the Platform.  For each exercise, you should use the extension's validation functionality to ensure you’re creating well formed CostFormation (you can validate by saving your file locally).  These exercises are somewhat contrived so that they will validate and publish in most customer’s environments.  Because environments are so varied, the results of publishing will be varied.  You may see the logic referencing things that don’t exist in your environment slightly smiling face 

Note - If you have any questions about publishing your dimensions you can see the documentation <a href="https://docs.cloudzero.com/docs/publish-definitions#publishing-your-definitions" target="_blank">here.</a>

#### Exercise 1 - You’re working with finance to help automate their Cost Per Customer reporting.  They give you a csv with the following mappings.  Create a “Customer” dimension for finance to use.

<a href="https://downloads.cloudzero.com/documentation/resources/academy/customer_map.csv"> Customer\_Map.csv </a>

**Exercise 1 Solutions** - 3 solutions are provided.  All are correct, but the optimized ones are optimized from a verbosity / readability perspective vs the other.  All would have the same performance profile within CloudZero.

<a href="https://downloads.cloudzero.com/documentation/resources/academy/Group_Exercise1_NonOptimized.cz.yml"> Group\_Exercise1\_NonOptimized.cz.yml </a> 

<a href="https://downloads.cloudzero.com/documentation/resources/academy/Group_Exercise1_Optimized.cz.yml"> Group\_Exercise1\_Optimized.cz.yml </a>

<a href="<https://downloads.cloudzero.com/documentation/resources/academy/Group_Exercise1_Optimized_ServiceDisplay.cz.yml"> Group\_Exercise1\_Optimized\_ServiceDisplay.cz.yml </a>

<br />

#### Exercise 2 - Finance reviewed your dimensions and asked for the following changes to be made:

* There was a mistake in the mappings and service microsoft.storage should really go to Customer = “Acme”
* For service “AmazonEC2” (currently Customer = “Internal”)
  * The “us-east-1” Region can be associated with customer = “Tesla”
  * Everything else can stay with “Internal”
* Everything that’s not associated with a customer should go to an element called “Shared”

**Exercise 2 Solution** - The attached solution uses the optimized dimension above as a starting point.

<a href="https://downloads.cloudzero.com/documentation/resources/academy/Group_Exercise2_Optimized.cz.yml">  Group\_Exercise2\_Optimized.cz.yml </a>

<br />

#### Exercise 3 - Finance reviewed your results from Exercise 2 and asked for two final changes:

* Everything in account `<INSERT ONE OF YOUR ACCOUNT IDs HERE>` should go to customer = “Apple”
* They’d like to try and improve the coverage of the Customer dimension (i.e find additional spend that might belong to the customers).  Their idea was to search against the resource’s names for the Customer names.  If the Customer’s names exist anywhere in the resource’s names, then associate those resources with the Customer.
  * For this exercise, let’s match against the Name tag and the values in resource summary

**Exercise 3 Solution** - The attached solution uses Exercise 2 as a starting point.

<a href="https://downloads.cloudzero.com/documentation/resources/academy/Group_Exercise3_Optimized.cz.yml"> Group\_Exercise3\_Optimized.cz.yml </a>