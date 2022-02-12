## Web-intro

Accessed the chall, this is what I saw

![anh1](https://live.staticflickr.com/65535/51875724377_7fcebe2e7e_z.jpg)

There is no clue about what to do, so I looked at the HTTP request to check if there was anything wrong

![anh2](https://live.staticflickr.com/65535/51876764283_f44e9900ac_z.jpg)

The cookie session they set maybe injectable because when you decode the first part of the cookie before the first dot using base64 decode, you can see that it has the string `{'logged_in': False}`. When I saw it, I tried to change it value to `True`, then encoded it without making any change at the other part of the cookie, but it failed. So I looked for a tool to decode this cookie, and I found the tool named [Flask Unsign](https://pypi.org/project/flask-unsign/). This tool can be used to decode Flask cookie, brute-force and craft session cookies of Flask.

Then I used Flask Unsign to decode the cookie

![anh3](https://live.staticflickr.com/65535/51876688136_33e83fc1e5_z.jpg)

Brute-force its secret

![anh4](https://live.staticflickr.com/65535/51877330140_933bd90fb9_z.jpg)

Finally, Signed a new cookie with the secret

![anh5](https://live.staticflickr.com/65535/51877330135_78a3d793eb_z.jpg)

Sent request to server and obtain the flag

![anh6](https://live.staticflickr.com/65535/51876764248_81a48cb4f7_z.jpg)

Flag is: `CTF{66bf8ba5c3ee2bd230f5cc2de57c1f09f471de8833eae3ff7566da21eb141eb7}`