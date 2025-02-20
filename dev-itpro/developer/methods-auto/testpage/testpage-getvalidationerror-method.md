---
title: "TestPage.GetValidationError([Integer]) Method"
description: "Gets the list of all validation error that occurred on a test page as a string."
ms.author: solsen
ms.custom: na
ms.date: 07/07/2021
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
ms.service: "dynamics365-business-central"
author: SusanneWindfeldPedersen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# TestPage.GetValidationError([Integer]) Method
> **Version**: _Available or changed with runtime version 1.0._

Gets the list of all validation error that occurred on a test page as a string.


## Syntax
```AL
Error :=   TestPage.GetValidationError([Index: Integer])
```
## Parameters
*TestPage*  
&emsp;Type: [TestPage](testpage-data-type.md)  
An instance of the [TestPage](testpage-data-type.md) data type.  

*[Optional] Index*  
&emsp;Type: [Integer](../integer/integer-data-type.md)  
The index of the validation error that occurred on the test page.  


## Return Value
*Error*  
&emsp;Type: [String](../string/string-data-type.md)  
A string where each line represents a validation error that occured on the test page.


[//]: # (IMPORTANT: END>DO_NOT_EDIT)
## See Also
[TestPage Data Type](testpage-data-type.md)  
[Getting Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)