<properties urlDisplayName="Issuer name and issuer key" pageTitle="Nom et clé de l'émetteur dans BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Découvrez comment récupérer le nom et la clé de l'émetteur  pour le bus des services ou le contrôle d'accès (ACS) dans BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />




# BizTalk Services : Nom et clé de l'émetteur

Azure BizTalk Services utilise le nom et la clé de l'émetteur Service Bus, ainsi que le nom et la clé de l'émetteur Access Control. Plus précisément :


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Tâche</strong></td>
<td><strong>Nom et clé de l'émetteur</strong></td>
</tr>
<tr>
<td>Déploiement de votre application à partir de Visual Studio</td>
<td>Nom et clé de l'émetteur Access Control</td>
</tr>
<tr>
<td>Configuration du portail Azure BizTalk Services</td>
<td>Nom et clé de l'émetteur Access Control</td>
</tr>
<tr>
<td>Création de relais métier avec les services d'adaptateur BizTalk dans Visual Studio</td>
<td>Nom et clé de l'émetteur Service Bus</td>
</tr>
</table>

La présente rubrique répertorie les étapes permettant de récupérer le nom et la clé de l'émetteur. 

## Nom et clé de l'émetteur Access Control
Le nom et la clé de l'émetteur Access Control sont utilisés par les éléments suivants :

- Votre application Azure BizTalk Service créée dans Visual Studio : pour déployer correctement votre application BizTalk Service dans Visual Studio sur Azure, vous entrez le nom et la clé de l'émetteur Access Control. 
- Le portail Azure BizTalk Services : la première fois que vous vous connectez au portail BizTalk Services, vous entrez le nom de votre service BizTalk comme fournisseur de services, le nom de l'émetteur Access Control et la clé de l'émetteur Access Control.

### Pour récupérer le nom et la clé de l'émetteur Access Control

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre service BizTalk. 
4. Sélectionnez **Informations de connexion** dans la barre des tâches. L'espace de noms Access Control, l'émetteur par défaut (Nom de l'émetteur) et la clé par défaut (Clé de l'émetteur) sont répertoriés et peuvent être copiés-collés.<br/><br/>
Pour résumer :<br/>
Nom de l'émetteur = émetteur par défaut<br/>
Clé de l'émetteur = clé par défaut


Vous pouvez également cliquer sur **Ouvrir le portail de gestion ACS** pour récupérer les valeurs Access Control :

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, sélectionnez **BizTalk Services**.
3. Sélectionnez votre service BizTalk.
4. Sélectionnez le bouton Informations de connexion, puis **Ouvrir le portail de gestion ACS**.
5. Dans le portail, sous **Paramètres de service**, cliquez sur **Identités de service**. Cette action affiche votre identité de service, qui correspond à la valeur de votre nom d'émetteur Access Control. Cliquez sur votre lien d'identité de service pour afficher le mot de passe, qui correspond à la valeur de votre clé d'émetteur. Leurs valeurs peuvent être copiées.<br/><br/>
Par exemple, dans **Identités de service**, vous voyez " owner ". " Owner " correspond au nom de votre émetteur Access Control. Lorsque vous cliquez sur le lien " Owner ", vous voyez le **Mot de passe**. Lorsque vous cliquez sur le lien " Password ", vous voyez la valeur. Cette valeur Password correspond à votre clé d'émetteur Access Control. <br/><br/>
Pour résumer :<br/>
Nom de l'émetteur = nom de l'identité du service<br/>
Clé de l'émetteur = valeur de mot de passe

Dans le volet de navigation de gauche, vous pouvez également sélectionner **Active Directory** pour récupérer les valeurs Access Control. 

<div class="dev-callout"> 
<b>Important</b> 
<p>Lorsqu'un espace de noms Access Control est créé au moyen d'<strong>Active Directory</strong>, une identité de service n'est <strong>pas</strong> créée automatiquement. Lorsque vous déployez un service BizTalk, un espace de noms Access Control, une identité de service appelée " Owner " (Nom de l'émetteur), un mot de passe (Clé de l'émetteur) et une clé symétrique sont automatiquement créés.</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942"> La page </a>Utilisation du service de gestion ACS pour configurer des identités de service</p> fournit des informations supplémentaires sur les identités de service Access Control.
</div>


## Nom et clé de l'émetteur Service Bus
Le nom et la clé de l'émetteur Service Bus sont utilisés par BizTalk Adapter Services. Dans votre projet BizTalk Services dans Visual Studio, vous utilisez les services d'adaptateur BizTalk pour vous connecter à un système métier local. Pour vous connecter, vous créez le relais métier et entrez les détails relatifs à votre système métier. À cette occasion, vous entrez également le nom et la clé de l'émetteur Service Bus.

### Pour récupérer le nom et la clé de l'émetteur Service Bus

1. Connectez-vous au [portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Dans le volet de navigation de gauche, cliquez sur **Service Bus**.
3. Cliquez sur votre espace de noms. Dans la barre des tâches, cliquez sur **Informations de connexion**. Cette action affiche l'**Émetteur par défaut** (Nom de l'émetteur) et la **Clé par défaut** (Clé de l'émetteur). Leurs valeurs peuvent être copiées.<br/><br/>
Pour résumer :<br/>
Nom de l'émetteur = émetteur par défaut<br/>
Clé de l'émetteur = clé par défaut

## Suivant
Autres rubriques Azure BizTalk Services :

-  [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Didacticiels : Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## Voir aussi
-  [ Utilisation du service de gestion ACS pour configurer des identités de service](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard, et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services : Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services : Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services : Sauvegarde et restauration](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services : Limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
