<properties
	pageTitle="Configurer un nom de domaine personnalisé dans Azure App Service (GoDaddy)"
	description="Apprenez à utiliser un nom de domaine à partir de GoDaddy avec Azure Web Apps"
	services="app-service\web"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="erikre"/>

# Configurer un nom de domaine personnalisé dans Azure App Service (acheté directement sur GoDaddy)

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Si vous avez acheté un domaine via Azure App Service Web Apps, reportez-vous à l’étape finale de l’article <a href="/documentation/articles/custom-dns-web-site-buydomains-web-app" title="Web Apps" class="current">Acheter un domaine pour Web Apps</a>.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

Cet article explique comment utiliser un nom de domaine personnalisé acheté directement auprès de [GoDaddy](https://godaddy.com) avec [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##Présentation des enregistrements DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## Ajout d'un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à une application web dans App Service, vous devez ajouter une nouvelle entrée pour ce domaine dans la table DNS en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1. Connectez-vous à votre compte GoDaddy.com et sélectionnez **My Account**, puis **Manage my domains**. Enfin dans le menu déroulant, sélectionnez le nom de domaine à utiliser avec votre application web Azure, puis sélectionnez **Manage DNS**.

	![page des domaines personnalisés de GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. Dans la page **Domain details**, faites défiler jusqu'à l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine.

	![Onglet DNS Zone File](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	Sélectionnez **Add Record** pour ajouter un enregistrement existant.

	Pour **modifier** un enregistrement existant, sélectionnez l'icône représentant un stylo et du papier à côté de l'enregistrement souhaité.

	> [AZURE.NOTE]Avant d’ajouter de nouveaux enregistrements, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines populaires (sous **Host** dans l’éditeur), par exemple **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

4. Quand vous ajoutez un enregistrement, vous devez d'abord sélectionner son type.

	![sélectionner le type d'enregistrement](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	Vous devez ensuite indiquer l'**Host** (domaine ou sous-domaine personnalisé) et sa cible dans **Points to**.

	![ajouter un enregistrement de zone](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Durant l’ajout d’un enregistrement (hôte A) (**A (host) record**) , vous devez attribuer au champ **Host** la valeur **@** (correspondant au nom de domaine racine, par exemple **contoso.com**), la valeur * (caractère générique correspondant à plusieurs sous-domaines) ou le sous-domaine à utiliser (par exemple, **www**.) Dans le champ **Points to**, indiquez l’adresse IP de votre application web Azure.

		> [AZURE.NOTE]Quand vous utilisez des enregistrements A (hôte), vous devez également ajouter un enregistrement CNAME avec la configuration suivante :
		>
		> * champ **Host** contenant la valeur **awverify** dont le champ **Points to** contient **awverify.&lt;yourwebappname&gt;.azurewebsites.net**.
		>
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Quand vous ajoutez un **CNAME (alias) record**, vous devez définir le champ **Host** en fonction du sous-domaine à utiliser. Par exemple, **www**. Dans le **Points to**, indiquez le nom de domaine **.azurewebsites.net** de votre application web Azure. Par exemple, **contoso.azurwebsites.net**.


5. Une fois que vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Finish** pour enregistrer les changements.

<a name="enabledomain"></a>
## Activer le nom de domaine sur votre application web

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).
 

<!---HONumber=Sept15_HO3-->