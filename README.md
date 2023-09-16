# Insta_bruteforce

![image](https://github.com/torino64/Insta_bruteforce/assets/47543092/70e9563f-c927-4bca-956f-4738a5956773)

Instagrame_bruteforce is a professionally crafted Python script tailored for evaluating the password strength of Instagram accounts, employing a robust bruteforce attack approach. This script is platform-agnostic and seamlessly compatible with Kali Linux/Ubuntu/Parrot OS operating systems, ensuring flexibility for users across different computing environments. To enhance anonymity and security, Instagrame_bruteforce integrates Tor service, routing its network requests through the Tor network for added privacy and protection.

# Installation
# Kali Linux/Ubuntu/Parrot OS:

```
sudo apt-get update
sudo apt-get install git
sudo apt-get install tor
sudo apt install python3
sudo apt-get install pip
pip install --upgrade pip setuptools
git clone https://github.com/torino64/Insta_bruteforce.git
ls
cd Insta_bruteforce
ls
pip install -r requirements.txt
sudo service tor start
sudo service tor status
ctrl +c
sudo python3 insta_bruteforce.py
```
If you encounter an issue when trying to run 'insta_bruteforce,' it's essential to verify your Tor configuration. Ensure that Tor has been correctly set up with the control port enabled. You can inspect the Tor configuration file, usually found at /etc/tor/torrc, and confirm that it includes the following line:

```
ControlPort 9051
```
If it's not there, add it, and then restart Tor.


# Unlocking Instagram: The Game-Changing Features of Our Brute Force Tool

What truly sets this Instagram brute force script apart from others, readily available on GitHub, is its meticulous attention to both security and evasion techniques. While some scripts might merely initiate the Tor service and proceed with the attack, this script takes a much more comprehensive approach. It seamlessly integrates Tor proxies into its network requests to ensure anonymity and dynamic IP address rotation during the brute force operation.

The script achieves this by configuring Tor proxies as follows:

```
self.proxies = {
    'http': 'socks5://127.0.0.1:9050',
    'https': 'socks5://127.0.0.1:9050'
}
```

By doing so, all communication between the script and the Instagram server is meticulously routed through the Tor network, providing a constantly changing public IP address. Without this integration, even if the Tor service is running (as initiated with the 'sudo service tor start' command), making requests without these proxies, as shown below:

```
data = json.loads(requests.get('https://www.instagram.com/data/shared_data/', headers=headers).text)
```

would result in a static public IP address. Consequently, with just a few unsuccessful attempts, the script would risk being blocked, limiting brute force attempts to a mere three.

Additionally, this script boasts an 'encrypt_password' function, capable of encrypting passwords before they are sent to the server. As an essential security measure, this function accepts parameters such as 'key_id', 'pub_key', 'password', and 'version'. It's vital to note that sending unencrypted passwords to the server would not proceed with the credential verification process. To acquire the necessary parameters for 'encrypt_password', the script sources them from 'https://www.instagram.com/data/shared_data/'.

```
def encrypt_password(self, key_id, pub_key, password, version=10):
            key = Random.get_random_bytes(32)
            iv = bytes([0] * 12)

            time = int(datetime.now().timestamp())
            aes = AES.new(key, AES.MODE_GCM, nonce=iv, mac_len=16)
            aes.update(str(time).encode('utf-8'))
            encrypted_password, cipher_tag = aes.encrypt_and_digest(password.encode('utf-8'))

            pub_key_bytes = binascii.unhexlify(pub_key)
            seal_box = SealedBox(PublicKey(pub_key_bytes))
            encrypted_key = seal_box.encrypt(key)

            encrypted = bytes([1,
                            key_id,
                            *list(struct.pack('<h', len(encrypted_key))),
                            *list(encrypted_key),
                            *list(cipher_tag),
                            *list(encrypted_password)])
            encrypted = base64.b64encode(encrypted).decode('utf-8')

            return quote_plus(f'#PWD_INSTAGRAM_BROWSER:{version}:{time}:{encrypted}')
```
Furthermore, the script modifies certain attributes in the headers of the login page, 'https://www.instagram.com/accounts/login/ajax/'. For each request, it generates a unique 'user_agent' and extracts the CSRF token from 'https://www.instagram.com/data/shared_data/'. Additionally, it introduces new attributes like 'X-Asbd-Id,' among others.

While the script encounters occasional challenges, particularly due to captchas, it persists in its quest for successful brute force attempts. Future developments will focus on devising methods to bypass captchas. Thank you for your time.

![image](https://github.com/torino64/Insta_bruteforce/assets/47543092/c73a2728-5970-41ee-88ff-e81f35d99687)

As depicted in the image above, you'll notice that the IP address undergoes frequent changes with each new request:

    AT - Austria
    NL - Netherlands
    RU - Russia
    LU - Luxembourg

The extensive text you see typically indicates a captcha-related challenge. On the other hand, other messages pertain to the waiting period during the verification process. If you come across 'authenticated: false,' it suggests an unsuccessful password attempt.

# 📢 Warning
**This tool is exclusively intended for educational purposes. We want to emphasize that if you employ this tool for any other objectives beyond education, we disclaim any responsibility for such actions. The Phone Number Information tool cannot be held accountable for misuse or any illegal activities. We strongly advise that you restrict its usage to penetration testing or educational endeavors. Remember, hacking itself is not a criminal act. We welcome pull requests with open arms, fostering a collaborative environment.**

# I'm Not A Hacker I'm A Cyber Security Professional



