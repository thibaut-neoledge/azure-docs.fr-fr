<properties 
	pageTitle="Utilisation de certificats avec Enterprise Integration Pack | Microsoft Azure App Service" 
	description="Découvrez comment utiliser les certificats avec Enterprise Integration Pack et vos applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="deonhe"/>

# En savoir plus sur les certificats et Enterprise Integration Pack

## Vue d'ensemble
Enterprise Integration utilise des certificats pour sécuriser les communications B2B. Vous pouvez utiliser deux types de certificats dans vos applications Enterprise Integration :
- Des certificats publics, qui doivent être achetés auprès d’une autorité de certification (CA)
- Des certificats privés, que vous pouvez créer vous-même (parfois appelés certificats auto-signés).

## Que sont les certificats ?
Les certificats sont des documents numériques utilisés pour vérifier l’identité des participants dans des communications électroniques et pour sécuriser ces communications électroniques.

## Pourquoi utiliser des certificats ?
Parfois, les communications B2B doivent rester confidentielles. Enterprise Integration utilise des certificats pour sécuriser ces communications de deux manières :
- En chiffrant le contenu des messages
- En signant numériquement les messages

## Comment télécharger des certificats ?

### Certificat public
Pour utiliser un **certificat public** dans vos applications logiques avec fonctionnalités B2B, vous devez tout d’abord le télécharger dans votre compte d’intégration. D’autre part, pour utiliser un **certificat auto-signé**, ce certificat doit d’abord être téléchargé dans le coffre de clés [Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés").

Après avoir téléchargé un certificat, vous pourrez l'utiliser pour sécuriser vos messages B2B lorsque vous définissez leurs propriétés dans les [contrats](./app-service-logic-enterprise-integration-agreements.md) que vous créez.

Voici les étapes détaillées pour télécharger vos certificats publics sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** auquel vous ajouterez le certificat ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Sélectionnez la mosaïque **Certificats** ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Certificats qui s’affiche ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Entrez un **nom** pour votre certificat, sélectionnez le type de certificat (dans cet exemple, j’ai utilisé un certificat public) puis choisissez l’icône de dossier située à droite de la zone de texte **Certificat**. Le sélecteur de fichiers apparaît et vous permet de rechercher et de sélectionner le fichier de certificat que vous souhaitez télécharger sur votre compte d’intégration. Une fois que vous avez sélectionné le certificat, choisissez **OK** dans le sélecteur de fichiers. Cette opération valide et télécharge le certificat sur votre compte d’intégration. Enfin, de retour dans le panneau **Ajouter un certificat**, sélectionnez le bouton **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. En moins d'une minute, une notification vous indique que le téléchargement du certificat est terminé.
8. Sélectionnez la mosaïque **Certificats**. Une fois la page actualisée, vous devez voir le certificat qui vient d'être ajouté : ![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### Certificat privé
Vous pouvez également télécharger des certificats privés sur votre compte d’intégration. Pour ce faire, vous devez exécuter la procédure suivante :
1. [Télécharger votre clé privée dans Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés")
2. Créer un certificat privé
3. Télécharger le certificat privé sur votre compte d’intégration

Après avoir effectué les étapes ci-dessus, vous pouvez utiliser le certificat privé pour créer des contrats.

Voici les étapes détaillées pour télécharger vos certificats privés sur votre compte d’intégration une fois que vous êtes connecté au portail Azure :
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** auquel vous ajouterez le certificat ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Sélectionnez la mosaïque **Certificats**![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Certificats qui s’affiche ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. Entrez un **nom** pour votre certificat, sélectionnez le type de certificat (dans cet exemple, j’ai utilisé un certificat public) puis choisissez l’icône de dossier située à droite de la zone de texte **Certificat**. Le sélecteur de fichiers apparaît et vous permet de rechercher et de sélectionner le fichier de certificat que vous souhaitez télécharger sur votre compte d’intégration. Une fois que vous avez sélectionné le certificat, choisissez **OK** dans le sélecteur de fichiers. Cette opération valide et télécharge le certificat sur votre compte d’intégration. Enfin, de retour dans le panneau **Ajouter un certificat**, sélectionnez le bouton **OK**. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. En moins d'une minute, une notification vous indique que le téléchargement du certificat est terminé.
8. Sélectionnez la mosaïque **Certificats**. Une fois la page actualisée, vous devez voir le certificat qui vient d'être ajouté : ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

Après avoir téléchargé un certificat, vous pourrez l'utiliser pour sécuriser vos messages B2B lorsque vous définissez leurs propriétés dans des [contrats](./app-service-logic-enterprise-integration-agreements.md).


## Étapes suivantes
- - [Création d'une application logique utilisant des fonctionnalités B2B](./app-service-logic-enterprise-integration-b2b.md)
- [Créer un contrat B2B](./app-service-logic-enterprise-integration-agreements.md)
- [En savoir plus sur Azure Key Vault](../key-vault/key-vault-get-started.md "En savoir plus sur le coffre de clés")

<!---HONumber=AcomDC_0824_2016-->