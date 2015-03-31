<properties 
	pageTitle="Affichage des données SAML renvoyées par ACS (Java)" 
	description="Découvrez comment afficher SAML renvoyé par le service de contrôle d'accès dans les applications Java hébergées sur Azure." 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Affichage des données SAML (Security Assertion Markup Language) renvoyées par Azure Access Control Service

Ce guide explique comment afficher les données SAML (Security Assertion Markup Language) sous-jacentes renvoyées à votre application par Azure Access Control Service (ACS). Ce guide s'appuie sur la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse][] en fournissant le code nécessaire à l'affichage des données SAML L'application terminée sera semblable à ce qui suit.

![Example SAML output][saml_output]

Pour plus d'informations sur ACS, consultez la section [Étapes suivantes](#next_steps) .

> [AZURE.NOTE]
> Le filtre ACS Azure (par Microsoft Open Technologies) est une version préliminaire de la technologie destinée à la communauté. En tant que logiciel préliminaire, il n'est pas officiellement pris en charge par Microsoft Open Technologies, Inc. ou Microsoft.

## Sommaire

-   [Conditions préalables][]
-   [Ajout de la bibliothèque JspWriter au chemin d'accès de la build et à l'assembly de déploiement][]
-   [Modification du fichier JSP pour afficher les données SAML][]
-   [Exécuter l'application][]
-   [Étapes suivantes][]


## <a name="pre"></a>Conditions préalables

Pour réaliser les tâches présentées dans ce guide, suivez l'exemple de la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse][] et utilisez-le comme point de départ de ce didacticiel.

## <a name="add_library"></a>Ajout de la bibliothèque JspWriter au chemin d'accès de la build et à l'assembly de déploiement

Ajoutez la bibliothèque contenant la classe **javax.servlet.jsp.JspWriter** au chemin d'accès de la build et à l'assembly de déploiement. Si vous utilisez Tomcat, utilisez la bibliothèque **jsp-api.jar** située dans le dossier **lib** d'Apache.

1. Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur **MyACSHelloWorld**, puis cliquez sur **Chemin d'accès de la build**, sur **Configurer le chemin d'accès de la build**, sur l'onglet **Bibliothèques** et sur **Ajouter des archives JAR externes**.
2. Dans la boîte de dialogue **Sélection de l'archive JAR**, accédez au fichier JAR requis, sélectionnez-le, puis cliquez sur **Ouvrir**.
3. Dans la boîte de dialogue **Propriétés de MyACSHelloWorld**, cliquez sur **Assembly de déploiement**.
4. Dans la boîte de dialogue **Assembly de déploiement Web**, cliquez sur **Ajouter**.
5. Dans la boîte de dialogue **Nouvelle directive d'Assembly**, cliquez sur **Entrées du chemin d'accès de la génération Java**, puis sur **Suivant**.
6. Sélectionnez la bibliothèque qui convient, puis cliquez sur **Terminer**.
7. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de MyACSHelloWorld**.

## <a name="modify_jsp"></a>Modification du fichier JSP pour afficher les données SAML

Modifiez le fichier **index.jsp** de manière à utiliser le code suivant.

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	    <%@ page import="javax.xml.parsers.*"
	             import="javax.xml.transform.*"
	             import="org.w3c.dom.*"
	             import="java.io.*"
	             import="javax.xml.transform.stream.*"
	             import="javax.xml.transform.dom.*"
	             import="javax.xml.xpath.*"
	             import="javax.servlet.jsp.JspWriter" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Sample ACS Filter</title>
	</head>
	<body>
		<h3>SAML information from sample ACS program</h3>
		<%!
	    void displaySAMLInfo(Node node, String parent, JspWriter out)
	    {
	    
		    try
		    {
				String nodeName;
			    int nChild, i;
			    
			    nodeName = node.getNodeName();
			    out.println("<br>");
			    out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
			       
			       // Attributes.
			       NamedNodeMap attribsMap = node.getAttributes();
			       if (null != attribsMap)
			       {
	                     for (i=0; i < attribsMap.getLength(); i++)
	                     {
	                            Node attrib = attribsMap.item(i);
	                            out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
	                     }
			       }
			       
			       // Child nodes.
			       NodeList list = node.getChildNodes();
			       if (null != list)
	 		       {
			              nChild = list.getLength();
			              if (nChild > 0)
			              {                    
	
				                 // If it is a text node, just print the text.
				                 if (list.item(0).getNodeName() == "#text")
				                 {
	                                 out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
				                 }
				                 else
				                 {
				                	 // Print out the child node names.
				                	 out.print("Contains " + nChild + " child node(s): ");   
		   		                     for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    
					                    out.print("<b>" + temp.getNodeName() + "</b>");
					                    if (i < nChild - 1)
					                    {
					                    	// Separate the names.
					                    	out.print(", ");
					                    }
					                    else
					                    {
					                    	// Finish the sentence.
					                    	out.print(".");
					                    }
					                    	
				                     }
					                 out.println("<br>");
					                 
					                 // Process the child nodes.
					                 for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    displaySAMLInfo(temp, parent + nodeName + "\\", out);
				                     }
				               }
			              }
			          }
			      }
			    catch (Exception e)
			    {
			    	System.out.println("Exception encountered.");
			    	e.printStackTrace();	    	
			    }
		    }
	    %>
	
	    <%
	    try 
	    {
		    String data  = (String) request.getAttribute("ACSSAML");
		    
		    DocumentBuilder docBuilder;
			Document doc = null;
			DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
			docBuilderFactory.setIgnoringElementContentWhitespace(true);
			docBuilder = docBuilderFactory.newDocumentBuilder();
			byte[] xmlDATA = data.getBytes();
			
			ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
			doc = docBuilder.parse(in);
			doc.getDocumentElement().normalize();
			
			// Iterate the child nodes of the doc.
	        NodeList list = doc.getChildNodes();
	
	        for (int i=0; i < list.getLength(); i++)
	        {
	        	displaySAMLInfo(list.item(i), "", out);
	        }
		}
	    catch (Exception e) 
	    {
	    	out.println("Exception encountered.");
	    	e.printStackTrace();
		}
	    
	    %>
	</body>
	</html>

## <a name="run_application"></a>Exécution de l'application

1. Exécutez votre application dans l'émulateur de l'ordinateur ou déployez-la dans Azure en suivant la procédure décrite à la rubrique [Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse][].
2. Lancez un navigateur et ouvrez votre application Web. Une fois connecté à votre application, vous verrez les données SAML ainsi que l'assertion de sécurité fournie par le fournisseur d'identité.

## <a name="next_steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités ACS et découvrir des scénarios plus complexes, consultez la page [Access Control Service 2.0][].

[Conditions préalables]: #pre
[Modification du fichier JSP pour afficher les données SAML]: #modify_jsp
[Ajout de la bibliothèque JspWriter au chemin d'accès de la build et à l'assembly de déploiement]: #add_library
[Exécuter l'application]: #run_application
[Étapes suivantes]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Authentification des utilisateurs Web auprès d'Azure Access Control Service à l'aide d'Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png

<!--HONumber=47-->
