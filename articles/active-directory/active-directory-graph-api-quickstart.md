<properties
   pageTitle="Démarrage rapide pour l'API Graph Azure AD | Microsoft Aure"
   description="L’API Graph Azure Active Directory fournit un accès par programme à Azure AD via les points de terminaison de l’API REST OData. Les applications peuvent utiliser l'API Graph pour effectuer des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les données et objets d'annuaire."
   services="active-directory"
   documentationCenter="n/a"
   authors="JimacoMS"
   manager="msmbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="08/24/2015"
      ms.author="v-jibran@microsoft.com"/>

# Démarrage rapide pour l'API Graph Azure AD

L’API Graph Azure Active Directory (AD) donne accès par programme à Azure AD via les points de terminaison de l’API REST OData. Les applications peuvent utiliser l'API Graph pour effectuer des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les données et objets d'annuaire. Par exemple, vous pouvez utiliser l'API Graph pour créer un nouvel utilisateur, afficher ou mettre à jour ses propriétés, modifier son mot de passe, vérifier l'appartenance au groupe d'accès basé sur un rôle, et désactiver ou supprimer l'utilisateur. Pour plus d'informations sur les fonctionnalités de l'API Graph et les scénarios d'application, consultez [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) et [Configuration requise pour l'API Graph Azure AD](https://msdn.microsoft.com/library/azure/hh974464.aspx).

## Construction d'une URL d’API Graph

Dans l'API Graph, pour accéder aux données et objets d'annuaire (en d'autres termes, les ressources ou les entités) sur lesquels vous souhaitez effectuer des opérations CRUD, vous pouvez utiliser des URL basées sur le protocole OData (Open Data). Les URL utilisées dans l'API Graph se composent de quatre parties principales : racine de service, identificateur de locataire, chemin d'accès de ressource et options de chaîne de requête : `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Prenons l'exemple de l'URL suivante : `https://graph.windows.net/contoso.com/groups?api-version=1.5`.

- **Racine du service** : dans l'API Graph Azure AD, la racine du service est toujours https://graph.windows.net.
- **Identificateur de locataire** : cela peut être un nom de domaine (inscrit) vérifié, dans l'exemple ci-dessus, contoso.com. Cela peut également être un ID d'objet locataire ou l’alias « myorganiztion » ou « me ». Pour plus d'informations, consultez [Traitement des entités et opérations dans l'API graphique](https://msdn.microsoft.com/library/azure/dn424880.aspx)).
- **Chemin d'accès de ressource** : cette section d'une URL identifie la ressource avec laquelle interagir (utilisateurs, groupes, utilisateur ou groupe spécifique, etc.) Dans l'exemple ci-dessus, il s’agit de « groupes de niveau supérieur » auquel adresser ce jeu de ressources. Vous pouvez également adresser une entité spécifique, par exemple « users/{objectId} » ou « users/userPrincipalName ».
- **Paramètres de requête** : ? sépare la section du chemin d'accès de ressource de la section des paramètres de requête. Le paramètre de requête « api-version » est requis sur toutes les demandes dans l'API Graph. L'API Graph prend également en charge les options de requête OData suivantes : **$filter**, **$orderby**, **$expand**, **$top** et **$format**. Les options de requête suivantes ne sont pas prises en charge actuellement : **$count**, **$inlinecount** et **$skip**. Pour plus d'informations, consultez [Options de requêtes, de filtres et de pagination prises en charge dans l'API Azure AD Graph](https://msdn.microsoft.com/library/azure/dn727074.aspx).

## Versions d'API Graph

Les versions suivantes ont été publiées pour l'API Graph.

* Version bêta
* Version 1.5
* Version 2013-11-08
* Version 2013-04-05

Vous spécifiez la version pour une demande d'API Graph dans le paramètre de requête « api-version ». Pour la version 1.5, utilisez une valeur numérique de version ; api-version=1.5. Pour les versions précédentes, utilisez une chaîne de date au format AAAA-MM-JJ ; par exemple, api-version=2013-11-08. Pour les fonctionnalités en version préliminaire, utilisez la chaîne « beta » ; par exemple, api-version=beta. Pour plus d'informations sur les différences entre les versions de l'API Graph, consultez [Contrôle de version de l'API graphique Azure AD](https://msdn.microsoft.com/library/azure/dn835125.aspx).

## Métadonnées d'API Graph

Pour renvoyer le fichier de métadonnées de l’API Graph, ajoutez le segment « $metadata » après l'identificateur de locataire dans l'URL. Par exemple, l'URL suivante renvoie des métadonnées sur la société de démonstration utilisée par l'explorateur graphique : `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.5`. Vous pouvez entrer cette URL dans la barre d'adresses d'un navigateur Web pour afficher les métadonnées. Le document de métadonnées CSDL renvoyé décrit les entités et les types complexes, leurs propriétés et les fonctions et actions exposées par la version de l'API Graph demandée. L'omission du paramètre api-version a pour effet de renvoyer des métadonnées pour la version la plus récente.

## Requêtes courantes

[Requêtes courantes de l'API Azure AD Graph](https://msdn.microsoft.com/library/azure/jj126255.aspx) répertorie des requêtes courantes qui peuvent être utilisées avec Azure AD Graph, y compris des requêtes permettant d’accéder aux ressources de niveau supérieur dans votre annuaire, et d’y effectuer des opérations.

Par exemple, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.5` renvoie des informations sur la société contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.5` répertorie tous les objets utilisateur dans l’annuaire contoso.com.

## Utilisation de l'explorateur graphique

Vous pouvez utiliser l'explorateur graphique pour l'API Graph Azure AD pour interroger les données d'annuaire lorsque vous générez votre application.

> [AZURE.IMPORTANT]L'explorateur graphique ne prend pas en charge l’écriture ou la suppression de données de l’annuaire. Il permet uniquement d’effectuer des opérations de lecture sur votre annuaire Azure AD.

Voici la sortie que vous verriez si vous deviez accéder à l'explorateur graphique, sélectionniez Utiliser la société de démonstration puis entriez `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.5` pour afficher tous les utilisateurs figurant dans le répertoire de démonstration :

![explorateur api graph Azure AD](./media/active-directory-graph-api-quickstart/screen_shot.jpg)

**Chargez l'explorateur graphique** : pour charger l'outil, accédez à [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Cliquez sur **Utiliser la société de démonstration** pour exécuter l'explorateur graphique sur les données d’un exemple de client. Vous n'avez pas besoin d'informations d'identification pour utiliser la société de démonstration. Vous pouvez également cliquer sur **Connexion** pour vous connecter avec vos informations d'identification de compte Azure AD afin d’exécuter l'explorateur graphique sur votre client. Si vous exécutez l'explorateur graphique sur votre propre client, vous ou votre administrateur devrez donner votre consentement lors de la connexion. Si vous avez un abonnement à Office 365, vous disposez automatiquement d'un client Azure AD. Les informations d'identification que vous utilisez pour vous connecter à Office 365 sont, en fait, des comptes Azure AD. Vous pouvez les utiliser avec l'explorateur graphique.

**Exécutez une requête** : pour exécuter une requête, entrez votre requête dans la zone de texte de requête, puis cliquez sur **GET** ou sur le bouton **Enter**. Les résultats s’affichent dans la zone de réponse. Par exemple, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.5` affiche la liste de tous les objets de groupe figurant dans l’annuaire de démonstration.

Notez les fonctionnalités et les limitations de l'Explorateur de graphique suivantes : Fonctionnalité de saisie semi-automatique sur des jeux de ressources. Pour voir celle-ci en action, cliquez sur **Utiliser la société de démonstration**, puis cliquez sur la zone de texte de requête (où figure l'URL de la société). Vous pouvez sélectionner un jeu de ressources dans la liste déroulante.

- Prend en charge les alias d’adressage « me » et « myorganization ». Par exemple, vous pouvez utiliser `https://graph.windows.net/me?api-version=1.5` pour renvoyer l'objet utilisateur de l'utilisateur connecté, ou `https://graph.windows.net/myorganization/users?api-version=1.5` pour renvoyer tous les utilisateurs dans le répertoire actif. Notez que l’utilisation de l’alias « me » renvoie une erreur pour la société de démonstration, car aucun utilisateur connecté ne fait la demande.

- Section d’en-tête de réponse. Celle-ci facilite le dépannage de problèmes qui se produisent lors de l'exécution des requêtes.

- Visionneuse JSON pour la réponse, avec des capacités de développement et de réduction.

- Aucune prise en charge de l'affichage d'une photo miniature.

## Utilisation de Fiddler pour écrire dans l’annuaire

Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser le débogueur web Fiddler pour les opérations « d’écriture » dans votre annuaire Azure AD. Pour plus d'informations et pour installer Fiddler, consultez [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Dans l'exemple ci-dessous, vous allez utiliser le débogueur web Fiddler pour créer un nouveau groupe de sécurité « MyTestGroup » dans votre annuaire Azure AD.

**Obtenez un jeton d'accès** : pour accéder à Azure AD Graph, les clients doivent tout d’abord s'authentifier auprès d'Azure AD. Pour plus d’informations, consultez la page [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

**Composez et exécutez une requête** : procédez comme suit :

1. Ouvrez le débogueur web Fiddler et basculez vers l’onglet **Composer**.
2. Dans la mesure où vous souhaitez créer un groupe de sécurité, sélectionnez **Publier** comme méthode HTTP dans le menu déroulant. Pour plus d'informations sur les opérations et les autorisations relatives à un objet de groupe, consultez [Groupe](https://msdn.microsoft.com/library/azure/hh974486.aspx) dans [Références de l'API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Dans le champ en regard de **Publier**, entrez ce qui suit comme URL de demande : `https://graph.windows.net/mytenantdomain/groups?api-version=1.5`.

    > [AZURE.NOTE]Vous devez remplacer mytenantdomain par le nom de domaine de votre propre annuaire Azure AD.

4. Dans le champ situé sous le menu déroulant Publier, entrez ce qui suit :

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE]Remplacez votre &lt;votre jeton d'accès&gt; par le jeton d'accès de votre annuaire Azure AD.

5. Dans le champ **Corps de la demande**, entrez ce qui suit :

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Pour plus d'informations sur la création de groupes, consultez [Création d’un groupe](https://msdn.microsoft.com/library/azure/dn151614.aspx).

Pour plus d'informations sur les entités et les types Azure AD exposés par Graph, ainsi que des informations sur les opérations exécutables sur ceux-ci Graph, consultez [Référence de l'API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## Étapes suivantes

En savoir plus sur l’[API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

<!---HONumber=Oct15_HO3-->