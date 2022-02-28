## redherring wtfCTF

This challenge give us a link to github page. We must clone it and deploy the challenge using Docker. The source is [here](https://github.com/madjelly/redherringwtfCTF)
After cloning the source, you will have Dockerfile, source code folder and a file call `docker-compose.yml`. Just deploy the challenge using `docker-compose up`

![anh1](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/wtfCTF2022/img_src/redherring_1.PNG)

Next, access the challenge at `http://localhost:7000` you will see a page like this

![anh2](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/wtfCTF2022/img_src/redherring_2.PNG)

This page let you know the hashed flag. Use `view source` to open the page source. The javascript code snippets below is what we need to solve this challenge.

```javascript
<script>
        function hashFlag() {
            console.log("Hashing the flag")
            fetch("http://localhost:3003/hash")
            .then((res) => {
                console.log("Got response")
                return res.json()
            })
            .then((data) => {
                console.log("Got data")
                console.log(data.flags[0])
                
                let flag = data.flags[0].flag
                const str = flag.split("");
    
                let i, n;
                let sum = 0;
                for (i = 0; i < 15; i++) {
                    n = str[i];
                    sum = sum + n.charCodeAt();
                }

                let hash = sum * (sum + 1);
                document.getElementById("hashed-flag").innerHTML = `Your hashed flag is: <strong>` + hash + `</strong>`
            })
            .catch((err) => {
                console.log("Got error :)")
                console.log(err)
            })
        }
        function makeTestAPICall() {
            console.log("Making API Call")
            fetch("http://localhost:3003/test/nginx")
            .then((res) => {
                console.log("Got response")
                return res.json()
            })
            .then((data) => {
                console.log("Got data")
                document.getElementById("nginx-message").innerHTML = data.message
                console.log(data)
            })
            .catch((err) => {
                console.log("Got error :)")
                console.log(err)
            })
        }
        function getHashedFlag() {
            console.log("Fetching hashed flag")
            fetch("http://localhost:3003/hash")
            .then((res) => {
                console.log("Got response")
                return res.json()
            })
            .then((data) => {
                console.log("Got data")
                document.getElementById("hashed-flag").innerHTML = data.hash
                console.log(data)
            })
            .catch((err) => {
                console.log("Got error :)")
                console.log(err)
            })
        }
        function getAllFlagsFromDb() {
            console.log("Fetching flags from DB")
            fetch("http://localhost:3003/flag/db")
            .then((res) => {
                console.log("Got response")
                return res.json()
            })
            .then((data) => {
                console.log("Got data")
                let templ = ""
                for(let i = 0; i < data.flags.length; i++){
                    templ += `<li>${data.flags[i].flag}</li>`
                    document.getElementById("flag-list").innerHTML = templ
                }
                console.log(data)
            })
            .catch((err) => {
                console.log("Got error :)")
                console.log(err)
            })
        }
    </script>
```

As you can see from the code above, we have many functions but what we must focus is the `hashFlag` function. It get the flag from `http://localhost:3003/hash` and hash it to the number we saw at the index page. Maybe you will think that we must debug the function from the start to get the flag but nope, we just open `http://localhost:3003/hash` and will get the flag.

![anh3](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/wtfCTF2022/img_src/redherring_flag.PNG)

I think this challenge is a little bit easy although it's the hardest challenge in this CTF event.