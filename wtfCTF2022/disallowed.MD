## Disallowed

![anh1]()

Open the source of this challenge

```html
<!DOCTYPE html>
<html>
    <head>
    <style>
        .wrapper{
            background-color: black;
            text-align: center;
        }
        body{
            background-color: black;
        }

    </style>
    </head>
    <body>
        <div class='wrapper'>
        <div class='up-bar' style='color:pink;font-size: 80px;padding:50px'>Just some HTML</div>
        <div class='low-bar' style='color:white;font-size:24px'>Another HTML but smaller.</div>
        </div>
        
    </body>
</html>
<!--Do you know what robots.txt is?-->
```

You can see the hint say that `Do you know what robots.txt is?`. So I access /robots.txt (You can read about robots.txt in Google)

![anh2]()

The page disallowed access to 007.html. As a hacker, we must know what this page is hiding :D

![anh3]()

View the source of 007.html and obtain the flag

```html
<html>
    <head>
        <style>
            body{
                background-color: black;
            }
        </style>
    </head>
    <body>
        <div style='font-size:64px;text-align:center;color:white;padding:50px'>@_@</div>
        <div style='color:pink;font-size:40px;text-align:center'>Good , now only if you look around here a little more you will find the flag.</div>
    </body>
    <!--wtfCTF{r0b0ts_N0t_4ll0w3d}-->
</html>
```