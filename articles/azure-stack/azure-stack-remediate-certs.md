---
title: Corriger les problèmes liés aux certificats pour Azure Stack | Microsoft Docs
description: Utilisez l’outil Azure Stack Readiness Checker pour examiner les problèmes liés aux certificats et les corriger.
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
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 0a5c0204909a3fa6730a5e852fb24b2213e093a1
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650578"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corriger les problèmes courants liés aux certificats PKI Azure Stack

Les informations contenues dans cet article peuvent vous aider à comprendre et à résoudre les problèmes courants liés aux certificats PKI Azure Stack. Quand vous utilisez l’outil Azure Stack Readiness Checker pour [valider les certificats PKI Azure Stack](azure-stack-validate-pki-certs.md), vous constaterez peut-être la présence de problèmes. L’outil vérifie si les certificats répondent aux exigences PKI d’un déploiement d’Azure Stack et s’ils respectent la rotation des secrets Azure Stack, puis journalise les résultats dans un [fichier report.json](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Chiffrement PFX

**Échec** : le chiffrement PFX n’est pas TripleDES-SHA1.

**Correction** : exportez les fichiers PFX avec le chiffrement **TripleDES-SHA1**. Il s’agit de la valeur par défaut pour tous les clients Windows 10 lors de l’exportation à partir du composant logiciel enfichable Certificat ou à l’aide de `Export-PFXCertificate`.

## <a name="read-pfx"></a>Lecture du fichier PFX

**Avertissement** : Le mot de passe protège uniquement les informations privées dans le certificat.  

**Correction** : Exportez les fichiers PFX avec le paramètre facultatif **Activer la confidentialité de certificat**.  

**Échec** : Le fichier PFX n’est pas valide.  

**Correction** : Réexportez le certificat en suivant les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algorithme de signature

**Échec** : L’algorithme de signature est SHA1.

**Correction** : Utilisez les étapes de la procédure de génération d’une demande de signature de certificat (CRS) Azure Stack pour regénérer la demande avec l’algorithme de signature SHA256. Ensuite, resoumettez la demande de signature de certificat à l’autorité de certification pour réémettre le certificat.

## <a name="private-key"></a>Clé privée

**Échec** : La clé privée est manquante ou ne contient pas l’attribut Ordinateur Local.  

**Correction** : Sur l’ordinateur ayant généré la demande de signature de certificat, réexportez le certificat en suivant les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Ces étapes incluent l’exportation du certificat à partir du magasin de certificats de l’ordinateur local.

## <a name="certificate-chain"></a>Chaîne d’approbation

**Échec** : La chaîne d’approbation n’est pas complète.  

**Correction** : Les certificats doivent contenir une chaîne d’approbation complète. Réexportez le certificat en suivant les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment), puis sélectionnez l’option **Inclure tous les certificats dans le chemin d’accès de certification, si possible**.

## <a name="dns-names"></a>Noms DNS

**Échec** : DNSNameList sur le certificat ne contient pas le nom du point de terminaison de service Azure Stack, ni une correspondance avec un caractère générique valide. Les correspondances avec un caractère générique ne sont valides que pour l’espace de noms situé à l’extrême gauche du nom DNS. Par exemple, _*.region.domain.com_ est valide pour *portal.region.domain.com* (contrairement à _*.table.region.domain.com_).

**Correction** : Utilisez les étapes de la procédure de génération d’une demande de signature de certificat Azure Stack pour regénérer la demande avec les noms DNS corrects afin de prendre en charge les points de terminaison Azure Stack. Resoumettez la demande de signature de certificat à une autorité de certification, puis suivez les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) pour exporter le certificat à partir de l’ordinateur ayant généré la demande.  

## <a name="key-usage"></a>Utilisation de la clé

**Échec** : La signature numérique ou le chiffrement de clé ne figure pas dans l’utilisation de la clé, ou l’authentification du serveur ou du client ne figure pas dans l’utilisation améliorée de la clé.  

**Correction** : Utilisez les étapes de la procédure de [génération d’une demande de signature de certificat Azure Stack](azure-stack-get-pki-certs.md) pour regénérer la demande avec les attributs corrects d’utilisation de la clé. Resoumettez la demande de signature de certificat à l’autorité de certification, puis vérifiez qu’aucun modèle de certificat ne remplace l’utilisation de la clé dans la demande.

## <a name="key-size"></a>Taille de la clé

**Échec** : La taille de la clé est inférieure à 2048.

**Correction** : Utilisez les étapes de la procédure [Génération de CSR Azure Stack](azure-stack-get-pki-certs.md) pour regénérer la demande de signature de certificat (CSR) avec la bonne longueur de clé (2048), puis resoumettez la demande à l’autorité de certification.

## <a name="chain-order"></a>Ordre de la chaîne

**Échec** : L’ordre de la chaîne d’approbation est incorrect.  

**Correction** : Réexportez le certificat en suivant les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment), puis sélectionnez l’option **Inclure tous les certificats dans le chemin d’accès de certification si possible**. Vérifiez que seul le certificat feuille est sélectionné pour l’exportation.

## <a name="other-certificates"></a>Autres certificats

**Échec** : Le package PFX contient des certificats qui ne sont pas le certificat feuille ou qui ne font pas partie de la chaîne d’approbation.  

**Correction** : Réexportez le certificat en suivant les étapes de la procédure [Préparer des certificats PKI Azure Stack pour le déploiement](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment), puis sélectionnez l’option **Inclure tous les certificats dans le chemin d’accès de certification si possible**. Vérifiez que seul le certificat feuille est sélectionné pour l’exportation.

## <a name="fix-common-packaging-issues"></a>Résoudre les problèmes courants de packaging

**AzsReadinessChecker** inclut une applet de commande d’assistance appelée `Repair-AzsPfxCertificate` qui peut importer puis exporter un fichier PFX afin de résoudre les problèmes de packaging courants, comme :

- *Chiffrement PFX* n’est pas TripleDES-SHA1.
- *Clé privée* sans attribut Ordinateur Local.
- *Chaîne d’approbation* incomplète ou incorrecte. L’ordinateur local doit contenir la chaîne de certificats si elle n’est pas présente dans le package PFX.
- *Autres certificats*

`Repair-AzsPfxCertificate` n’est d’aucune utilité si vous devez générer une nouvelle demande signature de certificat et réémettre un certificat.

### <a name="prerequisites"></a>Prérequis

L’ordinateur sur lequel l’outil s’exécute doit répondre aux prérequis suivants :

- Windows 10 ou Windows Server 2016, avec connectivité à Internet.
- PowerShell 5.1 ou ultérieur. Pour vérifier votre version, exécutez l’applet de commande PowerShell suivante, puis examinez les versions *Major* et *Minor* :

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Configurez [PowerShell pour Azure Stack](azure-stack-powershell-install.md).
- Téléchargez la dernière version de l’outil [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

### <a name="import-and-export-an-existing-pfx-file"></a>Importer et exporter un fichier PFX

1. Sur un ordinateur qui répond aux prérequis, ouvrez une invite PowerShell d’administration, puis exécutez la commande suivante pour installer AzsReadinessChecker :

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. À l’invite PowerShell, exécutez l’applet de commande suivante pour définir le mot de passe PFX. Remplacez `PFXpassword` par le mot de passe réel :

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. À l’invite PowerShell, exécutez ce qui suit pour exporter un nouveau fichier PFX :

   - Pour `-PfxPath`, spécifiez le chemin du fichier PFX que vous utilisez. Dans l’exemple suivant, le chemin est `.\certificates\ssl.pfx`.
   - Pour `-ExportPFXPath`, spécifiez l’emplacement et le nom du fichier PFX à exporter. Dans l’exemple suivant, le chemin est `.\certificates\ssl_new.pfx` :

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Au terme de l’exécution de l’outil, examinez la sortie pour voir si l’opération a réussi :

   ```powershell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Accédez à cette adresse pour en savoir plus sur la sécurité dans Azure Stack](azure-stack-rotate-secrets.md).
