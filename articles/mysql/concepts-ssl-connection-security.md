---
title: "Connectivité SSL de la base de données Azure pour MySQL | Microsoft Docs"
description: "Informations de configuration de la base de données Azure pour MySQL et des applications associées afin d’utiliser correctement les connexions SSL."
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6dc147c9dd3983c2c599108162fa285d87ffc7a3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Connectivité SSL dans la base de données Azure pour MySQL
La base de données Azure pour MySQL prend en charge la connexion de votre serveur de base de données aux applications clientes via SSL (Secure Sockets Layer). L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.

## <a name="default-settings"></a>Paramètres par défaut
Par défaut, le service de base de données doit être configuré pour exiger des connexions SSL lors de la connexion à MySQL.  Il est recommandé d’éviter de désactiver l’option SSL dans la mesure du possible. 

Lorsque vous approvisionnez un nouveau serveur de base de données Azure pour MySQL par le biais du Portail Azure et d’Azure CLI, l’application de connexions SSL est activée par défaut. 

De même, les chaînes de connexion prédéfinies dans les paramètres « Chaînes de connexion » du serveur sur le Portail Azure incluent les paramètres requis par les langages courants pour se connecter au serveur de base de données via SSL. Le paramètre SSL varie en fonction du connecteur, par exemple « ssl=true », « sslmode=require » ou « sslmode=require » et d’autres variations.

Pour savoir comment activer ou désactiver la connexion SSL pour le développement d’applications, consultez la page [Guide pratique : configurer SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Étapes suivantes
[Bibliothèques de connexions de la base de données Azure pour MySQL](concepts-connection-libraries.md)

