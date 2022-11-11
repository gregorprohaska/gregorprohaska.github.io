---
layout: post
title: Running BIML in Visual Studio 2019
categories: [BIML, SSIS]
---

Getting a new laptop is a lot of fun, everything is soooo fast, till you remember that you have to reinstall everything. ;-)
Yesterday, I tried to install BimlExpress on my new laptop with a fresh installation of Visual Studio 2019 and failed. Today, I succeded at last...
<!--more-->

After installing Visual Studio 2019 (see this [post](/sql/ssas/ssis/ssrs/2022/03/02/visual-studio-for-ssas/) to find it) I installed the Extension for SSIS via Extensions > Manage Extensions (as described [here](https://learn.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-ver16#install-ssdt-with-visual-studio-2019)) and [BimlExpress] (https://www.varigence.com/bimlexpress). At first sight, everything looked good, install wizard showed green check mark and I even got a warning about old api usage. ;-)
[![deprecated API](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/deprecatedAPI.JPG "Warning about deprecated API")](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/deprecatedAPI.JPG){:.glightbox}

But soon I noticed that I wasn't able to add a new BIML-File nor generate a SSIS Pakage since they commands didn't appear in Solution Explorer.
[![missingBimlCommand](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/NoBiml-commands.JPG "no biml elements are shown")](https://gregorprohaska.github.io/assets/image/blogpictures/2022-11-11-biml-and-visual-studio-2019/NoBiml-commands.JPG){:.glightbox}

This was the end of yesterday!

Today Oliver Blum's answer to this [Stack Overflow question](https://stackoverflow.com/questions/69556582/bimlexpress-and-vs2019-issues) shed some light on this issue. After some further searching and trying I figured out, that by downloading the SSIS-Integration via Manage Extension you download <b>always<b> the latest version no matter if it is preview or GA. 
To get any other version then the latest you have to download it from [visualstudio marketplace](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) manualy.

Hope that helps someone in solving this issue quicker than mine (or helps me next time ;-)
