
---
title: Utilisation de certificats avec Enterprise Integration Pack | Microsoft Docs
description: Découvrez comment utiliser les certificats avec Enterprise Integration Pack et vos applications logiques
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: deonhe

---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>En savoir plus sur les certificats et Enterprise Integration Pack
## <a name="overview"></a>Vue d'ensemble
Enterprise Integration utilise des certificats pour sécuriser les communications B2B. Vous pouvez utiliser deux types de certificats dans vos applications Enterprise Integration :

* Des certificats publics, qui doivent être achetés auprès d’une autorité de certification (CA).
* Des certificats privés, que vous pouvez créer vous-même. Ces certificats sont parfois appelés « certificats auto-signés ».

## <a name="what-are-certificates?"></a>Que sont les certificats ?
Les certificats sont des documents numériques qui vérifient l’identité des participants dans des communications électroniques et qui sécurisent ces communications électroniques.

## <a name="why-use-certificates?"></a>Pourquoi utiliser des certificats ?
Parfois, les communications B2B doivent rester confidentielles. Enterprise Integration utilise des certificats pour sécuriser ces communications de deux manières :

* En chiffrant le contenu des messages
* En signant numériquement les messages  

## <a name="how-do-you-upload-certificates?"></a>Comment télécharger des certificats ?
### <a name="public-certificates"></a>Certificats publics
Pour utiliser un *certificat public* dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord télécharger le certificat dans votre compte d’intégration. Pour utiliser un *certificat auto-signé*, vous devez d’abord le télécharger dans le coffre de clés [Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés").

Après avoir téléchargé un certificat, vous pouvez l'utiliser pour sécuriser vos messages B2B lorsque vous définissez leurs propriétés dans les [contrats](app-service-logic-enterprise-integration-agreements.md) que vous créez.  

Voici les étapes détaillées pour télécharger vos certificats publics sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :

1. Sélectionnez **Parcourir**.  
    ![Sélectionnez Parcourir](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats.     
    ![Sélectionnez Comptes d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le compte d’intégration auquel vous ajouterez le certificat.  
    ![Sélectionnez le compte d’intégration auquel vous ajouterez le certificat](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Sélectionnez la mosaïque **Certificats** .  
   ![Sélectionnez la mosaïque Certificats](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Dans le panneau **Certificats** qui s’affiche, sélectionnez le bouton **Ajouter**.
    ![Cliquez sur le bouton Ajouter](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Entrez le **nom** de votre certificat, puis sélectionnez le type de certificat. (Dans cet exemple, nous avons utilisé le type de certificat public.) Sélectionnez ensuite l’icône de dossier à droite de la zone de texte **Certificat**.
7. Lorsque le sélecteur de fichiers apparaît, recherchez puis sélectionnez le fichier de certificat que vous souhaitez télécharger sur votre compte d’intégration.
8. Sélectionnez le certificat, puis choisissez **OK** dans le sélecteur de fichiers. Cette opération valide et télécharge le certificat sur votre compte d’intégration.
9. Enfin, de retour dans le panneau **Ajouter un certificat**, sélectionnez le bouton **OK**.  
    ![Cliquez sur le bouton OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  
10. D’ici une minute, une notification vous indique que le téléchargement du certificat est terminé.
11. Sélectionnez la mosaïque **Certificats** . Vous devez voir le certificat qui vient d’être ajouté.  
    ![Consultez le nouveau certificat](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificats privés
Vous pouvez également télécharger des certificats privés sur votre compte d’intégration en procédant comme suit :  

1. [Téléchargez votre clé privée dans Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés").  
   
   > [!TIP]
   > Vous devez autoriser la fonctionnalité Logic Apps du service Azure App à effectuer des opérations sur Key Vault. Vous pouvez autoriser l’accès au principal du service Logic Apps à l’aide de cette commande PowerShell : `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 
2. Créez un certificat privé.  
3. Téléchargez le certificat privé sur votre compte d’intégration.

Après avoir effectué les étapes précédentes, vous pouvez utiliser le certificat privé pour créer des contrats.

Voici les étapes détaillées pour télécharger vos certificats privés sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :  

1. Sélectionnez **Parcourir**.  
    ![Téléchargez vos certificats privés sur votre compte d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats.     
    ![Sélectionnez Comptes d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le compte d’intégration auquel vous ajouterez le certificat.  
    ![Sélectionnez le compte d’intégration auquel vous ajouterez le certificat](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Sélectionnez la mosaïque **Certificats** .  
    ![Sélectionnez la mosaïque Certificats](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  
5. Dans le panneau **Certificats** qui s’affiche, sélectionnez le bouton **Ajouter**.
    ![Cliquez sur le bouton Ajouter](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Entrez le **nom** de votre certificat, puis sélectionnez le type de certificat. (Dans cet exemple, nous avons utilisé le type de certificat public.) Sélectionnez ensuite l’icône de dossier à droite de la zone de texte **Certificat**.
7. Lorsque le sélecteur de fichiers apparaît, recherchez puis sélectionnez le fichier de certificat que vous souhaitez télécharger sur votre compte d’intégration.
8. Une fois que vous avez sélectionné le certificat, choisissez **OK** dans le sélecteur de fichiers. Cette action valide le certificat puis le télécharge sur votre compte d’intégration.
9. Enfin, de retour dans le panneau **Ajouter un certificat**, sélectionnez le bouton **OK**.  
    ![Ajouter le certificat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  
10. D’ici une minute, une notification vous indique que le téléchargement du certificat est terminé.
11. Sélectionnez la mosaïque **Certificats** . Vous devez voir le certificat qui vient d’être ajouté.
    ![Consultez le nouveau certificat](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Après avoir téléchargé un certificat, vous pouvez l'utiliser pour sécuriser vos messages B2B lorsque vous définissez leurs propriétés dans les [contrats](app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Étapes suivantes
* [Création d'une application logique utilisant des fonctionnalités B2B](app-service-logic-enterprise-integration-b2b.md)  
* [Créer un contrat B2B](app-service-logic-enterprise-integration-agreements.md)  
* [En savoir plus sur Azure Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés")  

<!--HONumber=Oct16_HO2-->


