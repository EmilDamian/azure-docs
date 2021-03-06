---
title: Parameterizing mapping data flows
description: Learn how to parameterize a mapping data flow from data factory pipelines
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
---

# Parameterizing mapping data flows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapping data flows in Azure Data Factory support the use of parameters. Define parameters inside of your data flow definition and use them throughout your expressions. The parameter values are set by the calling pipeline via the Execute Data Flow activity. You have three options for setting the values in the data flow activity expressions:

* Use the pipeline control flow expression language to set a dynamic value
* Use the data flow expression language to set a dynamic value
* Use either expression language to set a static literal value

Use this capability to make your data flows general-purpose, flexible, and reusable. You can parameterize data flow settings and expressions with these parameters.

## Create parameters in a mapping data flow

To add parameters to your data flow, click on the blank portion of the data flow canvas to see the general properties. In the settings pane, you will see a tab called **Parameter**. Select **New** to generate a new parameter. For each parameter, you must assign a name, select a type, and optionally set a default value.

![Create Data Flow parameters](media/data-flow/create-params.png "Create Data Flow parameters")

## Use parameters in a mapping data flow 

Parameters can be referenced in any data flow expression. Parameters begin with $ and are immutable. You will find the list of available parameters inside of the Expression Builder under the **Parameters** tab.

![Data flow parameter expression](media/data-flow/parameter-expression.png "Data flow parameter expression")

You can quickly add additional parameters by selecting **New parameter** and specifying the name and type.

![Data flow parameter expression](media/data-flow/new-parameter-expression.png "Data flow parameter expression")

## Assign parameter values from a pipeline

Once you've created a data flow with parameters, you can execute it from a pipeline with the Execute Data Flow Activity. After you add the activity to your pipeline canvas, you will be presented with the available data flow parameters in the activity's **Parameters** tab.

When assigning parameter values, you can use either the [pipeline expression language](control-flow-expression-language-functions.md) or the [data flow expression language](data-flow-expression-functions.md) based on spark types. Each mapping data flow can have any combination of pipeline and data flow expression parameters.

![Setting a Data Flow parameter](media/data-flow/parameter-assign.png "Setting a Data Flow parameter")

### Pipeline expression parameters

Pipeline expression parameters allow you to reference system variables, functions, pipeline parameters, and variables similar to other pipeline activities. When you click **Pipeline expression**, a side-nav will open allowing you to enter an expression using the expression builder.

![Setting a Data Flow parameter](media/data-flow/parameter-pipeline.png "Setting a Data Flow parameter")

When referenced, pipeline parameters are evaluated and then their value is used in the data flow expression language. The pipeline expression type doesn't need to match the data flow parameter type. 

#### String literals vs expressions

When assigning a pipeline expression parameter of type string, by default quotes will be added and the value will be evaluated as a literal. To read the parameter value as a data flow expression, check the expression box next to the parameter.

![Setting a Data Flow parameter](media/data-flow/string-parameter.png "Setting a Data Flow parameter")

If data flow parameter `stringParam` references a pipeline parameter with value `upper(column1)`. 

- If expression is checked, `$stringParam` evaluates to the value of column1 all uppercase.
- If expression is not checked (default behavior),  `$stringParam` evaluates to `'upper(column1)'`


#### Pipeline parameter example

Say you have an integer parameter `intParam` that is referencing a pipeline parameter of type String, `@pipeline.parameters.pipelineParam`. 

![Setting a Data Flow parameter](media/data-flow/parameter-pipeline-2.png "Setting a Data Flow parameter")

`@pipeline.parameters.pipelineParam` is assigned a value of `abs(1)` at runtime.

![Setting a Data Flow parameter](media/data-flow/parameter-pipeline-4.png "Setting a Data Flow parameter")

When `$intParam` is referenced in an expression such as a derived column, it will evaluate `abs(1)` return `1`. 

![Setting a Data Flow parameter](media/data-flow/parameter-pipeline-3.png "Setting a Data Flow parameter")

### Data flow expression parameters

Select **Data flow expression** will open up the data flow expression builder. You will be able to reference functions, other parameters and any defined schema column throughout your data flow. This expression will be evaluated as is when referenced.

> [!NOTE]
> If you pass in an invalid expression or reference a schema column that doesn't exist in that transformation, the parameter will evaluate to null.


### Passing in a column name as a parameter

A common pattern is to pass in a column name as a parameter value. If the column is defined in the data flow schema, you can reference it directly as a string expression. If the column isn't defined in the schema, use the `byName()` function. Remember to cast the column to its appropriate type with a casting function such as `toString()`.

For example, if you wanted to map a string column based upon a parameter `columnName`, you can add a derived column transformation equal to `toString(byName($columnName))`.

![Passing in a column name as a parameter](media/data-flow/parameterize-column-name.png "Passing in a column name as a parameter")

## Next steps
* [Execute data flow activity](control-flow-execute-data-flow-activity.md)
* [Control flow expressions](control-flow-expression-language-functions.md)
