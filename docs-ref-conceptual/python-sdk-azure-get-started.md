---
title: Get started with the Azure libraries for Python
description: Getting started with Aure libraries for Python
keywords: Azure, Python, SDK, API
author: lisawong19
ms.author: liwong
manager: douge
ms.date: 06/05/2017
ms.topic: get-started
ms.prod: azure
ms.technology: azure
ms.devlang: python
ms.service: multiple
ms.assetid: 
---

# Get started with the Azure libraries for Python

This tutorial demonstrates the usage of several Azure libraries for Python.  You will set up authentication, create and use an Azure Storage account, create and use an Azure SQL Database, deploy some virtual machines, and deploy an Azure App Service Web App from GitHub.

## Prerequisites

- An Azure account. If you don't have one, [get a free trial](https://azure.microsoft.com/free/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)
- [Python](https://www.python.org)

## Which Python and which version to use
There are several Python interpreters available - examples include:

* CPython - the standard and most commonly used Python interpreter
* PyPy - fast, compliant alternative implementation to CPython
* IronPython - Python interpreter that runs on .Net/CLR
* Jython - Python interpreter that runs on the Java Virtual Machine

**CPython** v2.7 or v3.3+ and PyPy 5.4.0 are tested and supported for the Azure SDK for Python.

## Where to get Python?
There are several ways to get CPython:

* Directly from [python.org](https://www.python.org)
* From a reputable distributor such as [Continuum](https://www.continuum.io), [Enthought](https://www.enthought.com) or [ActiveState](https://www.activestate.com)
* Build from source

Unless you have a specific need, we recommend the first two options.

## Set up authentication

The SDK is able to create a client using your CLI active subscription.

To define active credentials, use [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).
Default subscription ID is either the only one you have, or you can define it using 
[az account](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli)

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_cli_profile(ComputeManagementClient)
```
## Create a virtual environment

> [!IMPORTANT]
> Create a virtual environment is optional, but we strongly suggest you to do it.

Create a virtual environment in Bash (Linux or [Bash for Windows](https://msdn.microsoft.com/commandline/wsl/about))
```bash
pip install virtualenv
virtualenv mytestenv
cd mytestenv
source ./bin/activate
```

Create a virtual environment in Powershell:
```powershell
pip install virtualenv
virtualenv mytestenv
cd mytestenv
.\Scripts\activate
```

> [!IMPORTANT]
> Note that if you use [VSCode](https://code.visualstudio.com/) and the [Python extension](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python),  you can start it using "code ." and get your environment configured.

## Create a Linux virtual machine


## Deploy a web app from a GitHub repo


## Connect to a SQL database


## Write a blob into a new storage account

This code creates an [Azure storage account](https://docs.microsoft.com/azure/storage/storage-introduction) and then uses the Azure Storage libraries for Python to create a new html file in the cloud. 
```python
from azure.storage import CloudStorageAccount
from azure.storage.blob import PublicAccess
from azure.storage.blob.models import ContentSettings
from azure.mgmt.storage.models import (
    StorageAccountCreateParameters,
    StorageAccountUpdateParameters,
    Sku,
    SkuName,
    Kind
)
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient


# log in
resource_client = get_client_from_cli_profile(ResourceManagementClient)
storage_client = get_client_from_cli_profile(StorageManagementClient)

# You MIGHT need to add Storage as a valid provider for these credentials
# If so, this operation has to be done only once for each credentials
# resource_client.providers.register('Microsoft.Storage')
#
# create a new resource group
resource_client.resource_groups.create_or_update(
    'lisaresource',
    {
        'location':'eastus'
    }
)

# create a new storage account
async_create = storage_client.storage_accounts.create(
    'lisaresource',
    'lisastorage123',
    StorageAccountCreateParameters(
        sku=Sku(SkuName.standard_ragrs),
        kind=Kind.storage,
        location='eastus'
    )
)
async_create.wait()

# create a public storage container to hold the file
storage_keys = storage_client.storage_accounts.list_keys('lisaresource', 'lisastorage123')
storage_keys = {v.key_name: v.value for v in storage_keys.keys}

storage_client = CloudStorageAccount('lisastorage123', storage_keys['key1'])
blob_service = storage_client.create_block_blob_service()

blob_service.create_container('lisacontainer',public_access=PublicAccess.Container)


blob_service.create_blob_from_bytes(
    'lisacontainer',
    'helloworld.html',
    b'<center><h1>Hello World!</h1></center>',
    content_settings=ContentSettings('text/html')
)

print(blob_service.make_blob_url('lisacontainer', 'helloworld.html'))
```
To verify content successfully uploaded, naviage to the url printed. 

## Explore more samples

To learn more about how to use the Azure management libraries for Python to manage resources and automate tasks, see our sample code for [virtual machines](python-sdk-azure-web-apps-samples.md), [web apps](python-sdk-azure-web-apps-samples.md) and [SQL database](python-sdk-azure-sql-database-samples.md).


## Reference

A [reference](http://docs.microsoft.com/python/api) is available for all packages.