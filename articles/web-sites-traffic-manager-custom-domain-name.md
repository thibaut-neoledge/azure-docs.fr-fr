<properties 
	pageTitle="Configuration d'un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager" 
	description="Utilisation d'un nom de domaine personnalisé pour une application web dans Azure App Service intégrant Traffic Manager pour équilibrer la charge." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Configuration d'un nom de domaine personnalisé pour une application web dans Azure App Service utilisant Traffic Manager

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Cet article contient des instructions génériques sur l'utilisation d'un nom de domaine personnalisé avec Azure App Service utilisant Traffic Manager pour équilibrer la charge.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Dans cet article :

-   [Présentation des enregistrements DNS](#understanding-records)
-   [Configuration de vos applications web en mode Standard](#bkmk_configsharedmode)
-   [Ajout d'un enregistrement DNS pour votre domaine personnalisé](#bkmk_configurecname)
-   [Activation de Traffic Manager pour votre application web](#enabledomain)

<a name="understanding-records"></a>
## Présentation des enregistrements DNS

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Configuration de vos applications web en mode Standard

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Ajout d'un enregistrement DNS pour votre domaine personnalisé

Pour associer votre domaine personnalisé à une application web dans Azure App Service, vous devez ajouter une nouvelle entrée pour votre domaine personnalisé dans la table DNS, en utilisant les outils fournis par le bureau d'enregistrement de domaines auprès duquel vous avez acheté votre nom de domaine. Pour localiser et utiliser les outils DNS, procédez comme suit.

1. Connectez-vous à votre compte du bureau d'enregistrement de domaines, puis recherchez une page pour la gestion des enregistrements DNS. Recherchez des liens ou des zones indiquant **Nom de domaine**, **DNS** ou **Name Server Management**. Vous trouverez généralement un lien vers cette page en affichant vos informations de compte, puis en recherchant un lien tel que **Mes domaines**.

4. Lorsque vous avez trouvé la page de gestion de votre nom de domaine, recherchez un lien vous permettant de modifier les enregistrements DNS. Il est probablement répertorié en tant que lien de configuration de **fichier de zone**, d'**enregistrements DNS**, ou **avancé**.

	* Il est possible que plusieurs enregistrements soient déjà créés pour cette page, par exemple une entrée associant " **@** " ou " * " à une page  'domain parking'. Cette page peut également contenir des enregistrements pour des sous-domaines courants, tels que **www**.
	* La page mentionnera les **enregistrements CNAME** ou fournira une liste déroulante permettant de sélectionner un type d'enregistrement. Elle peut également mentionner d'autres enregistrements, tels que des **enregistrements A** et des **enregistrements MX**. Dans certains cas, les enregistrements CNAME sont appelés différemment, par exemple **Enregistrement d'alias**.
	* Cette page comprend également des champs permettant de **mapper** à partir d'un **nom d'hôte** ou d'un **nom de domaine** vers un autre nom de domaine.

5. Même si chaque bureau d'enregistrement a ses particularités, vous pouvez généralement mapper *from* votre nom de domaine personnalisé (tel que **contoso.com**) *to* vers le nom de domaine Traffic Manager (**contoso.trafficmanager.net**) utilisé pour votre application web.

6. Lorsque vous avez fini d'ajouter ou de modifier des enregistrements DNS auprès de votre bureau d'enregistrement, enregistrez les modifications.

<a name="enabledomain"></a>
## Activation de Traffic Manager

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d'ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir des informations détaillées sur le passage de Sites Web à App Service, consultez : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir des informations détaillées sur le passage de l'ancien portail au nouveau portail, consultez : [Référence pour la navigation au sein du portail en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=52-->