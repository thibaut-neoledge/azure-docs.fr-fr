<properties 
	pageTitle="Nom et clé de l'émetteur dans BizTalk Services | Azure" 
	description="Découvrez comment récupérer le nom et la clé de l'émetteur pour le bus des services ou le contrôle d'accès (ACS) dans BizTalk Services. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>




# Nom et clé de l'émetteur dans BizTalk Services

Azure BizTalk Services utilise le nom et la clé de l'émetteur Service Bus, ainsi que le nom et la clé de l'émetteur Access Control. Plus précisément :

Tâche | Nom et clé de l'émetteur
--- | ---
Déploiement de votre application à partir de Visual Studio | Nom et clé de l'émetteur Access Control
Configuration du portail Azure BizTalk Services | Nom et clé de l'émetteur Access Control
Création de relais métier avec les services d'adaptateur BizTalk dans Visual Studio | Nom et clé de l'émetteur Service Bus

La présente rubrique répertorie les étapes permettant de récupérer le nom et la clé de l'émetteur.

## Nom et clé de l'émetteur Access Control
Le nom et la clé de l'émetteur Access Control sont utilisés par les éléments suivants :

- Votre application Azure BizTalk Service créée dans Visual Studio : pour déployer correctement votre application BizTalk Service dans Visual Studio sur Azure, entrez le nom et la clé de l’émetteur Access Control. 
- Le portail Azure BizTalk Services : lorsque vous créez un service BizTalk et que vous ouvrez le portail BizTalk Services, votre nom et votre clé de l'émetteur Access Control sont automatiquement enregistrés pour vos déploiements avec les mêmes valeurs Access Control.

### Pour copier et coller le nom et la clé de l'émetteur Access Control

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre service BizTalk. 
4. Sélectionnez **Informations de connexion** dans la barre des tâches. L'espace de noms Access Control, l'émetteur par défaut (Nom de l'émetteur) et la clé par défaut (Clé de l'émetteur) sont répertoriés et peuvent être copiés-collés.<br/><br/> En résumé :<br/> nom de l'émetteur = émetteur par défaut<br/> clé de l'émetteur = clé par défaut


Vous pouvez également cliquer sur **Ouvrir le portail de gestion ACS** pour récupérer les valeurs Access Control :

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre service BizTalk.
4. Sélectionnez le bouton Informations de connexion, puis **Ouvrir le portail de gestion ACS**.
5. Dans le portail, sous **Paramètres de service**, sélectionnez **Identités de service**. Cette action affiche votre identité de service, qui correspond à la valeur de votre nom d'émetteur Access Control. Cliquez sur votre lien d'identité de service pour afficher le mot de passe, qui correspond à la valeur de votre clé d'émetteur. Leurs valeurs peuvent être copiées.<br/><br/> Par exemple, dans **Identités du service**, vous voyez « owner ». « Owner » correspond au nom de votre émetteur Access Control. Lorsque vous cliquez sur le lien « Owner », vous voyez le **Mot de passe**. Lorsque vous cliquez sur le lien « Password », vous voyez la valeur. Cette valeur Password correspond à votre clé d'émetteur Access Control. <br/><br/> En résumé : <br/> Nom de l'émetteur = Nom de l'identité du service<br/> Clé de l'émetteur = Valeur du mot de passe

Dans le volet de navigation de gauche, vous pouvez également sélectionner **Active Directory** pour récupérer les valeurs Access Control.

> [AZURE.IMPORTANT]Lorsqu'un espace de noms Access Control est créé avec <strong>Active Directory</strong>, une identité de service n'est **pas** créée automatiquement. Lorsque vous approvisionnez un service BizTalk, un espace de noms Access Control, une identité de service appelée « Owner » (nom de l'émetteur), un mot de passe (clé de l'émetteur) et une clé symétrique sont automatiquement créés.<br /> La page [Utilisation du service de gestion ACS pour configurer des identités de service](http://go.microsoft.com/fwlink/p/?LinkID=303942) fournit des informations supplémentaires sur les identités de service Access Control.


## Nom et clé de l'émetteur Service Bus
Le nom et la clé de l'émetteur Service Bus sont utilisés par BizTalk Adapter Services. Dans votre projet BizTalk Services dans Visual Studio, vous utilisez les services d'adaptateur BizTalk pour vous connecter à un système métier local. Pour vous connecter, vous créez le relais métier et entrez les détails relatifs à votre système métier. À cette occasion, vous entrez également le nom et la clé de l'émetteur Service Bus.

### Pour récupérer le nom et la clé de l'émetteur Service Bus

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **Service Bus**.
3. Sélectionnez votre espace de noms. Dans la barre des tâches, sélectionnez **Informations de connexion**. Cette action affiche l'**Émetteur par défaut** (Nom de l'émetteur) et la **Clé par défaut** (Clé de l'émetteur). Leurs valeurs peuvent être copiées.<br/><br/> En résumé :<br/> nom de l'émetteur = émetteur par défaut<br/> clé de l'émetteur = clé par défaut

## Suivant
Autres rubriques Azure BizTalk Services :

-  [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>  



## Voir aussi
-  [Utilisation du service de gestion ACS pour configurer des identités de service](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Approvisionnement de BizTalk Services avec le portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Limitation BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=62-->