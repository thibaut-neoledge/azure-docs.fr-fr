<properties
	pageTitle="Azure B2C Active Directory : clients de mise à l’échelle pour production/clients B2C de la version préliminaire | Microsoft Azure"
	description="Une rubrique sur les types de clients Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="swkrish"/>

# Azure B2C Active Directory : clients de mise à l’échelle pour production/clients B2C de la version préliminaire

Si vous envisagez d’écrire une application de production sur Azure Active Directory (Azure AD) B2C, vous devez être certain d’avoir le type de client approprié sur lequel travailler. Pour savoir ce que vous avez, suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure et regardez dans **Type de client**.

## Résumé

Azure AD B2C prend en charge les applications de production UNIQUEMENT sur les clients B2C **de mise à l’échelle pour production** en Amérique du Nord.

| Type de client | Pays/régions | Disponible ? |
| ----------- | -------------- | --------------------- |
| **Client de mise à l’échelle pour production** | Pays/régions d’Amérique du Nord | Oui |
| **Client de mise à l’échelle pour production** | Tous les pays/régions, à l’exception de l’Amérique du Nord | Non |
| **Client de la version préliminaire** | Tous les pays/régions | Non |

> [AZURE.NOTE]
Les clients Azure AD B2C (pour les consommateurs) sont actuellement indisponibles dans certains pays ou régions où les clients Azure AD (pour les employés) sont disponibles. Lisez les sections suivantes pour plus de détails.

## Client B2C de mise à l’échelle pour production en Amérique du Nord

Si vous [avez créé votre client B2C](active-directory-b2c-get-started.md) en Amérique du Nord, c’est-à-dire dans l’un des pays ou l’une des régions ci-après : États-Unis, Canada, Costa Rica, République dominicaine, El Salvador, Guatemala, Mexique, Panama, Porto Rico et Trinité-et-Tobago ET que le **type de client** sur votre interface utilisateur d’administrateur B2C indique **Mise à l’échelle pour production**, votre client peut être utilisé pour les applications de production.

> [AZURE.NOTE]
Les clients de mise à l’échelle pour production sont capables de mettre à l’échelle des centaines de millions d’identités de consommateurs par client.

![Capture d’écran d’un client de mise à l’échelle pour production](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## Client B2C de la version préliminaire dans un pays/région

Si vous avez créé un client B2C pendant la période d’évaluation d’Azure AD B2C, il est probable que votre **type de client** indique **Client de la version préliminaire**. Si c’est le cas, vous DEVEZ utiliser votre client uniquement à des fins de développement et de test, mais PAS pour les applications de production.

> [AZURE.IMPORTANT]
Il n’existe aucun chemin de migration à partir d’un client B2C de la version préliminaire vers un client B2C de mise à l’échelle pour production. Notez qu’il existe des problèmes connus liés à la suppression d’un client B2C en version préliminaire et à la recréation d’un client B2C de mise à l’échelle pour production portant le même nom de domaine. Vous devez créer un client B2C de mise à l’échelle pour production portant un nom de domaine différent.

![Capture d’écran d’un client de la version préliminaire](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## Client B2C de mise à l’échelle pour production en dehors de l’Amérique du Nord

Azure AD B2C n’est généralement PAS disponible en dehors de l’Amérique du Nord. Toutefois vous pouvez créer et utiliser des clients de mise à l’échelle pour production, à des fins de développement et de test dans l’un des pays ou régions suivants : Algérie, Autriche, Azerbaïdjan, Bahreïn, Biélorussie, Belgique, Bulgarie, Croatie, Chypre, République tchèque, Danemark, Égypte, Estonie, Finlande, France, Allemagne, Grèce, Hongrie, Islande, Irlande, Israël, Italie, Jordanie, Kazakhstan, Kenya, Koweït, Lettonie, Liban, Liechtenstein, Lituanie, Luxembourg, Macédoine, Malte, Monténégro, Maroc, Pays-Bas, Nigeria, Norvège , Oman, Pakistan, Pologne, Portugal, Qatar, Roumanie, Russie, Arabie saoudite, Serbie, Slovaquie, Slovénie, Afrique du Sud, Espagne, Suède, Suisse, Tunisie, Turquie, Ukraine, Émirats Arabes Unis et Royaume-Uni.

Dès l’annonce de la disponibilité mondiale d’Azure AD B2C dans les pays ou régions ci-dessus, vous pouvez continuer à utiliser ces clients de mise à l’échelle pour production et à mettre en service vos applications de production sans perte de données.

## Disponibilité des clients B2C

Les clients B2C sont actuellement indisponibles dans les pays ou régions suivants : Afghanistan, Argentine, Australie, Brésil, Chili, Colombie, Équateur, RAS de Hong Kong, Inde, Indonésie, Irak, Japon, Corée, Malaisie, Nouvelle-Zélande, Paraguay, Pérou, Philippines, Singapour, Sri Lanka, Taïwan, Thaïlande, Uruguay et Venezuela. Nous prévoyons de les inclure dans le futur.

<!---HONumber=AcomDC_0831_2016-->