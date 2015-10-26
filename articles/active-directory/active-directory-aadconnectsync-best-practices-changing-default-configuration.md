<properties
	pageTitle="Meilleures pratiques pour la modification de la configuration par défaut | Microsoft Azure | Microsoft Azure"
	description="Décrit les meilleures pratiques pour la modification de la configuration par défaut d’Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync : meilleures pratiques pour modifier la configuration par défaut

L’objectif de cette rubrique est de décrire les modifications prises en charge et celles qui ne sont pas prises en charge pour Azure AD Connect Sync.

La configuration créée par Azure AD Connect fonctionne « telle quelle » pour la plupart des environnements qui synchronisent Active Directory local avec Azure AD. Toutefois, dans certains cas, il est nécessaire d’appliquer certaines modifications à une configuration pour répondre à une spécification ou un besoin particulier.

## Modifications apportées au compte de service
Azure AD Connect Sync s’exécute sous un compte de service créé par l’Assistant Installation. Ce compte de service conserve les clés de chiffrement dans la base de données utilisée par la synchronisation. Il est créé avec un mot de passe de 127 caractères de long, défini pour ne pas expirer.

- La modification ou la réinitialisation du mot de passe du compte de service **n’est pas prise en charge**. Sa modification ou sa réinitialisation supprimera les clés de chiffrement et le service ne pourra plus accéder à la base de données ni démarrer.

## Modifications apportées au planificateur
Azure AD Connect Sync est configuré pour synchroniser les données d’identité toutes les 3 heures. Lors de l’installation, une tâche planifiée est créée, qui s’exécute sous un compte de service avec des autorisations pour utiliser le serveur de synchronisation.

- La modification de la tâche planifiée **n’est pas prise en charge**. Le mot de passe du compte de service n’est pas connu. Consultez [Modifications apportées au compte de service](#changes-to-the-service-account)
- La synchronisation à une fréquence supérieure à la fréquence par défaut de 3 heures **n’est pas prise en charge**.

## Modifications apportées aux règles de synchronisation

Bien que l’application de modifications à votre configuration d’Azure AD Connect Sync soit prise en charge, vous devez les appliquer avec précaution, car Azure AD Connect Sync est supposé être le plus proche possible d’une appliance.

Voici une liste de comportements attendus :

- Après la mise à niveau d’Azure AD Connect vers une version plus récente, la valeur par défaut est rétablie pour la plupart des paramètres.
- Les modifications apportées aux règles de synchronisation de base sont perdues après la mise à niveau.
- Les règles de synchronisation de base supprimées sont recréées en cas de mise à niveau vers une version plus récente.
- Les règles de synchronisation personnalisées que vous avez créées restent intactes en cas de mise à niveau vers une version plus récente.



Lorsque vous devez modifier la configuration par défaut, procédez comme suit :

- Lorsque vous devez modifier le flux d’attributs d’une règle de synchronisation de base, ne modifiez pas cette règle. Créez plutôt une nouvelle règle de synchronisation avec une priorité plus élevée (valeur numérique inférieure), qui contient le flux d’attributs souhaité.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.
- Si vous devez modifier la portée ou le paramètre de jointure dans une règle de synchronisation de base, documentez l’opération et appliquez la modification après la mise à niveau vers une version plus récente d’Azure AD Sync.



## Étapes suivantes
En savoir plus sur la configuration d’[Azure AD Connect sync](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!--Image references-->

<!---HONumber=Oct15_HO3-->