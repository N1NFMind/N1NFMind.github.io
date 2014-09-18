---
layout: project_category
title: SQL CLR
categories: [project_category, sql_clr]
date:   2014-09-17 11:15
project: sql_clr
---

# Welcome to my SQL CLR Projects!
Welcome to my open-source repository for SQL CLR projects! Within this site I have tried to clean up and document some of the utilities that I have built and found useful in my own career. Please feel free to take what you can from them!

## About SQL CLR
CLR is a very powerful addition to SQL Server, allowing developers to augment the core functionality of the services with their own functionality. When used correctly, this can lead to incredible performance gains of complex queries. When misused, this can hide problems, bog down, or even crash the entire server. Because of the risk, I build all of my SQL CLR projects so that they can be ran in "safe" mode. Refer to CLR Integration Code Access Security (http://msdn.microsoft.com/en-us/library/ms345101.aspx) for more information on why. 

## Notes on Installing
All of these projects are built as database projects in Microsoft Visual Studio 2013, and all follow a similar pattern. Because of this, the instructions are all very similar.

I would recommend downloading and deploying the version specific data-tier application package (*.dacpac) for your database. 
- ** Deploy from DACPAC** - To deploy from a data-tier application package, all you need to download is the one *.dacpac file that corresponds to your target SQL Server edition. Then, follow the version specific instructions to deploy that file. Refer to http://msdn.microsoft.com/en-us/library/ee210569.aspx to get started (this provides instructions for deploying a Data-tier Application to SQL Server 2014, but instructions for both SQL Server 2012 and 2008 R2 are available under "Other Versions")

Otherwise, if you have Microsoft Visual Studio 2013 and would like to make changes, you could publish from visual studio to an existing database.
- **Publish from Visual Studio** - Download the source code for the entire solution. Open up the solution in Microsoft Visual Studio 2013, make any desired changes. Be sure to set the target platform to the correct database version. Then, right click the project and select "publish" from Solution Explorer, or, with the focus on the correct project, select "Build >> Publish (project)..." from the menu bar. Specify your target database connection and any advanced publish settings that you need, and click "Publish". 

## Terms of use
All of these projects have the same terms of use. *Refer to the file **LICENSE** for the specifics...*

Most of this code I have written from scratch. Some snippets came from surfing the net, and some of the ideas came from work others have done and made available. Use this code as you see fit... I am not looking for credit, but to give back to the community. I do ask that as you use and learn from the work of others, please keep that in mind, and when you have a chance to give back, you do so. SQL Server is not open source, but that is no reason that the community cannot continue to be as supportive and sharing as I have always found it to be!

## Making changes
There are only two ways to learn: from others and by doing. The best way to learn in my opinion is to start by seeing what other people have done, and then either try to make changes to it or try to build it from scratch yourself. I highly encourage taking this code, using it and modifying it as you see fit! 

If you build an awesome enhancement or new related SQL CLR function and would like to contribute, please feel free! Simply fork off of this project and send me a pull request once you have applied your changes (https://help.github.com/articles/fork-a-repo). If it is not originally your work, please give credit where credit is due and reach out to the originator first where possible.

## Contact me
Feel free to contact me at github@n1nfmind.com. Please keep in mind that this is not my primary email address nor job, so I may not respond right away!
