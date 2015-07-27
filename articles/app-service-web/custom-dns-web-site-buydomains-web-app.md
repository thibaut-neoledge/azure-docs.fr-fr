
<properties
	pageTitle="Comment acheter un nom de domaine personnalisé dans Azure App Service Web Apps"
	description="Découvrez comment acheter un nom de domaine personnalisé avec une application web dans Azure App Service."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/21/2015"
	ms.author="mwasson"/>

# Acheter et configurer un nom de domaine personnalisé dans Azure App Service

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">Acheter un domaine pour Web Apps</a> <a href="/documentation/articles/web-sites-custom-domain-name" title="Web Apps">Web Apps avec des domaines externes</a> <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps avec Traffic Manager">Web Apps avec Traffic Manager</a> <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>


</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

Lorsque vous créez une application web, Azure l'attribue à un sous-domaine de azurewebsites.net. Par exemple, si votre application web se nomme **contoso**, l'URL est **contoso.azurewebsites.net**. Azure attribue également une adresse IP virtuelle.

Pour une application web de production, vous souhaitez probablement afficher un nom de domaine personnalisé pour vos utilisateurs. Cet article explique comment acheter et configurer un domaine personnalisé avec [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Vue d'ensemble

> [AZURE.NOTE]N'essayez pas d'acheter un domaine avec un abonnement qui n'est pas associé à une carte de crédit active. Cela pourrait entraîner la désactivation de votre abonnement.

Si vous n'avez pas de nom de domaine pour votre application web, vous pouvez facilement en acheter un sur le [portail de gestion Azure](https://portal.azure.com). Pendant le processus d'achat, vous pouvez choisir de mapper automatiquement les enregistrements DNS du domaine racine et WWW à votre application web. Vous pouvez également gérer votre domaine directement dans le portail Azure.


Procédez comme suit pour acheter des noms de domaine et les attribuer à votre application web.

1. Dans votre navigateur, ouvrez le [portail de gestion Azure](https://portal.azure.com).

2. Sous l’onglet **Web Apps**, cliquez sur le nom de votre application web et sélectionnez **Paramètres**, puis **Domaines personnalisés et SSL**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Dans le volet **Domaines personnalisés et SSL**, cliquez sur **Acheter des domaines**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Dans le volet **Acheter des domaines**, utilisez la zone de texte pour taper le nom de domaine que vous voulez acheter et appuyez sur Entrée. Les domaines disponibles proposés s'affichent simplement dans la zone de texte. Sélectionnez le domaine que vous souhaitez acheter. Vous pouvez choisir d'acheter plusieurs domaines à la fois.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Cliquez sur **Informations de contact** et remplissez le formulaire d'informations de contact du domaine.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE]Il est très important de remplir tous les champs obligatoires aussi précisément que possible, notamment l'adresse de messagerie. Si vous achetez un domaine sans « Protection de confidentialité », vous devrez peut-être vérifier votre adresse de messagerie avant l'activation du domaine. Dans certains cas, l'inexactitude des informations de contact fournies peuvent empêcher l'achat des domaines.

6. À présent, vous pouvez choisir l'une des options suivantes :

	a) « Renouveler automatiquement » votre domaine chaque année
	
	b) « Protection de confidentialité », incluse GRATUITEMENT dans le prix d'achat
	
	c) « Attribuer des noms d'hôte par défaut » pour le domaine racine et WWW dans l'application web actuelle.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE]L'option C configure automatiquement les liaisons DNS et de nom d'hôte. De cette façon, votre application web est accessible à l'aide du domaine personnalisé dès que l'achat est terminé (sous réserve des délais de propagation DNS dans certains cas). Si votre application web se trouve derrière Azure Traffic Manager, l'option permettant d'attribuer un domaine racine n'est pas affichée, car les enregistrements A ne fonctionnent pas avec Traffic Manager.
>
>Vous pouvez toujours attribuer les domaines/sous-domaines que vous avez achetés pour une application web à une autre, et vice versa. Consultez l'étape 8 pour plus d'informations.

	
7. Cliquez sur **Sélectionner** dans le volet **Acheter des domaines**, les informations d'achat s'affichent dans le volet **Confirmation d'achat**. Si vous acceptez les conditions juridiques et cliquez sur **Acheter**, votre commande est envoyée et vous pouvez surveiller le processus d'achat dans **Notification**. L'achat de domaine peut prendre plusieurs minutes. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Si vous avez correctement commandé un domaine, vous pouvez gérer le domaine et l'attribuer à votre application web. Cliquez sur **« ... »**, à droite de votre domaine. Vous pouvez **Annuler l'achat** ou **Gérer le domaine**. Cliquez sur **Gérer le domaine**. Dans ce volet pour pouvez choisir de lier un **sous-domaine** à votre application web. Si vous souhaitez lier un **sous-domaine** à une autre application web, effectuez cette étape dans le contexte de l'application web correspondante. À partir de là, vous pouvez choisir d'attribuer le domaine au point de terminaison Traffic Manager (si l'application web se trouve derrière TM) en sélectionnant simplement le nom Traffic Manager dans le menu déroulant. De cette manière, le domaine/sous-domaine est automatiquement attribué à toutes les applications web situées derrière ce point de terminaison Traffic Manager. 

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE]Vous pouvez « Annuler l'achat » dans les 5 jours pour obtenir un remboursement intégral. Au bout de 5 jours, vous ne pouvez plus « Annuler l'achat », mais vous pouvez « Supprimer » le domaine. La suppression du domaine le retire de votre abonnement sans remboursement, et le domaine devient disponible.

	Once configuration has completed, the custom domain name will be listed in the **Hostname bindings** section of your web app.

À ce stade, vous devez pouvoir entrer le nom de domaine personnalisé dans votre navigateur et vérifier que vous êtes bien dirigé vers votre application web.
 

<!---HONumber=July15_HO3-->