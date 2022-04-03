# autoflag

This is what you'll see when you open the challenge

![anh1](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh1.PNG)

If you click on `give me the flag` button, you will get this

![anh2](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh2.PNG)

And this is definitely not the flag we want. So I came back to home page of this challenge and click on the `Checkout the API Flag`, it lead to a Github repository. You will know what you must do when you take a look at the HTTP response

![anh3](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh3.PNG)

It told you to check the git. So you should check for the commit history to search for anything useful. Click on the commit message and you will get something that's very interesting :D

![anh4](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh4.png)

What you will get is a source code of the AUTOFLAG API.

![anh5](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh5.PNG)

Source code:

```javascript

//AUTOFLAG API V.1 : AUTOMATICALLY AUTHENTICATE USERS THEN REDIRECT TO FLAG

async function signToken() {
    header = `{"typ":"JWT","alg":"HS256"}`
    data = `{"fresh":false,"iat":1648755893,"jti":"a5139f01-3e20-446c-9627-16580f32f118","type":"access","sub":"anonymous","nbf":1648755893,"exp":1648756793}`
    unsignedToken = base64url(header) + "." + base64url(data)
    console.log(unsignedToken)
    let signature = await getSignedHMAC(unsignedToken)
    signature = signature.replaceAll('+', '-').replaceAll('=', '')
    let JWT = unsignedToken + "." + signature
    return JWT
}

function base64url(source) {
    encodedSource = btoa(source);
    while (encodedSource.endsWith('=')) {
        encodedSource = encodedSource.substring(0, encodedSource.length - 1)
    }
    encodedSource = encodeURI(encodedSource)
    console.log(encodedSource)
    return encodedSource;
}

function getSignedHMAC(unsignedToken) {
    return new Promise((resolve, reject) => {
        var xhr = new XMLHttpRequest()
        xhr.open("POST", '/api/sign-hmac', true)

        //Send the proper header information along with the request
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded")

        xhr.onreadystatechange = function () { // Call a function when the state changes.
            if (this.readyState === XMLHttpRequest.DONE && this.status === 200) {
                resolve(xhr.responseText)
            }
        }
        xhr.send(`message=${unsignedToken}`)
    })
}

async function signToken() {
    header = `{"typ":"JWT","alg":"HS256"}`
    data = `{"fresh":false,"iat":1648755893,"jti":"a5139f01-3e20-446c-9627-16580f32f118","type":"access","sub":"anonymous","nbf":1648755893,"exp":1648756793}`
    unsignedToken = base64url(header) + "." + base64url(data)
    console.log(unsignedToken)
    let signature = await getSignedHMAC(unsignedToken)
    signature = signature.replaceAll('+', '-').replaceAll('=', '')
    let JWT = unsignedToken + "." + signature
    document.cookie = JWT
}

signToken()

document.getElementById('redir').innerText = 'Redirecting you to your flag shortly'
setTimeout(() => {
    window.location.replace("/flag");
}, 2000)


```

As you can see from the source code above, what it will do is send a POST request to `/api/sign-hmac` to get an unsigned Token, then use it to create a JWT. Just grab the code, add a `console.log()` command to print out JWT that return from the function and paste to the console of this challenge. Then run it.

![anh6](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/Anh6.PNG)

After that, change your cookie to the new JWT and you will get the flag.

![anh7](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/UmassCTF2022/img_src/flag.PNG)

P.s: if you get the message like "token is expired", just change the value in `exp` field to another date time value in the future.