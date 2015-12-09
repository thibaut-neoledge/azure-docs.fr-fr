<properties
  pageTitle="FAQ sur IoT Azure Suite | Microsoft Azure"
  description="Forum aux questions pour IoT Suite."
  services=""
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="na"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# Forum Aux Questions

### Combien d’instances de DocumentDB puis-je configurer dans un abonnement ?

Cinq. Vous pouvez créer un ticket de support pour augmenter cette limite, mais par défaut, vous pouvez uniquement configurer cinq instances de DocumentDB par abonnement. Par conséquent, vous pouvez configurer au maximum cinq solutions de surveillance à distance préconfigurées dans un abonnement.

### Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux API Bing Maps gratuites dans un abonnement. La solution de surveillance à distance est configurée par défaut avec une API Bing Maps gratuite. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option supprimer d’une solution préconfigurée dans azureiotsuite.com ?

- Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée ; si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées. 

- Si vous supprimez le groupe de ressources dans le [portail Azure][lnk-azure-portal], vous supprimez uniquement les ressources présentes dans ce groupe de ressources. Vous devrez également supprimer l’application Azure Active Directory associée à la solution préconfigurée dans le [portail Azure Classic][lnk-classic-portal].

### Comment supprimer un client AAS ?

Consultez billet de blog de Eric Golpe [Procédure pas à pas de la suppression d’un client Azure AD][lnk-delete-aad-tennant].


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=Nov15_HO4-->