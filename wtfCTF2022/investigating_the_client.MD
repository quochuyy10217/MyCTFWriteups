## Investigating the client

Open the challenge link, you will see the page like this

![anh1](https://raw.githubusercontent.com/quochuyy10217/MyCTFWriteups/main/wtfCTF2022/img_src/investiagate_1.PNG)

Now open the page's source to find if it has anything useful. You will see a javascript code snippets below.

```javascript
<script>
            function validate(){
                const pass = document.getElementById('password').value;
                split = 4;
                if(pass.substring(0,split) == 'd3Rm'){
                    if (pass.substring(split,split*2) == 'Q1RG'){
                        if(pass.substring(split*2,split*3) == 'e2Mx'){
                            if(pass.substring(split*3,split*4) == 'aTNu'){
                                if(pass.substring(split*4,split*5) == 'dF9z'){
                                    if(pass.substring(split*5,split*6) == 'MWQz'){
                                        if(pass.substring(split*6,split*7) == 'X2ww'){
                                            if(pass.substring(split*7,split*8) == 'ZzFu'){
                                                if(pass.substring(split*8,split*9) == 'fQ=='){
                                                    alert("Correct Password, Hint:hint.html");
                                                }
                                            }
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
                else{
                    alert('Incorrect Password');
                }

            }

        </script>
```

Take a quick look at the `validate` function, you will know that this function will check for the password inputted by user by split the password and compare it with a substring. The first `if` condition is check if substring from index 0 to index 3 of the password we enter is equal to `d3Rm`. After that it check for the substring from index 4 to index 7 of the password, and then check for the substring from index 8 to index 11. As you can see, the index is increased sequencelly so we don't need to spend too much time debug this function. Just copy all the substrings from the first `if` condition to the last `if` condition, you will have a string like this: `d3RmQ1RGe2MxaTNudF9zMWQzX2wwZzFufQ==`.

The string is encoded in base64. I know that because the string encoded in base64 will have the equal sign `=` at the end of the string. You can also find out that the string is encoded in base64 by reading this line of the snippets code below `alert("Correct Password, Hint:hint.html");`. Access `https://swati-verma671.github.io/Password/hint.html` and they will tell you that the string is encoded in base64.

Now search for "base64 online decoder" to decode this string and get the flag. The flag is `wtfCTF{c1i3nt_s1d3_l0g1n}`