---
title: Rapport de validation pour Azure Stack | Microsoft Docs
description: Utilisez le rapport de l’outil Azure Stack Readiness Checker pour passer en revue les résultats de la validation.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 0f6230dd3fe59e2aa34e358bfa9133f736d17f36
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732447"
---
# <a name="azure-stack-validation-report"></a>Rapport de validation Azure Stack
Utilisez l’outil Azure Stack Readiness Checker pour exécuter des validations prenant en charge le déploiement et la maintenance d’un environnement Azure Stack. L’outil écrit les résultats dans un fichier de rapport .json. Le rapport affiche des données résumées et détaillées sur l’état des prérequis en vue du déploiement d’Azure Stack. Le rapport affiche également des informations sur la Rotation des secrets des déploiements Azure Stack existants.  

## <a name="where-to-find-the-report"></a>Emplacement du rapport
Quand l’outil s’exécute, il journalise les résultats dans **AzsReadinessCheckerReport.json**. L’outil crée également un journal nommé **AzsReadinessChecker.log**. L’emplacement de ces fichiers est indiqué avec les résultats de la validation dans PowerShell.

![run-validation](./media/azure-stack-validation-report/validation.png)

Les deux fichiers conservent les résultats des vérifications de validation exécutées postérieurement sur le même ordinateur.  Par exemple, vous pouvez exécuter l’outil pour valider des certificats, le réexécuter pour valider l’identité Azure, puis l’exécuter une troisième fois pour valider l’inscription. Les résultats des trois validations sont disponibles dans le rapport .json généré.  

Par défaut, les deux fichiers sont écrits à l’emplacement *C:\Users\<nom_utilisateur>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Utilisez le paramètre **-OutputPath** ***&lt;chemin&gt;*** à la fin de la ligne de commande d’exécution pour spécifier un emplacement de rapport différent.   
- Utilisez le paramètre **-CleanReport** à la fin de la commande d’exécution pour effacer les informations du fichier *AzsReadinessCheckerReport.json*. concernant les exécutions précédentes de l’outil.

## <a name="view-the-report"></a>Afficher le rapport
Pour voir le rapport dans PowerShell, affectez à la valeur de **-ReportPath** le chemin au rapport. Cette commande affiche le contenu du rapport et identifie les validations qui n’ont pas encore de résultats.

Par exemple, pour voir le rapport à partir d’une invite PowerShell ouverte à l’emplacement du rapport, exécutez : 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

La sortie se présente comme suit :

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
```

## <a name="view-the-report-summary"></a>Afficher le récapitulatif du rapport
Pour voir un récapitulatif du rapport, ajoutez le commutateur **-Summary** à la fin de la ligne de commande PowerShell. Par exemple :  
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

Le récapitulatif affiche les validations qui n’ont pas de résultats et indique l’état de réussite ou d’échec des validations terminées. La sortie se présente comme suit :

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```


## <a name="view-a-filtered-report"></a>Afficher un rapport filtré
Pour afficher un rapport filtré sur un seul type de validation, utilisez le paramètre **-ReportSections** avec l’une des valeurs suivantes :
- Certificat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Tâches   
- Tous  

Par exemple, pour afficher le récapitulatif du rapport pour les certificats uniquement, utilisez la ligne de commande PowerShell suivante : 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Voir aussi
