<properties
	pageTitle="PowerApps: Patch function"
	description="Reference information for the Patch function in PowerApps, including syntax and examples"
	services="powerapps"
	documentationCenter="na"
	authors="gregli-msft"
	manager="dwrede"
	editor=""
	tags=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2015"
   ms.author="gregli"/>

# Patch function in PowerApps #

Modifies or creates a [record](working-with-tables.md) in a [data source](working-with-data-sources.md), or merges records outside of a data source.

## Overview ##

Use the **Patch** function to modify a record of a data source.  The values of specific [properties](working-with-tables.md#elements-of-a-table) are modified without affecting other properties.

Use **Patch** with the **[Defaults](function-defaults.md)** function to create records. You can use this behavior to build a [single screen](working-with-data-sources.md) for both creating and editing records.  

Even if you're not working with a data source, you can use **Patch** to merge two or more records.

## Description ##

### Modify or create a record in a data source ###

To use this function with a data source, specify the data source, and then specify a base record:

- If you want to modify a record, the base record needs to be found in the data source. In most cases, you will have obtained this record from the data source through a gallery control or a function such as **[First](function-first.md)**. The record will contain  values for [primary key](working-with-data-sources.md#primary-keys) properties which uniquely identify the record to be modified.

- If you want to create a record, you can use the **[Defaults](function-defaults.md)** function to create a base record with default values.  The values for primary key properties will be blank.

Then specify one or more change records, each of which contains named properties for the properties to be modified from the base.  Change records are processed in order from the beginning of the [argument list](file-name.md) to the end, with later named properties overriding earlier ones.

The [return value](file-name.md) of **Patch** is the record that you modified or created. If you created a record, the return value may include a primary key property that was automatically generated by the data source.

When you update a data source, a number of issues may arise. Use the **[Errors](function-errors.md)** function to identify and examine as issues, as described in [Working with Data Sources](working-with-data-sources.md#errors).

Related functions include the **[Update](function-update.md)** function for replacing an entire record and the **[Collect](function-collect.md)** function for creating records.

### Merge records outside of a data source ###

Specify two or more records that you want to merge. Records are processed in order from the beginning of the argument list to the end, with later named properties overriding earlier ones.


**Patch** returns the merged record without any [side effects](file-name.md).

## Syntax ##

#### Modify or create a record in a data source ####

**Patch**( *DataSource*, *BaseRecord*, *ChangeRecord1* [, *ChangeRecord2*, … ])

- *DataSource* – Required. The data source that contains the record that you want to modify or create.

- *BaseRecord* – Required. The record to modify or create.  If primary key columns are specified, the record is found and modified; if not a new record is created.

- *ChangeRecord(s)* – Required.  One or more records that contain named properties to modify in the *BaseRecord*.  Change records are processed in order from the beginning of the argument list to the end, with later named properties overriding earlier ones.

#### Merge records ####

**Patch**( *Record1*, *Record2* [, …] )

- *Record(s)* - Required.  At least two records that you want to merge. Records are processed in order from the beginning of the argument list to the end, with later named properties overriding earlier ones.

## Examples ##

#### Modify or create a record (in a data source) ###

In these examples, you'll modify or create a record in a data source that's named **IceCream**. The ID column contains a primary key that the data source generates.  The data source begins with the data in this table:

| ID (automatically generated<br>primary key) | Flavor    | Quantity |
|-----|-----------|----------|
| 1   | Chocolate | 100      |
| 2   | Vanilla   | 200      |

| Formula | Description | Result |
|---------|-------------|--------|
| **Patch(&nbsp;IceCream,<br>{&nbsp;ID:&nbsp;1,&nbsp;Flavor:&nbsp;"Chcocolate",&nbsp;Quantity:100&nbsp;}, {&nbsp;Quantity:&nbsp;400&nbsp;} )** | Modifies a record in the **IceCream** data source:<ul><li> The **ID** column of the record to modify contains the value of **1**. (The Chocolate record has that ID.)</li><li>The value in the **Quantity** column changes to **400**. | {&nbsp;ID:&nbsp;1, Flavor:&nbsp;"Chocolate", Quantity:&nbsp;400 }<br><br>The Chocolate entry in the **IceCream** data source has been modified. |
| **Patch( IceCream, Defaults(&nbsp;IceCream ), {&nbsp;Flavor:&nbsp;“Strawberry”&nbsp;}&nbsp;)** | Creates a record in the **IceCream** data source:<ul><li>The ID column contains a value that the data source generates automatically (3).</li><li>The Quantity column contains the default value (0) for that column in the **IceCream** data source, as the **Defaults** function specifies.<li>The **Flavor** column contains the value of **Strawberry**.</li> | { ID:&nbsp;3, Flavor:&nbsp;“Strawberry”, Quantity:&nbsp;0&nbsp;}<br><br>The Strawberry entry in the **IceCream** data source has been created. |

After the above formulas have been evaluated, the data source ends with these values:

| ID (automatically generated<br>primary key) | Flavor    | Quantity |
|-----|-----------|----------|
| 1   | Chocolate | 400      |
| 2   | Vanilla   | 200      |
| 3   | Strawberry | 0 |

#### Merge records (outside of a data source) ####

| Formula | Description | Result |
|---------|-------------|--------|
|**Patch(&nbsp;{&nbsp;Name:&nbsp;"James",&nbsp;Score:&nbsp;90&nbsp;}, {&nbsp;Name:&nbsp;"Jim",&nbsp;Passed:&nbsp;true&nbsp;} )** | Merges two records outside of a data source:<br><ul><li>The values in the **Name** column of each record don't match. The result contains the value (**Jim**) in the record that's closer to the end of the argument list instead of the value (**James**) in the record that's closer to the start.</li><li>The first record contains a column (**Score**) that doesn't exist in the second record. The result contains that column with its value (**90**).</li><li>The second record contains a column (**Passed**) that doesn't exist in the first record. The result contains that column with its value (**true**). | {&nbsp;Name:&nbsp;"Jim", Score:&nbsp;90, Passed:&nbsp;true&nbsp;} |