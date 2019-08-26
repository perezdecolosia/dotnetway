---
title: "Remove cached windows credentials for access to shared resources"
date: 2017-06-12T17:11:16+02:00
draft: false
comments: true
tags: ["Cached credenials", "Network", "Shared resources", "Windows"]
---

Today I was connecting to the NAS that I have at home for the first time since my new installation of Windows 8, so when accessing it through the file browser, it asked for the user credentials which Windows always saves once you correctly access a user. to the resource even if you do not have the check to save them activated.

Well as normally you had them cached in Windows 7 I did not investigate how to delete them to access with another user, until today when I skipped the request for credentials, I entered with another user of the NAS that has less privileges to try it. What happens when you want to enter a folder of my user when you have entered with another, that Windows asks for the user's credentials and when you enter them you throw this error:

"Multiple connections for a server or shared resource supported by the user, using more than one user name, are not allowed Disconnect previous connections to the server or shared resources and go back again."

It tells us that it does not allow us to access the owner of that resource again, because we already have the credentials of the previous user saved and we can not access it with the new one, which I have entertained in fighting today a bit to see how I could erase these credentials so that I could log in with my usual user, that is, I would ask for my credentials again when I accessed the NAS.

The solution for not having to restart and disconnect the network units:

Just open a command line and see what resources we have occupied, with "net use", and then delete them with "net use \ share / del" where the shared resource is the address that the "net use" has shown us

Well not simple, now I have no excuse to try different logins of the shared resources that go configuring

