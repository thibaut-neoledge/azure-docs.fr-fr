---
title: "Exemple d’application à utiliser avec des environnements comprenant des limites de sécurité | Microsoft Docs"
description: "Déployez cette application web simple pour tester des scénarios de flux de trafic suite à la création d’un réseau de périmètre."
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4d3f5e759f46bd4c4998c5d4bf70d64190ad972e


---
# <a name="sample-application-for-use-with-security-boundary-environments"></a>Exemple d’application à utiliser avec des environnements comprenant des limites de sécurité
[Revenir à la page des bonnes pratiques concernant les limites de sécurité][Accueil]

Ces scripts PowerShell peuvent être exécutés localement sur les serveurs IIS01 et AppVM01. Ils installent et configurent une application web très simple qui affiche une page html à partir du serveur frontal IIS01 avec du contenu provenant du serveur principal AppVM01.

Cette application fournit un environnement de test simple pour la plupart des exemples de réseau de périmètre. Elle permet aussi de voir l’impact des modifications apportées aux points de terminaison, groupes de sécurité réseau, itinéraires définis par l’utilisateur et règles de pare-feu sur les flux de trafic.

## <a name="firewall-rule-to-allow-icmp"></a>Règle de pare-feu pour autoriser ICMP
Cette instruction PowerShell simple peut être exécutée sur n’importe quelle machine virtuelle Windows pour autoriser le trafic ICMP (Ping). Elle permet au protocole ping de franchir le Pare-feu Windows (pour la plupart des distributions Linux, ICMP est activé par défaut), ce qui facilite les tests et la résolution des problèmes.

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Remarque** : si vous utilisez les scripts ci-dessous, l’ajout de cette règle de pare-feu est la première instruction.

## <a name="iis01---web-application-installation-script"></a>IIS01 - Script d’installation de l’application web
Ce script :

1. ouvre IMCPv4 (Ping) sur le Pare-feu Windows du serveur local pour faciliter les tests ;
2. installe IIS et le .NET Framework 4.5 ;
3. crée une page web ASP.NET et un fichier Web.config ;
4. modifie le pool d’applications par défaut pour faciliter l’accès aux fichiers ;
5. affecte votre compte d’administrateur et votre mot de passe à l’utilisateur Anonyme.

Ce script PowerShell doit être exécuté localement, l’accès à IIS01 s’effectuant via RDP.

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"

    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>

        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'

        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'

        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC

        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01 - Script d’installation du serveur de fichiers
Ce script configure le serveur principal pour cette application simple. Ce script :

1. ouvre IMCPv4 (Ping) sur le pare-feu pour faciliter les tests ;
2. crée un répertoire ;
3. crée un fichier texte auquel la page web ci-dessus accédera à distance ;
4. affecte au répertoire et au fichier des autorisations Anonyme pour autoriser l’accès ;
5. désactive la sécurité renforcée d’Internet Explorer pour faciliter la navigation à partir de ce serveur. 

> [!IMPORTANT]
> **Meilleure pratique**: ne désactivez jamais la sécurité renforcée d’Internet Explorer sur un serveur de production, et sachez qu’il est généralement déconseillé de surfer sur le web à partir d’un serveur de production. Sachez aussi que l’ouverture de partages de fichiers en vue d’un accès anonyme, bien que déconseillée, est effectuée ici par souci de simplicité.
> 
> 

Ce script PowerShell doit être exécuté localement, l’accès à AppVM01 s’effectuant via RDP. PowerShell doit être exécuté avec des autorisations d’administrateur pour garantir la réussite de l’exécution.

    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work

    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory

    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl

    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host


## <a name="dns01---dns-server-installation-script"></a>DNS01 - Script d’installation du serveur DNS
Aucun script n’est inclus dans cet exemple d’application pour configurer le serveur DNS. Si les tests que vous conduisez sur les règles de pare-feu, groupes de sécurité réseau ou itinéraires définis par l’utilisateur nécessitent l’inclusion du trafic DNS, le serveur DNS01 doit être configuré manuellement. Dans le fichier XML de configuration réseau pour les deux exemples, DNS01 est le serveur DNS principal, et le serveur DNS public hébergé par le niveau 3 est le serveur DNS secondaire. Le serveur DNS de niveau 3 est le serveur DNS utilisé pour le trafic non local, et comme DNS01 n’est pas configuré, aucun traffic DNS local n’a lieu.

<!--Link References-->
[Accueil]: ../best-practices-network-security.md



<!--HONumber=Nov16_HO3-->


