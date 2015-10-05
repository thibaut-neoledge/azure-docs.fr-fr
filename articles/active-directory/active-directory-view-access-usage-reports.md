<properties
	pageTitle="Affichage de vos rapports d’accès et d’utilisation | Microsoft Azure"
	description="Explique comment afficher les rapports d'accès et d'utilisation pour évaluer l'intégrité et la sécurité du répertoire de votre société."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="kenhoff;Justinha;curtand"/>

# Afficher vos rapports d'accès et d'utilisation

Vous pouvez utiliser les rapports d'accès et d'utilisation Azure Active Directory pour obtenir une visibilité complète sur l'intégrité et la sécurité du répertoire de votre société. Grâce à ces informations, un administrateur de répertoire est capable de déterminer plus précisément les risques de sécurité potentiels et donc de les atténuer au maximum.

Dans le portail de gestion Azure, les rapports sont classés comme suit :

- Rapports d'anomalies : contiennent des événements de connexion qui peuvent nous sembler anormaux. Notre objectif est de vous faire part de ces activités et de vous permettre de déterminer si un événement est suspect.
- Rapports d’application intégrée : fournissent des indications sur l'utilisation des applications du cloud au sein de votre société. Azure Active Directory permet d’intégrer des milliers d'applications du cloud.
- Rapports d'erreurs : indiquent les erreurs qui peuvent survenir lors de la configuration de comptes sur des applications externes.
- Rapports spécifiques à l'utilisateur : affichent les données d'activité relatives aux périphériques/connexions d’un utilisateur spécifique.
- Journaux d'activité : contiennent un enregistrement de tous les événements audités durant les 24 dernières heures, 7 derniers jours ou 30 derniers jours, des modifications d’activité de groupes, et des activités d'enregistrement et de réinitialisation de mot de passe.

> [AZURE.NOTE]
>
- Certains rapports d'utilisation de ressources et d’anomalies avancés ne sont disponibles que lorsque vous activez [Azure Active Directory Premium](active-directory-get-started-premium.md). Des rapports avancés vous permettent d'améliorer la sécurité d'accès, de répondre aux menaces potentielles et d'accéder aux analyses relatives à l'utilisation de l’application et à l’accès au périphérique.
- Les clients situés en Chine peuvent accéder à Azure Active Directory Premium ainsi qu’aux éditions De base à l'aide de l'instance mondiale d'Azure Active Directory. Actuellement, les éditions De base et Premium d’Azure Active Directory ne sont pas prises en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d’informations, contactez-nous via le [Forum Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

## Rapports

|	Rapport |	Description |
|	------												|	-----																						|
|	**Rapports d’activités anormales**
|	[Connexions à partir de sources inconnues](active-directory-reporting-sign-ins-from-unknown-sources.md) |	Peut indiquer une tentative de connexion sans être suivi.. |
|	[Connexions après plusieurs échecs](active-directory-reporting-sign-ins-after-multiple-failures.md) |	Peut indiquer une attaque en force brute réussie. |
|	[Connexions depuis plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md) |	Peut indiquer que plusieurs utilisateurs se connectent avec le même compte. |
|	[Connexions à partir d’adresses IP affichant une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md) |	Peut indiquer une connexion réussie après une tentative d'intrusion insistante. |
|	[Connexions à partir de périphériques potentiellement infectés](active-directory-reporting-sign-ins-from-possibly-infected-devices.md) |	Peut indiquer une tentative de connexion à partir de périphériques potentiellement infectés. |
|	[Activité de connexion anormale](active-directory-reporting-irregular-sign-in-activity.md) |	Peut indiquer les événements anormaux des modèles de connexion des utilisateurs. |
|	[Utilisateurs ayant une activité de connexion anormale](active-directory-reporting-users-with-anomalous-sign-in-activity.md) |	Indique des utilisateurs dont les comptes ont été compromis. |
|	Utilisateurs avec des informations d’identification volées |	Utilisateurs avec des informations d’identification volées |
|	**Journaux d’activité**
|	Rapport d’audit |	Événements audités dans votre répertoire |
|	Activité de réinitialisation de mot de passe |	Fournit une vue détaillée des réinitialisations de mot de passe qui se produisent dans votre organisation. |
|	Activité de l’enregistrement de la réinitialisation de mot de passe |	Fournit une vue détaillée des réinitialisations de mot de passe qui se produisent dans votre organisation. |
|	Activité de groupes en libre-service |	Fournit un journal d'activité pour toutes les activités de groupes en libre-service dans votre répertoire |
|	**Applications intégrées**
|	Utilisation des applications |	Fournit un résumé de l'utilisation de toutes les applications SaaS intégrées à votre répertoire. |
|	Activité d’approvisionnement de compte |	Fournit un historique des tentatives d'approvisionnement des comptes vers des applications externes. |
|	État de substitution de mot de passe |	Fournit une vue d'ensemble détaillée de l'état de substitution de mot de passe automatique des applications SaaS. |
|	Erreurs de configuration de compte |	Indique un impact sur l'accès des utilisateurs aux applications externes. |
|	**Gestion des droits**
|	Utilisation de RMS |	Fournit un résumé de l'utilisation de Rights Management |
|	Utilisateurs RMS les plus actifs |	Répertorie les 1 000 premiers utilisateurs actifs ayant accédé aux fichiers protégés par RMS |
|	Utilisation d’un périphérique RMS |	Répertorie les périphériques utilisés pour l'accès aux fichiers protégés par RMS |
|	Utilisation d’applications fonctionnant avec RMS |	Fournit des applications activées pour l'utilisation de RMS |

## Éditions de rapport

|	Rapport |	Gratuit |	De base |	Premium |
|	------												|	----	|	-----	|	--------	|
|	**Rapports d’activités anormales**
|	Connexions à partir de sources inconnues |	✓ |	✓ |	✓ |
|	Connexions après plusieurs échecs |	✓ |	✓ |	✓ |
|	Connexions depuis plusieurs zones géographiques |	✓ |	✓ |	✓ |
|	Connexions à partir d’adresses IP affichant une activité suspecte | | |	✓ |
|	Connexions à partir de périphériques potentiellement infectés | | |	✓ |
|	Activité de connexion anormale | | |	✓ |
|	Utilisateurs ayant une activité de connexion anormale | | |	✓ |
|	Utilisateurs avec des informations d’identification volées | | |	✓ |
|	**Journaux d’activité**
|	Rapport d’audit |	✓ |	✓ |	✓ |
|	Activité de réinitialisation de mot de passe | | |	✓ |
|	Activité de l’enregistrement de la réinitialisation de mot de passe | | |	✓ |
|	Activité de groupes en libre-service | | |	✓ |
|	**Applications intégrées**
|	Utilisation des applications | | |	✓ |
|	Activité d’approvisionnement de compte |	✓ |	✓ |	✓ |
|	État de substitution de mot de passe | | |	✓ |
|	Erreurs de configuration de compte |	✓ |	✓ |	✓ |
|	**Gestion des droits**
|	Utilisation de RMS | | |	RMS uniquement |
|	Utilisateurs RMS les plus actifs | | |	RMS uniquement |
|	Utilisation d’un périphérique RMS | | |	RMS uniquement |
|	Utilisation d’applications fonctionnant avec RMS | | |	RMS uniquement |



## Rapports d’activités anormales
<p>Les rapports d'activités de connexion anormales signalent les connexions suspectes à Office365, au portail de gestion Azure, au panneau d'accès Azure&#160;AD, à Sharepoint Online, Dynamics CRM Online, et d'autres services en ligne de Microsoft.</p>
<p>Tous ces rapports, à l'exception du rapport «&#160;Connexions après plusieurs échecs&#160;», signalent également les connexions <i>fédérées</i> suspectes aux services susmentionnés, quel que soit le fournisseur de fédération. </p>
<p>Les rapports suivants sont disponibles : </p><ul> <li>[Connexions à partir de sources inconnues](active-directory-reporting-sign-ins-from-unknown-sources.md).</li> <li>[Connexions après plusieurs échecs](active-directory-reporting-sign-ins-after-multiple-failures.md).</li> <li>[Connexions depuis plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md).</li> <li>[Connexions à partir d'adresses IP affichant une activité suspecte](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md).</li> <li>[Activité de connexion anormale](active-directory-reporting-irregular-sign-in-activity.md).</li> <li>[Connexions à partir de périphériques potentiellement infectés](active-directory-reporting-sign-ins-from-possibly-infected-devices.md).</li> <li>[Utilisateurs ayant une activité de connexion anormale](active-directory-reporting-users-with-anomalous-sign-in-activity.md).</li> <li>Utilisateurs avec des informations d'identification volées</li></ul>










## Journaux d’activité

### Rapport d'audit

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique un enregistrement de tous les événements audités durant les 24 dernières heures, 7 derniers jours ou 30 derniers jours. <br />Pour plus d'informations, consultez la rubrique [Événements de rapport d’audit d'Azure Active Directory](active-directory-reporting-audit-events.md)  | Répertoire > onglet Rapports |

![Rapport d’audit](./media/active-directory-view-access-usage-reports/auditReport.PNG)

### Activité de réinitialisation de mot de passe

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique toutes les tentatives de réinitialisation de mot de passe réalisées dans votre société | Répertoire > onglet Rapports |

![Activité de réinitialisation de mot de passe](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)

### Activité de l’enregistrement de la réinitialisation de mot de passe

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique l’ensemble des enregistrements de réinitialisation de mot de passe réalisés dans votre société | Répertoire > onglet Rapports |

![Activité de l’enregistrement de la réinitialisation de mot de passe](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)

### Activité de groupes en libre-service

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique toutes les activités des groupes libre-service gérés dans votre répertoire. | Répertoire > Utilisateurs > <i>Utilisateur</i> > onglet Périphériques |

![Activité de groupes en libre-service](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)











## Rapports d’applications intégrées

### Utilisation des applications : résumé

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Utilisez ce rapport pour afficher l'utilisation de toutes les applications SaaS dans votre répertoire. Ce rapport est basé sur le nombre de clics effectués par les utilisateurs sur l'application dans le volet d'accès. | Répertoire > onglet Rapports |

![Résumé de l’utilisation des applications](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)


### Utilisation des applications : présentation détaillée

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Utilisez ce rapport pour découvrir combien de fois une application SaaS spécifique a été utilisée. Ce rapport est basé sur le nombre de clics effectués par les utilisateurs sur l'application dans le volet d'accès. | Répertoire > onglet Rapports |

### Tableau de bord de l’application

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Ce rapport indique les connexions à l'application, effectuées par les utilisateurs de votre société et cumulées sur une période définie. Le graphique de la page du tableau de bord vous permettra d’identifier les tendances des utilisations de l'application. | Répertoire > Application > onglet Tableau de bord |

## Rapports d'erreurs

### Erreurs de configuration de compte

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Utilisez-le pour examiner les erreurs survenant lors de la synchronisation des comptes des applications SaaS vers Azure Active Directory. | Répertoire > onglet Rapports |

![Erreurs de configuration de compte](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)









## Rapports d’erreur spécifiques à l’utilisateur

### Appareils

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Utilisez ce rapport lorsque vous souhaitez découvrir l'adresse IP et l'emplacement géographique des périphériques qu’un utilisateur spécifique a utilisés pour accéder à Azure Active Directory. | Répertoire > Utilisateurs > <i>Utilisateur</i> > onglet Périphériques |

### Activité

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique les activités de connexion d'un utilisateur. Le rapport inclut des informations telles que l'application à laquelle l’utilisateur s’est connecté, le périphérique utilisé, l’adresse IP et l’emplacement. Nous ne récupérons pas l'historique des utilisateurs qui se connectent à l’aide d’un compte Microsoft. | Répertoire > Utilisateurs > <i>Utilisateur</i> > onglet Activités |

#### Événements de connexion inclus dans le rapport d'activité de l'utilisateur

Seuls certains types d'événements de connexion apparaîtront dans le rapport d'activité de l'utilisateur.

| Type d'événement | Inclus ? |
| ----------------------								| ---------		|
| Connexions au [Volet d'accès](http://myapps.microsoft.com/) | Oui |
| Connexions au [Portail de gestion Azure](https://manage.windowsazure.com/) | Oui |
| Connexions au [Portail Microsoft Azure](http://portal.azure.com/) | Oui |
| Connexions au [Portail Office 365](http://portal.office.com/) | Oui |
| Connexions à une application native, telle qu’Outlook (consultez l'exception ci-dessous) | Oui |
| Connexions à une application fédérée/configurée via le volet d'accès, telle que Salesforce | Oui |
| Connexions à une application basée sur le mot de passe via le volet d'accès, telle que Twitter | Oui |
| Connexions à une application métier personnalisée qui a été ajoutée au répertoire | Non (bientôt disponible) |
| Connexions à une application de proxy de l'application Azure AD qui a été ajoutée au répertoire | Non (bientôt disponible) |

> Remarque : Pour limiter le nombre de parasites dans ce rapport, les connexions à l’application native [Lync/Skype Entreprise](http://products.office.com/fr-FR/skype-for-business/online-meetings) et celles effectuées via l’[Assistant de connexion de Microsoft Online Services](http://community.office365.com/fr-FR/w/sso/534.aspx) ne sont pas indiquées.










## Éléments à prendre en compte si vous suspectez une violation de la sécurité

Si vous suspectez qu'un compte d'utilisateur a été compromis ou tout type d’activité utilisateur suspecte pouvant entraîner une violation de la sécurité de vos données de répertoire dans le cloud, effectuez une ou plusieurs des opérations suivantes : 

- Contactez l'utilisateur pour vérifier l'activité
- Réinitialisez le mot de passe de l'utilisateur.
- [Activez l'authentification multi-facteur](http://go.microsoft.com/fwlink/?linkid=335774) pour renforcer la sécurité

## Afficher ou télécharger un rapport

1. Dans le portail de gestion Azure, cliquez sur **Active Directory**, sur le nom du répertoire de votre société, puis sur **Rapports**.
2. Dans la page Rapports, cliquez sur le rapport que vous souhaitez afficher et/ou télécharger.

    > [AZURE.NOTE]S’il s’agit de votre première utilisation de la fonctionnalité de création de rapports d’Azure Active Directory, un message s’affiche pour sélectionner votre choix. Si vous acceptez, cliquez sur la coche pour continuer.

3. Cliquez sur le menu déroulant situé près d'Intervalle, puis sélectionnez l’une des plages horaires qui doivent être utilisées lors de la génération de ce rapport :
    - 24 dernières heures
    - 7 derniers jours
    - 30 derniers jours
4. Cliquez sur l'icône de coche pour exécuter le rapport.
	- Près de 1 000 événements seront affichés dans le portail de gestion Azure.
5. Le cas échéant, cliquez sur **Télécharger** pour télécharger le rapport dans un fichier compressé au format de valeurs séparées par des virgules (CSV) pour une lecture hors connexion ou à des fins d'archivage.
	- Près de 75 000 événements figureront dans le fichier téléchargé.

## Ignorer un événement

Si vous affichez des rapports d'anomalie, vous remarquerez peut-être que vous pouvez ignorer les différents événements qui sont mentionnés dans les rapports connexes. Pour ignorer un événement, surlignez-le simplement dans le rapport, puis cliquez sur **Ignorer**. Le bouton **Ignorer** supprimera définitivement l'événement surligné du rapport et ne peut être utilisé que par des administrateurs généraux autorisés.

## Notifications automatiques par courrier électronique

Pour plus d'informations sur les notifications de création de rapport Azure AD, consultez [Notifications de création de rapport Azure Active Directory](active-directory-reporting-notifications.md).

## Étapes suivantes

- [Prise en main d’Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)

<!---HONumber=Sept15_HO4-->