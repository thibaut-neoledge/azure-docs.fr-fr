<properties 
	pageTitle="Surveillez votre infrastructure d’identité locale dans le cloud." 
	description="Cette page décrit Azure AD Connect Health et vous explique pourquoi l’utiliser." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Surveillance de votre infrastructure d’identité locale dans le cloud

Azure AD Connect Health vous permet de surveiller votre infrastructure d’identité locale et d’en tirer des informations. Vous pouvez ainsi consulter des alertes, en savoir plus sur les performances, connaître les modèles d’utilisation, découvrir les paramètres de configuration, maintenir une connexion fiable à Office 365 et bien plus encore. Tout ceci peut être accompli à l’aide d’un agent installé sur les serveurs cibles. Pour plus d’informations sur la configuration requise et l’installation d’Azure AD Connect Health, consultez la section [Spécifications d’AD Connect Health](active-directory-aadconnect-health-requirements.md).

![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Ces informations sont toutes disponibles dans le portail Azure AD Connect Health. Ce portail vous permet d’afficher des alertes, de surveiller les performances et d’analyser les utilisations. Ces informations sont présentées au même endroit pour faciliter leur utilisation et vous permettre de gagner du temps en trouvant immédiatement l’information dont vous avez besoin.

![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Les mises à jour prévues pour Azure AD Connect Health incluent une amélioration de la surveillance et de l’analyse d’autres composants d’identité et de services tels que les services Azure AD Connect Sync. Vous pouvez ainsi bénéficier d’un tableau de bord unique focalisé sur l’identité, vous permettant de profiter d’un environnement intégré, sécurisé et robuste que vos utilisateurs peuvent exploiter pour améliorer leur productivité.


![Présentation d’Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Pourquoi utiliser Azure AD Connect Health

L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Toutefois, avec cette intégration, vous devez vous assurer que cet environnement est sécurisé pour que les utilisateurs puissent accéder aux ressources locales et cloud en toute sécurité depuis n’importe quel appareil. Azure AD Connect Health fournit une approche basée sur le cloud pour surveiller et obtenir facilement des informations sur l’infrastructure d’identité locale que vous utilisez pour accéder à Office 365 ou à d’autres applications Azure AD. Son installation est aussi simple que celle d’un agent sur chacun de vos serveurs d’identité local.

Azure AD Connect Health pour AD FS prend en charge AD FS 2.0 dans Windows Server 2008/2008 R2, AD FS Windows Server 2012/2012 R2. Cette prise en charge inclut également tous les serveurs AD FS Proxy AD FS ou les serveurs Proxy d’application web qui permettent la prise en charge de l’authentification pour l’accès extranet. Azure AD Connect Health pour AD FS fournit les fonctionnalités clé suivantes :

- Affichage d’alertes et prises de mesures pour accéder de façon fiable aux applications AD FS protégées, y compris Azure AD
- Notifications par courrier électronique pour les alertes critiques
- Affichage des données de performances pour déterminer la planification de capacité
- Affichage détaillé de vos modèles de connexion AD FS pour déterminer les anomalies ou établir des lignes de base pour la planification de capacité

La vidéo suivante fournit une vue d’ensemble sur Azure AD Connect Health :

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]


## Première utilisation d’Azure Active Directory Connect Health à partir du portail Azure
Pour démarrer avec Azure Active Directory Connect Health, exécutez la procédure suivante. Avant de pouvoir consulter des données sur votre instance d’Azure AD Connect Health, il vous faudra installer l’agent Azure AD Connect Health sur vos serveurs cibles. Pour télécharger l’agent Azure AD Connect Health, dans le premier panneau, sélectionnez Démarrage rapide et Obtenir les outils. Pour télécharger directement l’agent, cliquez sur le lien ci-dessous. Pour utiliser Azure Active Directory Connect Health, procédez comme suit :

1. Connectez-vous au [portail Microsoft Azure.](https://portal.azure.com/)
2. Pour accéder à Azure Active Directory Connect Health, rendez-vous sur Marketplace et recherchez ce composant, ou sélectionnez Marketplace, puis Sécurité + Identité.
3. Sur le panneau de présentation (un panneau correspond à une portion de la vue générale ; considérez les panneaux comme des fenêtres ou des ouvertures), cliquez sur Créer. Un autre panneau s’ouvre ; il comporte vos informations de répertoire.
4. Sur le panneau de répertoire, cliquez sur Créer. Pour utiliser Azure AD Connect Health, vous devez posséder une licence Azure Active Directory Premium. Pour plus d’informations sur Azure AD Premium, consultez la rubrique Prise en main d’Azure AD Premium.






![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/portal1.png)



## Le portail Azure Active Directory Connect Health
Ce portail vous permet d’afficher des alertes, de surveiller les performances et d’analyser les utilisations. Lorsque vous accédez pour la première fois à Azure AD Connect Health, vous verrez le premier panneau. Un panneau est une portion de la vue générale. Considérez les panneaux comme des fenêtres. Le premier panneau affiché comporte les options Démarrage rapide, Services et Configurer. Vous trouverez sous la capture une brève description de chacune d’entre elles.

![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/portal2.png)

- **Démarrage rapide** : sélectionnez cette option pour ouvrir le panneau Démarrage rapide. Ici, téléchargez l’agent Azure AD Connect Health en sélectionnant Obtenir les outils, accédez à la documentation et fournissez des commentaires.
- **Active Directory Federation Services** : ce panneau représente l’ensemble des services AD FS actuellement surveillés par Azure AD Connect Health. Les options qui apparaissent dans cette rubrique seront évoquées dans la section ci-dessous. Consultez la rubrique Services Azure Active Directory Connect Health.
- Configurer : vous pouvez ici activer ou désactiver les éléments suivants :
<ol>
1. La mise à jour automatique de l’agent Azure AD Connect Health vers la version la plus récente. Concrètement, cela signifie que votre système est automatiquement mis à jour vers la dernière version disponible de l’agent Azure AD Connect Health. Cette option est activée par défaut.
2. L’autorisation de l’accès, par Microsoft, aux données d’intégrité de votre répertoire Azure AD à des fins exclusives de résolution des problèmes. Si cette option est activée, Microsoft pourra consulter les données auxquelles vous avez accès. Cette option simplifie la résolution des problèmes. Elle est désactivée par défaut.




## Services Azure Active Directory Connect Health
Cette section représente les services actifs et les instances de ces services surveillés par Azure AD Connect Health. Cliquez sur les points de suspension pour ouvrir un panneau affichant l’ensemble des instances.

![Services Azure AD Connect Health](./media/active-directory-aadconnect-health/portal3.png)

Si vous sélectionnez une des instances, Azure AD Connect Health ouvre un panneau comportant des informations sur cette instance de services. Ce panneau est une mine d’informations relatives à cette instance. Il comporte une vue d’ensemble, les propriétés, les alertes, les données de surveillance et une analyse de l’utilisation. Pour en savoir plus sur ces éléments, cliquez sur les liens vers les sections suivantes, situés en haut de cette page.



----------------------------------------------------------------------------------------------------------
## Téléchargement de l’agent Azure AD Connect Health

Pour commencer à utiliser Azure AD Connect Health, vous pouvez télécharger sa dernière version ici : [Télécharger l’agent Azure AD Connect Health](http://go.microsoft.com/fwlink/?LinkID=518973) Vérifiez que vous avez ajouté le service à partir de Marketplace avant d’installer les agents.

----------------------------------------------------------------------------------------------------------

## Alertes Azure Active Directory Connect Health
Cette section vous fournit une liste des alertes actives. Chaque alerte inclut les informations associées, la procédure de résolution et des liens vers la documentation afférente. Si vous sélectionnez une alerte active ou résolue, vous verrez apparaître un nouveau panneau comportant des informations supplémentaires, ainsi qu’une procédure de résolution de l’alerte et des liens vers de la documentation supplémentaire. Vous pouvez également afficher des données d’historique sur les alertes résolues par le passé.

![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/alert1.png)

Si vous sélectionnez une alerte, vous aurez accès à des informations supplémentaires, à une procédure de résolution de l’alerte et à des liens vers de la documentation supplémentaire.

![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## Surveillance des performances d’Azure Active Directory Connect Health
L’analyse des performances Azure AD Connect Health fournit des informations d’analyse sur les mesures. Si vous sélectionnez la zone Surveillance, un panneau s’ouvre et présente des informations détaillées sur les mesures.


![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)


Si vous sélectionnez l’option Filtre en haut du panneau, vous pouvez filtrer par serveur afin d’afficher les mesures spécifiques à chacun d’entre eux. Pour modifier les mesures, cliquez avec le bouton droit sur le graphique de surveillance situé sous le panneau de surveillance, puis sélectionnez Modifier le graphique. Ensuite, dans le nouveau panneau ouvert, vous pouvez sélectionner des mesures supplémentaires à partir de la liste déroulante et spécifier une plage de temps pour l’affichage des données de performances.


![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/perf2.png)

## Analyse de l’utilisation et rapports Azure Active Directory Connect Health
L’analyse de l’utilisation d’Azure AD Connect Health observe le trafic d’authentification des serveurs de fédération. Sélectionnez la zone Analyse de l’utilisation pour ouvrir le panneau Analyse de l’utilisation, qui vous indique les mesures et les regroupements.

>[AZURE.NOTE]Pour utiliser l’analyse de l’utilisation avec AD FS, vous devez vous assurer que les audits AD FS sont activés. Pour plus d’informations, consultez la section Spécifications d’AD Connect Health.

![Portail Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Pour sélectionner des mesures supplémentaires, spécifiez une plage de temps ou, pour modifier le regroupement, cliquez avec le bouton droit sur le graphique d’analyse de l’utilisation, puis sélectionnez Modifier le graphique. Vous pouvez ensuite spécifier la plage de temps, modifier ou sélectionner les mesures et modifier le regroupement. Vous pouvez afficher la distribution du trafic d’authentification en fonction de « mesures » différentes et regrouper les mesures en fonction des paramètres de « regroupement » appropriés décrits ci-dessous.

| Mesure | Regroupement | En quoi consiste le regroupement ? Quel est son intérêt ? |
| ------ | -------- | -------------------------------------------- |
| Nombre total de requêtes : le nombre total de requêtes traitées par le service de fédération | Tout | Ce paramètre indique le nombre total de requêtes sans regroupement. |
| | Application | Cette option regroupe la totalité des requêtes en fonction de la partie de confiance cible. Ce regroupement permet de comprendre le pourcentage du trafic total reçu par chaque application. |
| | Serveur | Cette option regroupe la totalité des requêtes en fonction du serveur qui a traité la requête. Ce regroupement permet de mieux comprendre la distribution de charge du trafic total. |
| | Jonction d’espace de travail | Cette option regroupe la totalité des requêtes en fonction de la provenance des requêtes, selon qu’elles soient ou non issues d’appareils joints à un espace de travail (connu). Ce regroupement permet de comprendre si vos ressources sont consultées à l’aide d’appareils inconnus de l’infrastructure d’identité. |
| | Méthode d'authentification | Cette option permet de regrouper la totalité des requêtes en fonction de la méthode d’authentification utilisée. Ce regroupement permet de mieux comprendre la méthode courante d’authentification utilisée. Voici les méthodes d’authentification possibles <ol> <li>Authentification intégrée de Windows (Windows)</li> <li>Authentification basée sur les formulaires (formulaires)</li> <li>Authentification unique</li> <li>Authentification de certificat X509</li> <br>Notez que la requête fait l’objet d’une authentification unique si les serveurs de fédération la reçoivent avec un cookie d’authentification unique. Le cas échéant, si le cookie est valide, l’utilisateur ne doit pas fournir d’informations d’identification et bénéficie d’un accès transparent à l’application. Cette configuration est courante si vous disposez de multiples parties de confiance protégées par les serveurs de fédération. |
| | Emplacement réseau | Cette option permet de regrouper la totalité des requêtes en fonction de l’emplacement du réseau de l’utilisateur. Il peut s’agir d’un réseau intranet ou extranet. Ce regroupement permet de comparer les pourcentages de trafic issus de l’internet et de l’extranet. |
| Nombre total de requêtes échouées : nombre total de requêtes échouées traitées par le service de fédération. <br> (Cette mesure est disponible uniquement sur AD FS pour Windows Server 2012 R2)| Type d’erreur | Cette option indique le nombre d’erreurs en fonction des types d’erreur prédéfinis. Ce regroupement permet de mieux comprendre les types courants d’erreurs. <ul><li>Mot de passe ou nom incorrect : erreurs dues à un mot de passe ou un nom incorrect.</li> <li>« Verrouillage extranet » : défaillances provoquées par les requêtes reçues d’un utilisateur ne disposant d’aucun accès à l’extranet </li><li> « Mot de passe expiré » : défaillances provoquées par des utilisateurs se connectant avec des mots de passe expirés </li><li>« Compte désactivé » : défaillances dues aux utilisateurs se connectant avec un compte désactivé </li><li>« Authentification de l’appareil » : défaillances provoquées par les utilisateurs ne réussissant pas à s’authentifier à l’aide de l’authentification de l’appareil </li><li>« Authentification par certificat utilisateur » : défaillances dues aux utilisateurs ne réussissant pas à se connecter à cause d’un certificat non valide </li><li>« MFA » : défaillances dues aux utilisateurs ne réussissant pas à s’identifier à l’aide de l’authentification multifacteur (Multi Factor Authentication, MFA) </li><li>« Autres informations d’identification » : « Autorisation d’émission » : défaillances dues à des défauts d’autorisation </li><li>« Délégation d’émission » : défaillances dues à des erreurs de délégation d’émission </li><li>« Acceptation de jetons » : défaillances dues au rejet, par ADFS, du jeton d’un fournisseur d’identité tiers </li><li>« Protocole » : défaillances dues à des erreurs de protocoles.</li><li>« Inconnu » : répertorie tous les éléments. Les autres défaillances n’entrent pas dans les catégories définies.</li> |
| | Serveur | Cette option regroupe les erreurs en fonction du serveur. Elle permet de mieux comprendre la distribution des erreurs entre les serveurs. Une distribution inégale peut indiquer qu’un serveur présente un état défectueux. |
| | Emplacement réseau | Cette option regroupe les erreurs basées sur l’emplacement réseau des requêtes (intranet et extranet). Elle procure une meilleure visibilité sur le type des requêtes défaillantes. |
| | Application | Ici, les défaillances basées sur l’application cible (partie de confiance) sont regroupées. Elle permet d’identifier l’application cible présentant le nombre le plus important d’erreurs. |
| Nombre d’utilisateurs : nombre moyen d’utilisateurs actifs dans le système | Tout | Cette option fournit le nombre moyen de personnes utilisant le service de fédération durant l’intervalle sélectionné. Les utilisateurs ne sont pas regroupés. <br>Cette moyenne dépend de l’intervalle sélectionné. |
| | Application | Cette option regroupe le nombre moyen d’utilisateurs sur l’application cible (partie de confiance). Elle procure une plus grande visibilité sur le nombre d’utilisateurs exécutant chaque application. |

## Étapes suivantes
Pour démarrer avec Azure AD Connect Health, consultez la section [Spécifications d’AD Connect Health](active-directory-aadconnect-health-requirements.md). Une fois que l’agent est installé et collecte les données, consultez [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md) pour en savoir plus sur la configuration d’Azure AD Connect Health, ou consultez le [FAQ.](active-directory-aadconnect-health-faq.md)


**Ressources supplémentaires**

* [Azure AD Connect Health sur MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=July15_HO4-->