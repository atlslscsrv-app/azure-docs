---
title: Monitor device connectivity using the Azure IoT Central Explorer
description: Monitor device messages and observe device twin changes through the IoT Central Explorer CLI.
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
ms.tool: azure-cli
ms.custom: device-developer
services: iot-central
# This topic applies to device developers and solution builders.
---

# Monitor device connectivity using Azure CLI

Use the Azure CLI IoT extension to see messages your devices are sending to IoT Central and observe changes in the device twin. You can use this tool to debug and observe device connectivity and diagnose issues of device messages not reaching the cloud or devices not responding to twin changes.

[Visit the Azure CLI extensions reference for more details](/cli/azure/iot/central)

## Prerequisites

A work or school account in Azure, added as a user in an IoT Central application.

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../../includes/azure-cli-prepare-your-environment-h3.md)]

## Install the IoT Central extension

Run the following command from your command line to install:

```azurecli
az extension add --name azure-iot
```

Check the version of the extension by running:

```azurecli
az --version
```

You should see the azure-iot extension is 0.9.9 or higher. If it is not, run:

```azurecli
az extension update --name azure-iot
```

## Using the extension

The following sections describe common commands and options that you can use when you run
`az iot central`. To view the full set of commands and options, pass
`--help` to `az iot central` or any of its subcommands.

### Login

Start by signing into the Azure CLI. 

```azurecli
az login
```

### Get the Application ID of your IoT Central app
In **Application > Management**, copy the **Application ID**. You use this value in later steps.

### Monitor messages
Monitor the messages that are being sent to your IoT Central app from your devices. The output includes all headers and annotations.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### View device properties
View the current read and read/write device properties for a given device.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## Next steps

A suggested next step is to learn [how to connect Azure IoT Edge for Linux on Windows (EFLOW)](./howto-connect-eflow.md).