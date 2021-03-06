---
title: 'Azure AD Connect : Mise à niveau automatique | Microsoft Docs'
description: Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6083237426e86220f8a3cb7fbe0cdfc30f44f8cc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208824"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect : Mise à jour automatique
Cette fonctionnalité date de la build [1.1.105.0 (publiée en février 2016)](reference-connect-version-history.md#111050).  Cette fonctionnalité a été mise à jour dans la [build 1.1.561](reference-connect-version-history.md#115610) et prend désormais en charge des scénarios supplémentaires.

## <a name="overview"></a>Vue d’ensemble
Grâce à la fonctionnalité de **mise à niveau automatique** , vous pouvez facilement vous assurer que votre installation Azure AD Connect est à jour. Cette fonctionnalité est activée par défaut pour les installations expresses et les mises à niveau de DirSync. Quand une nouvelle version est publiée, votre installation est mise à niveau automatiquement.
La mise à niveau automatique est activée par défaut pour les éléments suivants :

* Installation de la configuration rapide et mises à niveau de DirSync.
* SQL Express LocalDB, toujours utilisée par la configuration rapide. DirSync avec SQL Express utilise également LocalDB.
* Le compte AD est le compte MSOL_ par défaut créé par la configuration rapide et DirSync.
* Avoir moins de 100 000 objets dans le métaverse.

L’état actuel de la mise à niveau automatique peut être affiché avec l’applet de commande PowerShell `Get-ADSyncAutoUpgrade`. Elle peut avoir les états suivants :

| État | Commentaire |
| --- | --- |
| Activé |La mise à niveau automatique est activée. |
| Interrompu |Défini par le système uniquement. **Actuellement, le système n’est pas autorisé à recevoir des mises à niveau automatiques.** |
| Désactivé |La mise à niveau automatique est désactivée. |

Vous pouvez passer de **Activé** à **Désactivé** avec `Set-ADSyncAutoUpgrade`. Seul le système doit définir l’état **Interrompu**.

La mise à niveau automatique utilise Azure AD Connect Health pour l’infrastructure de mise à niveau. Pour qu’une mise à niveau automatique fonctionne, assurez-vous d’avoir ouvert les URLs dans votre serveur proxy pour **Azure AD Connect Health** comme indiqué dans la rubrique [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Si l’interface utilisateur du **Synchronization Service Manager** est en cours d’exécution sur le serveur, la mise à niveau est suspendue jusqu’à la fermeture de l’interface utilisateur.

## <a name="troubleshooting"></a>Résolution de problèmes
Si votre installation Connect ne se met pas elle-même à niveau comme prévu, procédez comme suit pour savoir d’où vient le problème.

Tout d'abord, ne vous attendez pas à ce qu’il y ait déjà une tentative de mise à niveau automatique le jour du lancement d’une nouvelle version. Toute tentative de mise à niveau a un caractère aléatoire intentionnel. Ne vous inquiétez pas si votre installation n'est pas mise à niveau immédiatement.

Si vous pensez que quelque chose ne convient pas, commencez par exécuter `Get-ADSyncAutoUpgrade` pour garantir l’activation de la mise à niveau automatique.

Assurez-vous ensuite que vous avez ouvert les URL requises dans votre proxy ou pare-feu. La mise à jour automatique utilise Azure AD Connect Health comme décrit dans la [présentation](#overview). Si vous utilisez un proxy, vérifiez que Health a été configuré pour utiliser un [serveur proxy](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Testez également la [connectivité de Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) à Azure AD.

Une fois que vous avez vérifié la connectivité à Azure AD, passez aux journaux d’événements. Démarrez l’Observateur d’événements et consultez le journal des événements **Application** . Ajoutez un filtre de journal des événements pour la source **mise à niveau d’Azure AD Connect** et la plage d'ID d’événements **300-399**.  
![Filtre de journal des événements pour la mise à niveau automatique](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Les journaux des événements associés à l’état de mise à niveau automatique s’affichent alors.  
![Filtre de journal des événements pour la mise à niveau automatique](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Le code de résultat a un préfixe avec une vue d’ensemble de l’état.

| Préfixe du code de résultat | Description |
| --- | --- |
| Succès |La mise à niveau de l’installation a réussi. |
| UpgradeAborted |Un problème temporaire a arrêté la mise à niveau. Une nouvelle tentative va être effectuée. Celle-ci devrait réussir à un stade ultérieur. |
| UpgradeNotSupported |La configuration du système entraîne le blocage de sa mise à niveau automatique. Elle sera tentée à nouveau pour voir si l'état est modifié, mais l'on s’attend à ce que le système doive être mis à niveau manuellement. |

Voici une liste de messages les plus courants. Elle n’est pas exhaustive, mais le message de résultat doit exposer clairement la nature du problème.

| Message de résultat | Description |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Impossible d’écrire dans le Registre. |
| UpgradeAbortedInsufficientDatabasePermissions |Le groupe Administrateurs intégré ne dispose pas des autorisations d’accès à la base de données. Effectuez une mise à niveau manuelle vers la dernière version d’Azure AD Connect pour résoudre ce problème. |
| UpgradeAbortedInsufficientDiskSpace |Il n'y a pas suffisamment d'espace disque pour prendre en charge une mise à niveau. |
| UpgradeAbortedSecurityGroupsNotPresent |Impossible de trouver et de résoudre tous les groupes de sécurité utilisés par le moteur de synchronisation. |
| UpgradeAbortedServiceCanNotBeStarted |Échec du démarrage du service NT **Microsoft Azure AD Sync** . |
| UpgradeAbortedServiceCanNotBeStopped |Échec de l’arrêt du service NT **Microsoft Azure AD Sync** . |
| UpgradeAbortedServiceIsNotRunning |Le service NT **Microsoft Azure AD Sync** n’est pas en cours d’exécution. |
| UpgradeAbortedSyncCycleDisabled |L’option SyncCycle du [Scheduler](how-to-connect-sync-feature-scheduler.md) a été désactivée. |
| UpgradeAbortedSyncExeInUse |L’ [interface utilisateur du gestionnaire des services de synchronisation](how-to-connect-sync-service-manager-ui.md) est ouverte sur le serveur. |
| UpgradeAbortedSyncOrConfigurationInProgress |L'Assistant Installation est en cours d'exécution ou une synchronisation a été planifiée à l'extérieur du planificateur. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | Vous avez sélectionné AD FS en tant que méthode de connexion. | 
| UpgradeNotSupportedCustomizedSyncRules |Vous avez ajouté vos propres règles de personnalisation à la configuration. |
| UpgradeNotSupportedDeviceWritebackEnabled |Vous avez activé la fonctionnalité [Écriture différée des appareils](how-to-connect-device-writeback.md) . |
| UpgradeNotSupportedGroupWritebackEnabled |Vous avez activé la fonctionnalité [Écriture différée de groupe](how-to-connect-preview.md#group-writeback) . |
| UpgradeNotSupportedInvalidPersistedState |L’installation n’est pas une configuration rapide ou une mise à niveau DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Vous avez plus de 100 000 objets dans le métaverse. |
| UpgradeNotSupportedMultiForestSetup |Vous vous connectez à plusieurs forêts. L’installation rapide se connecte à une seule forêt. |
| UpgradeNotSupportedNonLocalDbInstall |Vous n’utilisez pas une base de données LocalDB SQL Server Express. |d
| UpgradeNotSupportedNonMsolAccount |Le [compte de connecteur AD DS](reference-connect-accounts-permissions.md#ad-ds-connector-account) n’est plus le compte MSOL_ par défaut. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Lorsque vous configurez AAD Connect, vous avez choisi *Ne pas configurer* lors de la sélection de la méthode d’authentification unique. | 
| UpgradeNotSupportedPtaSignInMethod | Vous avez sélectionné Authentification directe comme mode d’authentification. |
| UpgradeNotSupportedStagingModeEnabled |Le serveur est défini pour être en [mode intermédiaire](how-to-connect-sync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |Vous avez activé la fonctionnalité [Écriture différée de l’utilisateur](how-to-connect-preview.md#user-writeback) . |

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](whatis-hybrid-identity.md).
