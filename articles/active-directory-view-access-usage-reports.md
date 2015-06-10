<properties 
	pageTitle="Afficher vos rapports d'accès et d'utilisation" 
	description="Une rubrique qui vous explique comment afficher les rapports d'accès et d'utilisation pour évaluer l'intégrité et la sécurité du répertoire de votre société." 
	services="active-directory" 
	documentationCenter="" 
	authors="kenhoff" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2015" 
	ms.author="kenhoff;Justinha"/>

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
- Les clients situés en Chine peuvent accéder à Azure Active Directory Premium ainsi qu’aux éditions De base à l'aide de l'instance mondiale d'Azure Active Directory. Actuellement, Azure Active Directory Premium et les éditions De base ne sont pas pris en charge dans le service Microsoft Azure utilisé par 21Vianet en Chine. Pour plus d'informations, contactez-nous sur le [Forum d’Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).


## Rapports d'anomalies

Nom du rapport | Disponible dans cette édition    	
------------- | -------------  
[Connexions à partir de sources inconnues](#sign-ins-from-unknown-sources) | Gratuit et premium
[Connexions après plusieurs échecs](#sign-ins-after-multiple-failures) | Gratuit et premium
[Connexions depuis plusieurs zones géographiques](sign-ins-from-multiple-geographies) | Gratuit et premium
[Connexions à partir d’adresses IP affichant une activité suspecte](#sign-ins-from-ip-addresses-with-suspicious-activity) | Premium
[Activité de connexion anormale](#anamolous-sign-in-activity) | Premium
[Connexions à partir de périphériques potentiellement infectés](#sign-ins-from-possibly-infected-devices) | Premium
[Utilisateurs ayant une activité de connexion anormale](#users-with-anomalous-sign-in-activity) | Premium
[Utilisation des applications : résumé](#application-usage-summary) | Premium
[Utilisation des applications : présentation détaillée](#application-usage-detailed) | Premium
[Tableau de bord de l’application](#application-dashboard) | Gratuit et premium
[Erreurs de configuration de compte](#account-provisioning-errors) | Gratuit et premium
[Appareils](#devices) | Premium
[Activité](#activity) | Gratuit et premium
[Rapport d'audit](#audit-report) | Gratuit et premium
[Rapport d'activité de groupes](#groups-activity-report) | Premium
[Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe](#password-reset-registration-activity-report) | Premium
[Activité de réinitialisation de mot de passe](#password-reset-activity) | Premium
 

### Connexions à partir de sources inconnues

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport indique les utilisateurs qui sont parvenus à se connecter à votre répertoire lors de l’affectation d’une adresse IP client qui a été reconnue par Microsoft en tant qu'adresse IP proxy anonyme. Ces proxys sont souvent utilisés par les utilisateurs qui souhaitent masquer l'adresse IP de leur ordinateur, et peuvent également être utilisés dans un but malveillant : les pirates utilisent parfois ces proxys. </p><p> Les résultats de ce rapport indiquent le nombre de fois où un utilisateur est parvenu à se connecter à votre répertoire à partir de cette adresse et de l'adresse IP du proxy.</p> | Répertoire > onglet Rapports |

### Connexions après plusieurs échecs

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Ce rapport indique les utilisateurs qui sont parvenus à se connecter, mais après plusieurs tentatives de connexion infructueuses. Les causes possibles sont : <ul><li>L'utilisateur a oublié son mot de passe</li><li>Son mot de passe lui a été dérobé</li></ul><p>Les résultats de ce rapport vous indiqueront le nombre de tentatives consécutives effectuées par l’utilisateur avant de parvenir à se connecter et l'horodatage associé à la première connexion réussie.</p><p><b>Paramètres de rapport</b> : vous pouvez configurer le nombre minimal de tentatives de connexion infructueuses qui doivent être effectuées consécutivement avant d'apparaître dans le rapport. Lorsque vous modifiez ce paramètre, il est important de savoir que ces modifications ne seront pas appliquées aux échecs de connexion existants qui sont déjà mentionnés dans un rapport existant. Elles seront toutefois appliquées à toutes les connexions futures. Cependant, seuls les administrateurs titulaires d’une licence peuvent effectuer ces modifications. | Répertoire > onglet Rapports |

### Connexions depuis plusieurs zones géographiques

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport inclut les activités de connexion réussie d'un utilisateur durant lesquelles deux connexions semblent être issues de régions différentes, mais dont le laps de temps constaté entre ces connexions ne permet pas à l'utilisateur de se rendre d’une région à une autre. Les causes possibles sont :</p><ul><li>L’utilisateur partage son mot de passe</li><li>L’utilisateur utilise un bureau à distance pour se connecter via un navigateur Web</li><li>Un pirate s'est connecté au compte d'un utilisateur depuis un autre pays.</li></ul><p>Les résultats de ce rapport vous indiqueront les événements de connexion réussie, ainsi que le laps de temps constaté entre les connexions, les régions d’où semblent provenir les connexions et le temps qu’il faut à l’utilisateur pour se rendre d’une région à une autre.</p><p>Cette durée indiquée n’est qu'une estimation et elle peut être différente de la durée réelle entre ces emplacements. De plus, aucun événement n'est généré pour les connexions constatées dans différentes régions voisines.</p> | Répertoire > onglet Rapports |

### Connexions à partir d’adresses IP affichant une activité suspecte

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport inclut les tentatives de connexion qui ont été effectuées à partir d'adresses IP depuis lesquelles une activité suspecte a été observée. L’activité suspecte inclut plusieurs tentatives de connexion infructueuses à partir de la même adresse IP sur une période relativement courte, et toute autre activité considérée comme suspecte. Cela peut signifier qu'un pirate a essayé de se connecter à partir de cette adresse IP.</p><p>Les résultats de ce rapport vous indiqueront les tentatives de connexion qui proviennent d'une adresse IP depuis laquelle une activité suspecte a été détectée, ainsi que l'horodatage associé à la connexion.</p> | Répertoire > onglet Rapports |

### Activité de connexion anormale

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Ce rapport inclut les connexions qui ont été identifiées comme « anormales » par nos algorithmes d'apprentissage automatique. Une tentative de connexion peut être considérée comme anormale si celle-ci a été effectuée depuis un emplacement inattendu et/ou à une heure également suspecte. Cela peut signifier qu'un pirate a essayé de se connecter à l'aide de ce compte. L'algorithme d'apprentissage automatique considère les événements comme « anormaux » ou « suspects », où « suspects » indique une probabilité plus élevée de violation de la sécurité.</p><p>Les résultats de ce rapport vous indiqueront ces connexions, ainsi que la classification, l’emplacement et l’horodatage associé à chaque connexion.</p><p>Nous enverrons une notification par courrier électronique aux administrateurs généraux si nous constatons 10 événements de connexion ou plus en l’espace de 30 jours. Veillez à inclure aad-alerts-noreply@mail.windowsazure.com dans votre liste d'expéditeurs approuvés.</p> | Répertoire > onglet Rapports |

### Connexions à partir de périphériques potentiellement infectés

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Utilisez ce rapport lorsque vous souhaitez découvrir les connexions effectuées depuis des périphériques sur lesquels des logiciels malveillants peuvent s’exécuter. Nous mettons en corrélation les adresses IP des connexions et les adresses IP à partir desquelles une tentative a été effectuée pour contacter un serveur de logiciels malveillants.</p><p>Recommandation : ce rapport considère qu'une adresse IP a été associée au même périphérique dans les deux cas. Nous vous recommandons donc de contacter l'utilisateur et d'analyser le périphérique de l'utilisateur pour vous en assurer.</p><p>Pour plus d'informations sur le traitement des infections de logiciels malveillants, consultez le [Centre de protection contre les logiciels malveillants](http://go.microsoft.com/fwlink/?linkid=335773). </p> | Répertoire > onglet Rapports |

### Utilisateurs ayant une activité de connexion anormale

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| <p>Utilisez ce rapport lorsque vous souhaitez afficher tous les comptes d'utilisateurs pour lesquels une activité de connexion anormale a été identifiée. Ce rapport inclut des données provenant de tout autre rapport d'activité anormale. Les résultats de ce rapport vous fourniront plus d'informations sur l'utilisateur et vous indiqueront pourquoi l'événement de connexion a été identifié comme anormal, de même que la date et l’heure, et d’autres informations pertinentes au sujet de l'événement.</p> | Répertoire > onglet Rapports |

## Rapports d’application intégrée

### Utilisation des applications : résumé

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Utilisez ce rapport pour afficher l'utilisation de toutes les applications SaaS dans votre répertoire. Ce rapport est basé sur le nombre de clics effectués par les utilisateurs sur l'application dans le volet d'accès. | Répertoire > onglet Rapports |

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
| Connexions à une application basée sur le mot de passe via le volet d'accès, telle que Twitter | Non (bientôt disponible) |
| Connexions à une application métier personnalisée qui a été ajoutée au répertoire | Non (bientôt disponible) |

> Remarque : Pour limiter le nombre de parasites dans ce rapport, les connexions à l’application native [Lync/Skype Entreprise](http://products.office.com/fr-fr/skype-for-business/online-meetings) et celles effectuées via l’[Assistant de connexion de Microsoft Online Services](http://community.office365.com/fr-fr/w/sso/534.aspx) ne sont pas indiquées.

## Journaux d’activité

### Rapport d'audit

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique un enregistrement de tous les événements audités durant les 24 dernières heures, 7 derniers jours ou 30 derniers jours. <br />Pour plus d'informations, consultez la rubrique [Événements de rapport d’audit d'Azure Active Directory](active-directory-reporting-audit-events.md)  | Répertoire > onglet Rapports |

### Rapport d'activité de groupes

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique toutes les activités des groupes libre-service gérés dans votre répertoire. | Répertoire > Utilisateurs > <i>Utilisateur</i> > onglet Périphériques |

### Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique l’ensemble des enregistrements de réinitialisation de mot de passe réalisés dans votre société | Répertoire > onglet Rapports |

### Activité de réinitialisation de mot de passe

| Description | Emplacement du rapport |
| :-------------     | :-------        |
| Indique toutes les tentatives de réinitialisation de mot de passe réalisées dans votre société | Répertoire > onglet Rapports |

## Éléments à prendre en compte si vous suspectez une violation de la sécurité

Si vous suspectez qu'un compte d'utilisateur a été compromis ou tout type d’activité utilisateur suspecte pouvant entraîner une violation de la sécurité de vos données de répertoire dans le cloud, effectuez une ou plusieurs des opérations suivantes : 

- Contactez l'utilisateur pour vérifier l'activité
- Réinitialisez le mot de passe de l'utilisateur.
- [Activez l'authentification multi-facteur](http://go.microsoft.com/fwlink/?linkid=335774) pour renforcer la sécurité

## Afficher ou télécharger un rapport

1. Dans le portail de gestion Azure, cliquez sur **Active Directory**, sur le nom du répertoire de votre société, puis sur **Rapports**.
2. Dans la page Rapports, cliquez sur le rapport que vous souhaitez afficher et/ou télécharger.
    >
    > [AZURE.NOTE]
    
3. Cliquez sur le menu déroulant situé près d'Intervalle, puis sélectionnez l’une des plages horaires qui doivent être utilisées lors de la génération de ce rapport :
    - 24 dernières heures
    - 7 derniers jours
    - 30 derniers jours
4. Cliquez sur l'icône de coche pour exécuter le rapport.
5. Le cas échéant, cliquez sur **Télécharger** pour télécharger le rapport dans un fichier compressé au format de valeurs séparées par des virgules (CSV) pour une lecture hors connexion ou à des fins d'archivage.

## Ignorer un événement

Si vous affichez des rapports d'anomalie, vous remarquerez peut-être que vous pouvez ignorer les différents événements qui sont mentionnés dans les rapports connexes. Pour ignorer un événement, surlignez-le simplement dans le rapport, puis cliquez sur **Ignorer**. Le bouton **Ignorer** supprimera définitivement l'événement surligné du rapport et ne peut être utilisé que par des administrateurs généraux autorisés.

## Notifications automatiques par courrier électronique 

### Quels rapports génèrent les notifications par courrier électronique

Pour l’instant, seuls le rapport d’activité de connexion anormale et le rapport d’activité des connexions anormales utilisent le système de notification par courrier électronique.

### Que déclenche l’envoi de la notification par courrier électronique ?

Par défaut, Azure Active Directory est configuré pour envoyer automatiquement des notifications par courrier électronique à tous les administrateurs généraux. Le courrier électronique est envoyé dans les conditions suivantes pour chaque rapport.

Pour le rapport d’activité de connexion anormale :

- Sources inconnues : 10 événements
- Plusieurs échecs : 10 événements
- Adresses IP affichant une activité suspecte : 10 événements
- Périphériques infectés : 10 événements

Pour les utilisateurs ayant un rapport d’activité de connexion anormale :

- Sources inconnues : 10 événements
- Plusieurs échecs : 10 événements
- Adresses IP affichant une activité suspecte : 10 événements
- Périphériques infectés : 5 événements
- Rapport de connexions anormales : 15 événements

Le courrier électronique est envoyé si une des conditions mentionnées ci-dessus est remplie dans les 30 jours, ou lorsque le dernier courrier électronique a été envoyé il y a moins de 30 jours.

Les connexions dites anormales sont celles qui ont été identifiées comme « anormales » par nos algorithmes d’apprentissage automatique, car elles ont été effectuées depuis un emplacement inattendu et/ou à une heure également suspecte. Cela peut signifier qu'un pirate a essayé de se connecter à l'aide de ce compte. Le tableau ci-dessus fournit plus d'informations sur ce rapport.

### Qui reçoit les notifications par courrier électronique ?

Le courrier électronique est envoyé à tous les administrateurs généraux titulaires d'une licence Active Directory Premium. Par mesure de précaution, ce courrier électronique est également envoyé sur leur adresse de messagerie de secours. Les administrateurs doivent inclure aad-alerts-noreply@mail.windowsazure.com dans leur liste d'expéditeurs approuvés pour ne pas rater le courrier électronique.

### Quelle est la fréquence d’envoi de ces courriers électroniques ?

Une fois le premier courrier électronique envoyé, un second suivra lorsque 10 événements de connexion anormale se seront produits dans les 30 jours suivant l’envoi du premier courrier électronique. Comment puis-je accéder au rapport mentionné dans le courrier électronique ?

Lorsque vous cliquerez sur le lien, vous serez redirigé vers la page du rapport du portail de gestion Azure. Pour accéder au rapport, vous devez être à la fois :

- Un administrateur ou un coadministrateur de votre abonnement Azure
- Un administrateur général du répertoire, titulaire d’une licence Active Directory Premium. Pour plus d’informations, consultez les éditions d’Azure Active Directory. 

### Puis-je désactiver ces courriers électroniques ?

Oui, pour désactiver les notifications liées à des connexions anormales dans le portail de gestion Azure, cliquez sur **Configurer**, puis sélectionnez **Désactivé** sous la rubrique **Notifications**.

## Étapes suivantes

- [Prise en main d'Azure Active Directory Premium (AD)](active-directory-get-started-premium.md)
- [Ajouter des stratégies de promotion professionnelles aux pages du volet d’accès et de connexion](active-directory-add-company-branding.md)

<!---HONumber=58-->