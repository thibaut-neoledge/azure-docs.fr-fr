<properties
   pageTitle="Objets principal du service et application"
   description="Discussion sur la relation entre les objets Application et ServicePrincipal dans Azure Active Directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/08/2015"
   ms.author="mbaldwin"/>


# Objets principal du service et application

Le schéma ci-dessus décrit la relation entre un objet application et un objet principal du service dans le contexte d’un exemple d’application appelée **Application RH**. Trois clients sont disponibles : **Adatum**, le client qui développe l’application, ainsi que **Contoso** et **Fabrikam**, les clients qui utilisent l’application nommée **Application RH**.

![Relation entre un objet Application et un objet ServicePrincipal](./media/active-directory-application-objects/application-objects-relationship.png)


Quand vous inscrivez une application dans le portail de gestion Azure, deux objets sont créés dans le client de votre annuaire :

- **Objet application** : cet objet représente une définition de votre application. Une description détaillée de ses propriétés est fournie dans la section **Objet application** ci-dessous.

- **Objet principal du service** : cet objet représente une instance de votre application dans le client de votre annuaire. Vous pouvez appliquer des stratégies à des objets principal du service, notamment en attribuant des autorisations au principal du service qui permettent à l’application de lire les données d’annuaire de votre client. Chaque fois que vous modifiez votre objet application, les modifications sont également appliquées à l’objet principal du service associé dans votre client.


> [AZURE.NOTE]Si votre application est configurée pour l’accès externe, les modifications apportées à l’objet application ne sont pas répercutées dans le principal du service d’un client consommateur tant que ce dernier n’a pas supprimé, puis accordé de nouveau l’accès.
 


Dans le schéma ci-dessus, l’étape 1 correspond au processus de création des objets application et principal du service.

À l’étape 2, quand un administrateur d’entreprise accorde l’accès, un objet principal du service est créé dans le client Azure AD de l’entreprise et il reçoit le niveau d’accès à l’annuaire que l’administrateur d’entreprise a accordé.

À l’étape 3, les clients consommateurs d’une application (comme Contoso et Fabrikam) possèdent chacun leur propre objet principal du service qui représente leur instance de l’application. Dans cet exemple, ils ont chacun un principal du service qui représente l’application RH.
 




## Propriétés de l’objet application

Le tableau suivant répertorie toutes les propriétés d’un objet application et inclut des informations importantes pour les développeurs. Ces propriétés s’appliquent aux applications web, aux API web et aux applications clientes natives qui sont inscrites auprès d’Azure AD.

 
### Généralités

Propriété | Description
| ------------- | ----------- 
| Nom | Nom complet de l’application. Propriété obligatoire dans l’Assistant **Ajout d’application**.
| Logo | Logo de l’application qui représente votre application ou entreprise. Ce logo permet aux utilisateurs externes d’associer plus facilement la demande d’accès à votre application. Quand vous téléchargez un logo, respectez les spécifications indiquées dans l’Assistant **Télécharger un logo**. Si vous ne fournissez pas de logo, un logo par défaut s’affiche.
| Accès externe | Détermine si les utilisateurs d’organisations externes sont autorisés à accorder à votre application une authentification unique et un accès aux données incluses dans l’annuaire de leur organisation. Ce contrôle affecte uniquement la possibilité d’accorder l’accès. Il ne modifie pas l’accès qui a déjà été accordé. Seuls les administrateurs d’entreprise peuvent accorder l’accès.
 

### Authentification unique
 
Propriété | Description
| ------------- | ----------- 
| URI ID d’application | Identificateur logique unique de votre application. Propriété obligatoire dans l’Assistant **Ajout d’application**. <br><br>Étant donné que la valeur URI ID d’application est un identificateur logique, il est inutile de la résoudre en adresse Internet. Votre application la présente lors de l’envoi d’une demande d’authentification unique à Azure AD. Azure AD identifie votre application et envoie la réponse d’authentification (un jeton SAML) à la valeur URL de réponse qui a été fournie pendant l’inscription de l’application. Utilisez la valeur URI ID d’application pour définir la propriété wtrealm (pour WS-Federation) ou la propriété Issuer (pour SAML-P) lors d’une demande de connexion. La valeur **URI ID d’application** doit être unique dans Azure AD au sein de votre organisation<br><br>**Remarque** : lors de l’activation d’une application pour des utilisateurs externes, la valeur URI ID d’application de l’application doit être une adresse comprise dans un des domaines vérifiés de votre annuaire. Par conséquent, il ne peut pas s’agir d’un URN (Uniform Resource Name). Cette restriction empêche d’autres organisations de spécifier (et de prendre) une propriété unique qui appartient à votre organisation. Pendant le développement, vous pouvez remplacer votre valeur URI ID d’application par un emplacement situé dans le domaine initial de votre organisation (si vous n’avez pas vérifié un domaine personnalisé/personnel) et mettre à jour votre application afin d’utiliser cette nouvelle valeur. Le domaine initial correspond au domaine de niveau 3 que vous créez au moment de l’inscription, par exemple contoso.onmicrosoft.com.
| URL de l’application | Adresse d’une page web où les utilisateurs peuvent se connecter et utiliser votre application. Propriété obligatoire dans l’Assistant **Ajout d’application**.<br><BR>**Remarque** : la valeur définie pour cette propriété dans l’Assistant Ajout d’application est également définie en tant que valeur de l’URL de réponse.
| URL de réponse | Adresse physique de votre application. Azure AD envoie un jeton avec la réponse d’authentification unique à cette adresse. Pendant la première inscription dans l’Assistant **Ajout d’application**, la valeur définie pour URL de l’application l’est également en tant que valeur URL de réponse. Lors d’une demande de connexion, utilisez la valeur URL de réponse pour définir la propriété wreply (pour WS-Federation) ou **AssertionConsumerServiceURL** (pour SAML-P).<br><BR>**Remarque** : lors de l’activation d’une application pour des utilisateurs externes, URL de réponse doit être une adresse **https://**. | URL des métadonnées de fédération | (Facultatif). Représente l’URL physique du document des métadonnées de fédération de votre application. Celle-ci est obligatoire pour prendre en charge la déconnexion SAML-P. Azure AD télécharge le document des métadonnées qui est hébergé sur ce point de terminaison et l’utilise pour découvrir la partie publique du certificat que vous utilisez pour vérifier la signature sur vos demandes de déconnexion et l’URL de déconnexion de votre application. Vous ne pouvez pas configurer cette propriété quand vous ajoutez d’abord votre application. Elle ne peut être configurée qu’ultérieurement.<br><BR>****Remarque** : si vous avez besoin de prendre en charge la déconnexion SAML-P, mais que vous n’avez pas de point de terminaison des métadonnées de fédération pour votre application, contactez le support technique pour connaître les autres possibilités.
 

### Appel de l’API Graph ou d’API web
 
Propriété | Description
| ------------- | ----------- 
| ID client | Identificateur unique de votre application. Vous devez utiliser cet identificateur dans les appels à l’API Graph ou à d’autres API web inscrites auprès d’Azure AD. Azure AD génère automatiquement cette valeur pendant l’inscription de l’application et vous ne pouvez pas la modifier.<BR><BR>Pour permettre à votre application d’accéder à l’annuaire (pour l’accès en lecture ou en écriture) via l’API Graph, vous avez besoin d’un ID client et d’une clé (appelée clé secrète client dans OAuth 2.0). Votre application utilise l’ID client et la clé pour demander un jeton d’accès auprès du point de terminaison de jeton Azure AD OAuth 2.0. (Pour afficher tous les points de terminaison Azure AD, dans la barre de commandes, cliquez sur **Afficher les points de terminaison**.) Quand vous utilisez l’API Graph pour obtenir ou définir (modifier) les données d’annuaire, votre application utilise ce jeton d’accès dans l’en-tête Authorize de la demande à l’API Graph.
| Clés | Si votre application lit ou écrit des données dans Azure AD, notamment des données accessibles via l’API Graph, votre application a besoin d’une clé. Quand vous demandez un jeton d’accès pour appeler l’API Graph, votre application fournit son **ID Client** et sa **clé**. Le point de terminaison du jeton utilise l’ID et la clé pour authentifier votre application avant d’émettre le jeton d’accès. Vous pouvez créer plusieurs clés pour traiter les scénarios de substitution de clé. En outre, vous pouvez supprimer les clés arrivées à expiration, endommagées ou inutilisées.
| Gérer l’accès | Choisissez un des trois différents niveaux d’accès : authentification unique (SSO), authentification unique et lecture des données d’annuaire, ou authentification unique et lecture/écriture des données d’annuaire. Vous pouvez également supprimer l’accès. Pour plus d’informations sur l’accès à l’annuaire, consultez la page [Niveaux d’accès de l’application](https://msdn.microsoft.com/library/azure/b08d91fa-6a64-4deb-92f4-f5857add9ed8#BKMK_AccessLevels).<br><BR>**Remarque** : les modifications apportées au niveau d’accès à l’annuaire de votre application s’appliquent uniquement à votre annuaire. Elles ne s’appliquent pas aux clients qui ont accordé l’accès à votre application.
 
 
### Clients natifs
 
Propriété | Description
| ------------- | ----------- 
| URI de redirection | URI vers lequel Azure AD redirige l’agent utilisateur en réponse à une demande d’autorisation OAuth 2.0. La valeur n’a pas besoin d’être un point de terminaison physique, mais doit être un URI valide.

##


 
 

<!---HONumber=62-->