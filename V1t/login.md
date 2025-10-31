# Login Page

## Description
Question tells us to go to https://tommytheduck.github.io/login and login to get the flag

## Solution
First thing I tried was `curl -I -v https://tommytheduck.github.io/login/`  
This didn't give anything useful so i tried sending a user pass through here  
`curl -i -u username:password https://tommytheduck.github.io/login/`  
This gave me the whole authentication scheme and the hashed username and password:
`  const ajnsdjkamsf = 'ba773c013e5c07e8831bdb2f1cee06f349ea1da550ef4766f5e7f7ec842d836e'; // replace  
      const lanfffiewnu = '48d2a5bbcf422ccd1b69e2a82fb90bafb52384953e77e304bef856084be052b6'; // replace  `
Cracking the hashes let me login and get the flag

## Flag
v1t{p4ssw0rd}
