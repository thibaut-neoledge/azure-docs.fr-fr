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
  ms.date="04/22/2016"
  ms.author="araguila"/>
   
# Forum Aux Questions (FAQ) relatives à IoT Suite

### Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option supprimer d’une solution préconfigurée dans azureiotsuite.com ?

- Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée ; si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées. 

- Si vous supprimez le groupe de ressources dans le [portail Azure][lnk-azure-portal], vous supprimez uniquement les ressources présentes dans ce groupe de ressources. Vous devrez également supprimer l’application Azure Active Directory associée à la solution préconfigurée dans le [portail Azure Classic][lnk-classic-portal].

### Combien d’instances de DocumentDB puis-je configurer dans un abonnement ?

Cinq. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez provisionner que cinq instances de DocumentDB par abonnement. Par conséquent, vous pouvez configurer au maximum cinq solutions de surveillance à distance préconfigurées dans un abonnement.

### Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux API Bing Maps gratuites dans un abonnement. La solution de surveillance à distance est configurée par défaut avec une API Bing Maps gratuite. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### J’ai mis en place le déploiement d’une solution de surveillance à distance avec une carte statique. Comment faire pour ajouter une carte Bing interactive ? 
1. Obtenez votre QueryKey Bing Maps API pour Entreprise sur le [portail Azure][lnk-azure-portal] \: 
 1. Accédez au groupe de ressources contenant Bing Maps API pour Entreprise dans le [portail Azure][lnk-azure-portal].
 2. Cliquez sur Tous les paramètres, puis sur Gestion des clés. 
 3. Vous remarquerez la présence de deux clés : MasterKey et QueryKey. Copiez la valeur de QueryKey.

     > [AZURE.NOTE] Vous n’avez aucun compte Bing Maps API pour Entreprise ? Créez-le sur le [portail Azure][lnk-azure-portal] en cliquant sur + Nouveau, en recherchant API Bing Maps API pour Entreprise et en suivant la procédure.

2. Déroulez le code le plus récent dans [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Effectuez un déploiement local ou cloud selon les instructions de déploiement par ligne de commande dans le dossier /docs/ du référentiel.

4. Une fois le déploiement local ou cloud effectué, recherchez dans votre dossier racine le fichier *.user.config créé. Ouvrez ce fichier dans un éditeur de texte.

5. Modifiez la ligne suivante en y incluant la valeur que vous avez copiée pour votre clé QueryKey :
   
  `<setting name="MapApiQueryKey" value="" />`


### Comment supprimer un client AAS ?

Consultez billet de blog de Eric Golpe [Procédure pas à pas de la suppression d’un client Azure AD][lnk-delete-aad-tennant].

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0427_2016-->