## Digital Overdose 2021 Autumn CTF

#### git commit -m "whatever"

![Anh1](../../img/wuGit1.PNG)

This is what you will see when you open the page

![Anh2](../../img/wuGit2.PNG)

When I read what they write in the page, I didn't understand at first. But fortunately, I remember what they write in the title of this chall: "git". So i began to check if this page has /.git page or not. And when i did that, something appeared.

![Anh3](../../img/wuGit3.PNG)

But they told me that I don't have permission to see this. But there is many tools can let me gain the permission to the "./git" page. And the one i chose is [GitHacker](https://github.com/WangYihang/GitHacker). With this tool, you can download everything in the "./git" page.

![Anh4](../../img/wuGit4.PNG)

After I had downloaded the source code, i remembered what they said "If only you could read the source code". So I went to look for the content of the index.html page.

![Anh5](../../img/wuGit5.PNG)

You can see that, they encrypt the flag got from the "flag.txt" file, but we don't have that file. So i think that we will change something from the index.html page. I used the privatekey and the ciphertext which I got when open the index page, and run the file again to get the flag.

![Anh6](../../img/wuGit6.PNG)

And bingo! The flag is appear :D

![Anh7](../../img/wuGit7.PNG)

Because the flag will change every time I request a new instance, so I can't show you the flag, but I can show you the way :)))
