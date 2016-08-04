<properties
  pageTitle="FAQ sur IoT Azure Suite | Microsoft Azure"
  description="Forum Aux Questions (FAQ) relatives à IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="06/27/2016"
  ms.author="araguila"/>
   
# Forum Aux Questions (FAQ) relatives à IoT Suite

### Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option supprimer d’une solution préconfigurée dans azureiotsuite.com ?

- Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée ; si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées.

- Si vous supprimez le groupe de ressources dans le [portail Azure][lnk-azure-portal], vous supprimez uniquement les ressources présentes dans ce groupe de ressources. Vous devrez également supprimer l’application Azure Active Directory associée à la solution préconfigurée dans le [portail Azure Classic][lnk-classic-portal].

### Combien d’instances d’IoT Hub puis-je configurer dans un abonnement ? 

Dix. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que dix instances IoT Hub par abonnement, comme indiqué dans l’article concernant les [limites d’abonnement Azure][link-azuresublimits]. Par conséquent, étant donné que chaque solution préconfigurée approvisionne un nouvel IoT Hub, vous ne pouvez configurer que dix solutions préconfigurées maximum dans un abonnement.

### Combien d’instances de DocumentDB puis-je configurer dans un abonnement ?

Cinquante. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que cinquante instances de DocumentDB par abonnement.

### Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de surveillance à distance est configurée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### J’ai mis en place le déploiement d’une solution de surveillance à distance avec une carte statique. Comment faire pour ajouter une carte Bing interactive ? 
1. Obtenez votre QueryKey Bing Maps API pour Entreprise sur le [portail Azure][lnk-azure-portal] \:
 1. Accédez au groupe de ressources contenant Bing Maps API pour Entreprise dans le [portail Azure][lnk-azure-portal].
 2. Cliquez sur Tous les paramètres, puis sur Gestion des clés.
 3. Vous remarquerez la présence de deux clés : MasterKey et QueryKey. Copiez la valeur de QueryKey.

     > [AZURE.NOTE] Vous n’avez aucun compte Bing Maps API pour Entreprise ? Créez-le sur le [portail Azure][lnk-azure-portal] en cliquant sur + Nouveau, en recherchant Bing Maps API pour Entreprise et en suivant la procédure.

2. Déroulez le code le plus récent dans [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Effectuez un déploiement local ou cloud selon les instructions de déploiement par ligne de commande dans le dossier /docs/ du référentiel.

4. Une fois le déploiement local ou cloud effectué, recherchez dans votre dossier racine le fichier *.user.config créé. Ouvrez ce fichier dans un éditeur de texte.

5. Modifiez la ligne suivante en y incluant la valeur que vous avez copiée pour votre clé QueryKey :
   
  `<setting name="MapApiQueryKey" value="" />`

### Puis-je créer une solution préconfigurée si je dispose de Microsoft Azure pour DreamSpark ?
Pour le moment, il est impossible de créer une solution préconfigurée avec un compte [Microsoft Azure pour DreamSpark][lnk-dreamspark]. Vous pouvez toutefois créer en quelques minutes un [compte d’évaluation Azure gratuit][lnk-30daytrial], que vous pourrez utiliser pour créer une solution préconfigurée.

### Comment supprimer un client AAS ?

Consultez billet de blog de Eric Golpe [Procédure pas à pas de la suppression d’un client Azure AD][lnk-delete-aad-tennant].

## Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

- [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-overview]
- [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0727_2016-->