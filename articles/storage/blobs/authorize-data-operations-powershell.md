---
title: Run PowerShell commands with Azure AD credentials to access blob data
titleSuffix: Azure Storage
description: PowerShell supports signing in with Azure AD credentials to run commands on blob data in Azure Storage. An access token is provided for the session and used to authorize calling operations. Permissions depend on the Azure role assigned to the Azure AD security principal.
services: storage
author: tamram

ms.service: storage
ms.topic: how-to
ms.date: 05/12/2022
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
---

# Run PowerShell commands with Azure AD credentials to access blob data

Azure Storage provides extensions for PowerShell that enable you to sign in and run scripting commands with Azure Active Directory (Azure AD) credentials. When you sign in to PowerShell with Azure AD credentials, an OAuth 2.0 access token is returned. That token is automatically used by PowerShell to authorize subsequent data operations against Blob storage. For supported operations, you no longer need to pass an account key or SAS token with the command.

You can assign permissions to blob data to an Azure AD security principal via Azure role-based access control (Azure RBAC). For more information about Azure roles in Azure Storage, see [Assign an Azure role for access to blob data](assign-azure-role-data-access.md).

## Supported operations

The Azure Storage extensions are supported for operations on blob data. Which operations you may call depends on the permissions granted to the Azure AD security principal with which you sign in to PowerShell. Permissions to Azure Storage containers are assigned via Azure RBAC. For example, if you have been assigned the **Blob Data Reader** role, then you can run scripting commands that read data from a container. If you have been assigned the **Blob Data Contributor** role, then you can run scripting commands that read, write, or delete a container or the data they contain.

For details about the permissions required for each Azure Storage operation on a container, see [Call storage operations with OAuth tokens](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> When a storage account is locked with an Azure Resource Manager **ReadOnly** lock, the [List Keys](/rest/api/storagerp/storageaccounts/listkeys) operation is not permitted for that storage account. **List Keys** is a POST operation, and all POST operations are prevented when a **ReadOnly** lock is configured for the account. For this reason, when the account is locked with a **ReadOnly** lock, users users who do not already possess the account keys must use Azure AD credentials to access blob data. In PowerShell, include the `-UseConnectedAccount` parameter to create an **AzureStorageContext** object with your Azure AD credentials.

## Call PowerShell commands using Azure AD credentials

To use Azure PowerShell to sign in and run subsequent operations against Azure Storage using Azure AD credentials, create a storage context to reference the storage account, and include the `-UseConnectedAccount` parameter.

The following example shows how to create a container in a new storage account from Azure PowerShell using your Azure AD credentials. Remember to replace placeholder values in angle brackets with your own values:

1. Sign in to your Azure account with the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) command:

    ```powershell
    Connect-AzAccount
    ```

    For more information about signing into Azure with PowerShell, see [Sign in with Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Create an Azure resource group by calling [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Create a storage account by calling [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Get the storage account context that specifies the new storage account by calling [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). When acting on a storage account, you can reference the context instead of repeatedly passing in the credentials. Include the `-UseConnectedAccount` parameter to call any subsequent data operations using your Azure AD credentials:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Before you create the container, assign the [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) role to yourself. Even though you are the account owner, you need explicit permissions to perform data operations against the storage account. For more information about assigning Azure roles, see [Assign an Azure role for access to blob data](assign-azure-role-data-access.md).

    > [!IMPORTANT]
    > Azure role assignments may take a few minutes to propagate.

1. Create a container by calling [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Because this call uses the context created in the previous steps, the container is created using your Azure AD credentials.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## Next steps

- [Assign an Azure role for access to blob data](assign-azure-role-data-access.md)
- [Authorize access to blob data with managed identities for Azure resources](authorize-managed-identity.md)
