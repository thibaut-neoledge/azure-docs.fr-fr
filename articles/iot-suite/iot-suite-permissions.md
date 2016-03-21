<properties
  pageTitle="Suite IoT Azure et Azure Active Directory | Microsoft Azure"
  description="Décrit comment Azure IoT Suite utilise Azure Active Directory pour gérer les autorisations."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="03/02/2016"
  ms.author="araguila"/>
  
# Autorisations sur le site azureiotsuite.com

## Que se passe-t-il lorsque vous vous connectez ?

Lorsque vous vous connectez pour la première fois à [azureiotsuite.com][lnk-azureiotsuite], le site détermine vos niveaux d’autorisation en fonction du client Azure Active Directory (AAD) et de l’abonnement Azure sélectionné.

1.  Le site commence par rechercher dans Azure les clients AAD auxquels vous appartenez afin de remplir la liste des clients qui apparaît en regard de votre nom d’utilisateur connecté. À ce stade, le site peut obtenir des jetons d’utilisateur pour un seul client. Par conséquent, lorsque vous basculez sur un autre client en utilisant la liste déroulante dans le coin supérieur droit, le site vous reconnecte à ce client pour obtenir les jetons pour ce client.

2.  Ensuite, le site détecte à partir d’Azure, les abonnements que vous avez associés au client sélectionné. Vous pouvez voir les abonnements disponibles lorsque vous créez une nouvelle solution préconfigurée.

3.  Enfin, le site extrait toutes les ressources dans les abonnements et les groupes de ressources marqués en tant que solutions préconfigurées et renseigne les mosaïques de la page d’accueil.

Les sections suivantes décrivent les rôles qui contrôlent l’accès aux solutions préconfigurées.

## Rôles AAD

Les rôles AAD contrôlent la possibilité d’approvisionnement de solutions préconfigurées et gèrent les utilisateurs dans une solution préconfigurée.

Vous trouverez d’autres informations sur les rôles d’administrateur dans AAD dans [Attribution des rôles d’administrateur dans Azure AD][lnk-aad-admin], mais cet article se concentre essentiellement sur l’**administrateur général** et les rôles **utilisateur/membre de domaine** utilisés par les solutions préconfigurées.

**Administrateur général :** il peut y avoir de nombreux administrateurs généraux par client AAD. Lorsque vous créez un client AAD, vous en devenez par défaut l’administrateur. L’administrateur général peut approvisionner une solution préconfigurée et se voir attribuer le rôle d’**ADMINISTRATEUR** de l’application au sein de leur client AAD. Toutefois, si un autre utilisateur du même client AAD crée une application, le rôle attribué par défaut à l’administrateur général par défaut **LECTURE SEULE IMPLICITE**. Les administrateurs généraux peuvent attribuer des rôles aux applications utilisant le [portail Azure Classic][lnk-classic-portal].

**Utilisateur/membre de domaine :** pour chaque client AAD, il peut y avoir de nombreux utilisateurs/membres. Un utilisateur de domaine peut approvisionner une solution préconfigurée via le site [azureiotsuite.com][lnk-azureiotsuite]. Le rôle par défaut qu’ils se voient attribuer pour l’application qu’ils approvisionnent est celui d’**ADMINISTRATEUR**. Ils peuvent créer une application à l’aide du script build.cmd dans le référentiel [azure-iot-solution][lnk-github-repo], mais le rôle qu’ils reçoivent par défaut est **LECTURE SEULE IMPLICITE**, car ils n’ont pas l’autorisation d’affecter des rôles. Si un autre utilisateur du client AAD crée une application, ils se voient affecter le rôle **LECTURE SEULE IMPLICITE** par défaut pour cette application. Ils n’ont pas la possibilité d’attribuer des rôles aux applications ; par conséquent, ils ne peuvent pas ajouter des utilisateurs ou des rôles pour les utilisateurs d’une application même s’ils l’ont approvisionnée.

**Utilisateur invité/Invité :** il peut y avoir de nombreux utilisateurs invités/invités par le client AAD. Les utilisateurs invités disposent d’un ensemble limité de droits au sein du client AAD. Par conséquent, les utilisateurs invités ne peuvent pas approvisionner une solution préconfigurée dans le client AAD.

Pour plus d’informations, consultez les ressources suivantes :

- [Création ou modification des utilisateurs dans Azure AD][lnk-create-edit-users]
- [Attribution de rôles d’application dans AAD][lnk-assign-app-roles]

## Rôles de l’administrateur d’abonnement Azure

Les rôles d’administration Azure permettent de contrôler la fonctionnalité de mappage d’un abonnement Azure sur un client AD.

Vous trouverez d’autres informations sur les rôles de co-administrateur et d’administrateur de compte dans l’article [Comment ajouter ou modifier un coadministrateur, un administrateur de service et un administrateur de compte][lnk-admin-roles].

## Rôles d’application

Les rôles d’application contrôlent l’accès aux périphériques de votre solution préconfigurée.

Dans l’application créée, deux rôles et un rôle implicite sont définis lorsque vous approvisionnez une solution préconfigurée.

-   **ADMINISTRATEUR :** contrôle totalement l’ajout, la gestion et la suppression des appareils

-   **LECTURE SEULE :** a la possibilité d’afficher les appareils

-   **LECTURE SEULE IMPLICITE :** il s’agit du même mode que la lecture seule, mais il est accordé à tous les utilisateurs de votre client AAD. Il a été prévu pour des raisons pratiques au cours du développement. Vous pouvez supprimer ce rôle en modifiant le fichier source [RolePermissions.cs][lnk-resource-cs].

### Modification des rôles d’application

Vous devez être un administrateur général AAD pour modifier les rôles d’un utilisateur :

1. Connectez-vous au [portail Azure Classic][lnk-classic-portal].

2. Sélectionnez **Active Directory**

3. Cliquez sur le nom de votre client AAD

4. Cliquez sur **Applications**.

5. Si vous ne voyez pas votre application dans la liste, faites défiler le menu déroulant jusqu’à **Afficher** supprimer jusqu’à **Applications que ma société possède** et cliquez sur la coche.

6. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfigurée.

7. Cliquez sur **Utilisateurs**.

8. Sélectionnez l’utilisateur dont vous souhaitez permuter les rôles.

9. Cliquez sur le bouton affecter et le rôle que vous souhaitez attribuer, cliquez sur la case à cocher.

## Forum Aux Questions

### Je suis un administrateur de service et je souhaite modifier le mappage d’annuaire entre mon abonnement et un client AAD spécifique. Comment faire ?

1. Accédez au [portail Azure Classic][lnk-classic-portal] et cliquez sur **Paramètres** dans la liste des services sur le côté gauche.

2. Sélectionnez l’abonnement dont vous souhaitez modifier le mappage d’annuaire.

3. Cliquez sur **Modifier l’annuaire**.

4. Sélectionnez l’**annuaire** que vous souhaitez utiliser dans la liste déroulante. Cliquez sur la flèche Suivant.

5. Vérifiez le mappage d’annuaire et les coadministrateurs affectés. Notez que si vous migrez depuis un autre annuaire, tous les coadministrateurs de l’annuaire d’origine sont supprimés.

### Je suis un utilisateur/membre du domaine sur le client AAD et j’ai créé une solution préconfigurée. Comment puis-je me voir attribuer un rôle pour mon application ?

Demandez à un administrateur général de vous attribuer le rôle d’administrateur général sur le client AAD pour obtenir les autorisations nécessaires pour affecter vous-même les rôles aux utilisateurs, ou demandez à un administrateur général de vous attribuer un rôle. Si vous souhaitez modifier le client AAD sur lequel votre solution préconfigurée a été déployée, passez à la question suivante.

### Comment puis-je activer le client AAD auquel la solution préconfigurée de contrôle à distance et l’application sont affectées ?

Vous pouvez exécuter un déploiement sur le cloud depuis <https://github.com/Azure/azure-iot-remote-monitoring> et refaire un déploiement avec un client AAD nouvellement créé. Comme vous êtes administrateur général par défaut, lorsque vous créez un nouvel utilisateur AAD, vous avez un accès pour ajouter des utilisateurs et affecter des rôles à ces utilisateurs.

1. Créer un annuaire AAD dans le [portail de gestion Azure][lnk-classic-portal].

2. Accédez à <https://github.com/Azure/azure-iot-remote-monitoring> Pour plus d’informations sur les déploiements dans le cloud, consultez [Déploiement cloud][lnk-wiki-clouddeployment].

3. Exécutez `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (par exemple, `build.cmd cloud debug myRMSolution`)

4. Lorsque vous y êtes invité, définissez le **tenantid** du client que vous venez de créer et non celui du client précédent.


### Je souhaite modifier la fonctionnalité administrateur de service ou coadministrateur lors d’une connexion avec un compte de société

Voir l’article de support [Modification de la fonctionnalité Administrateur de service et Coadministrateur lors d’une connexion avec un compte de société][lnk-service-admins].

### Pourquoi est-ce que je reçois cette erreur ? « Votre compte n’a pas les autorisations suffisantes pour créer une solution. Veuillez contacter votre administrateur de compte ou essayer avec un autre compte. »

Examinons le diagramme suivant :

![][img-flowchart]

**Pourquoi affiche-t-il cette erreur alors que j’ai un abonnement Azure ?** *Un abonnement Azure est nécessaire pour créer des solutions préconfigurées. Vous pouvez créer un compte d’essai gratuit en quelques minutes.*

Si vous êtes sûr de disposer d’un abonnement Azure, validez le mappage de votre abonnement client et assurez-vous que c’est le bon client qui est sélectionné dans la liste déroulante. Si vous avez validé le client souhaité, suivez le schéma ci-dessus et validez le mappage de votre abonnement et de ce client AAD.

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Manually-setting-up-roles-and-assigning-permissions-in-Azure-Active-Directory-(AAD)#assigning-users-to-the-roles
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-wiki-clouddeployment]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Cloud-deployment

<!---HONumber=AcomDC_0309_2016-->