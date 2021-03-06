---
title: Prise en main d’Azure MFA dans le cloud
description: Microsoft Azure Multi-Factor Authentication demande un accès conditionnel lors de la prise en main
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbfade56ff12aa719927d496606a6ce4b4fe5a38
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167148"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Déploiement basé sur le cloud Azure Multi-Factor Authentication

La prise en main d’Azure Multi-Factor Authentication (Azure MFA) est un processus simple.

Avant de commencer, assurez-vous que vous disposez des prérequis suivants :

* Un compte d’administrateur général dans votre locataire Azure AD. Si vous avez besoin d’aide lors de cette étape, consultez notre article [Prise en main d'Azure AD](../get-started-azure-ad.md).
* Des licences correctement attribuées aux utilisateurs. Si vous avez besoin de plus d’informations, consultez l’article [Guide pratique pour obtenir l’authentification multifacteur Azure](concept-mfa-licensing.md).

## <a name="choose-how-to-enable"></a>Choisir comment activer

**Activé par la stratégie d’accès conditionnel** : cette méthode est décrite dans cet article. C’est la méthode la plus souple pour activer la vérification en deux étapes pour vos utilisateurs. Activer à l’aide de la stratégie d’accès conditionnel ne fonctionne que pour Azure MFA dans le cloud, et c’est une fonctionnalité payante d’Azure AD.

**Activé par Azure AD Identity Protection** : cette méthode utilise la stratégie des risques Azure AD Identity Protection pour imposer la vérification en deux étapes basée uniquement sur le risque de connexion pour toutes les applications cloud. Cette méthode requiert une licence Azure Active Directory P2. Vous trouverez plus d’informations sur cette méthode dans [Guide pratique pour configurer la stratégie d’utilisateur à risque](../identity-protection/howto-user-risk-policy.md).

**Activé par l’utilisateur de modification d’état** : c’est la méthode traditionnelle pour exiger une vérification en deux étapes. Elle fonctionne avec Azure MFA dans le cloud et le serveur Azure MFA. Cette méthode nécessite que les utilisateurs effectuent la vérification en deux étapes **chaque fois** qu’ils se connectent, puis remplace les stratégies d’accès conditionnel. Vous trouverez plus d’informations sur cette méthode dans [Comment exiger la vérification en deux étapes pour un utilisateur](howto-mfa-userstates.md).

> [!Note]
> Vous trouverez plus d’informations sur les licences et la tarification sur les pages de tarification [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) et [Authentification multifacteur](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="choose-authentication-methods"></a>Choisir les méthodes d’authentification

Activez au moins une méthode d’authentification pour vos utilisateurs en fonction des besoins de votre organisation. Nous pensons que l’application Microsoft Authenticator fournit une meilleure expérience aux utilisateurs une fois activée. Pour mieux comprendre quelles méthodes sont disponibles et comment les configurer, consultez l’article [Présentation des méthodes d’authentification](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Faire s’inscrire les utilisateurs

Une fois que vous activez la stratégie d’accès conditionnel, les utilisateurs sont obligés de s’inscrire la prochaine fois qu’ils utilisent une application protégée par cette stratégie. Si vous activez une stratégie nécessitant une authentification multifacteur pour tous les utilisateurs, sur toutes les applications cloud, cette action peut causer des problèmes pour vos utilisateurs et votre support technique. La recommandation consiste à demander aux utilisateurs d’inscrire des méthodes d’authentification au préalable à l’aide du portail d’inscription à l’adresse [https://aka.ms/mfasetup](https://aka.ms/mfasetup). De nombreuses organisations jugent que la création d’affiches, de cartes de table et d’e-mails permet de favoriser l’adoption.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Activer l’authentification multifacteur avec l’accès conditionnel

Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.

### <a name="choose-verification-options"></a>Choisir les options de vérification

Avant d’activer l’authentification multifacteur Azure, votre organisation doit déterminer quelles options de vérification elle autorise. Pour les besoins de cet exercice, vous allez autoriser les appels téléphoniques et les messages vers un téléphone, qui sont des options génériques que la plupart des personnes sont en mesure d’utiliser. Vous trouverez plus d’informations sur les méthodes d’authentification et leur utilisation dans l’article [Quelles sont les méthodes d’authentification ?](concept-authentication-methods.md)

1. Accédez à **Azure Active Directory**, **Utilisateurs**, **Multi-Factor Authentication**.

   ![Accès au portail Multi-Factor Authentication à partir du panneau Utilisateurs Azure AD dans le portail Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Sous le nouvel onglet qui vient de s’ouvrir dans votre navigateur, accédez aux **paramètres de service**.
1. Sous **options de vérification**, cochez toutes les cases pour les méthodes disponibles pour les utilisateurs.

   ![Configurer des méthodes de vérification dans l’onglet de paramètres de service Authentification multifacteur](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Cliquez sur **Save**(Enregistrer).
5. Fermez l’onglet **paramètres de service**.

### <a name="create-conditional-access-policy"></a>Créez une stratégie d’accès conditionnel

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte d’administrateur général.
1. Accédez à **Azure Active Directory**, **Accès conditionnel**.
1. Sélectionnez **Nouvelle stratégie**.
1. Entrez un nom explicite pour votre stratégie.
1. Sous **utilisateurs et groupes** :
   * Dans l’onglet **Inclure**, sélectionnez la case d’option **Tous les utilisateurs**
   * RECOMMANDÉ : sous l’onglet **Exclure**, cochez la case **Utilisateurs et groupes** et choisissez un groupe à utiliser pour les exclusions lorsque les utilisateurs n’ont pas accès à leurs méthodes d’authentification.
   * Cliquez sur **Done**.
1. Sous **Applications Cloud**, sélectionnez la case d’option **Toutes les applications cloud**.
   * FACULTATIF : sous l’onglet **Exclure**, choisissez les applications cloud pour lesquelles votre organisation ne nécessite pas d’authentification multifacteur.
   * Cliquez sur **Done**.
1. Sous la section **Conditions** :
   * FACULTATIF : si vous avez activé Azure Identity Protection, vous pouvez choisir d’évaluer le risque de connexion dans le cadre de la stratégie.
   * FACULTATIF : si vous avez configuré des emplacements approuvés ou des emplacements nommés, vous pouvez spécifier l’inclusion ou l’exclusion de ces emplacements à partir de la stratégie.
1. Sous **Accord**, assurez-vous que la case d’option **Accorder l’accès** est sélectionnée.
    * Cochez la case **Exiger une authentification multifacteur**.
    * Cliquez sur **Sélectionner**.
1. Ignorez la section **Session**.
1. Basculez **Activer la stratégie** sur **Activé**.
1. Cliquez sur **Créer**.

![Créer une stratégie d’accès conditionnel pour activer l’authentification multifacteur pour les utilisateurs du portail Azure dans le groupe pilote](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Authentification multifacteur Azure

Pour confirmer que votre stratégie d’accès conditionnel fonctionne, testez la connexion à une ressource qui ne nécessite pas l’authentification multifacteur, puis au portail Azure qui exige l’authentification multifacteur.

1. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en mode de navigation privée, et accédez à [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Connectez-vous à l’utilisateur test créé dans le cadre de la section conditions préalables de cet article et notez qu’il ne devrait pas vous demander d’effectuer l’authentification multifacteur.
   * Fermez la fenêtre du navigateur.
2. Ouvrez une nouvelle fenêtre de navigateur dans InPrivate ou en mode de navigation privée, et accédez à [https://portal.azure.com](https://portal.azure.com).
   * Connectez-vous avec l’utilisateur test créé dans le cadre de la section conditions préalables de cet article et notez que vous devez à présent vous inscrire pour utiliser Azure Multi-Factor Authentication.
   * Fermez la fenêtre du navigateur.

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez configuré Azure Multi-Factor Authentication dans le cloud.

Qu’est-ce qui détermine qu’un utilisateur est ou n’est pas invité à effectuer une MFA ? Consultez la section [Rapport de connexion Azure AD du document Rapports dans Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Pour configurer des paramètres supplémentaires tels que les adresses IP approuvées, les messages vocaux personnalisés et les alertes de fraude, consultez l’article [Configurer les paramètres d’Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Vous pouvez trouver des informations sur la gestion des paramètres utilisateur pour Azure Multi-Factor Authentication dans l’article [Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud](howto-mfa-userdevicesettings.md).

[Activer l’inscription convergée pour Azure Multi-Factor Authentication et la réinitialisation de mot de passe en libre-service Azure AD](concept-registration-mfa-sspr-converged.md).
