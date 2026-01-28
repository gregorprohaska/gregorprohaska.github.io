---
layout: post
title: Running BIML in Visual Studio 2019
categories: [BIML, SSIS]
---

Getting a new laptop is a lot of fun, everything is soooo fast....
.... till you remember that you have to reinstall everything. ;-)

Yesterday I tried to install BimlExpress with a fresh installation of Visual Studio 2019 and failed. Today I succeded at last...
<!--more-->

After installing Visual Studio 2019 (see this [post](/sql/ssas/ssis/ssrs/2022/03/02/visual-studio-for-ssas/)), I installed the extension for SSIS via Extensions > Manage Extensions (as described [here](https://learn.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-ver16#install-ssdt-with-visual-studio-2019)) and [BimlExpress](https://www.varigence.com/bimlexpress) from file explorer. At first glance everything looked fine, the installation wizard showed a green tick and I even got a warning about using an outdated API ;-)
[![deprecated API](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/deprecatedAPI.JPG "Warning about deprecated API")](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/deprecatedAPI.JPG){:.glightbox}

But soon I noticed that I could neither add a new BIML file nor generate an SSIS package because the commands did not appear in the Solution Explorer.
[![missingBimlCommand](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/NoBiml-commands.JPG "no biml elements are shown")](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/NoBiml-commands.JPG){:.glightbox}

That was the end of yesterday!

Today, Oliver Blum's answer to this [Stack Overflow question](https://stackoverflow.com/questions/69556582/bimlexpress-and-vs2019-issues) shed some light. After further investigation, I found out that when you download the SSIS Integration via Manage Extension, the latest version is <b>always</b> downloaded, regardless of whether it is preview or GA. 

To get a version other than the latest, you have to manually download it from [visualstudio marketplace](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects).

... and downloading and installing version 3.16 finally solved my problem.

Hope that helps someone in solving this issue quicker than me (or helps me next time ;-)
