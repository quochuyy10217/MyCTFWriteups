## Digital Overdose 2021 Autumn CTF

### notrequired

When you open an instance of this chall, you will see a page like this

![wuLFI1](https://user-images.githubusercontent.com/67006728/136723727-9d22946e-eaef-4c13-bb03-bdccd844f2e2.PNG)

Now look carefully at the url of this chall, you will see that the url is like `/index.php?page=index.html`. This is an evidance show that this page may be suffer from LFI. If you don't know what LFI is, please read [this](https://book.hacktricks.xyz/pentesting-web/file-inclusion). You should learn about PHP wrapper as well because we will use it to solve this chall.

To check if the page is suffer from LFI or not, I will use the PHP wrapper to check: I change the url to `/index.php?page=php://filter/convert.base64-encode/resource=index.html`. And the result is

![wuLFI2](https://user-images.githubusercontent.com/67006728/136723735-981ab250-15a9-4d23-8065-e1e34473c864.PNG)

When I use the PHP wrapper, the content of the page is convert to base64. This method will be useful when you want to read the PHP code of the page.After that I use base64 decoder to see what is the source code of the page

![wuLFI3](https://user-images.githubusercontent.com/67006728/136723742-02b33246-085a-4fda-95ff-778534ac811a.PNG)

As you can see in the image above, the source code has a comment that remind the developer himself to delete /bin/secrets.txt. So we will go to that page to check if it is deleted or not.

![wuLFI4](https://user-images.githubusercontent.com/67006728/136723746-75d2dfe5-b3b4-45c1-b449-cfeb63e30f74.PNG)

And boom! We get the flag~

### git commit -m "whatever"

![wuGit1](https://user-images.githubusercontent.com/67006728/136659402-0623a624-e2b1-4ae1-98f7-393b9479e2cc.PNG)

This is what you will see when you open the page

![wuGit2](https://user-images.githubusercontent.com/67006728/136659409-d4e31605-f3e5-452f-82df-f5c1400f111f.PNG)

When I read what they write in the page, I didn't understand at first. But fortunately, I remember what they write in the title of this chall: "git". So i began to check if this page has /.git page or not. And when i did that, something appeared.

![wuGit3](https://user-images.githubusercontent.com/67006728/136659412-8fd418f6-a520-4fdf-b631-de37646e1e0a.PNG)

But they told me that I don't have permission to see this. But there are many tools can let me gain the permission to the "./git" page. And the one I chose is [GitHacker](https://github.com/WangYihang/GitHacker). With this tool, you can download everything in the "./git" page.

![wuGit4](https://user-images.githubusercontent.com/67006728/136659417-a84c2df2-7809-47b5-bb20-ea766a40cbee.PNG)

After I had downloaded the source code, i remembered what they said "If only you could read the source code". So I went to look for the content of the index.html page.

![wuGit5](https://user-images.githubusercontent.com/67006728/136659421-e9558cb2-3d7b-4082-a9f8-af6680c7b5d9.PNG)

You can see that, they encrypt the flag got from the "flag.txt" file, but we don't have that file. So i think that we will change something from the index.html page. I used the privatekey and the ciphertext which I got when open the index page, and run the file again to get the flag.

![wuGit6](https://user-images.githubusercontent.com/67006728/136659427-ab4cfb28-e116-4598-8376-6c2e6a0d2dc8.PNG)

And bingo! The flag is appear :D

![wuGit7](https://user-images.githubusercontent.com/67006728/136659430-ab86d816-19a7-41a0-8226-86a481503e5b.PNG)

Because the flag will change every time I request a new instance, so I can't show you the flag, but I can show you the way :)))
