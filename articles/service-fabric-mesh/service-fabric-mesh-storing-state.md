---
title: Options de stockage d’état sur Azure Service Fabric mesh | Microsoft Docs
description: Découvrez comment stocker un état de façon fiable dans des applications Service Fabric mesh s’exécutant sur Azure Service Fabric mesh.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 81d9fc7babef7c1d14a0acc420d0c16c05b83215
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354329"
---
# <a name="state-management-with-service-fabric"></a>Gestion d’état avec Service Fabric

Service Fabric prend en charge de nombreuses option de stockage d’état. Pour une vue d’ensemble conceptuelle des modèles de gestion d’état et de Service Fabric, consultez [Concepts de Service Fabric : état](/azure/service-fabric/service-fabric-concepts-state). Tous ces concepts s’appliquent, que vos services s’exécutent à l’intérieur ou à l’extérieur de Service Fabric mesh. 

Avec Service Fabric mesh, vous pouvez facilement déployer une nouvelle application et la connecter à un magasin de données existant hébergé dans Azure. En plus la possibilité d’utiliser toute base de données distante, il existe plusieurs options pour le stockage de données, selon que le service désire un stockage local ou distant. 

## <a name="volumes"></a>Volumes

Les conteneurs utilisent souvent des disques temporaires. Toutefois, les disques temporaires sont éphémères. Quand un conteneur plante, vous obtenez un nouveau disque temporaire et perdez vos informations. Il est également difficile de partager des informations sur les disques temporaires avec d’autres conteneurs. Les volumes sont des répertoires montés à l’intérieur de vos instances de conteneur, que vous pouvez utiliser pour conserver l’état. Les volumes vous offrent un stockage de fichiers universel et vous permettent de lire/écrire des fichiers à l’aide d’API de fichier d’E/S de disque normal. La ressource Volume décrit comment monter un répertoire et quel stockage de sauvegarde utiliser. Vous pouvez choisir le stockage Azure Files ou un disque Service Fabric Volume pour stocker les données.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric Reliable Volume est un pilote de volume Docker utilisé pour monter un volume local sur un conteneur. Les lectures et les écritures sont des opérations locales et rapides. Les données sont répliquées sur des nœuds secondaires, ce qui les rend hautement disponibles. Le basculement est également rapide. Lorsqu’un conteneur plante, il bascule vers un nœud qui a déjà une copie de vos données. Pour un exemple, consultez [Guide pratique pour déployer une application avec Service Fabric Reliable Volume.](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)

### <a name="azure-files-volume"></a>Azure Files Volume

Azure Files Volumes est un pilote de volume Docker utilisé pour monter un partage Azure Files sur un conteneur. Le stockage Azure Files utilise le stockage réseau, donc les lectures et les écritures ont lieu sur le réseau. Par rapport à Service Fabric Reliable Volume, le stockage Azure Files est moins performant, mais constitue une option moins chère et totalement fiable. Pour un exemple, consultez [Guide pratique pour déployer une application avec Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le modèle d’application, voir [Ressources Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
