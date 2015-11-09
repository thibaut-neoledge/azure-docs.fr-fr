<properties
   pageTitle="Conditions préalables pour Azure Active Directory Connect | Microsoft Azure"
   description="Description de l'article qui sera affichée sur les pages de destination et dans la plupart des résultats de recherche"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Conditions préalables pour Azure Active Directory Connect (Azure AD Connect)
Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect.

## Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici ce dont vous aurez besoin.

**Azure AD**

- Un abonnement Azure ou un [abonnement d'essai Azure](http://azure.microsoft.com/pricing/free-trial/). Cette condition est nécessaire uniquement pour accéder au portail Azure mais pas pour l'utilisation d’Azure AD Connect. Si vous utilisez PowerShell ou Office 365, vous n’avez pas besoin d’un abonnement Azure pour utiliser Azure AD Connect. Si vous possédez une licence Office 365, vous pouvez également utiliser le portail Office 365. Avec une licence Office 365 payante, vous pouvez également vous connecter au portail Azure à partir du portail Office 365.
- Vérifiez le domaine que vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous envisagez d’utiliser contoso.com pour vos utilisateurs, vérifiez que ce domaine a été vérifié et que vous n’utilisez pas uniquement le domaine par défaut contoso.onmicrosoft.com.
- Un annuaire Azure AD autorise par défaut 50 000 objets. Quand vous vérifiez le domaine, la limite est augmentée à 300 000 objets. Si vous avez besoin de davantage d’objets dans Azure AD, vous devez ouvrir une demande de support pour que la limite soit relevée en conséquence. Si vous avez besoin de plus de 500 000 objets, vous avez besoin d’une licence Office 365, Azure AD De base, Azure AD Premium ou Enterprise Mobility Suite.

**Environnement et serveurs locaux**

- La version de schéma Active Directory et le niveau fonctionnel de forêt doivent être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version aussi longtemps que les exigences relatives au schéma et le niveau de forêt sont remplies.
- Azure Active Directory Connect doit être installé sur Windows Server 2008 ou version ultérieure. Ce serveur peut être un contrôleur de domaine ou un serveur membre si vous utilisez la configuration rapide. Si vous utilisez des paramètres personnalisés, le serveur peut également être autonome et n’a pas besoin d’être joint à un domaine.
- Si vous prévoyez d’utiliser la synchronisation de mot de passe, le serveur doit s’exécuter sur Windows Server 2008 R2 SP1 ou version ultérieure.
- Si les services de fédération Active Directory sont déployés, les serveurs sur lesquels ces services ou le proxy d’application web doivent être installés doivent être Windows Server 2012 R2 ou version ultérieure. La gestion à distance de Windows doit être activée sur ces serveurs pour l’installation à distance.
- Azure AD Connect nécessite une base de données SQL Server pour stocker les données d’identité. Une base de données SQL Server 2012 Express LocalDB (version légère de SQL Server Express) est installée par défaut et le compte du service est créé sur l'ordinateur local. SQL Server Express a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous avez besoin de gérer un volume plus important d’objets d’annuaire, vous devez pointer le processus d’installation vers une autre version de SQL Server. Azure AD Connect prend en charge toutes les versions de Microsoft SQL Server à partir de SQL Server 2008 (SP4) et jusqu’à SQL Server 2014.

**Comptes**

- Un compte d’administrateur général Azure AD pour l’annuaire Azure AD auquel vous souhaitez effectuer l’intégration
- Un compte d’administrateur d’entreprise pour votre annuaire Active Directory local si vous utilisez la configuration rapide ou effectuez une mise à niveau depuis DirSync.
- [Comptes Active Directory](active-directory-aadconnect-accounts-permissions.md) si vous utilisez le chemin d’installation des paramètres personnalisés.

**Connectivité**

- Si vous utilisez un proxy sortant pour la connexion à Internet, le paramètre suivant dans le fichier **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** doit être ajouté pour que l’Assistant d’installation et le dispositif de synchronisation d’Azure AD puissent se connecter à Internet et à Azure AD.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Ce texte doit être entré en bas du fichier. Dans ce code, &lt;PROXYADRESS&gt; représente le nom d’hôte ou l’adresse IP réel du proxy. -Si votre proxy limite les URL effectivement accessibles, les URL documentées dans [URL et plages d’adresses IP Office 365](https://support.office.com/fr-FR/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doivent être ouvertes dans le proxy.

**Autres**

- Facultatif : un compte d’utilisateur test pour vérifier la synchronisation.

## Configuration requise pour les composants

Azure AD Connect repose sur PowerShell et .Net 4.5.1. Selon votre version de Windows Server, procédez comme suit :


- Windows Server 2012 R2
  - PowerShell est installé par défaut ; aucune action n’est nécessaire.
  - Les versions .NET 4.5.1 et ultérieures sont offertes par le biais de Windows Update. Assurez-vous que vous avez installé les dernières mises à jour de Windows Server dans le Panneau de configuration.
- Windows Server 2008 R2 et Windows Server 2012
  - La dernière version de PowerShell est disponible dans **Windows Management Framework 4.0**, récupérable du [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
  - Les versions .NET 4.5.1 et ultérieures sont disponibles dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La dernière version prise en charge de PowerShell est disponible dans **Windows Management Framework 3.0**, disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
 - Les versions .NET 4.5.1 et ultérieures sont disponibles dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).

## Composants de prise en charge d’Azure AD Connect

Voici une liste de composants qu’Azure AD Connect installe sur le serveur sur lequel il est installé. Cette liste est destinée à une installation Express de base. Si vous choisissez d’utiliser un autre serveur SQL Server dans la page d’installation des services de synchronisation, SQL Express LocalDB n’est pas installé localement.

- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Client natif Microsoft SQL Server 2012
- Base de données locale Microsoft SQL Server 2012 Express
- Module Azure Active Directory pour Windows PowerShell
- Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique
- Package de redistribution Microsoft Visual C++ 2013


## Configuration matérielle requise pour Azure AD Connect
Le tableau ci-dessous présente la configuration minimale requise pour l’ordinateur de synchronisation Azure AD Connect.

| Nombre d’objets dans Active Directory | UC | Mémoire | Taille du disque dur |
| ------------------------------------- | --- | ------ | --------------- |
| Moins de 10 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 10 000 et 50 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 50 000 et 100 000 | 1,6 GHz | 16 Go | 100 Go |
| Dans les systèmes comportant 100 000 objets ou plus, la version complète de SQL Server est requise.| | | |
| Entre 100 000 et 300 000 | 1,6 GHz | 32 Go | 300 Go |
| Entre 300 000 et 600 000 | 1,6 GHz | 32 Go | 450 Go |
| Plus de 600 000 | 1,6 GHz | 32 Go | 500 Go |

La configuration minimale requise pour les ordinateurs exécutant les services de fédération Active Directory ou les serveurs d’applications web est la suivante :

- Processeur : double cœur 1,6 GHz ou supérieur
- Mémoire : 2 Go ou plus
- Machine virtuelle Azure : configuration A2 ou supérieure


## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO1-->