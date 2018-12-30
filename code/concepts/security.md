# Security

## Crypto
https://danielmiessler.com/study/encoding-encryption-hashing-obfuscation/#encoding

## Links
https://sudo.pagerduty.com/for_engineers/


---
## SQL Injection
Using `' OR 1=1 --`
```
SELECT *
FROM users u
WHERE
  u.username = 'admin'
  AND u.password = ''
  OR 1=1
```
