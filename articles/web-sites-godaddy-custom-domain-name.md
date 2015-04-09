<properties 
	pageTitle="Configurer un nom de domaine personnalisé dans Azure App Service (GoDaddy)" 
	description="Apprenez à utiliser un nom de domaine à partir de GoDaddy avec Azure Web Apps" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Configurer un nom de domaine personnalisé dans Azure App Service (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Cet article explique comment utiliser un nom de domaine personnalisé acheté auprès de [Go Daddy](https://godaddy.com) avec [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Ajouter un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activer le domaine sur votre application web](#enabledomain)

<h2><a name="understanding-records"></a>Présentation des enregistrements DNS</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Ajouter un enregistrement DNS pour votre domaine personnalisé</h2>

Pour associer votre domaine personnalisé à une application web dans App Service, vous devez ajouter une nouvelle entrée pour ce domaine dans la table DNS en utilisant les outils fournis par GoDaddy. Pour trouver les outils DNS pour GoDaddy.com, procédez comme suit.

1. Connectez-vous à votre compte GoDaddy.com, sélectionnez **My Account**, puis **Manage my domains**. Enfin, sélectionnez le menu déroulant du nom de domaine à utiliser avec votre application web Azure, puis sélectionnez **Manage DNS**.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Dans la page **Domain details**, accédez à l'onglet **DNS Zone File**. Il s'agit de la section qui vous permet d'ajouter et de modifier des enregistrements DNS pour votre nom de domaine. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Sélectionnez **Add Record** pour ajouter un enregistrement existant.

	Pour **modifier** un enregistrement existant, sélectionnez l'icône représentant un style et du papier à côté de l'enregistrement souhaité.

	> [AZURE.NOTE] Avant d'ajouter de nouveaux enregistrements, notez que GoDaddy a déjà créé des enregistrements DNS pour les sous-domaines fréquemment utilisés (sous **Host** dans l'éditeur), par exemple **email**, **files**, **mail**, etc. Si le nom que vous souhaitez utiliser existe déjà, modifiez l'enregistrement existant plutôt que d'en créer un nouveau.

4. Quand vous ajoutez un enregistrement, vous devez d'abord sélectionner son type.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Renseignez ensuite le champ **Host** (domaine ou sous-domaine personnalisé) et sa cible dans le champ **Points to**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Lorsque vous ajoutez un enregistrement A (hôte) via **A (host) record**, vous devez définir le champ **Host** sur la valeur **@** (qui correspond au nom de domaine racine, par exemple **contoso.com**,) sur la valeur * (caractère générique pour la correspondance avec plusieurs sous-domaines) ou sur le sous-domaine à utiliser (par exemple, **www**). Définissez le champ **Points to** sur l'adresse IP de votre application web Azure.
	
		> [AZURE.NOTE] Quand vous utilisez des enregistrements A (hôte), vous devez également ajouter un enregistrement CNAME avec la configuration suivante :
		> 
		> * champ **Host** présentant la valeur **awverify** dont le champ **Points to** est défini sur **awverify.&lt;VotreApplicationWeb&gt;.azurewebsites.net**.
		> 
		> Cet enregistrement CNAME permet à Azure de confirmer que vous possédez le domaine décrit par l'enregistrement A.

	* Lorsque vous ajoutez un enregistrement CNAME(alias) via **CNAME (alias) record**, vous devez définir le champ **Host** sur le sous-domaine à utiliser. Par exemple, **www**. Définissez le champ **Points to** sur le nom de domaine **.azurewebsites.net** de votre application web Azure. Par exemple, **contoso.azurwebsites.net**.


5. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements, cliquez sur **Finish** pour enregistrer les modifications.

<h2><a name="enabledomain"></a>Activer le nom de domaine sur votre application web</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->