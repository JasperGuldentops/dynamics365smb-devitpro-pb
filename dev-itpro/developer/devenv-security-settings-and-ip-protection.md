---
title: "Resource Exposure Policy Setting"
description: This topic explains how to set the resource exposure policy for allowing download or debugging into extension to see the source code.
author: SusanneWindfeldPedersen
ms.custom: na
ms.date: 01/11/2022
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.service: "dynamics365-business-central"
ms.author: solsen
---

# Resource Exposure Policy Setting

[!INCLUDE[2021_releasewave2_changed](../includes/2021_releasewave2_changed.md)]

When developing an extension, your code is by default protected against downloading or debugging. Read below about adding Intellectual Property (IP) protection against downloading or debugging into an extension to see the source code in the extensions.

The extension development package provides a pre-configured setting for protection against viewing or downloading the code of the extensions. However, this setting can also be controlled in the manifest; the `app.json` file.

## The properties of the resource exposure policy

> [!NOTE]  
> With [!INCLUDE [prod_short](includes/prod_short.md)] 2021 release wave 2, the `ShowMyCode` setting has been replaced by the `resourceExposurePolicy` setting which offers a richer access control. The `ShowMyCode` will be deprecated in a future release and cannot be used together with the `resourceExposurePolicy` setting. If `ShowMyCode` is set, default values for `resourceExposurePolicy` will be applied (`false`).

When you start a new project, an `app.json` file is generated automatically, which contains the information about the extension that you are building on. In the `app.json` file you can specify a setting called `resourceExposurePolicy` that defines the accessibility of the resources and source code during different operations. `resourceExposurePolicy` specifies the following list of options: `allowDebugging`, `allowDownloadingSource`, and `includeSourceInSymbolFile`. Each of these properties define the specific areas in which the source code of an extension can be accessed. All of the options are by default set to `false` which means that by default no dependent extension can debug or download the source code of your extension. The syntax of the `resourceExposurePolicy` setting is the following:

```al
`"resourceExposurePolicy": {"allowDebugging": <boolean>, "allowDownloadingSource": <boolean>, "includeSourceInSymbolFile": <boolean>}`
```

> [!NOTE]  
> The `resourceExposurePolicy` setting is not visible in the `app.json` file when it is generated. If you want to change the default value from `false`, you must add the setting as shown in the syntax example above.

### allowDebugging

To allow debugging into your extension, when the extension is taken as a dependency, you must set the `allowDebugging` flag, otherwise debugging is not allowed. The default value of `allowDebugging` is `false`. 

> [!NOTE]  
> The **AL: Go!** template sets `allowDebugging` to `true`.

If you want to allow debugging into your extension to view the source code, the `allowDebugging` property in the `app.json` file must be set to `true`. For example, if a developer develops extension A and he or someone else on the team develops extension B, and B depends on A, then debugging B will only step into the code for A if a method from A is called and if the `allowDebugging` flag is set to `true` in the `app.json` file for extension A as shown in the example below. By adding this setting, you *enable debugging* into an extension to view the source code and variables when that extension is set as a dependency.

```json
`"resourceExposurePolicy": {"allowDebugging": true}`
```

> [!NOTE]  
> `allowDebugging` does not apply to [Profiles](devenv-profile-object.md), [Page Customizations](devenv-page-customization-object.md) and [Views](devenv-views.md), because these objects cannot define any custom logic in procedures or triggers. The code for Profiles, Page Customizations, and Views defined in an extension with `allowDebugging` set to `false` can still be accessed and copied using [Designer](devenv-inclient-designer.md).

#### The [NonDebuggable] attribute

Unless you have specified the `[NonDebuggable]` attribute on methods and variables, setting the `allowDebugging` to `true` will allow stepping into this code. If you, however, have marked the methods and variables marked with the `[NonDebuggable]` attribute, these will remain non-debuggable. For more information, see [NonDebuggable Attribute](attributes/devenv-nondebuggable-attribute.md).


#### When should I set `allowDebugging` to `true`?

The default value of the `allowDebugging` flag is `false`. It is recommended that you only set this flag to `true` if you trust who is extending your extension. If `allowDebugging` is set to `true` anyone who extends your code has access to debug into it. If you want to allow only some to have access to your code, you can override the resource policy. For more information, see [Overriding the resource policy](devenv-security-settings-and-ip-protection.md#overriding-the-resource-policy) in this topic.


#### When can code be viewed even though the `allowDebugging` flag is set to `false`?

There are a few cases where code can be debugged into despite the `allowDebugging` flag has been set to `false`. These are:

- Someone will still be able to view your code if an extension is deployed through Visual Studio Code as a **DEV extension**, as opposed to deployed using a cmdlet, by using the **Extension Management** page in [!INCLUDE [prod_short](includes/prod_short.md)] or via AppSource.
- Custom external tools for AL might get access to the DAL information exposed by the debugger by listening to debugger events triggered by Visual Studio Code.
 
### allowDownloadingSource

When this is set to `true` in the `app.json` file of extension A, the source code and any media files of extension A can be downloaded, for example, from the **Download Source** option in the **Extension Management** page in [!INCLUDE[prod_short](includes/prod_short.md)]. Extension A can be a PTE or a DEV extension. The default value of `allowDownloadingSource` is `false`.

### includeSourceInSymbolFile

When this is set to `true` in the `app.json` file of extension A, the downloaded symbol file in Visual Studio Code which is accessed by using the **Downloading Symbols** functionality, contains symbols, source code, and all other resources of extension A. **Go to Definition** to view the code also depends on this property. The default value of `includesourceInSymbolFile` is `false`.

### Example JSON file

Example JSON file with default values when generated by using the **AL: Go!** command.

```json
...
"resourceExposurePolicy": {
    "allowDebugging": true, 
    "allowDownloadingSource": false, 
    "includeSourceInSymbolFile": false
  },
  "runtime": "8.0",
  "keyVaultUrls": [
    "https://mykeyvault.vault.azure.net"
  ],
  "applicationInsightsConnectionString": "MyConnectionString1234"
...
```


## Overriding the resource policy

The resource exposure override can be used to dynamically grant users access. This is useful, if you have, for example, set the `allowDebugging` flag to `false` in your `app.json` file, but you want to allow specific AAD tenants access temporarily. Be aware that if you do not specify anything in the `BC-ResourceExposurePolicy-Overrides` secret described below, then no one can debug your code if `allowDebugging` is set to `false`. On the contrary, if you have set `allowDebugging` to `true` in your `app.json` file, then it does not matter what you specify in the `BC-ResourceExposurePolicy-Overrides` secret, anyone will be able to debug into that code.

### Requirement for overriding the resource policy

It is a requirement for enabling overriding the resource policy, that you have a key vault set up. This is an onboarding process that is described in the links below. Follow the guidelines for keeping your key vault safe in the guidelines in these links. If the key vault is used for multiple purposes you can create different policies for access to override the secret in the key vault. **Remember to register *all* apps that you want to be able to access your key vaults, it is not enough to just add the key vault setting to your `app.json` manifest files.**

> [!IMPORTANT]  
> Resource exposure policy overrides can be used to dynamically grant users of a given AAD tenant ID access. However, the users performing the action, such as debugging, must be delegated admins on the target environment.

For more information, see [Using Key Vault Secrets in Business Central Extensions](devenv-app-key-vault-overview.md) and [Setting up App Key Vaults for Business Central Online](../administration/setup-app-key-vault.md).


### The `BC-ResourceExposurePolicy-Overrides` secret

Once the key vault is set up, the policy of an extension can be overridden by using settings in your extension's key vault. A secret named `BC-ResourceExposurePolicy-Overrides` must be added to the key vault, the value of the secret is a .json file with the structure as shown in the example below. Because the json secret value in this case spans multiple lines, you must use Azure PowerShell instead of the Azure Portal to define these. To enable one or more of the properties for use by an AAD tenant, you must add the tenant ID to enable that property for the users of the tenant. This enables, temporarily, access to source code, for example, for debugging purposes. 


```powershell

$json = '{ 
    "allowDebugging": [ 
      "9e2b6561-1ba6-4790-abcc-c84abf9a8961" 
    ], 
    "allowDownloadingSource": [ 
      "9e2b6561-1ba6-4790-abcc-c84abf9a8961" 
    ], 
    "includeSourceInSymbolFile": [ 
      "9e2b6561-1ba6-4790-abcc-c84abf9a8961" 
    ] 
}'
$Secret = ConvertTo-SecureString -String $json -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName "YourKeyVaultName" -Name "BC-ResourceExposurePolicy-Overrides" -SecretValue $Secret
```

> [!NOTE]  
> If debugging is enabled dynamically a breakpoint can be set in the protected source code when the debugging session is started.

### Partner telemetry

Specifying the `applicationInsightsConnectionString` setting for your extension, in the `app.json` file, enables a signal to be sent every time the policy is read from the key vault, if there is an issue with reading the policy, or an issue with parsing the JSON. For more information, see [Sending Extension Telemetry to Azure Application Insights](devenv-application-insights-for-extensions.md).

```json
"applicationInsightsConnectionString": "MyConnectionString1234"
```
 
## See Also

[JSON Files](devenv-json-files.md)  
[AL Development Environment](devenv-reference-overview.md)  
[NonDebuggable Attribute](attributes/devenv-nondebuggable-attribute.md)  
