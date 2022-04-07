# SQL Injection - Insert

This challenge has two features: Authentication and Register.

![anh1]()

To exploit this website, you must insert your payload using the register future. Firstly, I created an account, then logged into it. 

![anh2]()

After I had logged in, I saw that the Username field and the Email field is displayed. So the inject point would be one of them. It took me some time to realize that the Email field is injectable. When I entered `'` into the Email fields it would tell me that `request failed`, but when I entered `"`, nothing would happend => The query is written using `'`, not `"`.

The query when I created a new account must be like this: `INSERT INTO table_name VALUES (‘username’,’password’,’email’)`. Then we can inject the payload like this: `'),(‘vnvn’,’vnvn’,’vnvn’)#`. The query will be `INSERT INTO table_name VALUES (‘username’,’password’,’'),(‘vnvn’,’vnvn’,’vnvn’)# ‘)` and we can insert 2 records using just one SQL command. The `')` at the end is filtered out by the `#`.

After that I tried to login using the username/password is vnvn/vnvn to check if the injection is worked or not. I think you already know the answer :D

Next, I checked SQL version using `'),('id1','pw1',(select version()))#`

![anh3]()

Then I checked for the all the table name using `information_schema`. The payload is `'),('id2','pw2',(select group_concat(table_name) from information_schema.tables where table_schema=database()))#`. Please remember to use `group_concat` because if the result contains more than one row, you will get `Request failed` message.

![anh4]()

I got two table name, that is `membres` and `flag`. I checked for the `flag` table first. 
`'),('id3','pw3',(select group_concat(column_name) from information_schema.columns where table_name='flag'))#`

![anh5]()

The last thing is to get the flag using `'),('id4','pw4',(select group_concat(flag) from flag))#`

![anh6]()

and solve this challenge

![anh7]()

The flag is `moaZ63rVXUhlQ8tVS7Hw`.

