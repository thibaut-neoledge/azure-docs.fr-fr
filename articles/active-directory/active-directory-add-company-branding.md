<properties 
	pageTitle="Ajout d’une marque de société aux pages de connexion et du volet d’accès" 
	description="Rubrique qui explique comment une entreprise peut appliquer une même apparence à tous les sites web et à tous les services qu’elle gère pour que les utilisateurs ne soient pas trop déroutés chaque fois qu’ils doivent utiliser ces différents sites." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Ajout d’une marque de société aux pages de connexion et du volet d’accès

> [AZURE.NOTE]
> 
- La promotion de la marque de société est une fonctionnalité disponible uniquement si vous avez effectué une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
- Les clients vivant en Chine peuvent accéder aux éditions De base et Premium d’Azure Active Directory à l’aide de l’instance mondiale d’Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous via le [Forum Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

Beaucoup d’entreprises souhaitent appliquer une même apparence à tous leurs sites web et à tous les services qu’elles gèrent afin que les utilisateurs ne soient pas trop déroutés chaque fois qu’ils doivent utiliser ces différents sites. Azure Active Directory offre cette possibilité en vous permettant de personnaliser l’apparence des pages web côté utilisateur final pour qu’elles reprennent le logo et la palette de couleurs de votre entreprise :

- **Page de connexion** : page vers laquelle les utilisateurs sont redirigés lorsqu’ils se connectent à Office 365 ou autres applications web et applications modernes utilisant Azure AD comme fournisseur d’identité. La plupart des utilisateurs vont être confrontés à cette page, soit pour accéder à Home Realm Discovery, qui permet au système de rediriger les utilisateurs fédérés vers leur STS local (par exemple, AD FS), ou pour entrer leurs informations d’identification.

- **Page Volet d’accès** : la page Volet d’accès est un portail web qui permet aux utilisateurs finaux avec un compte professionnel ou d’établissement scolaire dans un annuaire Azure AD pour consulter et lancer des applications cloud auxquelles ils ont été autorisés à accéder par l’administrateur Azure AD. Le volet d’accès est accessible à tous les utilisateurs de votre organisation à l’adresse myapps.microsoft.com.

## Personnalisation de la page de connexion

La page de connexion est en général la page web la plus souvent utilisée par les utilisateurs finaux qui souhaitent accéder via le navigateur aux applications et services cloud auxquels votre organisation est abonnée. Son apparence est donc primordiale. Si vous souhaitez utiliser la page de connexion par défaut, vous n’avez rien à faire.

### Combien de temps faut-il pour voir apparaître les modifications de la marque sur les pages de connexion ?

Il peut se passer jusqu’à une heure avant que les utilisateurs ne voient les modifications que vous avez apportées à la page de connexion.

### Quand les utilisateurs verront-ils la page de connexion personnalisée ?

Les utilisateurs verront la page de connexion personnalisée lorsqu’ils visitent un service avec une URL spécifique au locataire, comme https://outlook.com/**contoso**.com ou https://mail.**contoso**.com (si vous avez créé un enregistrement CNAME).

S’ils visitent un service avec une URL non spécifique (par exemple https://mail.office365.com), ils verront une page de connexion non personnalisée. La page de connexion s’actualise pour afficher votre marque une fois que les utilisateurs ont entré leur ID utilisateur ou sélectionné une vignette d’utilisateur.

> [AZURE.NOTE]
> 
- Votre nom de domaine doit apparaître « Actif » dans la section **Active Directory** > **Annuaire** > **Domaines** du portail de gestion Azure où vous avez effectué la personnalisation.
- La personnalisation de la page de connexion ne s’étend pas à la page de connexion client de Microsoft. Ce qui veut dire que les utilisateurs qui se connectent avec un compte Microsoft personnel (anciennement Windows Live ID) peuvent voir une liste personnalisée de vignettes utilisateur générées par Azure AD, mais la marque de votre organisation ne s’applique pas à la page de connexion du compte Microsoft.

### Que verront mes utilisateurs après la personnalisation de la page de connexion ?

Si vous souhaitez afficher la marque, la palette de couleurs de votre société ou tout autre élément personnalisable sur cette page, reportez-vous aux images qui suivent pour comprendre la différence entre les deux.

Lorsque l’utilisateur tente de se connecter à partir d’un ordinateur, voici un exemple de ce qu’il voit dans la page de connexion Office 365 *avant* personnalisation :

![][1]

Et voici ce que ce même utilisateur verra *après* personnalisation :

![][2]

Lorsque l’utilisateur tente de se connecter à partir d’un appareil mobile, voici un exemple de ce qu’il voit dans la page de connexion Office 365 *avant* personnalisation :

![][3]

Et voici ce que ce même utilisateur verra *après* personnalisation :

![][4]

### Quels sont les éléments de la page que je peux personnaliser ?

Vous pouvez personnaliser les éléments suivants sur la page de connexion :

![][5]

 Élément de la page | Emplacement sur la page
	------------- | -------------
Logo de bannière | Affiché en haut à droite de la page. Remplace le logo qu’affiche normalement le site auquel vos utilisateurs se connectent (par exemple Office 365 ou Azure).
Grande illustration/couleur d’arrière-plan | Affichée à gauche de la page. Remplace l’image qu’affiche normalement le site auquel vos utilisateurs se connectent. La couleur d’arrière-plan peut s’afficher à la place de la grande illustration pour les connexions à faible bande passante ou lorsque l’écran est très étroit.
Texte de la page de connexion | S’affiche au-dessus du pied de page, lorsque vous devez transmettre des informations à vos utilisateurs avant qu’ils ne se connectent avec leur compte professionnel ou scolaire. Par exemple, vous pouvez mentionner le numéro de téléphone de votre support technique ou une mention légale.

> [AZURE.NOTE]Tous ces éléments sont facultatifs. Par exemple, si vous spécifiez un Logo de bannière mais aucune grande illustration, la page de connexion affiche votre logo et l’illustration du site de destination (autrement dit, l’image d’autoroute de Californie d’Office 365).

Vous pouvez également traduire tous les éléments de cette page. Une fois que vous avez configuré un jeu d’éléments de personnalisation « par défaut », vous pouvez configurer d’autres versions avec différents paramètres régionaux. Vous pouvez également combiner différents éléments. Vous pouvez par exemple afficher :

- Créez une grande illustration par défaut adaptée à toutes les cultures, puis créez des versions spécifiques pour l’anglais et le français. Les utilisateurs dont les navigateurs sont paramétrés sur une de ces deux langues verront l’image spécifique, tandis que tous les autres verront l’affichage par défaut.
- Configurez différents logos pour votre organisation (par exemple, des versions en japonais ou en hébreu).

### Comment l’illustration apparaîtra-t-elle une fois le navigateur redimensionné ?

Lors du redimensionnement de la fenêtre du navigateur, la grande illustration, comme celle présentée précédemment, est presque toujours rognée pour prendre en compte les différentes tailles d’écrans. Ainsi, il est conseillé de conserver les éléments visuels clés dans l’illustration pour qu’ils soient toujours affichés dans l’angle supérieur gauche (à droite pour les langues qui se lisent de droite à gauche). Ceci est important, car le redimensionnement s’effectue généralement à partir de l’angle inférieur droit vers l’angle supérieur gauche, ou du bas vers le haut.

L’illustration suivante montre comment l’illustration est rognée lorsque le navigateur est redimensionné à gauche :

![][6]

Et voici comment elle apparaîtra une fois le navigateur redimensionné vers le haut :

![][7]

## Personnalisation de la page du volet d’accès

La page du volet d’accès est essentiellement une page portail pour tous les utilisateurs qui souhaitent un accès rapide, via des vignettes interactives, aux différentes applications cloud auxquelles vous leur avez donné accès. Si vous ne souhaitez pas utiliser la page de connexion par défaut, vous n’avez rien à faire.

### Que voient les utilisateurs une fois que j’ai personnalisé la page d’accès ?

![][8]

## Ajout de la marque de votre société à votre annuaire

Un ensemble par défaut d’éléments personnalisables peut être configuré par annuaire dans le portail de gestion. Une fois que les valeurs par défaut ont été enregistrées, l’administrateur a également la possibilité d’ajouter des versions localisées de chaque élément, pour différentes langues ou différents paramètres régionaux. Tous les éléments personnalisables sont facultatifs.

Par exemple, si vous configurez un Logo de bannière par défaut, mais aucune grande illustration, la page de connexion affiche votre logo dans le coin supérieur droit, mais l’illustration par défaut du site s’affiche. Si vous configurez Logo de bannière par défaut et un texte de page de connexion en anglais et que vous configurez la langue de la page en allemand, les utilisateurs qui choisissent l’allemand voient votre Logo de bannière par défaut, mais avec le texte en allemand. Bien que vous puissiez configurer un jeu pour chaque langue prise en charge par Azure AD, nous vous recommandons de limiter le nombre de variantes, pour des raisons de performances et de maintenance.

Pour ajouter votre marque à l’annuaire :

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com) tant qu’administrateur de l’annuaire que vous voulez personnaliser.
2. Sélectionnez l’annuaire que vous voulez personnaliser.
3. Sélectionnez l’onglet **Configurer**, puis sélectionnez **Personnaliser la marque**.
4. Modifiez les éléments que vous voulez personnaliser. Notez que tous les champs sont facultatifs.
5. Cliquez sur **Save**.

Il peut se passer jusqu’à une heure avant que les utilisateurs ne voient les modifications que vous avez apportées à la page de connexion.

Pour ajouter la marque de l’entreprise spécifique à une langue :

1. Dans le [portail de gestion Azure](https://manage.windowsazure.com), sous l’onglet **Configurer**, sélectionnez **Personnaliser la marque**.
2. Sélectionnez **Ajouter des paramètres de marque pour une langue spécifique**, sélectionnez pour laquelle vous souhaitez personnaliser le logo, puis cliquez sur **Suivant**.
3. Modifiez uniquement les éléments pour lesquels vous souhaitez configurer des remplacements spécifiques par langue. Notez que tous les champs sont facultatifs. Si un champ est vide, la valeur personnalisée par défaut s’affiche à la place (ou la valeur par défaut Microsoft si aucune valeur par défaut personnalisée n’est configurée).
4. Cliquez sur **Save**.

Pour supprimer la marque de votre annuaire

1. Dans le [portail de gestion Azure](https://manage.windowsazure.com), sous l’onglet **Configurer**, sélectionnez **Personnaliser la marque**.
2. Dans la page Personnaliser la marque, sélectionnez **Modifier les paramètres de marque existants**, puis passez à la page suivante.
3. En fonction des éléments que vous voulez supprimer, effectuez une ou plusieurs des opérations suivantes :
	1. Pour le Logo de bannière, cliquez sur la case à cocher **Supprimer le logo téléchargé**.
    2. Pour le Logo de la mosaïque, cliquez sur la case à cocher **Supprimer le logo téléchargé**.
    3. Pour l’étiquette du nom d’utilisateur sur la page de connexion, effacez tout le texte.
    4. Pour le texte de la page de connexion, effacez tout le texte.
    5. Pour l’illustration de la page de connexion, cliquez sur la case à cocher **Supprimer l’illustration**.
    6. Pour la couleur d’arrière-plan de la page de connexion, supprimez tout le texte.
4. Cliquez sur **Enregistrer** pour supprimer les éléments.
5. Si nécessaire, cliquez à nouveau sur **Personnaliser la marque** et répétez ces étapes pour tous les éléments de personnalisation à supprimer. Tous les paramètres de personnalisation sont supprimés lorsque vous cliquez sur **Personnaliser la marque** et que vous voyez le formulaire **Personnaliser la marque par défaut** sans aucun paramètre configuré.

## Test et exemples

Nous vous recommandons d’effectuer un test avec un client test avant d’apporter des modifications à votre environnement de production. Pour vérifier si votre marque a été appliquée, la solution la plus simple consiste à ouvrir une session de navigateur InPrivate ou Incognito, puis de consulter https://outlook.com/contoso.com, en remplaçant contoso.com par le domaine que vous avez personnalisé. Notez que cela fonctionne également avec les domaines qui ressemblent à contoso.onmicrosoft.com.

Pour vous aider à créer des personnalisations efficaces, nous avons personnalisé deux pages de connexion fictives :

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Pour tester les paramètres spécifiques à une langue, vous devez modifier la langue par défaut dans votre navigateur web et choisir une langue que vous avez définie dans votre personnalisation. Dans Internet Explorer, vous pouvez configurer ce paramètre dans le menu **Options Internet**.

## Éléments personnalisables

Certains éléments personnalisables d’Azure AD peuvent être utilisés de plusieurs manières. Les logos de société ne peuvent être configurés qu’une seule fois par annuaire et sont utilisés à la fois sur les pages de connexion et d’accès, alors que certains éléments personnalisables sont spécifiques à la page de connexion. Le tableau suivant fournit des détails sur les différents éléments personnalisables.

Nom | Description | Contraintes | Recommandations
	------------- | ------------- | ------------- | -------------
Logo de bannière | Le Logo de bannière s’affiche sur la page de connexion et sur le Volet d’accès. | <p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 Ko</p> | <p>Utilisez le logo complet de votre organisation (avec pictogramme et logo)</p><p>La taille doit être inférieure à 30 pixels pour éviter l’apparition de barres de défilement sur les appareils mobiles</p><p>La taille ne doit pas dépasser 4 Ko</p><p>Utilisez un fichier PNG transparent. (Dites-vous que l’arrière-plan de la page de connexion ne sera pas toujours blanc.)</p>
Logo de la mosaïque | (Actuellement non utilisé dans la page de connexion.) À l’avenir, ce texte pourrait être utilisé pour remplacer le pictogramme générique « Compte professionnel ou scolaire » à différents endroits. | <p>JPG ou PNG</p><p>120 x 120 pixels</p><p>10 Ko</p> | <p>Restez simple (pas de texte de petite taille), cette image peut être redimensionnée à 50 %
</p> |
Étiquette du nom d’utilisateur de la page de connexion | (Actuellement non utilisé dans la page de connexion.) À l’avenir, ce texte pourrait être utilisé pour remplacer le pictogramme générique « Compte professionnel ou scolaire » à différents endroits. Vous pouvez définir quelque chose comme « Compte Contoso » ou « ID Contoso ». | <p>Texte Unicode, jusqu’à 50 caractères</p><p>Texte brut uniquement (pas de liens, ni de balises HTML)</p> | <p>Court et simple</p><p>Demandez à vos utilisateurs comment ils font généralement référence au compte professionnel ou scolaire que vous leur fournissez.</p>
Texte de la page de connexion | Ce texte « standard » apparaît sous le formulaire de la page de connexion et peut être utilisé pour communiquer des instructions supplémentaires ou des informations sur l’aide et l’assistance. | <p>Texte Unicode, jusqu’à 256 caractères</p><p>Texte brut uniquement (pas de liens, ni de balises HTML)</p> | Moins de 250 caractères (environ 3 lignes de texte)
Illustration de la page de connexion | Cette illustration est une grande image qui s’affiche sur la page de connexion à gauche du formulaire. | <p>JPG ou PNG</p><p>1420 x 1200</p><p>500 Ko</p> | <p>1420 x 1200 pixels</p><p>Important : la plus petite possible, idéalement moins de 200 Ko. Si cette image est trop grande, elle affecte les performances de la page de connexion lorsque l’image n’est pas mise en cache.</p><p>Cette image sera presque toujours rognée, pour s’adapter aux différentes tailles d’écrans. Conservez les principaux éléments visuels dans l’angle supérieur gauche (en haut à droite pour les langues qui se lisent de droite à gauche), car le redimensionnement s’effectue à partir de l’angle inférieur droit vers l’angle supérieur gauche, à mesure que la fenêtre du navigateur se réduit.</p>
Couleur d’arrière-plan de la page de connexion | La couleur d’arrière-plan de la page de connexion est utilisée dans la zone à gauche du formulaire de la page. Elle est visible même lorsqu’il n’y a aucune illustration sur la page de connexion. | Il doit s’agir d’une couleur RVB au format hexadécimal (par exemple #FFFFFF) | <p>La couleur d’arrière-plan peut s’afficher à la place de la grande illustration pour les connexions à faible bande passante</p><p>Nous vous suggérons de choisir la couleur principale du Logo de bannière</p>


## Étapes suivantes

- [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

<!---HONumber=58--> 