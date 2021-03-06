---
title: 'Didacticiel : Intégration d’Azure Active Directory à SpaceIQ | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6695fe4854c6d91d3d2ba671104d1b481721356c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162269"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Didacticiel : Intégration d’Azure Active Directory à SpaceIQ

Dans ce didacticiel, vous allez apprendre à intégrer SpaceIQ à Azure Active Directory (Azure AD).

L’intégration de SpaceIQ dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à SpaceIQ.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SpaceIQ (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à SpaceIQ, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement à SpaceIQ pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SpaceIQ à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-spaceiq-from-the-gallery"></a>Ajout de SpaceIQ à partir de la galerie
Pour configurer l’intégration de SpaceIQ avec Azure AD, vous devez ajouter SpaceIQ, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SpaceIQ à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **SpaceIQ**, sélectionnez **SpaceIQ** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![SpaceIQ dans la liste des résultats](./media/spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SpaceIQ avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SpaceIQ équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur SpaceIQ associé doit être établie.

Dans SpaceIQ, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SpaceIQ, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test SpaceIQ](#create-a-spaceiq-test-user)** pour avoir un équivalent de Britta Simon dans SpaceIQ lié à la représentation Azure AD associée.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SpaceIQ.

**Pour configurer l’authentification unique Azure AD avec SpaceIQ, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SpaceIQ**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

1. Dans la section **Domaine et URL SpaceIQ**, effectuez les étapes suivantes :

    ![Informations d’authentification unique Domaine et URL SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. Dans la zone de texte **Identificateur**, tapez l’URL : `https://api.spaceiq.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Mettez à jour ces valeur avec l’URL de réponse et l’identificateur réels. La procédure est expliquée plus loin dans le didacticiel.
 
1. Dans la section **Certificat de signature SAML**, cliquez sur **(Certificat Base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/spaceiq-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de SpaceIQ**, cliquez sur **Configurer SpaceIQ** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configuration de SpaceIQ](./media/spaceiq-tutorial/tutorial_spaceiq_configure.png) 

1.  Ouvrez une nouvelle fenêtre de navigateur, puis connectez-vous au site de votre entreprise SpaceIQ en tant qu’administrateur.

1. Une fois que vous êtes connecté, cliquez sur le signe de puzzle en haut à droite, puis cliquez sur **« Integration »**

    ![Account Settings](./media/spaceiq-tutorial/setting1.png) 

1. Sous **All PROVISIONING & SSO**, cliquez sur la vignette **Azure** pour ajouter une instance d’Azure comme fournisseur d’identité.

    ![Icône SAML](./media/spaceiq-tutorial/setting2.png)

1. Dans la boîte de dialogue **SSO**, effectuez les étapes suivantes :

    ![Paramètres d’authentification SAML](./media/spaceiq-tutorial/setting3.png)

    a. Dans la zone **SAML Issuer URL**, collez la valeur de la zone **URL d’entité SAML** copiée à partir de la fenêtre Configuration de l’application Azure AD.
    
    b. Copiez la valeur **SAML CallBack Endpoint URL (read-only)** et collez-la dans la zone **URL de réponse** dans le portail Azure, dans la section **Domaine et URL de SpaceIQ**.
    
    c. Copiez la valeur **SAML Audience URI (read-only)** et collez-la dans la zone **Identificateur** dans le portail Azure, dans la section **Domaine et URL de SpaceIQ**.

    d. Ouvrez le fichier de certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone **Certificat X.509**.
    
    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/spaceiq-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/spaceiq-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/spaceiq-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/spaceiq-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-spaceiq-test-user"></a>Créer un utilisateur de test SpaceIQ

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans SpaceIQ. Pour ajouter les utilisateurs dans la plateforme SpaceIQ, rapprochez-vous de [l’équipe de support de SpaceIQ](mailto:eng@spaceiq.com) . Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SpaceIQ.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SpaceIQ, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **SpaceIQ**.

    ![Lien SpaceIQ dans la liste des applications](./media/spaceiq-tutorial/tutorial_spaceiq_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette SpaceIQ dans le volet d’accès, vous devez être connecté automatiquement à votre application SpaceIQ.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/spaceiq-tutorial/tutorial_general_203.png

