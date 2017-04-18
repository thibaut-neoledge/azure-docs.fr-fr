---
title: "Ajout d’une marque de société aux pages de connexion et du volet d’accès dans Azure Active Directory"
description: "Découvrir comment ajouter une société à la page de connexion Azure et à la page du panneau d’accès"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Ajout d’une marque de société aux pages de connexion et du volet d’accès
De nombreuses entreprises veulent que tous les sites web et services administrés par leurs soins présentent un aspect similaire. Azure Active Directory offre cette possibilité en permettant aux informaticiens professionnels de personnaliser l’apparence des pages web suivantes, afin qu’elles affichent les logos et images de l’entreprise :

* **Page de connexion** : il s’agit de la page qui s’affiche lorsque les employés et les invités professionnels se connectent à Office 365 ou à d’autres applications qui utilisent Azure AD.
* **Page du volet d’accès** : le volet d’accès est un portail web qui vous permet de consulter et d’exécuter les applications cloud auxquelles votre administrateur Azure AD vous a autorisé à accéder. Le volet d’accès est disponible ici : [https://myapps.microsoft.com](https://myapps.microsoft.com).

Cette rubrique explique comment personnaliser la page de connexion et la page de panneau d’accès.

> [!NOTE]
> * La marque de société est une fonctionnalité disponible uniquement si vous avez effectué une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory, ou si vous disposez d’une licence Office 365. Pour en savoir plus, reportez-vous à la page Éditions d’Azure Active Directory.
> 
> * Les clients vivant en Chine peuvent accéder aux éditions De base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour en savoir plus, contactez-nous via le Forum Azure Active Directory.


## <a name="customizing-the-sign-in-page"></a>Personnalisation de la page de connexion
Les utilisateurs interagissent généralement avec la page de connexion Azure AD lorsqu’ils tentent d’accéder aux services et applications cloud auxquels votre organisation est abonnée.

Si vous apportez des modifications à la marque sur votre page de connexion, un délai pouvant atteindre une heure peut être nécessaire pour qu’elles apparaissent sur les systèmes des utilisateurs finaux.

Les éléments de la marque de société s’affichent sur la page de connexion Azure AD lorsque les utilisateurs accèdent à une URL spécifique d’un locataire, par exemple : https://outlook.com/contoso.com.

Lorsque l’utilisateur accède à un service se trouvant à une adresse URL générique, comme www.office.com, la page de connexion ne contient aucune information sur la marque de société, car le système ne connaît pas cet utilisateur. Toutefois, la marque de société s’affiche une fois que les utilisateurs ont indiqué leur ID utilisateur, ou sélectionné la mosaïque d’un utilisateur.

> [!NOTE]
> * Votre nom de domaine doit apparaître « Actif » dans la section **Active Directory** > **Répertoire** > **Domaines** du portail Azure Classic dans lequel vous avez effectué la personnalisation.
> * La personnalisation de marque de la page de connexion ne s’étend pas à la page de connexion des comptes Microsoft personnels. Si les employés ou des invités professionnels se connectent avec un compte Microsoft personnel, leur page de connexion ne reflète pas la personnalisation de marque de votre organisation.
>

Les captures d’écran suivantes expliquent de quelle manière les pages de connexion sont personnalisées.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Scénario 1 : Un employé de Contoso accède à une adresse URL d’application générique (par exemple, www.office.com)

Dans cet exemple, un utilisateur Contoso se connecte à une application mobile ou à une application web à l’aide d’une adresse URL générique. L’illustration de gauche représente toujours l’application ; le volet d’interaction, sur la droite, se met à jour pour afficher les éléments de marque Contoso, selon les besoins.

![Page de connexion Office 365 avant et après la personnalisation][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Scénario 2 : Un employé de Contoso accède à l’application Contoso limitée aux utilisateurs internes

Dans cet exemple, un utilisateur Contoso se connecte à une application interne à l’aide d’une adresse URL spécifique de la société. L’illustration de gauche représente la marque de société (Contoso). À droite, le volet d’interaction est verrouillé sur Contoso et aide les employés à se connecter.

![Page de connexion à l’application limitée][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Scénario 3 : Un employé de Contoso accède à l’application Contoso ouverte aux utilisateurs externes

Dans cet exemple, les utilisateurs se connectent à une application cœur de métier depuis Contoso, mais l’utilisateur peut ou non être un employé de Contoso. L’illustration de gauche représente le propriétaire de la ressource (Contoso), tout comme dans le cas \#2, ci-dessus. Cependant, cette fois-ci, le volet d’interaction de droite n’est pas verrouillé sur Contoso, afin d’indiquer aux utilisateurs externes qu’ils peuvent se connecter.

![Connexion via un accès ouvert][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Scénario 4 : Un invité professionnel de l’entreprise Fabrikam accède à l’application Contoso accessible aux utilisateurs externes

Dans cet exemple, un utilisateur Contoso se connecte à une application interne à l’aide d’une adresse URL spécifique de la société. L’illustration de gauche représente la marque de société (Contoso). À droite, le volet d’interaction est verrouillé sur Contoso et aide les employés à se connecter.

![Connexion en tant qu’utilisateur externe][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Quels sont les éléments de la page que je peux personnaliser ?

Vous pouvez personnaliser les éléments suivants sur la page de connexion :

![][5]

| Élément de la page | Emplacement sur la page |
|:--- | --- |
| Logo de bannière | Affiché en haut à droite de la page. Remplace le logo de l’application une fois que l’organisation de l’utilisateur est identifiée (en règle générale, une fois le nom d’utilisateur saisi). |
| Illustration d’arrière-plan | Affichée en tant qu’image pleine page sur la gauche de la page de connexion. Remplace l’illustration de l’application pour les scénarios de connexion avec des locataires (lorsque les utilisateurs accèdent à une application publiée par leur organisation, ou par une organisation au sein de laquelle ils jouent le rôle d’invité professionnel).<br>Sur une connexion à faible bande passante, l’illustration d’arrière-plan est remplacée par une couleur d’arrière-plan. Sur les écrans étroits tels que les téléphones, l’illustration n’est pas affichée.<br>L’illustration d’arrière-plan est détourée lorsque les utilisateurs redimensionnent leur navigateur. Lorsque vous concevez votre illustration, placez les éléments visuels clés dans l’angle supérieur gauche, afin qu’ils ne soient pas détourés. | 
| Case à cocher Maintenir la connexion | Affichée sous la zone de texte **Mot de passe**. |
| Texte de la page de connexion | Texte réutilisable à afficher au-dessus du pied de page. Il peut être utilisé pour communiquer des informations utiles à vos utilisateurs, telles que le numéro de téléphone de votre support technique ou une mention légale. |

> [!NOTE]
> Tous ces éléments sont facultatifs. Par exemple, si vous spécifiez un logo de bannière mais aucune illustration d’arrière-plan, la page de connexion affiche votre logo et l’illustration du site de destination (par exemple, l’image de l’autoroute de Californie d’Office 365).
>

Sur votre page de connexion, la case à cocher **Maintenir la connexion** permet à un utilisateur de rester connecté lorsqu’il ferme, puis rouvre son navigateur, et n’affecte pas la durée de vie de la session.

L’affichage de la case à cocher dépend du réglage du paramètre **Masquer Maintenir la connexion**.

![Paramètre Masquer Maintenir la connexion][6]

Pour masquer la case à cocher, définissez ce paramètre sur **Masqué**.

> [!NOTE]
> Certaines fonctionnalités de SharePoint Online et Office 2010 dépendent de la capacité des utilisateurs à sélectionner cette case à cocher. Si vous configurez ce paramètre sur Masqué, il se peut que vos utilisateurs voient apparaître des invites de connexion supplémentaires et inattendues.
>
>

Vous pouvez également traduire tous les éléments de cette page. Une fois que vous avez configuré un jeu d’éléments de personnalisation « par défaut », vous pouvez configurer des versions supplémentaires avec différents paramètres régionaux. Vous pouvez également combiner différents éléments. Vous pouvez par exemple afficher :

* Créez une illustration par défaut adaptée à toutes les cultures, puis créez des versions spécifiques pour l’anglais et le français. Lorsque vous définissez vos navigateurs sur l’une de ces deux langues, l’image spécifique s’affiche, tandis que l’illustration par défaut apparaît pour toutes les autres langues.
* Configurez différents logos pour votre organisation (par exemple, des versions en japonais ou en hébreu).

## <a name="access-panel-page-customization"></a>Personnalisation de la page du volet d’accès
La page du volet d’accès est essentiellement une page de portail permettant un accès rapide aux applications cloud auxquelles votre administrateur vous a autorisé à accéder. Dans cette page, vos applications apparaissent comme des vignettes interactives.

La capture d’écran suivante représente un exemple de page du panneau d’accès après la personnalisation.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Ajout de la marque de votre société à votre annuaire
Vous pouvez configurer un ensemble par défaut d’éléments personnalisables par répertoire dans le portail Azure Classic. Une fois que les valeurs par défaut ont été enregistrées, un administrateur peut ajouter des versions localisées de chaque élément, pour différentes langues ou différents paramètres régionaux. Tous les éléments personnalisables sont facultatifs.

Par exemple, si vous configurez un logo de bannière par défaut mais aucune grande illustration, la page de connexion affiche votre logo dans le coin supérieur. Toutefois, l’illustration par défaut du site s’affiche.

Imaginez la configuration suivante :

* Un logo de bannière par défaut et le texte de la page de connexion en anglais
* Un texte spécifique pour la page de connexion en allemand

Si votre langue préférée est l’allemand, vous voyez le logo de bannière par défaut, mais le texte en allemand.

Bien que vous puissiez configurer un jeu pour chaque langue prise en charge par Azure AD, nous vous recommandons de limiter le nombre de variantes, pour des raisons de performances et de maintenance.
 
**Pour ajouter la marque de votre société à votre répertoire, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
5. Modifiez les éléments que vous voulez personnaliser. Tous les champs sont facultatifs.
6. Cliquez sur **Save**.

Il peut s’écouler jusqu’à une heure avant que les modifications que vous avez apportées à la page de connexion soient visibles.

**Pour ajouter une marque de société spécifique à la langue, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
fs3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
5. Cliquez sur **Ajouter des paramètres de marque pour une langue spécifique**.
6. Sélectionnez la langue pour laquelle vous souhaitez personnaliser le logo, puis cliquez sur **Suivant**.
7. Modifiez uniquement les éléments pour lesquels vous souhaitez configurer des remplacements spécifiques par langue. Tous les champs sont facultatifs. Si un champ est vide, la valeur personnalisée par défaut s’affiche à la place (ou la valeur par défaut Microsoft si aucune valeur par défaut personnalisée n’est configurée).
8. Cliquez sur **Enregistrer**.

**Pour supprimer la marque de votre société de votre répertoire, procédez comme suit :**

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) en tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez le répertoire que vous voulez personnaliser.
3. Dans la barre d’outils située en haut, cliquez sur **Configurer**.
4. Cliquez sur **Personnaliser la marque**.
5. Dans la page Personnaliser la marque, sélectionnez **Modifier les paramètres de marque existants** , puis passez à la page suivante.
6. En fonction des éléments que vous voulez supprimer, effectuez une ou plusieurs des opérations suivantes :

    a. Sous **Logo de bannière**, sélectionnez **Supprimer le logo téléchargé**.

    b. Sous **Logo de la mosaïque**, sélectionnez **Supprimer le logo téléchargé**.

    c. Supprimez le texte de toutes les zones de texte.

    d. Cliquez sur **Suivant**.

    e. Supprimez le texte de toutes les zones de texte.
7. Cliquez sur **Enregistrer** pour supprimer les éléments.
8. Si nécessaire, cliquez à nouveau sur **Personnaliser la marque** et répétez ces étapes pour tous les éléments de personnalisation à supprimer.
    Tous les paramètres de personnalisation sont supprimés lorsque vous cliquez sur **Personnaliser la marque** et que vous voyez le formulaire **Personnaliser la marque par défaut** sans aucun paramètre configuré.


## <a name="customizable-elements"></a>Éléments personnalisables
Les logos de société sont utilisés pour les pages de connexion et du volet d’accès, tandis que d’autres éléments sont utilisés uniquement sur la page de connexion. Le tableau suivant fournit des détails sur les différents éléments personnalisables.

| Nom | Description | Contraintes | Recommandations |
| --- | --- | --- | --- |
| Logo de bannière |Le logo de bannière s’affiche sur la page de connexion et sur le volet d’accès. |<p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 Ko</p> |<p>Utilisez le logo complet de votre organisation (y compris le pictogramme et le logo)</p><p>Maintenez-le sous 30 pixels pour éviter l’apparition de barres de défilement sur les périphériques mobiles</p><p>Ne doit pas dépasser 4 Ko</p><p>Utilisez un fichier PNG transparent (ne considérez pas que la page de connexion a toujours un arrière-plan blanc)</p> |
| Logo de la mosaïque | Élément non utilisé actuellement |<p>JPG ou PNG</p><p>120 x 120 pixels</p><p>10 Ko</p> |<p>Restez simple (pas de texte de petite taille), cette image peut être redimensionnée à 50 % |
| </p> | | | |
| Étiquette du nom d’utilisateur de connexion | Élément non utilisé actuellement |<p>Texte Unicode, jusqu'à 50 caractères</p><p>Texte brut uniquement (aucun lien ni balise HTML)</p> |<p>Court et simple</p><p>Demandez à vos utilisateurs comment ils font généralement référence au compte professionnel ou scolaire que vous leur fournissez.</p> |
| Texte réutilisable de la page de connexion |Ce texte réutilisable apparaît sous le formulaire de la page de connexion et peut être utilisé pour communiquer des instructions supplémentaires ou des informations sur l’aide et le support. |<p>Texte Unicode, jusqu'à 256 caractères</p><p>Texte brut uniquement (aucun lien ni balise HTML)</p> |Moins de 250 caractères (environ 3 lignes de texte) |
| Illustration d’arrière-plan de la page de connexion | Grande image qui s’affiche sur la gauche de la page de connexion (sur la droite pour les langues DàG) lorsque les utilisateurs accèdent aux adresses URL spécifiques du locataire. |<p>JPG ou PNG</p><p>1420 x 1200</p><p>500 Ko</p> |<p>1420 x 1200 pixels</p><p>Important : la plus petite possible, idéalement moins de 200 Ko. Si cette image est trop grande, elle affecte les performances de la page de connexion lorsque l’image n’est pas mise en cache.</p><p>Cette image est presque toujours détourée pour s’adapter aux différentes proportions d’écrans. Placez les principaux éléments visuels dans l’angle supérieur gauche.</p> |
| Couleur d’arrière-plan de la page de connexion | Sur une connexion à faible bande passante, cette couleur unie est utilisée à la place de l’illustration d’arrière-plan. | Il doit s’agir d’une couleur RVB au format hexadécimal (par exemple : \#FFFFFF). | Nous vous suggérons de choisir la couleur principale du logo de bannière. |

## <a name="next-steps"></a>Étapes suivantes
* [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
* [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

